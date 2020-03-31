---
title: Úvod k řešení potíží s prostředky
titleSuffix: Azure Network Watcher
description: Tato stránka obsahuje přehled možností řešení potíží s prostředky sledování sítě
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 199b4fc762919c2e3988f477c14d09fc23b0136b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840685"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Úvod k řešení potíží s prostředky v Azure Network Watcher

Brány virtuální sítě poskytují připojení mezi místními prostředky a dalšími virtuálními sítěmi v rámci Azure. Pro zajištění, aby nedošlo k přerušení komunikace, je důležité monitorovat brány a jejich připojení. Sledování sítě poskytuje možnost řešení potíží s bránami a připojeními. Funkce lze volat prostřednictvím portálu, PowerShell, Azure CLI nebo REST API. Při volání, Network Watcher diagnostikuje stav brány nebo připojení a vrátí příslušné výsledky. Požadavek je dlouho běžící transakce. Výsledky jsou vráceny po dokončení diagnózy.

![portál][2]

## <a name="results"></a>Výsledky

Vrácené předběžné výsledky poskytují celkový obraz o stavu zdroje. Hlubší informace mohou být poskytnuty pro zdroje, jak je znázorněno v následující části:

Následující seznam je hodnoty vrácené s poradcepři potíží api:

* **startTime** - Tato hodnota je čas řešení potíží s voláním rozhraní API.
* **endTime** - Tato hodnota je čas, kdy skončilo řešení potíží.
* **kód** - Tato hodnota je Není v pořádku, pokud je selhání jedné diagnózy.
* **výsledky** – výsledky je kolekce výsledků vrácených na připojení nebo brány virtuální sítě.
    * **id** - Tato hodnota je typ poruchy.
    * **souhrn** - Tato hodnota je souhrnem chyby.
    * **podrobné** - Tato hodnota poskytuje podrobný popis poruchy.
    * **recommendedActions** - Tato vlastnost je kolekce doporučených akcí, které je třeba provést.
      * **actionText** - Tato hodnota obsahuje text popisující, jakou akci provést.
      * **actionUri** - Tato hodnota poskytuje identifikátor URI dokumentaci o tom, jak jednat.
      * **actionUriText** - Tato hodnota je krátký popis textu akce.

V následujících tabulkách jsou uvedeny různé typy chyb (id pod výsledky z předchozího seznamu), které jsou k dispozici, a pokud chyba vytvoří protokoly.

### <a name="gateway"></a>brána

| Typ chyby | Důvod | Protokol|
|---|---|---|
| NoFault | Pokud není zjištěna žádná chyba |Ano|
| GatewayNotFound | Nelze najít bránu nebo brána není zřízena |Ne|
| PlannedMaintenance |  Instance brány je v rámci údržby  |Ne|
| UserDrivenUpdate | Tato chyba nastane, když probíhá aktualizace uživatele. Aktualizací může být i operace, která spočívá ve změně velikosti. | Ne |
| VipUnResponsive | K této chybě dojde, když se nepodaří spojení s primární instancí brány kvůli neúspěšné sondě stavu. | Ne |
| PlatformInActive | Došlo k problému s platformou. | Ne|
| Služba není spuštěna. | Základní služba není spuštěna. | Ne|
| Bez připojenífoundforgateway | V bráně neexistují žádná připojení. Tato chyba je pouze varování.| Ne|
| Připojení není připojeno | Připojení nejsou připojena. Tato chyba je pouze varování.| Ano|
| GatewayCPUUsageExceeded | Aktuální využití procesoru brány je > 95 %. | Ano |

### <a name="connection"></a>Připojení

