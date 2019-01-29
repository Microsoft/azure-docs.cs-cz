---
title: Úvod do prostředků, řešení potíží ve službě Azure Network Watcher | Dokumentace Microsoftu
description: Tato stránka poskytuje přehled o funkce pro odstraňování potíží prostředek Network Watcher
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: c1145cd6-d1cf-4770-b1cc-eaf0464cc315
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 8048dde6158d9eaa9bf38a8c3020420b81bdd55b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099770"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Úvod k řešení potíží ve službě Azure Network Watcher prostředku

Brány virtuální sítě zajistěte připojení mezi místními prostředky a dalším virtuálním sítím v Azure. Pro zajištění, aby nedošlo k přerušení komunikace, je důležité monitorovat brány a jejich připojení. Network Watcher dává možnost Poradce při potížích s připojení a bran. Funkci lze volat na portálu, Powershellu, rozhraní příkazového řádku Azure nebo rozhraní REST API. Při volání, Network Watcher diagnostikuje stav brány nebo připojení a vrátí odpovídající výsledky. Požadavek je dlouhodobá transakce. Výsledky se vrátí po dokončení diagnostiky.

![portál][2]

## <a name="results"></a>Výsledky

Předběžné výsledky vrácené poskytnout celkový přehled o stavu prostředku. Podrobnější informace lze zadat pro prostředky, jak je znázorněno v následující části:

V následujícím seznamu jsou hodnoty vrátil Poradce při potížích rozhraní API:

* **startTime** – tato hodnota je čas spuštění Poradce při potížích API volání.
* **Koncový čas** – tato hodnota je čas ukončení poradce při potížích.
* **kód** – tato hodnota není v pořádku, pokud dojde k selhání jedné diagnostiku.
* **výsledky** -výsledků je kolekce výsledků vrácených na připojení nebo brány virtuální sítě.
    * **ID** – tato hodnota je typu selhání.
    * **Souhrn** – tato hodnota je uveden seznam chyby.
    * **Podrobné** -tuto hodnotu poskytuje podrobný popis chyby.
    * **recommendedActions** – tato vlastnost je kolekce doporučené akce.
      * **actionText** – tato hodnota obsahuje text popisující, jaké akce se má provést.
      * **actionUri** – tato hodnota obsahuje identifikátor URI pro dokumentaci o tom, jak fungují.
      * **actionUriText** – tato hodnota je krátký popis textu akce.

Následující tabulky popisují typy různých chybových (id v části s výsledky z předchozího seznamu), které jsou k dispozici, a pokud chyba vytvoří protokoly.

### <a name="gateway"></a>brána

| Typ chyby | Důvod | Protokol|
|---|---|---|
| NoFault | Pokud není zjištěna žádná chyba |Ano|
| GatewayNotFound | Nejde najít brána nebo brána není zřízená. |Ne|
| PlannedMaintenance |  Instance brány probíhá údržba  |Ne|
| UserDrivenUpdate | Toto selhání nastane, pokud probíhá aktualizace uživatele. Aktualizace může být operace změny velikosti. | Ne |
| VipUnResponsive | Toto selhání nastane, pokud primární instance brány nelze dosáhnout z důvodu selhání sondy stavu. | Ne |
| PlatformInActive | Se vyskytl problém s platformou. | Ne|
| ServiceNotRunning | Základní služba není spuštěná. | Ne|
| NoConnectionsFoundForGateway | Neexistuje žádné připojení k bráně. Tato porucha je pouze upozornění.| Ne|
| ConnectionsNotConnected | Připojení nejsou připojené. Tato porucha je pouze upozornění.| Ano|
| GatewayCPUUsageExceeded | Brána aktuální využití procesoru je > 95 %. | Ano |

### <a name="connection"></a>Připojení

