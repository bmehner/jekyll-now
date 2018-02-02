---
layout: post
title: On My Journey To A Paperless Life Part II - Backup
---
In my post about [My Journey To A Paperless Life](https://bmehner.github.io/on-my-journey-to-a-paperless-life/) I wrote about how I digitalize all my documents. The initial intention of this was to have a copy of everything important document outside our house. The blog post ends with the sentence "And of course the most important thing: Backing up these documents in the cloud is now a no-brainer." But is it really a no-brainer? Yes and no...

## Requirements For A Backup

After I started to scan every document and make them searchable with the help of OCR the amount of scanned files increased rapidly. I began to think about the backup part of my journey to a paperless life. I came up with a bunch of requirements:

1. **Offsite**: The backup should survive our house burning down, being flooded or other elementary dangers. Collective hardware crashes due to voltage peaks caused by lightning or similar events should also be covered.

1. **Cost effective**: Cost should be below 10€/month.

1. **Automated and regular**: I do not want to think about it. I want it to be one thing less I have to care about.

1. **Encrypted**: If I want to backup all important documents then this includes some fairly sensitive stuff: data on salary and taxes, account statements, insurance policies, pictures of my family and so on. Strong encryption is a must for the desired solution.

1. **Everything in one place**: If the shit hits the fan and all data at home is lost some how I need fast access to the data. It is not suitable to spread the data over different storages.

1. **Big enough to store 250GB of data**: Everything should be safe which is not easily reproducible. This includes all personal fotos and videos.

1. **Storage in Germany**: I want my data to be under German jurisdiction. Many countries deal with foreigners different than with nationals. Well known examples are the US and even Germany. I feel not comfortable using storage outside Germany.

1. **Protection against Ransomware**: The solution should provide protection against loosing files to encryption by Ransomware. Ransomware should have no access to the storage of the backup. Otherwise it would also encrypt my backup.

To sum it up: If my data at home is lost somehow, I want to reinstall my computer and recover all my data from my offsite location. The whole process should be completed in a time frame of 4 hours maximum.

## Solution? Not so easy...

Just backing up data to the cloud storage is actually a no-brainer. A solution that meets all the above requirements proved to be a lot trickier.

Dropbox for example provides an easy to use cloud storage. It has a backup option, too, so it would be suitable to protect the data against Ransomware. The backup option doubles the required storage which raises the costs of the solution. This maybe would have been tolerable. Unfortunately Dropbox does not support encryption of the data and there is no storage in Germany. 

Cloud storages with integrated encryption are rare and usually quite expensive. I had one possible provider but it went out of service before I was able to use it. Also the required space would have been too expensive.

So I struggled a long time to find the ideal solution. In the meantime I used rsync with a external harddrive attached to my computer. Not perfect...

## Duplicity And Strato HiDrive: Building Blocks Of A Solution

Then I stumbled over [Duplicity](http://duplicity.nongnu.org/). It backs up files and encrypts them on the fly. It is based on rsync which is reliable and widely used. It supports a ton of different types of storages like ssh/scp, WebDAV, Amazon S3, Google Drive and so on.

With the following command you perform a full backup on the first call and an incremantal backup on each following call:

    duplicity /home/me ssh://uid@other.host/some_dir 

Duplicity puts files in different tar archives of roughly the same size. Then it encrypts those files with gpg. After that it sends the file to the specified storage. It supports options for excluding files. There are also a bunch of commands that help you manage your backup like verifying, listing, removing and of course recovering files. I had found my tool for the backup. 

What was left was the decision where to store the backup. With Duplicity I had a lot of options now. I could even use Dropbox as it is supported. There were only two decisive criteria left: security and of course the price.

The winner finally was Strato HiDrive. The cost are 2,50 € for 250 GB per month. And it supports a bunch of ways to connect to it. One of them is ssh. So login and passwords are sufficiently protected. Additional all the servers are located in Germany where I live and are covered by German jurisdiction.

Finally a daily cron which starts the backup job and all my backup requirements are fullfilled. From time to time I test the recovery process and that's it. One thing less to care about.
