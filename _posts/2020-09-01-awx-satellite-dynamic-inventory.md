---
layout: default
title:  "AWX Satellite Inventory Issue"
---

While getting dynamic inventory setup for AWX I started having issues with some hosts randomly coming in as disbaled.  (Screenshot below)
Since this was close to 1000 hosts enabling them manually wasn't really an option, including the fact on next sync they would come in as disabled again.
The hosts all looked the same on the Red Hat Satellite UI. I tested editing the owner of the satellite object , the permissions of my sync user, and a multitude of other things, none of which fixed the issue. 
Running the hammer command below

	hammer host info --id 123

against a host coming in as disabled revealed this (excerpt from output):
 
		Parameters:
	All Parameters:
		Enabeld: no

The All Parameters portion of the output includes things like Owner, Owner Id, Owner Type, etc. with no real explination of what Enabled refers to. It refers to the setting inside Red Hat Satellite 'Include this host within Satellite reporting'.
In the UI this is located under All hosts, edit host, Additional Information. The field is named, Enabled with a check box. Checking this box will now make your host come in as enabled into AWX.


| ![disabled_host.png](/assets/disabled_host.png) | 
|:--:| 
| [link to pic](/assets/disabled_host.png) |


Since you could have a few hundred hosts that are doing this, we'll use some hammer commands wrapped in a bash loop to resolve this.

Hammer command to find reporting disabled host and set the status to enabled
	
	#!/bin/bash
	ORG="ACME"
	for i in $(hammer --csv host list --thin yes --organization $ORG --search status.enabled=false | grep -vi '^ID' | awk -F, {'print $1'})
	do
        hammer host update --id ${1} --enabled true
	done