| Typ chyby | Důvod | Protokol|
|---|---|---|
| NoFault | Pokud není zjištěna žádná chyba |Ano|
| GatewayNotFound | Nelze najít bránu nebo brána není zřízena |Ne|
| PlannedMaintenance | Instance brány je v rámci údržby  |Ne|
| UserDrivenUpdate | Tato chyba nastane, když probíhá aktualizace uživatele. Aktualizací může být i operace, která spočívá ve změně velikosti.  | Ne |
| VipUnResponsive | K této chybě dojde, když se nepodaří spojení s primární instancí brány kvůli neúspěšné sondě stavu. | Ne |
| ConnectionEntityNotFound | Chybí konfigurace připojení. | Ne |
| ConnectionIsMarkedOdpojened | Připojení je označeno jako "odpojeno" |Ne|
| ConnectionNotConfiguredonGateway | Základní služba nemá nakonfigurované připojení. | Ano |
| ConnectionMarkedStandby | Základní služba je označena jako pohotovostní režim.| Ano|
| Ověřování | Neshoda předsdílených klíčů | Ano|
| PeerReachability | Brána partnera není dostupná. | Ano|
| IkePolicyMismatch | Brána partnera má zásady IKE, které Azure nepodporuje. | Ano|
| Chyba wfpParse | Při analýzě protokolu WFP došlo k chybě. |Ano|

## <a name="supported-gateway-types"></a>Podporované typy bran

V následující tabulce jsou uvedeny, které brány a připojení jsou podporovány při řešení potíží s následováním sítě:

|  |  |
|---------|---------|
|**Typy bran**   |         |
|Síť VPN      | Podporuje se        |
|ExpressRoute | Nepodporuje se |
|**Typy sítě VPN** | |
|Na základě trasy | Podporuje se|
|Zásady založené | Nepodporuje se|
|**Typy připojení**||
|IPSec| Podporuje se|
|Síť VNet2Vnet| Podporuje se|
|ExpressRoute| Nepodporuje se|
|VPNClient| Nepodporuje se|

## <a name="log-files"></a>Soubory protokolu

Soubory protokolu pro řešení potíží s prostředky jsou po dokončení řešení potíží s prostředky uloženy v účtu úložiště. Následující obrázek znázorňuje ukázkový obsah volání, který vedl k chybě.

![zip soubor][1]

> [!NOTE]
> V některých případech je do úložiště zapsána pouze podmnožina souborů protokolů.

Pokyny ke stahování souborů z účtů azure storage najdete v článku [Začínáme s úložištěm objektů blob Azure pomocí rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Dalším nástrojem, který lze použít, je Průzkumník úložiště. Další informace o Průzkumníku úložiště naleznete zde na následujícím odkazu: [Průzkumník úložiště](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>Soubor ConnectionStats.txt

Soubor **ConnectionStats.txt** obsahuje celkové statistiky připojení, včetně příchozího a odchozího počtu bajtů, stavu připojení a doby, kdy bylo připojení navázáno.

> [!NOTE]
> Pokud volání rozhraní API pro řešení potíží vrátí v pořádku, jediná věc vrácená v souboru zip je soubor **ConnectionStats.txt.**

Obsah tohoto souboru je podobný následujícímu příkladu:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

Soubor **CPUStats.txt** obsahuje využití procesoru a paměť, které jsou k dispozici v době testování.  Obsah tohoto souboru je podobný následujícímu příkladu:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>Soubor IKEErrors.txt

Soubor **IKEErrors.txt** obsahuje všechny chyby ike, které byly nalezeny během monitorování.

Následující příklad ukazuje obsah souboru IKEErrors.txt. Vaše chyby se mohou lišit v závislosti na problému.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Vydrhnutá soubor wfpdiag.txt

Soubor protokolu **Scrubbed-wfpdiag.txt** obsahuje protokol wfp. Tento protokol obsahuje protokolování přetažení paketů a selhání protokolu IKE/AuthIP.

Následující příklad ukazuje obsah souboru Scrubbed-wfpdiag.txt. V tomto příkladu sdílený klíč připojení nebyl správný, jak je vidět z třetího řádku zdolního. Následující příklad je pouze výstřižek celého protokolu, protože protokol může být zdlouhavé v závislosti na problému.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.sum

Soubor **wfpdiag.txt.sum** je protokol zobrazující zpracované vyrovnávací paměti a události.

Následující příklad je obsah souboru wfpdiag.txt.sum.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>Další kroky

Informace o diagnostice problému s připojením brány nebo brány naleznete v [tématu Diagnostika problémů](diagnose-communication-problem-between-networks.md)s komunikací mezi sítěmi .
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
