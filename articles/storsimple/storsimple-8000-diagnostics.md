---
title: Nástroj pro diagnostiku k řešení potíží se zařízením s StorSimple 8000 | Microsoft Docs
description: Tento článek obsahuje přehled nástroje pro diagnostiku a popisuje, jak lze nástroj použít se zařízením StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: bf0570cd62a2c329407cfb6dd087020f53cdc991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954065"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Řešení potíží se zařízeními 8000 series pomocí nástroje Diagnostika StorSimple

## <a name="overview"></a>Přehled

Nástroj Diagnostika StorSimple diagnostikuje problémy související se stavem součásti systému, výkonu, sítě a hardwaru pro zařízení StorSimple. Nástroj pro diagnostiku se dá použít v různých scénářích. Mezi tyto scénáře patří plánování úloh, nasazení zařízení StorSimple, posouzení síťového prostředí a určení výkonu provozního zařízení. Tento článek obsahuje přehled nástroje pro diagnostiku a popisuje, jak lze nástroj použít se zařízením StorSimple.

Nástroj pro diagnostiku je určený hlavně pro řady StorSimple 8000 na místních zařízeních (8100 a 8600).

## <a name="run-diagnostics-tool"></a>Spustit diagnostický nástroj

Tento nástroj se dá spustit přes rozhraní Windows PowerShell zařízení StorSimple. Existují dva způsoby, jak získat přístup k místnímu rozhraní vašeho zařízení:

* K [připojení ke konzole sériového portu zařízení použijte k disvýstupu](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Vzdálený přístup k nástroji prostřednictvím Windows PowerShell pro StorSimple](storsimple-8000-remote-connect.md).

V tomto článku předpokládáme, že jste se připojili ke konzole sériového zařízení prostřednictvím výstupu.

#### <a name="to-run-the-diagnostics-tool"></a>Spuštění diagnostického nástroje

