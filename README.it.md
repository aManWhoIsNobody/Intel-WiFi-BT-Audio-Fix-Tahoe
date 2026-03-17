*Leggi in altre lingue: [English](README.md)*

# Intel-WiFi-BT-and_Audio-Fix-Tahoe
**Come sistemare il WiFi Intel (testato con AX210/1675) e il Bluetooth con OCLP-Mod di laobamac**

Ciao! Stavo cercando di sistemare il WiFi e il Bluetooth sul mio Thinkpad T480 con macOS Tahoe 26.3.1 e una scheda Intel AX210. Mi sono sentito un po' confuso e sopraffatto da tutti i post e le guide online, così ho pensato di scrivere io stesso una guida basata su ciò che ha funzionato per me.

Caricherò tutti i file che ho utilizzato e anche i link da cui li ho scaricati. Tieni presente che seguirò la gerarchia delle cartelle, e in particolare che il file `config.plist` che troverai è puramente come riferimento per le cose usate in questa guida, niente di più.

> **Disclaimer:**
> Tieni presente che questa è la mia prima guida/repo. Se faccio qualcosa di sbagliato, o se dimentico di menzionare o dare i crediti al lavoro di qualcun altro che ho usato per far funzionare il tutto, fatemelo sapere e correggerò il prima possibile!

---

## ⚠️ Prerequisiti

So che è un po' ironico, ma **avrai bisogno di una connessione internet funzionante** per avviare OCLP-Mod. Assicurati di avere una qualche connettività internet attiva sul tuo Hackintosh per iniziare. 
* Nel mio caso, ho usato `itlwm` con Heliport. 
* Il tethering USB è un'ottima alternativa. Se hai un telefono Android, puoi usare [HoRNDIS di TomHeaven](#) *(tutti i link sono in fondo alla guida)*.

*Nota sull'Audio:* Non mi concentrerò sull'audio. Se ti funzionava già in precedenza con `AppleALC` o simili, verrà gestito automaticamente nell'ultimo passaggio della guida da OCLP-Mod, senza che l'utente debba fare nulla.

## 📦 File Necessari

