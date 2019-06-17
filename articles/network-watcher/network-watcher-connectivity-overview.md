---
title: Úvod do služby Azure Network Watcher připojení, řešení potíží s | Dokumentace Microsoftu
description: Tato stránka poskytuje přehled o řešení problémů s možností připojení Network Watcher
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: kumud
ms.openlocfilehash: 9510905f67ee943b4b1dfa5a14c2753efac39da7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64705816"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Úvod k připojení řešení ve službě Azure Network Watcher

Řešení potíží s připojením funkce služby Network Watcher nabízí funkce pro kontrolu přímého připojení TCP z virtuálního počítače na virtuální počítač (VM), plně kvalifikovaný název domény (FQDN), identifikátor URI, nebo IPv4 adresu. Scénáře sítě jsou složitá, jsou implementovány pomocí skupin zabezpečení sítě, brány firewall, uživatelem definované trasy a prostředky, které poskytuje Azure. Komplexní se provedené konfigurace řešení problémů s problémy s připojením náročná. Network Watcher pomáhá snižovat dobu na nalezení a zjištění problémů s připojením. Vrácené výsledky můžou poskytovat přehled o Určuje, zda je problém s připojením platforma nebo o problém s konfigurací uživatele. Připojení můžete zkontrolovat pomocí [PowerShell](network-watcher-connectivity-powershell.md), [rozhraní příkazového řádku Azure](network-watcher-connectivity-cli.md), a [rozhraní REST API](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Řešení potíží s připojením vyžaduje, aby měl řešit z virtuálního počítače `AzureNetworkWatcherExtension` nainstalované rozšíření virtuálního počítače. Instalaci rozšíření na virtuálním počítači s Windows najdete [rozšíření virtuálního počítače Azure Network Watcher Agent pro Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a pro virtuální počítač s Linuxem, navštivte [rozšíření virtuálního počítače Azure Network Watcher Agent pro Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Na cílový koncový bod není vyžadován rozšíření.

## <a name="response"></a>Odpověď

V následující tabulce jsou uvedeny vlastnosti vrácena při řešení potíží s připojením po ukončení běhu.

|Vlastnost  |Popis  |
|---------|---------|
|connectionStatus     | Stav Kontrola připojení. Jsou možné výsledky **dostupné** a **Unreachable**.        |
|AvgLatencyInMs     | Průměrná latence při kontrole připojení v milisekundách. (Zobrazí pouze pokud se stav kontroly je dostupný)        |
|MinLatencyInMs     | Minimální latence při kontrole připojení v milisekundách. (Zobrazí pouze pokud se stav kontroly je dostupný)        |
|MaxLatencyInMs     | Maximální latence při kontrole připojení v milisekundách. (Zobrazí pouze pokud se stav kontroly je dostupný)        |
|ProbesSent     | Počet sond odeslané během kontroly. Maximální hodnota je 100.        |
|ProbesFailed     | Počet testů, které selhaly během kontroly. Maximální hodnota je 100.        |
|Směrování     | Směrování podle segmentu cesty ze zdroje do cíle.        |
|Hops[].Type     | Typ prostředku. Možné hodnoty jsou **zdroj**, **VirtualAppliance**, **VnetLocal**, a **Internet**.        |
|Směrování []. ID | Jedinečný identifikátor směrování.|
|Směrování []. Adresa | IP adresa směrování.|
|Směrování []. ID prostředku | ResourceID směrování, pokud segment směrování je prostředek Azure. Pokud je zdroj v Internetu, je ResourceID **Internet**. |
|Hops[].NextHopIds | Jedinečný identifikátor provést další segment směrování.|
|Směrování []. Problémy | Kolekce problémy, ke kterým došlo během kontroly v tomto segmentu směrování. Pokud nebyly žádné problémy, hodnota je prázdné.|
|Směrování []. Problémy s []. Počátek | V aktuální segment směrování, kde došlo k problému. Možné hodnoty:<br/> **Příchozí** – problém je na odkaz z předchozího segmentu pro aktuální segment směrování<br/>**Odchozí** – problém je na odkaz z aktuálního segmentu směrování pro další směrování<br/>**Místní** – je problém aktuální směrování.|
|Směrování []. Problémy s []. Závažnost | Závažnost zjištěném problému. Možné hodnoty jsou **chyba** a **upozornění**. |
|Směrování []. Problémy s []. Typ |Typ nalezen problém. Možné hodnoty: <br/>**CPU**<br/>**Paměť**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Směrování []. Problémy s []. Kontext |Podrobnosti týkající se problému nalezen.|
|Směrování []. Problémy s []. Kontext .key] |Vrátí klíč z páru klíč-hodnota.|
|Směrování []. Problémy s []. [] .value kontextu |Vrátí hodnotu z dvojice klíč-hodnota.|

Následuje příklad problému na hop.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Typy selhání

Řešení potíží s připojením typy selhání vrátí informace o připojení. Následující tabulka obsahuje seznam aktuální typy selhání vrátila.

|Type  |Popis  |
|---------|---------|
|Procesor     | Vysoké využití procesoru.       |
|Memory (Paměť)     | Vysoké využití paměti.       |
|GuestFirewall     | Provoz blokovaný z důvodu konfigurace brány firewall virtuálního počítače.        |
|DNSResolution     | Překlad názvů DNS se nezdařilo pro cílovou adresu.        |
|NetworkSecurityRule    | Pravidlo skupiny zabezpečení sítě blokuje provoz (pravidlo je vrácena)        |
|UserDefinedRoute|Je provoz vynechaný kvůli uživatelem definované nebo systémová trasa. |

### <a name="next-steps"></a>Další postup

Informace o řešení problémů s připojením pomocí [webu Azure portal](network-watcher-connectivity-portal.md), [PowerShell](network-watcher-connectivity-powershell.md), [rozhraní příkazového řádku Azure](network-watcher-connectivity-cli.md), nebo [rozhraní REST API](network-watcher-connectivity-rest.md).