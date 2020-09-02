---
layout: default
title:  "AWX Satellite Inventory Issue"
---

#Hammer command to find reporting disabled host and set the status to enabled


`#!/bin/bash
ORG="ACME"
for i in $(hammer --csv host list --thin yes --organization $ORG --search status.enabled=false | grep -vi '^ID' | awk -F, {'print $1'})
do
i        hammer host update --id ${1} --enabled true`
done
