---
title: Úvod do řešení potíží s prostředky
titleSuffix: Azure Network Watcher
description: Tato stránka poskytuje přehled možností řešení potíží s Network Watcher prostředky.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 0d0597c2df8731171505a090de6959d8a112c004
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98569976"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Úvod k řešení potíží s prostředky v Azure Network Watcher

Brány Virtual Network poskytují konektivitu mezi místními prostředky a dalšími virtuálními sítěmi v rámci Azure. Pro zajištění, aby nedošlo k přerušení komunikace, je důležité monitorovat brány a jejich připojení. Network Watcher poskytuje možnost řešení potíží s bránami a připojeními. Schopnost se dá volat prostřednictvím portálu, PowerShellu, rozhraní příkazového řádku Azure nebo REST API. Při volání Network Watcher diagnostikuje stav brány nebo připojení a vrátí příslušné výsledky. Požadavek je dlouhodobá transakce. Výsledky se vrátí po dokončení diagnostiky.

![Snímek obrazovky ukazuje diagnostiku Network Watcher V P N.][2]

## <a name="results"></a>Výsledky

V případě vrácených předběžných výsledků získáte celkový přehled o stavu prostředku. K dispozici jsou hlubší informace pro prostředky, jak je znázorněno v následující části:

Následující seznam uvádí hodnoty vracené pomocí rozhraní API pro řešení potíží:

* **čas_spuštění** – tato hodnota je čas spuštění volání rozhraní API při řešení potíží.
* **čas_ukončení** – tato hodnota představuje čas, kdy byl ukončen Poradce při potížích.
* **kód** – tato hodnota není v pořádku, pokud dojde k jedné chybě diagnostiky.
* **výsledky** – výsledky jsou kolekcí výsledků vrácených na připojení nebo bráně virtuální sítě.
    * **ID** – tato hodnota je typ chyby.
    * **Souhrn** – tato hodnota je souhrnem chyby.
    * **detailed** – tato hodnota poskytuje podrobný popis chyby.
    * **recommendedActions** – Tato vlastnost je kolekcí doporučených akcí, které je potřeba provést.
      * **actionText** – tato hodnota obsahuje text popisující, jakou akci chcete provést.
      * **actionUri** – tato hodnota poskytne identifikátor URI dokumentaci k tomu, jak se jednat.
      * **actionUriText** – tato hodnota je krátký popis textu akce.

V následujících tabulkách jsou uvedeny různé typy chyb (ID z předchozího seznamu), které jsou k dispozici, a pokud chyba vytváří protokoly.

### <a name="gateway"></a>brána

| Typ chyby | Důvod | Protokol|
|---|---|---|
| NoFault | Pokud není zjištěna žádná chyba |Ano|
| GatewayNotFound | Nejde najít bránu nebo bránu není zřízená. |Ne|
| PlannedMaintenance |  Probíhá údržba instance brány.  |Ne|
| UserDrivenUpdate | Tato chyba nastane, když probíhá aktualizace uživatele. Aktualizací může být i operace, která spočívá ve změně velikosti. | Ne |
| VipUnResponsive | K této chybě dojde, když se nepodaří spojení s primární instancí brány kvůli neúspěšné sondě stavu. | Ne |
| PlatformInActive | Došlo k problému s platformou. | Ne|
| ServiceNotRunning | Podkladová služba není spuštěná. | Ne|
| NoConnectionsFoundForGateway | V bráně neexistují žádná připojení. Tato chyba je jenom upozornění.| Ne|
| ConnectionsNotConnected | Připojení nejsou připojena. Tato chyba je jenom upozornění.| Ano|
| GatewayCPUUsageExceeded | Aktuální využití procesoru brány je > 95%. | Ano |

### <a name="connection"></a>Připojení

| Typ chyby | Důvod | Protokol|
|---|---|---|
| NoFault | Pokud není zjištěna žádná chyba |Ano|
| GatewayNotFound | Nejde najít bránu nebo bránu není zřízená. |Ne|
| PlannedMaintenance | Probíhá údržba instance brány.  |Ne|
| UserDrivenUpdate | Tato chyba nastane, když probíhá aktualizace uživatele. Aktualizací může být i operace, která spočívá ve změně velikosti.  | Ne |
| VipUnResponsive | K této chybě dojde, když se nepodaří spojení s primární instancí brány kvůli neúspěšné sondě stavu. | Ne |
| ConnectionEntityNotFound | Chybí konfigurace připojení. | Ne |
| ConnectionIsMarkedDisconnected | Připojení je označeno jako odpojené. |Ne|
| ConnectionNotConfiguredOnGateway | V podkladové službě není nakonfigurované připojení. | Ano |
| ConnectionMarkedStandby | Podkladová služba je označena jako pohotovostní.| Ano|
| Authentication | Neshoda předsdíleného klíče | Ano|
| PeerReachability | Partnerská brána není dostupná. | Ano|
| IkePolicyMismatch | Partnerská brána má zásady IKE, které Azure nepodporuje. | Ano|
| Chyba WfpParse | Při analýze protokolu WFP došlo k chybě. |Ano|

