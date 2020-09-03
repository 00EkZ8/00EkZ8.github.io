---
date: 2020-09-01
---

Setting up dynamic inventory for AWX I started having issues with hosts coming in as disabled.  (Screenshot below)

| ![disabled_host.png](/assets/disabled_host.png) | 
|:--:| 
| [Enlarge](/assets/disabled_host.png) |

Since this was multiple hundreds of hosts, across multiple locations and organizations, enabling them manually wasn't really an option. To to mention on next sync they would come in as disabled again.
The hosts all looked the same on the Red Hat Satellite UI at first glace. I tested editing the owner of the satellite object , the permissions of my sync user, and a multitude of other things while troubleshooting, none of which fixed the issue. 
Running the hammer command below

	hammer host info --id 123

against a host showing as disabled in the AWX inventory revealed this (excerpt from output):
 
		Parameters:
	All Parameters:
		Enabled: no

On an enabled hosts this setting was Enabled: yes

The All Parameters portion of the output includes things like Owner, Owner Id, Owner Type, etc. with no real explination of what Enabled refers to. It refers to the setting inside Red Hat Satellite 'Include this host within Satellite reporting'.
In the Satellite UI this is located under All hosts, edit host, Additional Information. The field is named,'Enabled' with a check box. Checking this box will now make your host come in as enabled into AWX.


That is probably fine for one or two, since you could have a few hundred hosts with this issue, we'll use some hammer commands wrapped in a bash loop to resolve this and make sure we get them all. The script below will allow you to make the change based on a Red Hat Satellite organization.

	
	#!/bin/bash
	ORG="ACME"
	for i in $(hammer --csv host list --thin yes --organization $ORG --search status.enabled=false | grep -vi '^ID' | awk -F, {'print $1'})
	do
        hammer host update --id ${1} --enabled true
	done

  * Hammer command to find reporting disabled host and set the status to enabled*