| Typ chyby | Důvod | Protokol|
|---|---|---|
| NoFault | Pokud není zjištěna žádná chyba |Ano|
| GatewayNotFound | Nejde najít brána nebo brána není zřízená. |Ne|
| PlannedMaintenance | Instance brány probíhá údržba  |Ne|
| UserDrivenUpdate | Toto selhání nastane, pokud probíhá aktualizace uživatele. Aktualizace může být operace změny velikosti.  | Ne |
| VipUnResponsive | Toto selhání nastane, pokud primární instance brány nelze dosáhnout z důvodu selhání sondy stavu. | Ne |
| ConnectionEntityNotFound | Chybí konfigurace připojení | Ne |
| ConnectionIsMarkedDisconnected | Připojení je označen "odpojené" |Ne|
| ConnectionNotConfiguredOnGateway | Základní služba nemá nakonfigurované připojení. | Ano |
| ConnectionMarkedStandby | Základní služba je označena jako pohotovostní režim.| Ano|
| Authentication | Předsdílené klíče se neshodují. | Ano|
| PeerReachability | Brána partnera není dostupná. | Ano|
| IkePolicyMismatch | Partner brány má IKE zásady, které nejsou podporovány službou Azure. | Ano|
| Chyba WfpParse | Došlo k chybě při analýze protokolu Ochrana souborů systému Windows. |Ano|

## <a name="supported-gateway-types"></a>Podporované typy bran

Následující tabulka uvádí, jaké brány a připojení jsou podporovány při odstraňování potíží Network Watcher:

|  |  |
|---------|---------|
|**Typy bran**   |         |
|Síť VPN      | Podporováno        |
|ExpressRoute | Nepodporuje se |
|**Typy sítě VPN** | |
|Směrování na základě | Podporováno|
|Na základě zásad | Nepodporuje se|
|**Typy připojení**||
|IPSec| Podporováno|
|VNet2Vnet| Podporováno|
|ExpressRoute| Nepodporuje se|
|VPNClient| Nepodporuje se|

## <a name="log-files"></a>Soubory protokolu

Soubory protokolů Poradce při potížích prostředků jsou uloženy v účtu úložiště po dokončení odstraňování prostředků. Následující obrázek ukazuje příklad obsah volání, která způsobila chybu.

![soubor ZIP][1]

> [!NOTE]
> V některých případech pouze podmnožinu souborů protokolů se zapisuje do úložiště.

Pokyny ke stahování souborů z účtů úložiště azure, najdete v tématu [Začínáme s Azure Blob storage pomocí .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Dalším nástrojem, který je možné je Průzkumníka služby Storage. Další informace o Průzkumníku služby Storage najdete tady na následující odkaz: [Storage Explorer](http://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

**ConnectionStats.txt** soubor obsahuje celkové statistiky připojení, včetně příchozí a Odchozí bajty, stav připojení a čas bylo vytvořeno připojení.

> [!NOTE]
> Pokud volání rozhraní API pro řešení problémů s vrátí v pořádku, jediné, co vrátí v souboru zip je **ConnectionStats.txt** souboru.

Obsah tohoto souboru jsou podobné následujícímu příkladu:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

**CPUStats.txt** soubor obsahuje využití procesoru a paměti, které jsou k dispozici v době testování.  Obsah tohoto souboru se podobá následujícímu příkladu:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

**IKEErrors.txt** soubor obsahuje všechny chyby IKE, které byly nalezeny během monitorování.

Následující příklad ukazuje obsah souboru IKEErrors.txt kopie. Chyby se může lišit v závislosti na problému.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

**Scrubbed wfpdiag.txt** protokolový soubor obsahuje wfp protokolu. Tento protokol obsahuje protokolování zahození paketu a IKE/AuthIP selhání.

Následující příklad ukazuje obsah souboru Scrubbed wfpdiag.txt. V tomto příkladu nebyl sdílený klíč připojení správné, jak je vidět z ve třetím řádku v dolní části. Následující příklad je pouze fragmentem celý protokol, protože protokol může být zdlouhavé v závislosti na problému.

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

**Wfpdiag.txt.sum** je soubor protokolu vyrovnávací paměti a události byly zpracovány.

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

## <a name="next-steps"></a>Další postup

Zjistěte, jak diagnostikovat potíže s brány nebo připojení k bráně, najdete v článku [diagnostikovat problémy s komunikací mezi sítěmi](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
