---
date: 2026-09-01
categories:
  - Release
tags:
  - 1.5.0
---

# RecoveryBox v1.5.0 Release

RecoveryBox version 1.5.0 is finally out. Technically it had been ready since mid-August, available in the project's GitHub tags, but I delayed the official release. I wanted to progress on the logo and the website's graphical improvements before publishing the new version. However, time goes by, I enjoyed a bit of my vacations to disconnect from everything computer-related, and naturally set the RecoveryBox aside. At the time of writing this, the logo is now created but not yet integrated into the website. We'll come back to that later. For now, let's talk about this 1.5.0 version.

## Version 1.5.0 Features

Version 1.5.0 is a bit of a miscellany, with several improvements and bug fixes. Promised, future versions will be more focused on specific features. But I still had to integrate various minor changes to have a coherent and functional version.

This version therefore includes battery management with Victron MPPT. For this, I had to look here and there to find information on the VE.Direct API. I wanted to do this with Python or bash. Although the protocol is open source and one hears quite a bit that it's easy to use, I found very few projects really worth exploiting. Furthermore, the documentation is very nebulous as well. By chance, I fell upon the [ve.direct project by foxharp](https://github.com/foxharp) which gave me a good basis for the correspondence of the various values. I wanted to integrate a clean server shutdown when the battery charge level drops too low. This prevents system corruption in case of power loss. This system only works if the server is directly connected to the battery and not on the MPPT's output load. If that is the case, the MPPT will just cut its output load if the battery charge drops too much before the server manages to launch its shutdown, and we return to the initial problem. Adding this script also allowed pulling battery information onto the RecoveryBox homepage. We can therefore have information regarding the battery voltage, information regarding the electrical input in case of connection to solar panels or an electrical input, as well as the Load output consumption if it is used as a 12 V output to power other equipment.

I also added a basic script for installations on a laptop or other equipment with an internal battery. This is much simpler; it only reports the battery state on the RecoveryBox homepage. The goal is simply to inform the user of the battery state to avoid unexpected power cuts.

This version also includes the possibility to enable HTTPS for all services except meshtastic-web-client (we'll come back to that). It is a fairly basic integration with a self-signed certificate generated during installation. It goes without saying that it is possible to replace this certificate with a valid one issued by a recognized certificate authority. I can hear you already saying "with Let's Encrypt it's free to get a valid certificate", yes that's true, but you still have to go through the validation and renewal procedure. With a standard provider, you have to renew the certificate every year, and this tends to become more and more frequent. Certificates are having shorter and shorter durations. With Let's Encrypt it's free but you have to renew every 3 months.

The RecoveryBox is intended to operate offline, so we cannot rely on random certificate renewal. One could argue that we set up an automatic renewal system and if the certificate expires, it's not a problem, we can still connect despite the error message. And that's where things bother me. For an unsuspecting user, if they use HTTPS with valid certificates, they will never have an error, never an exception to validate. Now if the equipment ends up offline and cannot renew the certificate, the user won't understand why they suddenly receive an error message about an expired certificate. This could create confusion, or the user might go to understand, to repair, or even consider the service as offline. This goes against the RecoveryBox objective, which is to remain simple and functional even offline. The decision has therefore been taken to operate with a self-signed certificate. In this way, the user has the error message on the first connection, but once get past it, they will not encounter any more certificate-related problems.

HTTPS is a feature to enable during installation. By default, it is disabled and one must choose to enable it if one wants to secure communications with a self-signed certificate. This allows keeping a simple and functional configuration while offering the possibility to secure exchanges if necessary.

HTTPS is however never enabled on the meshtastic web-client. This is possible but when HTTPS is activated all exchanges must be done via HTTPS; leaving the interface in HTTP it is possible to contact nodes via HTTP or HTTPS.

Enabling HTTPS requires accepting a self-signed certificate for each node one wants to administer. The manipulations seemed too heavy so I decided to keep the interface in HTTP regardless.

I also took advantage of this update to integrate [Flatnotes](https://github.com/dullage/flatnotes), a lightweight and fast note-taking system. This allows users to create and manage notes directly from the RecoveryBox interface. The installation proposes 3 modes: 

- open: The default mode, a single instance available for reading and writing without a password.
- secure: A secured instance with a password, allowing restricted access to authorized users.
- full: Two instances in parallel, a password-secured one with read and write rights and a second read-only one accessible without a password.
Flatnotes simply generates markdown. It can be used with standard markdown syntax or in WYSIWYG (What You See Is What You Get) mode for more visual and intuitive editing.

This update also includes some bugfixes such as improvement of wifi network scanning, a major optimization on iptables, and the modification of the openwebrx container behavior. Nowadays it only starts if an SDR device is detected. Before that, the container started and if no SDR was connected at that moment it could never use an SDR. It was necessary that the SDR was connected before the container started for it to be capable of detecting it. I therefore set up a udev rule to detect the most known SDRs (RTL-SDR, Airspy, HackRF, LimeSDR, SDRplay...) to start the openwebrx container only when an SDR is detected.

## Site Improvements

The site should soon receive some graphical improvements. It's time to integrate images to present the different RecoveryBox features in a more visual and attractive way. But above all the logo! I took far too much time to realize it. The idea was there but then it had to be done in vector format. I spent quite some time, I was discovering Inkscape and despite hundreds of hours spent on Photoshop I must admit the logic is not at all the same. But the result is worth it and I am quite satisfied with the final render.

The great challenge in adding images is keeping space. Recall, the site runs on a free static hosting of 100 Mo. So we must compress photos as much as possible. I found an online webp converter that allows significantly reducing image size while maintaining good visual quality. This should allow adding illustrations without exceeding the storage limit. I am nonetheless curious to see how much space I can gain with this image format. The idea is to put a small photo grid (6 or 8) clickable on the homepage. We'll see what MkDocs allows me to do, the glightbox plugin should do the job.

In the meantime, the site remains functional and continues to provide all necessary information about the RecoveryBox. Future updates should bring an even more enjoyable and intuitive user experience.