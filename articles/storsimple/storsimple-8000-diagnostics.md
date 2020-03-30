---
title: Diagnostický nástroj pro řešení potíží se zařízením StorSimple 8000 | Dokumenty společnosti Microsoft
description: Popisuje režimy zařízení StorSimple a vysvětluje, jak změnit režim zařízení pomocí prostředí Windows PowerShell pro StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 48bd909eefbaea15cf6ca2427e106ad9bc0ffbb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298746"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Řešení problémů se zařízeními řady 8000 pomocí nástroje StorSimple Diagnostics Tool

## <a name="overview"></a>Přehled

Nástroj StorSimple Diagnostics diagnostikuje problémy související se stavem systémových, výkonových, síťových a hardwarových součástí pro zařízení StorSimple. Diagnostický nástroj lze použít v různých scénářích. Tyto scénáře zahrnují plánování úloh, nasazení zařízení StorSimple, posouzení síťového prostředí a určení výkonu provozního zařízení. Tento článek obsahuje přehled diagnostického nástroje a popisuje, jak lze nástroj použít se zařízením StorSimple.

Diagnostický nástroj je primárně určen pro místní zařízení řady StorSimple 8000 (8100 a 8600).

## <a name="run-diagnostics-tool"></a>Spustit diagnostický nástroj

Tento nástroj lze spustit prostřednictvím rozhraní prostředí Windows PowerShell vašeho zařízení StorSimple. Existují dva způsoby přístupu k místnímu rozhraní zařízení:

* [PuTTY slouží k připojení k sériové konzoli zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Vzdálený přístup k nástroji prostřednictvím prostředí Windows PowerShell for StorSimple](storsimple-8000-remote-connect.md).

V tomto článku předpokládáme, že jste se připojili k sériové konzoli zařízení přes PuTTY.

#### <a name="to-run-the-diagnostics-tool"></a>Spuštění diagnostického nástroje

