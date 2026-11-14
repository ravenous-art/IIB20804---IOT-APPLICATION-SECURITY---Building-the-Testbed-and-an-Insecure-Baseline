# IIB20804 IoT Application Security - Lab 1 Report
**Lab Title:** Building the Testbed and an Insecure Baseline  
**Group Number:** Group 3 (Example)  
**Submission Date:** November 14, 2026  

---

## 1. System Architecture & Testbed Overview
The Lab 1 testbed consists of three functional tiers simulating an end-to-end IoT data pipeline:
* **Tier 1 (Device Layer):** Python device simulator (`sensor_sim.py`) acting as `sensor01` and `sensor02`, generating periodic JSON telemetry payloads (`temp`, `hum`, `ts`) every 5 seconds.
* **Tier 2 (Gateway Layer):** Mosquitto MQTT Broker (port `1883`, anonymous access permitted) and Python Bridge (`bridge.py`) acting as a forwarder.
* **Tier 3 (Platform Layer):** Mobius 3.0.1 oneM2M IN-CSE running on MySQL 8 (port `7579`), handling resource creation (`<ae>` and `<cnt>`) and storing content instances (`<cin>`).

---

## 2. Evidence Checkpoints Summary
* **Screenshot A1:** Mosquitto publisher/subscriber loop verification (`campus/test hello`).
* **Screenshot B1:** Multi-device live telemetry streaming topic subscription (`campus/#`).
* **Screenshot C1:** Bridge terminal output displaying HTTP `201` and oneM2M status code `2001 (Created)`.
* **Screenshot C2:** `curl` query output retrieving the latest virtual resource container value (`/la`).
* **Artifact:** `lab1_plain_mqtt.pcapng` (Wireshark capture of plaintext MQTT telemetry packets).

---

## 3. Section 8: Baseline Findings Table (Evidence)

| # | Finding | Evidence / Screenshot Link | MTSFB Threat / Weakness | Fixed In |
|---|---|---|---|---|
| **F1** | Broker accepts anonymous clients; anyone can read all topics | ![F1 Evidence](../findings%20table/F1.png) | T-Sec-3 / R-Sec-5 | Lab 3 |
| **F2** | Telemetry sent in plaintext; payload readable in Wireshark | ![F2 Evidence](../findings%20table/lab1_plain_mqtt.png) | T-Sec-4 / R-Sec-3 | Lab 3 |
| **F3** | Anyone can inject fake sensor readings | ![F3 Evidence](../findings%20table/F3.png) | Unauthorized access / Message injection | Lab 3, Lab 5 |
| **F4** | CSE trusts any X-M2M-Origin value | ![F4 Evidence](../findings%20table/F4.png) | Unauthorized access / Weak authentication | Lab 5 |
| **F5** | Open ports and services on gateway/server | ![F5 Evidence](../findings%20table/F5.png) | Network exposure / Unnecessary services | Lab 4 |
| **F6** | Hardcoded Wi-Fi and device credentials in source code | ![F6 Evidence](../findings%20table/F6.png) | Information disclosure / Insecure credential storage | Lab 3 |
| **F7** | PIN authentication allows repeated incorrect attempts without protection | *Refer to test logs* | Brute-force attack / Weak authentication | Project implementation |

---

## 4. Reflection Questions

1. **Which finding would an attacker exploit first in a real deployment, and why?**
   * *Answer:* **F1 / F3 (Anonymous Broker Access and Message Injection)**. An attacker would exploit this first because it requires zero authentication credentials to connect to the MQTT broker, allowing them to instantly eavesdrop on sensitive telemetry data or inject false readings into the system with minimal effort.
2. **Map findings F1–F6 to the four security domains (device, communication, gateway, cloud).**
   * *Device:* F6 (Hardcoded credentials).
   * *Communication:* F2 (Unencrypted plaintext MQTT traffic).
   * *Gateway:* F1 (Anonymous broker), F3 (Message injection), F5 (Network port exposure).
   * *Cloud / Platform:* F4 (Unrestricted `X-M2M-Origin` trust in Mobius CSE).
3. **Why is "the lab network is private" not a sufficient security control? Relate your answer to zero trust.**
   * *Answer:* Relying solely on network perimeter security assumes that anything inside the network is trustworthy. Under a Zero Trust model ("never trust, always verify"), internal systems or compromised devices inside the private network can act maliciously. Without application-layer authentication and encryption, internal attackers can freely sniff traffic or inject commands.
4. **For your chosen use case (Smart Secure Storage Box), describe one safety harm and one privacy harm that the baseline weaknesses could cause.**
   * *Safety Harm:* An attacker injecting false state data or tampering with lock signals could trick the monitoring system or cause mechanical lock failure, trapping belongings or failing to secure the container.
   * *Privacy Harm:* Intercepting unencrypted wireless communications or exploiting open access logs could expose the owner's access timestamps, usage frequency, and personal activity patterns.

---

## 5. Submission Checklist
* [x] Testbed architecture diagram included in repository.
* [x] Screenshots A1, B1, C1, C2 captured and documented.
* [x] `lab1_plain_mqtt.pcapng` packet capture file saved under `/evidence/lab1/`.
* [x] `nmap_scan.txt` export file generated and saved.
* [x] Completed Baseline Findings Table compiled.