Jakmile se připojíte k rozhraní Windows PowerShell zařízení, spusťte pomocí následujících kroků rutinu.
1. Přihlaste se ke konzole sériového portu zařízení pomocí postupu v části [použití výstupu pro připojení ke konzole sériového portu zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Zadejte následující příkaz:

    `Invoke-HcsDiagnostics`

    Pokud parametr scope není zadán, rutina spustí všechny diagnostické testy. Tyto testy zahrnují systém, stav hardwarových součástí, síť a výkon. 
    
    Chcete-li spustit pouze konkrétní test, zadejte parametr scope. Chcete-li například spustit pouze test sítě, zadejte

    `Invoke-HcsDiagnostics -Scope Network`

3. Vyberte a zkopírujte výstup z okna výstupu do textového souboru pro další analýzu.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Scénáře použití nástroje pro diagnostiku

Pomocí diagnostického nástroje můžete řešit potíže se stavem sítě, výkonu, systému a hardwaru v systému. Tady je několik možných scénářů:

* **Zařízení je offline** – vaše zařízení řady StorSimple 8000 je offline. Z rozhraní Windows PowerShellu se však zdá, že oba řadiče jsou v provozu.
    * Tento nástroj můžete použít k určení stavu sítě.
         
         > [!NOTE]
         > Nepoužívejte tento nástroj k vyhodnocení výkonu a nastavení sítě na zařízení před registrací (nebo konfigurací prostřednictvím Průvodce instalací). Během Průvodce instalací a registraci se zařízení přiřadí platná IP adresa. Tuto rutinu můžete spustit na zařízení, které není zaregistrované, pro stav hardwaru a systém. Použijte parametr scope, například:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Trvalé problémy se zařízením** – dochází k problémům se zařízením, které se jeví jako trvalé. V případě selhání se registrace nezdařila. V případě, že je zařízení úspěšně zaregistrované a funkční, můžete mít také problémy se zařízením.
    * V takovém případě použijte tento nástroj k předběžnému řešení potíží předtím, než zaznamenáte žádost o službu s podpora Microsoftu. Doporučujeme spustit tento nástroj a zachytit výstup tohoto nástroje. Tento výstup pak můžete poskytnout pro podporu urychlení řešení potíží.
    * Pokud dojde k nějaké hardwarové součásti nebo selhání clusteru, měli byste se přihlásit Support request.

* **Nízký výkon zařízení** – vaše zařízení StorSimple je pomalé.
    * V takovém případě spusťte tuto rutinu s parametrem Scope nastavenou na výkon. Analyzovat výstup. Získáte latenci cloudu pro čtení i zápis. Nahlášené latence použijte jako maximální cílový cíl, faktor v určité režii pro interní zpracování dat a potom nasaďte zatížení systému. Další informace najdete v tématu [použití testu sítě pro řešení potíží s výkonem zařízení](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Diagnostické testy a ukázkové výstupy

### <a name="hardware-test"></a>Hardwarový test

Tento test určuje stav hardwarových komponent, firmware USM a firmware disků běžících ve vašem systému.

* Hlášené hardwarové komponenty jsou ty součásti, které neprošel testem nebo nejsou v systému k dispozici.
* Firmware USM a verze firmwaru disku jsou hlášeny pro řadič 0, řadič 1 a sdílené součásti ve vašem systému. Úplný seznam hardwarových součástí najdete tady:

    * [Součásti v primární skříni](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Komponenty v EBOD skříni](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Pokud test hardwaru hlásí neúspěšné součásti, [Přihlaste se do žádosti o službu pomocí podpora Microsoftu](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Ukázkový výstup testu hardwaru spuštěného na zařízení 8100

Tady je ukázkový výstup ze zařízení StorSimple 8100. V zařízení modelu 8100 není skříň EBOD k dispozici. Proto nejsou hlášeny komponenty EBOD Controller.

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

### <a name="system-test"></a>Test systému

Tento test oznamuje systémové informace, dostupné aktualizace, informace o clusteru a informace o službě pro vaše zařízení.

* Systémové informace zahrnují model, sériové číslo zařízení, časové pásmo, stav kontroléru a podrobnou verzi softwaru spuštěnou v systému. Chcete-li pochopit různé systémové parametry hlášené jako výstup, přečtěte si [informace o interpretaci systémových informací](#appendix-interpreting-system-information).

* Dostupnost aktualizace hlásí, zda jsou k dispozici standardní a údržbářské režimy a jejich přidružené názvy balíčků. Pokud `RegularUpdates` a `MaintenanceModeUpdates` jsou `false` , znamená to, že aktualizace nejsou k dispozici. Vaše zařízení je aktuální.
* Informace o clusteru obsahují informace o různých logických součástech všech skupin clusteru klientovi HCS a jejich odpovídajících stavů. Pokud v této části sestavy vidíte offline skupinu clusterů, obraťte se na [Podpora Microsoftu](storsimple-8000-contact-microsoft-support.md).
* Informace o službě obsahují názvy a stavy všech služeb klientovi HCS a CiS spuštěných na vašem zařízení. Tyto informace jsou užitečné pro podpora Microsoftu při řešení potíží se zařízením.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Ukázkový výstup systémového testovacího běhu na zařízení 8100

Tady je ukázkový výstup testového běhu systému na zařízení 8100.

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

Tento test ověří stav síťových rozhraní, portů, připojení serverů DNS a NTP serveru, certifikátu TLS/SSL, přihlašovacích údajů k účtu úložiště, připojení k serverům aktualizací a připojení webového proxy serveru na zařízení StorSimple.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Ukázkový výstup testu sítě, pokud je povolený jenom DATA0

Tady je ukázkový výstup zařízení 8100. Můžete vidět výstup, který:
* Jsou povolená a nakonfigurovaná jenom síťová rozhraní DATA 0 a DATA 1.
* DATA 2-5 nejsou na portálu povolena.
* Konfigurace serveru DNS je platná a zařízení se může připojit prostřednictvím serveru DNS.
* Připojení k serveru NTP je také přesné.
* Porty 80 a 443 jsou otevřené. Port 9354 je ale zablokovaný. Na základě [systémových požadavků na síť](./storsimple-8000-system-requirements.md)musíte tento port pro komunikaci se službou Service Bus otevřít.
* Certifikát TLS/SSL je platný.
* Zařízení se může připojit k účtu úložiště: _myss8000storageacct_.
* Připojení k serverům aktualizací je platné.
* Webový proxy server není na tomto zařízení nakonfigurovaný.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Ukázkový výstup testu sítě, pokud jsou povolené DATA0 a DATA1

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

Tento test nahlásí cloudový výkon prostřednictvím latence pro čtení a zápis v cloudu pro vaše zařízení. Tento nástroj se dá použít k vytvoření standardních hodnot výkonu cloudu, které můžete dosáhnout pomocí StorSimple. Nástroj hlásí maximální výkon (nejlepší scénář pro latenci čtení a zápisu), které můžete získat pro vaše připojení.

Když nástroj hlásí maximální dosažitelný výkon, můžeme při nasazování úloh použít nahlášené latence pro čtení a zápis jako cíle.

Test simuluje velikost objektů BLOB přidružených k různým typům svazků v zařízení. Pravidelná vrstva a zálohy místně připojených svazků používají velikost objektu BLOB 64 KB. Vrstvené svazky s zaškrtnutou možností archivace používají velikost dat objektu BLOB 512 KB. Pokud má vaše zařízení nakonfigurované vrstvené a místně připnuté svazky, spustí se jenom test, který odpovídá velikosti dat objektů BLOB v 64 KB.

Chcete-li použít tento nástroj, proveďte následující kroky:

1.  Nejdřív vytvořte kombinaci vrstveného a vrstveného svazku se zaškrtnutou možností archivované. Tato akce zajistí, že nástroj spustí testy pro velikosti objektů BLOB 64 KB a 512 KB.

2. Po vytvoření a konfiguraci svazků spusťte rutinu. Zadejte:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Poznamenejte si latence čtení a zápisu hlášené nástrojem. Spuštění tohoto testu může trvat několik minut, než se výsledky nahlásí.

4. Pokud se latence připojení nacházejí v rámci očekávaného rozsahu, můžete latence hlášené nástrojem použít jako maximální dosažitelný cíl při nasazování úloh. Faktor v určité režii pro interní zpracování dat.

    Pokud jsou latence čtení i zápisu hlášené nástrojem pro diagnostiku vysoké:

    1. Nakonfigurujte Analýza úložiště pro služby BLOB Services a analyzujte výstup, abyste pochopili latence pro účet úložiště Azure. Podrobné pokyny najdete v tématu [povolení a konfigurace analýza úložiště](../storage/blobs/monitor-blob-storage.md). Pokud jsou tyto latence také vysoké a srovnatelné s čísly, která jste obdrželi z nástroje Diagnostika StorSimple, musíte do protokolu Azure Storage zaprotokolovat žádost o služby.

    2. Pokud je latence účtu úložiště nízká, obraťte se na správce sítě a prozkoumejte případné problémy s latencí ve vaší síti.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Ukázkový výstup testu výkonnosti spuštěného na zařízení 8100

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

## <a name="appendix-interpreting-system-information"></a>Příloha: interpretace systémových informací

Tady je Tabulka popisující, k jakým různým parametrům prostředí Windows PowerShell v mapě systémových informací. 

| Parametr PowerShellu    | Description  |
|-------------------------|------------------|
| Instance ID             | Každý kontroler má jedinečný identifikátor nebo identifikátor GUID, který je k němu přidružený.|
| Name                    | Popisný název zařízení nakonfigurovaného prostřednictvím Azure Portal během nasazování zařízení. Výchozím popisným názvem je sériové číslo zařízení. |
| Modelování                   | Model zařízení řady StorSimple 8000. Model může být 8100 nebo 8600.|
| SerialNumber            | Sériové číslo zařízení je přiřazeno k továrně a je 15 znaků dlouhé. Například 8600-SHX0991003G44HT označuje:<br> 8600 – je model zařízení.<br>SHX – je výrobní pracoviště.<br> 0991003 – je konkrétní produkt. <br> G44HT – při posledních 5 číslicích se zvýší a vytvoří se jedinečná sériová čísla. Nemusí se jednat o sekvenční sadu.|
| TimeZone                | Časové pásmo zařízení nakonfigurované v Azure Portal během nasazování zařízení.|
| CurrentController       | Kontroler, ke kterému jste připojeni přes rozhraní Windows PowerShell zařízení StorSimple.|
| ActiveController        | Kontroler, který je v zařízení aktivní a řídí veškerou síťovou a diskovou operaci. Může to být Controller 0 nebo Controller 1.  |
| Controller0Status       | Stav řadiče 0 na vašem zařízení. Stav kontroleru může být normální, v režimu obnovení nebo nedosažitelný.|
| Controller1Status       | Stav kontroleru 1 na vašem zařízení.  Stav kontroleru může být normální, v režimu obnovení nebo nedosažitelný.|
| SystemMode              | Celkový stav zařízení StorSimple. Stav zařízení může být normální, údržba nebo vyřazený z provozu (odpovídá deaktivaci v Azure Portal).|
| FriendlySoftwareVersion | Popisný řetězec, který odpovídá verzi softwaru zařízení. V případě systému, na kterém je spuštěná aktualizace 4, by měla být popisná verze softwaru StorSimple 8000 Series Update 4,0.|
| HcsSoftwareVersion      | Verze softwaru klientovi HCS spuštěná na vašem zařízení. Například verze softwaru klientovi HCS odpovídající aktualizaci StorSimple 8000 Series 4,0 je 6.3.9600.17820. |
| ApiVersion              | Verze softwaru rozhraní API Windows PowerShellu pro zařízení klientovi HCS.|
| VhdVersion              | Verze softwaru pro bitovou kopii továrního odeslání zařízení. Pokud zařízení resetujete do výchozího továrního nastavení, spustí se tato verze softwaru.|
| OSVersion               | Verze softwaru operačního systému Windows Server, který je na zařízení spuštěný. Zařízení StorSimple je založené na systému Windows Server 2012 R2, který odpovídá 6.3.9600.|
| CisAgentVersion         | Verze vašeho agenta CIS spuštěná na vašem zařízení StorSimple. Tento agent pomáhá komunikovat se službou StorSimple Manager běžící v Azure.|
| MdsAgentVersion         | Verze, která odpovídá agentu služby MDS běžícímu na vašem zařízení StorSimple. Tento agent přesouvá data do služby Monitoring and Diagnostics (MDS).|
| Lsisas2Version          | Verze odpovídající ovladačům LSI na zařízení StorSimple.|
| Kapacita                | Celková kapacita zařízení v bajtech.|
| RemoteManagementMode    | Určuje, jestli se zařízení dá vzdáleně spravovat přes jeho rozhraní Windows PowerShell. |
| FipsMode                | Určuje, jestli je na vašem zařízení povolený USA režim FIPS (Federal Information Processing Standard). Standard FIPS 140 definuje kryptografické algoritmy schválené pro použití v počítačových systémech USA federální správy pro ochranu citlivých dat. U zařízení se systémem Update 4 nebo novějším je režim FIPS ve výchozím nastavení povolen. |

## <a name="next-steps"></a>Další kroky

* Seznamte se s [syntaxí rutiny Invoke-HcsDiagnostics](/previous-versions/windows/powershell-scripting/mt795371(v=wps.630)).

* Přečtěte si další informace o [řešení potíží s nasazením](./storsimple-8000-troubleshoot-deployment.md) na zařízení StorSimple.