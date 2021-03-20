---
title: Seznámení s řešením potíží s připojením k Azure Network Watcher | Microsoft Docs
description: Tato stránka poskytuje přehled funkce řešení potíží s připojením Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: 9c855fff9e5791b9c0cf870acfc6de53e7a700b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97653992"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Úvod k řešení potíží s připojením v Azure Network Watcher

Funkce řešení potíží s připojením Network Watcher poskytuje možnost kontrolovat přímé připojení TCP z virtuálního počítače k virtuálnímu počítači (VM), plně kvalifikovanému názvu domény (FQDN), identifikátoru URI nebo adrese IPv4. Scénáře sítě jsou složité, jsou implementované pomocí skupin zabezpečení sítě, bran firewall, tras definovaných uživatelem a prostředků poskytovaných Azure. Složité konfigurace usnadňují řešení potíží s připojením náročné. Network Watcher pomáhá zkrátit dobu, po kterou můžete najít a zjistit problémy s připojením. Vrácené výsledky mohou poskytnout přehled o tom, zda je problém s připojením způsoben platformou nebo problémem s konfigurací uživatele. Připojení se dá zkontrolovat pomocí [PowerShellu](network-watcher-connectivity-powershell.md), rozhraní příkazového [řádku Azure](network-watcher-connectivity-cli.md)a [REST API](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Řešení potíží s připojením vyžaduje, aby virtuální počítač, ze kterého řešení řešíte, byl `AzureNetworkWatcherExtension` nainstalován rozšíření virtuálního počítače. Pokud chcete nainstalovat rozšíření na virtuální počítač s Windows, přejděte na web [azure Network Watcher Agent Virtual Machine Extension for Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a pro Linux VM, navštivte [rozšíření Azure Network Watcher Agent Virtual Machine pro Linux](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). V cílovém koncovém bodě není rozšíření vyžadováno.

## <a name="response"></a>Odpověď

V následující tabulce jsou uvedeny vlastnosti vrácené při dokončení řešení potíží s připojením.

|Vlastnost  |Popis  |
|---------|---------|
|ConnectionStatus     | Stav kontroly připojení. Možné výsledky jsou **dosažitelné** a **nedosažitelné**.        |
|AvgLatencyInMs     | Průměrná latence během kontroly připojení v milisekundách (Zobrazí se pouze v případě, že je stav kontroly dosažitelný)        |
|MinLatencyInMs     | Minimální latence během kontroly připojení v milisekundách (Zobrazí se pouze v případě, že je stav kontroly dosažitelný)        |
|MaxLatencyInMs     | Maximální latence během kontroly připojení v milisekundách (Zobrazí se pouze v případě, že je stav kontroly dosažitelný)        |
|ProbesSent     | Počet sond odeslaných během kontroly. Maximální hodnota je 100.        |
|ProbesFailed     | Počet sond, které během kontroly selhaly. Maximální hodnota je 100.        |
|Směrování     | Směrování podle cesty směrování ze zdroje do cíle.        |
|Směrování []. Textový     | Typ prostředku Možné hodnoty jsou **source**, **VirtualAppliance**, **VnetLocal** a **Internet**.        |
|Směrování []. Účet | Jedinečný identifikátor směrování|
|Směrování []. Adresáře | IP adresa směrování.|
|Směrování []. Prostředku | ResourceID prostředku směrování, pokud je segmentem směrování prostředek Azure. Pokud se jedná o internetový prostředek, ResourceID je **Internet**. |
|Směrování []. NextHopIds | Jedinečný identifikátor pořízeného dalšího segmentu směrování.|
|Směrování []. Chyba | Kolekce problémů, které byly zjištěny během kontroly tohoto směrování. Pokud nebyly zjištěny žádné problémy, hodnota je prázdná.|
|Směrování []. Problémy []. Zdroji | U aktuálního směrování, kde došlo k problému. Možné hodnoty:<br/> **Příchozí** – problém je na odkazu z předchozího směrování na aktuální segment směrování.<br/>**Odchozí** – problém je na propojení od aktuálního směrování k dalšímu segmentu směrování.<br/>**Místní** problém je na aktuálním směrování.|
|Směrování []. Problémy []. Závažnost | Závažnost problému byla zjištěna. Možné hodnoty jsou **Error** a **Warning**. |
|Směrování []. Problémy []. Textový |Typ problému byl nalezen. Možné hodnoty: <br/>**Procesor**<br/>**Memory (Paměť)**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Směrování []. Problémy []. Souvislost |Podrobnosti o zjištěném problému.|
|Směrování []. Problémy []. Context []. klíč |Klíč vrácené dvojice klíč-hodnota|
|Směrování []. Problémy []. Context []. hodnota |Hodnota dvojice klíč hodnota, která se vrátila|

Následuje příklad problému nalezeného na směrování.

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
## <a name="fault-types"></a>Typy chyb

Řešení potíží s připojením vrátí typy chyb připojení. Následující tabulka obsahuje seznam aktuálních vrácených typů selhání.

|Typ  |Popis  |
|---------|---------|
|Procesor     | Vysoké využití procesoru.       |
|Memory (Paměť)     | Vysoké využití paměti.       |
|GuestFirewall     | Provoz je zablokovaný kvůli konfiguraci brány firewall virtuálního počítače.        |
|DNSResolution     | Překlad DNS pro cílovou adresu se nezdařil.        |
|NetworkSecurityRule    | Provoz je blokován pravidlem NSG (pravidlo se vrátí).        |
|UserDefinedRoute|Provoz se zahodil z důvodu uživatelsky definované nebo systémové trasy. |

### <a name="next-steps"></a>Další kroky

Naučte se řešit potíže s připojením pomocí [Azure Portal](network-watcher-connectivity-portal.md), [PowerShellu](network-watcher-connectivity-powershell.md), rozhraní příkazového [řádku Azure](network-watcher-connectivity-cli.md)nebo [REST API](network-watcher-connectivity-rest.md).