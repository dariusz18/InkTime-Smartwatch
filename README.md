# Documentatie Tehnica Hardware - InkTime Smartwatch

Acest repository contine fisierele de proiectare hardware pentru InkTime, un dispozitiv purtabil bazat pe arhitectura nRF52840, optimizat pentru eficienta energetica ridicata si afisaj bi-stabil.

## 1. Descriere Functionalitate Hardware

Sistemul electronic este proiectat modular, avand urmatoarele subsisteme critice:

* **Unitatea Centrala de Procesare (MCU):** Proiectul utilizeaza SoC-ul **nRF52840** (Nordic Semiconductor), ales pentru suportul nativ Bluetooth 5.0 si consumul minim in modurile de repaus. Acesta gestioneaza comunicarea cu senzorii si controlul refresh-ului pentru display.
* **Modulul de Afisaj (E-Paper):** Dispozitivul integreaza un ecran tip Electronic Paper (e-Ink). Circuitul de comanda include un convertor de tip "boost" (format din inductanta de 68uH, diode Schottky si tranzistori MOSFET) necesar pentru generarea tensiunilor de polarizare specifice tehnologiei electroforetice.
* **Managementul Alimentarii (PMU):** * **LiPo Charger:** Incarcarea acumulatorului este gestionata de un controller dedicat, configurat pentru curent de incarcare sigur.
    * **DC/DC Converter:** Asigura conversia tensiunii bateriei la pragul stabil de 3.3V necesar rail-ului digital.
    * **Fuel Gauge:** Integratul MAX17048 monitorizeaza starea de sanatate a bateriei si transmite datele prin I2C.
* **Interfata cu Utilizatorul:** Sistemul include trei butoane mecanice conectate la pini GPIO cu pull-up intern si un driver haptic pentru semnalizarea evenimentelor prin vibratii.

## 2. Diagrama Bloc si Conectivitate

Arhitectura sistemului urmeaza fluxul: **Sursa Energie -> PMU -> MCU -> Periferice (Display/IMU)**.

### Tabel Conectare Pini nRF52840

| Periferic | Protocol Comunicare | Pini Alocati (MCU) |
| :--- | :--- | :--- |
| **E-Paper Display** | SPI (MOSI, SCK, CS) | P0.26, P0.27 (DC), P0.28 (RES) |
| **IMU / Fuel Gauge** | I2C (SDA, SCL) | P0.11, P0.12 |
| **Haptic Motor** | PWM | P0.14 |
| **Butoane (Up/Ent/Dn)** | Digital Input | P0.09, P0.10, P0.24 |
| **Programare/Debug** | SWD | SWDIO, SWDCLK |

## 3. Implementare Mecanica si Proiectare 3D

Carcasa proiectata in Autodesk Fusion asigura protectia mecanica a ansamblului. Proiectarea a tinut cont de:
* Pozitionarea antenei nRF52840 departe de planul de masa pentru semnal optim.
* Asamblarea tip "sandwich" a componentelor: Ecran -> PCB -> Baterie.
* Accesul extern pentru portul de incarcare USB-C, protejat prin circuite de suprimare a tensiunilor tranzitorii (TVS/ESD).

### Review Tehnologic: Avantajele tehnologiei E-Paper
Alegerea unui afisaj de tip E-Paper ofera dispozitivului o autonomie superioara. Datorita proprietatii de bi-stabilitate, pixelii isi mentin starea fara consum de energie. Consumul de curent are loc exclusiv in timpul tranzitiilor de stare (refresh), permitand MCU-ului sa ramana in mod "Deep Sleep" majoritatea timpului. Aceasta abordare elimina necesitatea unei iluminari de fundal (Backlight), vizibilitatea fiind direct proportionala cu lumina ambientala.
