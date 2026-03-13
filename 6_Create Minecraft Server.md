Phase 1: Creating the "Isolated" Instance
1. Create Instance: Select Minecraft Java Edition.
2. Deployment Settings:
    * Docker Selection: Choose "Use Docker".
    * If Docker-based instances fail to start, AMP may prompt you to add the amp user to the docker group:
      ```
      usermod -a -G docker amp
      ```
    * Network Mode: Select "Advanced (Isolated Bridge)". This creates a virtual "cage", separating the server from your Debian host.
    * Limit Resources: Enable this. Set Memory Limit to 13312 (13GB—12GB for the game + 1GB overhead) and CPU Core Limit to 4.

Phase 2: Java & Performance Tuning
Once the instance is created, go to Manage -> Configuration -> Java Settings:
1. Memory Limits: Set both Initial and Maximum Memory to 12288MB.
    * Why? It prevents the i5-8500 from wasting cycles resizing the "heap" during gameplay.
3. Java Virtual Machine: Ensure it is set to Java 17 or 21 (required for modern modpacks).
4. Optimization: Toggle "Use Aikar's Flags" to ON. This is the single best "free mod" for 15-player stability.
5. Garbage Collector: Ensure G1GC is selected.

Phase 3: Server Behavior
1. Go to Configuration -> Minecraft -> Server Settings: Sleep Mode: Set to "Disabled".
    * Why? playit.gg needs a live port to stay active. If AMP "sleeps" the server, the tunnel will often fail to wake it back up.
2. Auto Restart: Enable "Restart on Crash".
3. Update on Start: Enable this so the server grabs the latest modpack/jar fixes

4: Storage & Safety
1. Go to Configuration -> API and Backups: Backup Frequency: Set to Daily (at a time you aren't working, e.g., 3:00 AM).
2. Retention: Keep the last 7-10 backups. You can even select the NAS as the backup location if you have one :)
3. Storage Driver: Ensure AMP is using Overlay2 for the best "random read/write" performance on your SSD.
