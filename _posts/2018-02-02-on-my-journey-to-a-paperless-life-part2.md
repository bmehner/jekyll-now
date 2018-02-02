---
layout: post
title: On My Journey To A Paperless Life Part II - Backup
---
In my post about [My Journey To A Paperless Life](https://bmehner.github.io/on-my-journey-to-a-paperless-life/) I wrote about how I digitalize all my documents. The initial intention of this was to have a copy of everything important document outside our house. The blog post ends with the sentence "And of course the most important thing: Backing up these documents in the cloud is now a no-brainer." But is it really a no-brainer? Yes and no...

## Requirements For A Backup

After I started to scan every document and make them searchable with the help of OCR the amount of scanned files increased rapidly. I began to think about the backup part of my journey to a paperless life. I came up with a bunch of requirements:

1. **Out of the house**: The backup should survive our house burning down, being flooded or other elementary dangers. Collective hardware crashes due to voltage peaks caused by lightning or similar events should also be covered.

1. **Cost effective**: Cost should be below 10€/month.

1. **Automated and regular**: I do not want to think about it. I want it to be one thing less I have to care about.

1. **Encrypted**: If I want to backup all important documents then this includes some fairly sensitive stuff: data on salary and taxes, account statements, insurance policies, pictures of my family and so on. So strong encryption is a must for the desired solution.

1. **Everything in one place**: If the shit hits the fan and all data at home is lost some how I need fast access to the data. So it is not suitable to spread the data over different storages.

1. **Big enough to store 250GB of data**: Everything should be safe which is not easily reproducable. This includes all fotos and videos.

1. **Protection against Ransomware**: I want it out of the house. So basically every synching cloud storage like dropbox could do the trick. Unfortunately this does not protect you from Ransomware. The Ransomware would also encrypt the data in the cloud storage.

Just backing up data to the cloud storage is actually a no-brainer. A solution that meets all the above requirements proved to be a lot trickier.

Dropbox for example would provide an easy to use cloud storage. It provides also a backup option so it would be suitable to protect the data against Ransomware. The backup option doubles the required storage which raised the costs of the solution. This maybe would have been tolerable. Unfortunately Dropbox does not support encryption of the data. Every solution I found which adds encryption to dropbox had one or more other disadvantages that made the solution unsuitable. 

Cloud storages with integrated encryption are rare and usually quite expensive. I had one possible provider but it went out of service before I was able to use it. Also the required space would have been too expensive.

So I struggled a long time to find the ideal solution. In the meantime I used rsync with a external harddrive attached to my computer. Not perfect...

## Duplicity And Strato HiDrive: Building Blocks Of A Solution

Then I stumbled over Duplicity. It promised to backup files and encrypt them on the fly. Also it is based on rsync which is reliable and widely used. There are a lot storage providers that support it. 



