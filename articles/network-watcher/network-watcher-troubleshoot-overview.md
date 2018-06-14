---
title: Úvod k řešení potíží s v sledovací proces sítě Azure prostředku | Microsoft Docs
description: Tato stránka obsahuje přehled možnosti pro odstraňování potíží prostředků sledovací proces sítě
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
ms.openlocfilehash: 2f8a41834c1451d80c53cfed4bae3b7e36281702
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779256"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Úvod k řešení potíží s v sledovací proces sítě Azure prostředku

Brány virtuální sítě zajistěte připojení mezi místními prostředky a dalším virtuálním sítím v rámci Azure. Monitorování brány a jejich připojení jsou důležité k zajištění komunikace není přerušeno. Sledovací proces sítě poskytuje možnost Poradce při potížích s připojení a bran. Funkce je možné volat prostřednictvím portálu, prostředí PowerShell, rozhraní příkazového řádku Azure nebo REST API. Při volání, sledovací proces sítě diagnostikuje stav brány, nebo připojení a vrátí odpovídající výsledky. Žádost je dlouhotrvající transakci. Po dokončení diagnostiky, budou vráceny výsledky.

![portál][2]

## <a name="results"></a>Výsledky

Předběžné výsledky vrácené poskytují celkový přehled o stavu prostředku. Podrobnější informace lze zadat pro prostředky, jak je znázorněno v následující části:

V následujícím seznamu je hodnot vrácených s Poradce při potížích rozhraní API:

* **startTime** – tato hodnota je spuštění volání rozhraní API Poradce při potížích.
* **čas ukončení** – tato hodnota je čas ukončení poradce při potížích.
* **kód** – tato hodnota není v pořádku, pokud dojde k selhání jedné diagnostiku.
* **výsledky** -výsledků je kolekce výsledků vrácených na připojení nebo brány virtuální sítě.
    * **ID** – tato hodnota je typem chyby.
    * **Souhrn** – tato hodnota je souhrn poruchy.
    * **Podrobné** -tuto hodnotu poskytuje podrobný popis chyby.
    * **recommendedActions** -tato vlastnost je kolekce doporučených akcí.
      * **actionText** – tato hodnota obsahuje text popisující, jakou akci chcete provést.
      * **actionUri** -tuto hodnotu poskytuje identifikátor URI pro dokumentaci o tom, jak fungují.
      * **actionUriText** – tato hodnota je krátký popis text akce.

Následující tabulky popisují různé chyby typy (id pod výsledky v předchozím seznamu), které jsou k dispozici, a pokud selhání vytvoří protokoly.

### <a name="gateway"></a>brána

| Typ chyby | Důvod | Protokol|
|---|---|---|
| NoFault | Když je zjištěna žádná chyba |Ano|
| GatewayNotFound | Nelze najít bránu nebo brány není zřízený. |Ne|
| PlannedMaintenance |  Instance brány je v rámci údržby  |Ne|
| UserDrivenUpdate | Toto selhání nastane, když probíhá aktualizace uživatele. Aktualizace může být operace změny velikosti. | Ne |
| VipUnResponsive | Toto selhání nastane, když primární instance brány není dosažitelná z důvodu selhání kontroly stavu. | Ne |
| PlatformInActive | Nastane problém s platformou. | Ne|
| ServiceNotRunning | Základní služba není spuštěna. | Ne|
| NoConnectionsFoundForGateway | Na bráně existovat žádné připojení. Toto selhání je pouze upozornění.| Ne|
| ConnectionsNotConnected | Připojení nejsou připojené. Toto selhání je pouze upozornění.| Ano|
| GatewayCPUUsageExceeded | Aktuální využití procesoru brány je > 95 %. | Ano |

### <a name="connection"></a>Připojení

