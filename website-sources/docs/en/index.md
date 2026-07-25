---
title: Home
hide:
  - navigation
  - toc
---

<style>
  html {
    scroll-behavior: smooth;
  }
</style>

# RecoveryBox {: .md-typeset .md-display-1 }

## The digital solution when normalcy breaks down.


[Explore Technical Wiki](https://recoverybox.fr/wiki/){ .md-button .md-button--primary }
[Read the News](blog/index.md){ .md-button }

---

<div class="grid cards" markdown>

- [:material-battery-high: **An Autonomous Solution**](#section-autonomous)

    Designed to work in all conditions, even without an Internet connection.

- [:material-debian: **Open Source**](#section-debian)

    Built on an open-source foundation, ensuring transparency and flexibility.

- [:material-wifi: **WiFi Access Point**](#section-wifi)

    Creates a local WiFi network to connect all your devices and access services.

- [:material-radio: **SDR Radio (OpenWebRX Plus)**](#section-radio)

    Web interface to receive and listen to radio frequencies using RTL-SDR USB keys.

- [:material-lan: **Mesh Network (Meshtastic)**](#section-mesh)

    Web client to visualize Meshtastic nodes and map the local network.

- [:material-map: **Offline Mapping**](#section-carto)

    BRouter, tileserver-gl, and local maps to calculate routes without Internet.

- [:material-wikipedia: **Offline Documentation**](#section-documentation)

    Access French and English Wikipedia via ZIM files, without an Internet connection.

- [:material-crosshairs-gps: **GPS & Time Sync**](#section-gps)

    Chrony provides exact time to the server via a connected GPS module.

- [:material-server: **Client-Server Architecture**](#section-architecture)

    RecoveryBox relies on a client-server architecture; services are accessible simultaneously from any device connected to the local Wi-Fi.


</div>

---

## An Autonomous Solution {: #section-autonomous .rb-section-title }

RecoveryBox was designed as a complete, standalone solution capable of operating in isolated environments or during a crisis. Deployable in seconds, it provides immediate access to essential digital services without depending on an Internet connection. While the software can be used on its own, it reaches its full autonomy when combined with its dedicated hardware.

Solar panel, Victron MPPT regulator, and 20Ah LiFePO4 battery: every component guarantees total electrical independence. Developed around ultra-low-power mini-PCs, RecoveryBox delivers 24 hours of continuous operation without sunlight. Coupled with its 100W panel, it operates indefinitely in autonomous mode, ensuring the continuity of mapping, communications, and emergency resources.

This autonomy also translates into flexible connectivity (local WiFi access point, smartphone hotspot sharing, or wired connection) and a resilient design. Housed in a reinforced, waterproof, and shockproof case, it is easy to transport in the field. On the software side, its modular architecture isolates each service in a container, guaranteeing a customizable, robust, and failure-proof system.

## Open Source {: #section-debian .rb-section-title }

RecoveryBox runs on Debian GNU/Linux and 100% open-source building blocks, guaranteeing total transparency, enhanced security, and absolute digital sovereignty.

A proven and open ecosystem:

- **Infrastructure & Containers** : Relies on industry standards such as Docker for service isolation, Apache for web processing, and robust networking tools.

- **Exceptional Embedded Services** : Directly integrates flagship free projects such as [Kiwix](https://www.kiwix.org/) (offline documentation), [OpenWebRX+](https://openwebrx.de/) (SDR radio), or [BRouter](https://brouter.de/) (mapping).

- **Public Code on GitHub** : The entire source code and deployment scripts are freely accessible on [GitHub](https://github.com/mr-dgidgi/recoverybox), allowing anyone to audit the system, duplicate it, or customize it.

Free from any proprietary lock-in and guaranteed without any subscription, this open project puts proven technologies at the service of everyone's resilience.

## WiFi Access Point {: #section-wifi .rb-section-title }

Once powered on, RecoveryBox automatically generates its own local and secure Wi-Fi network.

No Internet connection, no router, and no 4G/5G coverage is required. Simply connect to this network from any device (smartphone, tablet, computer) to immediately access all embedded resources.

A true local interconnection hub:

- **Service Distribution** : It serves as a single entry point to all RecoveryBox applications (offline maps, emergency guides, messaging, radio tools).

- **Inter-Device Communication** : It allows connected users to exchange directly with each other within the local network.

- **Network Extension** : Other useful equipment can be connected to it in the field, such as Wi-Fi printers, IP cameras, or local NAS devices, creating a veritable autonomous infrastructure network.

## SDR Radio (OpenWebRX Plus) {: #section-radio .rb-section-title }

RecoveryBox Embeds the OpenWebRX+ solution, turning the system into a genuine radio spectrum listening and analysis center. Accessible directly from your web browser without any software to install, it allows you to intercept and visualize radio signals in real time.

Exceptional listening versatility:

- **Standard Broadcast** : Listening to FM, AM, and Shortwave (HF) bands to follow news bulletins and world radios.
- **Maritime Communications** : Decoding emergency signals, marine weather (FAX/SSTV), and vessel traffic (AIS).
- **Aircraft Tracking (ADS-B & ACARS)** : Live reception of the position, altitude, and flight data of surrounding aircraft.
- **Amateur Radio Networks & APRS** : Listening to local repeaters, tracking APRS geolocation beacons, and decoding digital modes (DMR, FT8, Packet Radio).

## Mesh Network (Meshtastic) {: #section-mesh .rb-section-title }

RecoveryBox natively integrates Meshtastic, an off-grid radio communication technology based on the LoRa protocol. It allows text messages and GPS positions to be exchanged over dozens of kilometers, without cellular network, Internet, or central infrastructure.

Deep integration into the RecoveryBox ecosystem:

- **Embedded Web Client** : Access the complete Meshtastic interface directly from your browser via the RecoveryBox Wi-Fi hotspot. No mobile app installation required.
- **Real-Time Local Mapping (Meshtastic-daemon)** : Thanks to the integration of a dedicated system daemon, RecoveryBox can query a Meshtastic node connected to its WiFi hotspot. The positions of all nearby Meshtastic nodes, beacons, and users are automatically extracted and displayed on the embedded offline map (BRouter).

RecoveryBox becomes a true tactical relay and situation monitoring center, providing an immediate geographical overview of all responders in the field, without any license required.

## Offline Mapping {: #section-carto .rb-section-title }

RecoveryBox Embeds a complete, 100% offline mapping system, enabling navigation, route planning, and precise orientation even in the event of a total Internet outage.

Key features:

- **100% Local Data** : The entire map data is stored directly on RecoveryBox. Map consultation and route calculation are performed locally, with maximum responsiveness and zero Cloud dependency.

- **GPX Track Management & Creation** : Easily import existing routes or create your own GPX files on the fly. An ideal tool for marking search areas, planning evacuation routes, or sharing points of interest in the field.

- **Updates & Custom Maps (generate-map)** : Using the dedicated generate-map tool, update your maps or easily compile new geographic zones from recent OpenStreetMap data, adapting the map coverage to your area of operations.

Coupled with GPS feeds from the Meshtastic network, an external GPS module, or simply the GPS built into users' smartphones and tablets, the mapping system turns RecoveryBox into a true tactical geolocation hub.

## Offline Documentation {: #section-documentation .rb-section-title }

RecoveryBox Embeds a true offline digital library, bringing encyclopedia knowledge and practical guides immediately accessible from any device connected to Wi-Fi.

A complete and evolving resource center:

- **Full Wikipedia via Kiwix** : Access the entirety of Wikipedia (French and English) without any Internet connection. You thus have a universe of knowledge at your fingertips.

- **Extensible ZIM/Kiwix Support** : Add other Kiwix-format databases in minutes (Vikidia articles, medical courses, survival guides, maps, etc.) to enrich your knowledge server.

- **Essential PDF Library** : Enjoy a selection of pre-embedded practical guides and reference manuals in French and English (first aid, DIY, technical manuals, cooking, gardening).

- **Simplified Document Management** : Easily deposit your own PDF documents or reference files into the system to adapt the documentation to the specific needs of your team or area of operations.

Thanks to this autonomous documentation base, RecoveryBox guarantees permanent access to vital and technical information, even during prolonged network outages.

## GPS & Time Sync {: #section-gps .rb-section-title }

In a fully disconnected environment, time and geolocation are fundamental reference points. RecoveryBox integrates native support for USB GPS receivers (plug-and-play), ensuring absolute temporal and spatial autonomy.

Key advantages:

- **Precise Automatic Timestamping** : Without access to Internet time servers (NTP), RecoveryBox automatically synchronizes its internal clock on the atomic time transmitted by the GPS signal. All your logs, messages, and records remain precisely dated.

- **Universal Positioning for All Your Devices** : Thanks to the USB GPS plugged into the box, you can locate yourself in real time on the interactive map, even if the device you are using to access it (a laptop without GPS or a tablet on Wi-Fi) does not have a built-in GPS chip.

Once connected, the USB GPS continuously powers all RecoveryBox services to ensure consistency across maps, logs, and communications.

## Client-Server Architecture {: #section-architecture .rb-section-title }

RecoveryBox was designed according to a "headless" client-server architecture (without a screen or built-in display device). This approach offers two decisive advantages in the field: maximum energy efficiency and smooth collective usage.

Advantages of a screenless system:

- **Optimized Power Consumption** : The absence of a screen and physical display components preserves battery energy, maximizing the overall electrical autonomy of the system.

- **Simultaneous Use & Multi-User** : Thanks to the integrated Wi-Fi access point, multiple people can connect at the same time from their own devices (smartphones, tablets, or laptops) and simultaneously use different services (map, Meshtastic messaging, documentation, radio).

- **Designed for Teamwork** : In a crisis situation or during field missions, RecoveryBox acts as a centralized server. Each team member accesses the resources they need directly from their usual screen, without creating a physical bottleneck around the unit.

Robust, energy-efficient, and collaborative, this architecture transforms RecoveryBox into a true shared digital command post.