Po připojení k rozhraní prostředí Windows PowerShell zařízení proveďte následující kroky ke spuštění rutiny.
1. Přihlaste se k sériové konzoli zařízení podle kroků uvedených v [části Použití putty pro připojení ke sériové konzoli zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Zadejte následující příkaz:

    `Invoke-HcsDiagnostics`

    Pokud není zadán parametr oboru, rutina provede všechny diagnostické testy. Tyto testy zahrnují stav systému, hardwarové součásti, síť a výkon. 
    
    Chcete-li spustit pouze určitý test, zadejte parametr oboru. Chcete-li například spustit pouze test sítě, zadejte

    `Invoke-HcsDiagnostics -Scope Network`

3. Vyberte a zkopírujte výstup z okna PuTTY do textového souboru pro další analýzu.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Scénáře použití diagnostického nástroje

Pomocí diagnostického nástroje můžete řešit potíže se stavem sítě, výkonu, systému a hardwaru systému. Zde jsou některé možné scénáře:

* **Zařízení offline** – zařízení řady StorSimple 8000 je offline. Z rozhraní prostředí Windows PowerShell se však zdá, že oba řadiče jsou v provozu.
    * Tento nástroj můžete použít k určení stavu sítě.
         
         > [!NOTE]
         > Nepoužívejte tento nástroj k posouzení výkonu a nastavení sítě na zařízení před registrací (nebo konfigurací pomocí průvodce nastavením). Během průvodce instalací a registrace je zařízení přiřazena platná adresa IP. Tuto rutinu můžete spustit na zařízení, které není zaregistrováno, pro stav hardwaru a systém. Použijte parametr oboru, například:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Trvalé problémy se zařízením** – dochází k problémům se zařízením, které se zdají přetrvávat. Například registrace selhává. Můžete také dochází k problémům se zařízením po zařízení je úspěšně registrována a funkční na chvíli.
    * V takovém případě použijte tento nástroj pro předběžné řešení potíží před protokolováním žádosti o službu s podporou společnosti Microsoft. Doporučujeme spustit tento nástroj a zachytit výstup tohoto nástroje. Potom můžete poskytnout tento výstup pro podporu pro urychlení řešení potíží.
    * Pokud dojde k selhání hardwarové součásti nebo clusteru, měli byste se přihlásit k žádosti o podporu.

* **Nízký výkon zařízení** – zařízení StorSimple je pomalé.
    * V takovém případě spusťte tuto rutinu s parametrem oboru nastaveným na výkon. Analyzujte výstup. Získáte latence čtení a zápisu v cloudu. Použijte hlášené latence jako maximální dosažitelný cíl, faktor v některé režie pro zpracování interních dat a pak nasadit úlohy v systému. Další informace naleznete v části [Řešení potíží s výkonem zařízení pomocí síťového testu](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Diagnostický test a výstupy vzorků

### <a name="hardware-test"></a>Test hardwaru

Tento test určuje stav hardwarových součástí, firmwaru USM a firmwaru disku spuštěného v systému.

* Hlášené hardwarové součásti jsou součásti, které neprošly testem nebo nejsou přítomny v systému.
* Verze firmwaru a firmwaru disku USM jsou hlášeny pro řadič 0, řadič 1 a sdílené součásti ve vašem systému. Úplný seznam hardwarových komponent naleznete na adrese:

    * [Komponenty v primárním krytu](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Komponenty ve skříni EBOD](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Pokud test hardwaru hlásí součásti se nezdařilo, [přihlaste se k žádosti o službu s podporou společnosti Microsoft](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Ukázkový výstup testu hardwaru na zařízení 8100

Zde je ukázkový výstup ze zařízení StorSimple 8100. V zařízení modelu 8100 není skříň EBOD k dispozici. Proto součásti řadiče EBOD nejsou hlášeny.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>Systémový test

Tento test hlásí informace o systému, dostupné aktualizace, informace o clusteru a informace o službě pro vaše zařízení.

* Systémové informace zahrnují model, sériové číslo zařízení, časové pásmo, stav řadiče a podrobnou verzi softwaru spuštěnou v systému. Chcete-li porozumět různým parametrům systému hlášeným jako výstup, přejděte na [informace o tlumočení systému](#appendix-interpreting-system-information).

* Dostupnost aktualizace hlásí, zda jsou k dispozici režimy pravidelné ho a údržby a jejich přidružené názvy balíčků. Pokud `RegularUpdates` `MaintenanceModeUpdates` a `false`jsou , znamená to, že aktualizace nejsou k dispozici. Vaše zařízení je aktuální.
* Informace o clusteru obsahují informace o různých logických součástech všech skupin clusterů HCS a jejich příslušných stavech. Pokud se v této části sestavy zobrazí skupina offline clusterů, [obraťte se na podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md).
* Informace o službě zahrnují názvy a stavy všech služeb HCS a CiS spuštěných na vašem zařízení. Tyto informace jsou užitečné pro podporu společnosti Microsoft při řešení potíží s problémem zařízení.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Ukázkový výstup systémového testu na zařízení 8100

Zde je ukázkový výstup testovacího testu systému na zařízení 8100.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_service        Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Test sítě

Tento test ověří stav síťových rozhraní, portů, připojení k serveru DNS a NTP, certifikátu TLS/SSL, pověření účtu úložiště, připojení k aktualizačním serverům a připojení webového proxy serveru na zařízení StorSimple.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Ukázkový výstup síťového testu, pokud je povolen pouze DATA0

Zde je ukázkový výstup zařízení 8100. Můžete vidět ve výstupu, který:
* Jsou povolena a konfigurována pouze síťová rozhraní DATA 0 a DATA 1.
* DATA 2 - 5 nejsou na portálu povoleny.
* Konfigurace serveru DNS je platná a zařízení se může připojit prostřednictvím serveru DNS.
* Připojení k serveru NTP je také v pořádku.
* Porty 80 a 443 jsou otevřené. Port 9354 je však blokován. Na základě [požadavků na systémovou síť](storsimple-system-requirements.md)je třeba otevřít tento port pro komunikaci sběrnice.
* Certifikace TLS/SSL je platná.
* Zařízení se může připojit k účtu úložiště: _myss8000storageacct_.
* Připojení k aktualizačním serverům je platné.
* Webový proxy server není v tomto zařízení nakonfigurován.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Ukázkový výstup síťového testu, pokud jsou povoleny DATA0 a DATA1

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Test výkonnosti

Tento test hlásí výkon cloudu prostřednictvím latence čtení a zápisu v cloudu pro vaše zařízení. Tento nástroj lze použít k vytvoření účaří výkonu cloudu, který můžete dosáhnout s StorSimple. Nástroj hlásí maximální výkon (nejlepší scénář pro latence čtení a zápisu), který můžete získat pro připojení.

Vzhledem k tomu, že nástroj hlásí maximální dosažitelný výkon, můžeme při nasazování úloh použít hlášené latence pro čtení a zápis jako cíle.

Test simuluje velikosti objektů blob přidružené k různým typům svazků v zařízení. Pravidelné vrstvené a zálohy místně vázaných svazků používají velikost objektu blob 64 kB. Vrstvené svazky se zaškrtnutou možností archivu používají velikost dat objektu blob 512 KB. Pokud má vaše zařízení vrstvené a místně nakonfigurované svazky, spustí se pouze test odpovídající velikosti dat objektu blob 64 kB.

Chcete-li použít tento nástroj, proveďte následující kroky:

1.  Nejprve vytvořte kombinaci vrstvených svazků a vrstvených svazků se zaškrtnutou možností archivace. Tato akce zajišťuje, že nástroj spustí testy pro velikosti objektů blob 64 KB a 512 kB.

2. Po vytvoření a konfiguraci svazků spusťte rutinu. Zadejte:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Poznamenejte si latence čtení a zápisu hlášené nástrojem. Tento test může trvat několik minut, než ohlásí výsledky.

4. Pokud latence připojení jsou všechny v očekávaném rozsahu, pak latence hlášené nástrojem lze použít jako maximální dosažitelný cíl při nasazování úloh. Faktor v některé režii pro interní zpracování dat.

    Pokud jsou čekací doba čtení a zápisu hlášená diagnostickým nástrojem vysoká:

    1. Nakonfigurujte analýzu úložiště pro služby blob a analyzujte výstup, abyste pochopili latence pro účet úložiště Azure. Podrobné pokyny najdete v [povolení a konfiguraci analýzy úložiště](../storage/common/storage-enable-and-view-metrics.md). Pokud jsou tyto latence také vysoké a srovnatelné s čísly, která jste obdrželi z nástroje StorSimple Diagnostics, musíte protokolovat požadavek na službu pomocí úložiště Azure.

    2. Pokud jsou latence účtu úložiště nízké, obraťte se na správce sítě a zjistěte případné problémy s latencí v síti.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Ukázkový výstup testu výkonu na zařízení 8100

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>Dodatek: interpretace systémových informací

Zde je tabulka popisující, co různé parametry prostředí Windows PowerShell v mapě systémové informace. 

| Parametr prostředí PowerShell    | Popis  |
|-------------------------|------------------|
| ID instance             | Každý řadič má jedinečný identifikátor nebo identifikátor GUID s ním spojené.|
| Name (Název)                    | Popisný název zařízení nakonfigurovaný na webu Azure Portal během nasazení zařízení. Výchozí popisný název je sériové číslo zařízení. |
| Model                   | Model zařízení řady StorSimple 8000. Model může být 8100 nebo 8600.|
| SerialNumber            | Sériové číslo zařízení je přiřazeno z výroby a je dlouhé 15 znaků. Například 8600-SHX0991003G44HT označuje:<br> 8600 – Je model zařízení.<br>SHX - Je výrobní místo.<br> 0991003 - Je specifický produkt. <br> G44HT- posledních 5 číslic se zvětší, aby se vytvořila jedinečná sériová čísla. To to nemusí být sekvenční sada.|
| TimeZone                | Časové pásmo zařízení nakonfigurované na webu Azure Portal během nasazení zařízení.|
| CurrentController       | Řadič, ke kterému jste připojeni prostřednictvím rozhraní prostředí Windows PowerShell vašeho zařízení StorSimple.|
| ActiveController        | Řadič, který je aktivní v zařízení a řídí všechny operace v síti a disku. Může se jedná o řadič 0 nebo řadič 1.  |
| Controller0Status       | Stav ovladače 0 v zařízení. Stav řadiče může být normální, v režimu obnovení nebo nedostupný.|
| Controller1Status       | Stav ovladače 1 na vašem zařízení.  Stav řadiče může být normální, v režimu obnovení nebo nedostupný.|
| Režim systému              | Celkový stav zařízení StorSimple. Stav zařízení může být normální, údržba nebo vyřazení z provozu (odpovídá deaktivované na webu Azure Portal).|
| Verze FriendlySoftwareVersion | Přátelský řetězec, který odpovídá verzi softwaru zařízení. Pro systém se systémem Update 4, přátelské verze softwaru by StorSimple 8000 Series Update 4.0.|
| HcsSoftwareVersion      | Verze softwaru HCS spuštěná na vašem zařízení. Například verze softwaru HCS odpovídající aktualizaci 4.0 řady StorSimple 8000 je 6.3.9600.17820. |
| ApiVersion              | Verze softwaru rozhraní API prostředí Windows PowerShell zařízení HCS.|
| Verze VhdVersion              | Verze softwaru bitové kopie z výroby, se kterou bylo zařízení dodáno. Pokud zařízení obnovíte na výchozí hodnoty z výroby, spustí se tato verze softwaru.|
| OSVersion               | Verze softwaru operačního systému Windows Server spuštěná v zařízení. Zařízení StorSimple je založeno na systému Windows Server 2012 R2, který odpovídá 6.3.9600.|
| CisAgentVersion         | Verze pro agenta Cis spuštěné na zařízení StorSimple. Tento agent pomáhá komunikovat se službou StorSimple Manager spuštěnou v Azure.|
| Verze MdsAgentVersion         | Verze odpovídající agentovi Mds spuštěnéna na zařízení StorSimple. Tento agent přesune data do služby monitorování a diagnostiky (MDS).|
| Lsisas2Version          | Verze odpovídající ovladačům LSI na zařízení StorSimple.|
| Kapacita                | Celková kapacita zařízení v bajtů.|
| Režim vzdálené správy    | Označuje, zda lze zařízení vzdáleně spravovat prostřednictvím rozhraní prostředí Windows PowerShell. |
| Režim FipsMode                | Označuje, zda je ve vašem zařízení povolen režim FIPS (Federal Information Processing Standard) (United States Federal Information Processing Standard). Standard FIPS 140 definuje kryptografické algoritmy schválené pro použití počítačovými systémy federální vlády USA pro ochranu citlivých dat. Pro zařízení s aktualizací 4 nebo novější je ve výchozím nastavení povolen režim FIPS. |

## <a name="next-steps"></a>Další kroky

* Naučte [se syntaxi rutiny Invoke-HcsDiagnostics](https://technet.microsoft.com/library/mt795371.aspx).

* Přečtěte si další informace o [řešení problémů s nasazením](storsimple-troubleshoot-deployment.md) na zařízení StorSimple.