## <a name="supported-gateway-types"></a>Podporované typy bran

Následující tabulka uvádí, které brány a připojení jsou podporované při řešení potíží s Network Watcher:

| Brána nebo připojení | Podporováno  |
|---------|---------|
|**Typy bran**   |         |
|Síť VPN      | Podporováno        |
|ExpressRoute | Nepodporuje se |
|**Typy sítě VPN** | |
|Založené na trasách | Podporováno|
|Založené na zásadách | Nepodporuje se|
|**Typy připojení**||
|IPSec| Podporováno|
|VNet2Vnet| Podporováno|
|ExpressRoute| Nepodporuje se|
|VPNClient| Nepodporuje se|

## <a name="log-files"></a>Soubory protokolu

Po dokončení řešení potíží s prostředkem se soubory protokolu řešení potíží se zdroji ukládají v účtu úložiště. Následující obrázek ukazuje vzorový obsah volání, jehož výsledkem je chyba.

![soubor zip][1]

> [!NOTE]
> V některých případech je do úložiště zapisována pouze podmnožina souborů protokolů.

Pokyny ke stahování souborů z účtů Azure Storage najdete v tématu [Začínáme s úložištěm objektů BLOB v Azure pomocí .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Průzkumník služby Storage se dá použít jiný nástroj. Další informace o Průzkumník služby Storage najdete na následujícím odkazu: [Průzkumník služby Storage](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

**ConnectionStats.txt** soubor obsahuje celkovou statistiku připojení, včetně příchozích a odchozích bajtů, stavu připojení a času, kdy bylo připojení navázáno.

> [!NOTE]
> Pokud volání rozhraní API pro řešení potíží vrátí v pořádku, jediná věc vrácená v souboru ZIP je soubor **ConnectionStats.txt** .

Obsah tohoto souboru je podobný následujícímu příkladu:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

**CPUStats.txt** soubor obsahuje využití CPU a paměť dostupnou v době testování.  Obsah tohoto souboru je podobný následujícímu příkladu:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

**IKEErrors.txt** soubor obsahuje chyby protokolu IKE, které byly zjištěny během monitorování.

Následující příklad ukazuje obsah souboru IKEErrors.txt. Vaše chyby se můžou lišit v závislosti na problému.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

Soubor protokolu **Scrubbed-wfpdiag.txt** obsahuje protokol WFP. Tento protokol obsahuje protokolování chyb odkládacího paketu a protokolu IKE/AuthIP.

Následující příklad ukazuje obsah souboru Scrubbed-wfpdiag.txt. V tomto příkladu není sdílený klíč připojení správný, protože ho můžete vidět na třetím řádku zdola. V následujícím příkladu je pouze fragment celého protokolu, protože v závislosti na problému může být protokol zdlouhavý.

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

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt. Sum

Soubor **wfpdiag.txt. Sum** je protokol zobrazující zpracovávané vyrovnávací paměti a události.

Následující příklad je obsah souboru wfpdiag.txt. Sum.
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

## <a name="considerations"></a>Požadavky 
* Pro každé předplatné se dá spustit jenom jedna operace řešení potíží. Pokud chcete spustit jinou operaci odstraňování potíží, počkejte na dokončení předchozí. Aktivace dalších operací v době, kdy předchozí akce nebyla dokončena, způsobí selhání dalších operací. 
* Chyba CLI: Pokud ke spuštění příkazu používáte Azure CLI, VPN Gateway a účet úložiště musí být ve stejné skupině prostředků. Zákazníci s prostředky v různých skupinách prostředků můžou místo toho použít PowerShell nebo Azure Portal.  


## <a name="next-steps"></a>Další kroky

Informace o tom, jak diagnostikovat problém s bránou nebo připojením k bráně, najdete v tématu [Diagnostika potíží s komunikací mezi sítěmi](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