| Typ chyby | Důvod | Protokol|
|---|---|---|
| NoFault | Když je zjištěna žádná chyba |Ano|
| GatewayNotFound | Nelze najít bránu nebo brány není zřízený. |Ne|
| PlannedMaintenance | Instance brány je v rámci údržby  |Ne|
| UserDrivenUpdate | Toto selhání nastane, když probíhá aktualizace uživatele. Aktualizace může být operace změny velikosti.  | Ne |
| VipUnResponsive | Toto selhání nastane, když primární instance brány není dosažitelná z důvodu selhání kontroly stavu. | Ne |
| ConnectionEntityNotFound | Chybí konfigurace připojení | Ne |
| ConnectionIsMarkedDisconnected | Připojení je označena "odpojené" |Ne|
| ConnectionNotConfiguredOnGateway | Základní služby není k dispozici připojení nakonfigurovaná. | Ano |
| ConnectionMarkedStandy | Základní služby je označena jako pohotovostní režim.| Ano|
| Authentication | Neshoda předsdílených klíčů | Ano|
| PeerReachability | Sdílené brána není dostupný. | Ano|
| IkePolicyMismatch | Bránu sdílené má IKE zásady, které nejsou podporované službou Azure. | Ano|
| Chyba WfpParse | Došlo k chybě při analýze protokolů Ochrana souborů systému Windows. |Ano|

## <a name="supported-gateway-types"></a>Podporované typy brány

Následující tabulka uvádí připojení a bran, které jsou podporovány při řešení problémů sledovací proces sítě:

|  |  |
|---------|---------|
|**Typy brány**   |         |
|Síť VPN      | Podporováno        |
|ExpressRoute | Nepodporuje se |
|**Typy sítě VPN** | |
|Na základě trasy | Podporováno|
|Na základě zásad | Nepodporuje se|
|**Typy připojení**||
|Protokol IPSec| Podporováno|
|VNet2Vnet| Podporováno|
|ExpressRoute| Nepodporuje se|
|VPNClient| Nepodporuje se|

## <a name="log-files"></a>Soubory protokolu

Soubory protokolů Poradce prostředků jsou uložené v účtu úložiště po dokončení řešení potíží s prostředků. Následující obrázek znázorňuje příklad obsah volání, jehož výsledkem chyba.

![soubor ZIP][1]

> [!NOTE]
> V některých případech pouze podmnožinu soubory protokolů se zapíše do úložiště.

Pokyny ke stahování souborů z účty azure storage, najdete v části [Začínáme s Azure Blob storage pomocí rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Jiný nástroj, který je možné je Storage Explorer. Další informace o Storage Explorer naleznete zde na následující odkaz: [Storage Explorer](http://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

**ConnectionStats.txt** soubor obsahuje celkové statistiky připojení, včetně příchozí a Odchozí bajty, stav připojení a času, které bylo vytvořeno připojení.

> [!NOTE]
> Pokud vrátí volání rozhraní API pro odstraňování potíží v pořádku, je jediné, co, vrátí se v souboru zip **ConnectionStats.txt** souboru.

Obsah tohoto souboru se podobně jako v následujícím příkladu:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

**CPUStats.txt** soubor obsahuje využití procesoru a paměti, které jsou k dispozici při testování.  Obsah tohoto souboru je stejný jako v následujícím příkladu:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

**IKEErrors.txt** soubor obsahuje chyby IKE, ke kterým během monitorování nebyly nalezeny.

Následující příklad ukazuje obsah souboru IKEErrors.txt. Chyby se může lišit v závislosti na problém.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Očistí wfpdiag.txt

**Scrubbed wfpdiag.txt** protokolový soubor obsahuje protokol Ochrana souborů systému Windows. Tento protokol obsahuje protokolování paketu vyřaďte a IKE/AuthIP selhání.

Následující příklad ukazuje obsah souboru Scrubbed wfpdiag.txt. V tomto příkladu nebyla sdílený klíč připojení správné, jak je vidět z ve třetím řádku dole. V následujícím příkladu je právě fragment celý protokolu, jako protokol může být náročná v závislosti na problém.

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

**Wfpdiag.txt.sum** je soubor protokolu vyrovnávací paměti a zpracovaných událostí.

V následujícím příkladu je obsah souboru wfpdiag.txt.sum.
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

Zjistěte, jak diagnostikovat potíže s brány nebo připojení k bráně, najdete v tématu [diagnostikovat problémy komunikace mezi sítěmi](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
