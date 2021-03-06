---
date: 2021-02-19
---
Recently while working on converting a few hundred servers from  Red Hat Identity manager(FreeIPA) to Active Directory for ssh logon and sudo. I had a need to resolve home directory permissions. When going to AD the UID number changes, SSSD uses the AD SID of the user to algorithmically to generate Posix IDs. The process is explained in detail here. 

[RedHat 7 Windows Integration guide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/windows_integration_guide/SSSD-AD#sssd-ad-proc)

Since our IDM usernames mostly matched the usernames that would be created in Active Directory I decided to loop through the current /home and use the name to assign the new owner. For the entire process I decided on ansible so we have a playbook that will un-join from the IDM realm then join the AD domain installing all needed dependencies a long the way. The last part of the playbook loops through the current home directories and re-assigns the permissions. If it hits a user that does not exist in AD it will fail, since `ignore_errors: True`  the job will keep running. 

A when statement is used for any local accounts that we don't want to touch with this process since their UID should not be affected. Depending on the server the local users are added to the when statement and are skipped. This gets a majority of users correctly switched over to AD from IDM with minimal intervention.

        {% raw %}
    - name: Register all current home directories
      command: ls -v /home
      register: homedirs
    
    - name: Changing homedir permissions for IDM to AD
      ansible.builtin.file:
        path: "/home/{{ item  }}"
        owner: "{{ item  }}"
        group: "domain users"
        recurse: yes
      with_items: "{{ homedirs.stdout_lines  }}"
      when:
        - 'item != "localuser1"
        - 'item != "localuser2"
      ignore_errors: True
        {% endraw %}