1. **OCLP-Mod di laobamac**: Scaricalo dalle [GitHub Releases](https://github.com/laobamac/OCLP-Mod/releases). 
   *Ho usato la versione 3.1.5 poiché è l'ultima disponibile al momento della stesura.*
2. **Kext**: Questi devono essere caricati nel tuo `config.plist` in questo **esatto ordine**. *(Se usi ProperTree, trascina semplicemente le voci dei kext per seguire questo schema e assicurati che siano ordinati correttamente).*

   **Per il WiFi:**
   * `IOSkywalkFamily.kext`
   * `IO80211FamilyLegacy.kext`
   * `AirPortBrcmNIC.kext` *(In ProperTree, questa voce verrà caricata automaticamente in quanto è un plugin di IO80211FamilyLegacy.kext)*
   * `AMFIPass.kext` *(Sto usando la versione 1.4.1)*
   * `AirportItlwm.kext` *(v2.3.0_stable_Ventura)*

   **Per il Bluetooth:**
   * `BlueToolFixup.kext`
   * `IntelBTPatcher.kext`
   * `IntelBluetoothFirmware.kext`

> **Suggerimento:** Se stai usando ProperTree, ti consiglio di usare la funzione **OC Snapshot** (o **OC Clean Snapshot** se avevi dei kext WiFi/BT che hai eliminato da un setup/tentativo precedente).

---

## ⚙️ Modifiche al config.plist

Mentre metti mano al tuo `config.plist`, dovrai fare alcune modifiche specifiche:

1. In **Kernel -> Block**, devi abilitare:
   * `com.apple.iokit.IOSkywalkFamily`

2. In **NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82**, devi:
   * **Disabilitare la SIP:** Imposta `csr-active-config` su `03080000` *(assicurati che il Type sia impostato su `Data`)*.
   * **Aggiungere i seguenti boot-args:** `-amfipassbeta -ipc_control_port_options=0`

*Nota:* Nel mio caso, da un precedente tentativo di sistemare WiFi/BT, ho anche i seguenti boot-args: `lilubetaall -ibtcompatbeta`. Non so se siano strettamente necessari. Sinceramente, ho dimenticato di rimuoverli e, dato che tutto sembra funzionare, li ho lasciati. Il mio consiglio: prima fai un test senza, e se qualcosa non va, prova ad aggiungerli (uno alla volta o entrambi, a te la scelta).

---

## 🚀 La Guida

**Punto di partenza:** Devi avere una connessione internet funzionante e la SIP già disabilitata (vedi sopra). Se hai queste due cose, sei pronto.

### Step 1: Prepara la tua EFI
1. Monta la tua partizione EFI e copia i kext in `EFI/OC/Kexts`.
2. Apri ProperTree (o il tuo editor plist preferito) e aggiungi i tuoi kext (es. usando OC Snapshot).
3. Vai su **Kernel -> Block** e abilita la voce menzionata in precedenza.
4. Vai su **NVRAM -> Add** e imposta i `boot-args` richiesti.
5. Salva il tuo `config.plist`. *(Nota: Non disabilitare ancora il tuo kext temporaneo per internet! Ci serve ancora per i prossimi passaggi).*

### Step 2: OCLP-Mod
Ora possiamo finalmente aprire **OCLP-Mod**. Purtroppo, l'intera app è in cinese (io ho usato Google Traduttore), ma puoi seguire gli screenshot allegati qui sotto.

<img width="615" height="331" alt="Screenshot 2026-03-17 alle 12 46 11" src="https://github.com/user-attachments/assets/1d8ef48d-4c1b-4228-987f-09530c0d44cc" />

Per prima cosa, assicurati di aver fatto tutto correttamente finora. Cliccando il primo pulsante, l'app ti mostrerà se puoi applicare le patch o meno.

<img width="615" height="331" alt="Screenshot 2026-03-17 alle 12 47 58" src="https://github.com/user-attachments/assets/2c6fa3eb-bbfa-4763-a0e3-14a4528dd379" />

* **Riquadro Rosso (1):** Se questi due pulsanti sono attivi, sei a posto. *(Il mio ha il primo disattivato in grigio perché ho già patchato il mio sistema).*
* **Riquadro Giallo (1.2):** Ti dice quali patch sono disponibili per il tuo sistema e/o la ragione (o le ragioni) per cui non puoi ancora patchare (es. la SIP è abilitata).
* **Riquadro Verde (2):** È il pulsante Indietro.

### Step 3: Scarica e Installa il KDK
Se i pulsanti funzionano, torna indietro e clicca il pulsante **KDK** nell'angolo in basso a sinistra.

<img width="615" height="331" alt="Screenshot 2026-03-17 alle 12 52 49" src="https://github.com/user-attachments/assets/09e15d5c-04f9-498d-b428-71d259e15d94" />

Vedrai questa pagina:

<img width="813" height="615" alt="Screenshot 2026-03-17 alle 12 54 32" src="https://github.com/user-attachments/assets/28a69335-a3b3-46b0-9249-8d862fa5e540" />

1. Scegli la versione di macOS 26 corrispondente *(io ho testato la 26.3.1)*.
2. Clicca il pulsante in basso a destra, come mostrato.
3. Ti chiederà dove vuoi scaricare il KDK. Puoi scegliere qualsiasi percorso.

<img width="378" height="247" alt="Screenshot 2026-03-17 alle 12 57 01" src="https://github.com/user-attachments/assets/2cc74913-757e-45c3-a121-ad0db461f253" />

Attendi la fine del download. 
Una volta completato, chiudi la/le finestra/e del KDK e vai nella cartella che hai scelto prima. Apri il `.dmg` scaricato e installa il `.pkg` al suo interno. 

### Step 4: Applica le Patch
Quando il KDK ha finito l'installazione, torna alla pagina principale di OCLP-Mod e clicca di nuovo il primo pulsante.

<img width="378" height="247" alt="Screenshot 2026-03-17 alle 12 57 34" src="https://github.com/user-attachments/assets/f2510f5a-0bbb-4028-b329-ca9eeb7684c3" />

Procedi con l'installazione delle patch cliccando il primo pulsante. 

<img width="615" height="331" alt="Screenshot 2026-03-17 alle 12 46 11" src="https://github.com/user-attachments/assets/da869d98-9d9f-49d8-81a0-8e3d6eeaa158" />

*(Non ricordo se chiede una conferma — controllerò su un altro portatile appena ne avrò l'occasione — in ogni caso lascialo lavorare e fagli fare la sua magia).*

### Step 5: Pulizia e Riavvio
Quando finisce, apparirà un pop-up che ti chiederà di riavviare il sistema (è il pulsante blu nell'angolo in basso a destra). 

**FERMATI! PRIMA DI RIAVVIARE:**
Se stavi usando un kext temporaneo (come `itlwm`) per avere l'accesso a internet, **questo è il momento di disabilitarlo** nel tuo `config.plist`. *(Ti consiglio di disabilitare la voce anziché cancellare del tutto il kext, nel caso in cui la patch vada storta e ti serva di nuovo internet).* Salva il `config.plist`.

1. Ora, clicca il pulsante per riavviare.
2. **Passaggio Fondamentale:** Quando arrivi al boot picker di OpenCore, DEVI fare un **Reset NVRAM** in modo che macOS carichi correttamente le modifiche. *(Se non vedi l'opzione perché è nascosta, premi la barra `Spaziatrice`).*
3. Il sistema si riavvierà di nuovo. Vai avanti e testa il tuo WiFi, Bluetooth e Audio!

> **P.S.** Se il tuo computer ha più bootloader che hanno la tendenza a scavalcare OpenCore nel menu di avvio del BIOS, ricordati di andare nel BIOS e sistemare l'ordine di avvio dopo aver resettato la NVRAM.

---

E questo dovrebbe essere tutto! Se qualcosa non è abbastanza chiaro o se magari hai bisogno di aiuto per qualcosa, non posso garantire di riuscirci, ma sarò felice di provarci.

---

## 🏆 Crediti, Guide, Ispirazione e Software Consigliato

Un enorme ringraziamento agli sviluppatori e alla community. Tutto questo non sarebbe stato possibile senza:

* **[OpenCore](https://github.com/acidanthera/OpenCorePkg)** di acidanthera
* **[OCLP-Mod](https://github.com/laobamac/OCLP-Mod)** di laobamac
* **[itlwm](https://github.com/OpenIntelWireless/itlwm)** di OpenIntelWireless
* **[u/Dependent-Heart420 su r/hackintosh](https://www.reddit.com/r/hackintosh/comments/1pyy0bn/all_road_lead_to_intel_native_wifi_tahoe_262/?show=original)**
* **[Native-Wifi-for-Hackintoshes-with-Intel-Wireless-cards-on-macOS-sequoia](https://github.com/randomappleboi/Native-Wifi-for-Hackintoshes-with-Intel-Wireless-cards-on-macOS-sequoia)** di randomappleboi
* **[Experimental Fork of OCLP 3.0.0 Nightly – modern Wi-Fi, AWDL and AppleHDA Fully Working under Tahoe](https://www.insanelymac.com/forum/topic/362042-experimental-fork-of-oclp-300-nightly-%E2%80%93-modern-wi-fi-awdl-and-applehda-fully-working-under-tahoe/#comments)** su InsanelyMac
* **[BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM)** di acidanthera
* **[AMFIPass.kext](https://github.com/dortania/OpenCore-Legacy-Patcher/blob/main/payloads/Kexts/Acidanthera/AMFIPass-v1.4.1-RELEASE.zip)** (via OCLP)
* **[IO80211FamilyLegacy.kext](https://github.com/dortania/OpenCore-Legacy-Patcher/blob/main/payloads/Kexts/Wifi/IO80211FamilyLegacy-v1.0.0.zip)** (via OCLP)
* **[IOSkywalkFamily.kext](https://github.com/dortania/OpenCore-Legacy-Patcher/blob/main/payloads/Kexts/Wifi/IOSkywalkFamily-v1.2.0.zip)** (via OCLP)
* **[ProperTree](https://github.com/corpnewt/ProperTree)** di corpnewt
* **[HoRNDIS](https://github.com/TomHeaven/HoRNDIS)** di TomHeaven
  
