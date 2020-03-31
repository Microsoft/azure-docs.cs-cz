---
title: Úvod k řešení potíží s připojením sledování sítě Azure | Dokumenty společnosti Microsoft
description: Tato stránka obsahuje přehled funkce řešení potíží s připojením sledování sítě
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
ms.openlocfilehash: cae3072a3468b232e95d7c1949948b71059695ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283274"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Úvod k řešení potíží s připojením v Azure Network Watcher

Funkce poradce při potížích s připojením sledovacího programu sítě umožňuje zkontrolovat přímé připojení TCP z virtuálního počítače do virtuálního počítače, plně kvalifikovaný název domény (FQDN), identifikátor URI nebo adresu IPv4. Síťové scénáře jsou složité, jsou implementovány pomocí skupin zabezpečení sítě, bran firewall, uživatelem definovaných tras a prostředků poskytovaných Azure. Složité konfigurace ztěžují řešení problémů s připojením. Sledování sítě pomáhá zkrátit dobu hledání a zjišťování problémů s připojením. Vrácené výsledky mohou poskytnout přehled o tom, zda je problém s připojením způsoben problémem s konfigurací platformy nebo uživatele. Připojení lze zkontrolovat pomocí [PowerShellu](network-watcher-connectivity-powershell.md), [rozhraní API Azure](network-watcher-connectivity-cli.md)a rozhraní REST [API](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Řešení potíží s připojením vyžaduje, `AzureNetworkWatcherExtension` aby v ym, který řešíte z má rozšíření virtuálního zařízení nainstalován. Pokud chcete nainstalovat rozšíření na virtuální počítač s Windows, navštivte [rozšíření virtuálního počítače Azure Network Watcher Agent pro Windows](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a pro virtuální počítač SIP na stránce Rozšíření [virtuálního počítače Azure Network Watcher Agent pro Linux](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Rozšíření není vyžadováno v cílovém koncovém bodě.

## <a name="response"></a>Odpověď

V následující tabulce jsou uvedeny vlastnosti vrácené po dokončení potíží s připojením.

|Vlastnost  |Popis  |
|---------|---------|
|Stav připojení     | Stav kontroly připojení. Možné výsledky jsou **dosažitelné** a **nedostupné**.        |
|AvgLatencyInMs     | Průměrná latence během kontroly připojení v milisekundách. (Zobrazí se pouze v případě, že je dostupný stav kontroly)        |
|MinLatencyInMs     | Minimální latence během kontroly připojení v milisekundách. (Zobrazí se pouze v případě, že je dostupný stav kontroly)        |
|MaxLatencyInMs     | Maximální latence během kontroly připojení v milisekundách. (Zobrazí se pouze v případě, že je dostupný stav kontroly)        |
|SondaOdesláno     | Počet sond odeslaných během kontroly. Maximální hodnota je 100.        |
|Sondy selhaly     | Počet sond, které se nezdařily během kontroly. Maximální hodnota je 100.        |
|Směrování     | Směrování podle směrování z místa původu do cíle.        |
|Chmel[]. Typ     | Typ zdroje. Možné hodnoty jsou **Source**, **VirtualAppliance**, **VnetLocal**a **Internet**.        |
|Chmel[]. Id | Jedinečný identifikátor směrování|
|Chmel[]. Adresu | IP adresa směrování.|
|Chmel[]. Resourceid | ResourceID směrování, pokud směrování je prostředek Azure. Pokud se jedná o internetový prostředek, ResourceID je **Internet**. |
|Chmel[]. Další HopIds | Jedinečný identifikátor dalšího pořízeného směrování.|
|Chmel[]. Problémy | Kolekce problémů, které byly zjištěny při kontrole na tomto směrování. Pokud nebyly žádné problémy, hodnota je prázdná.|
|Chmel[]. Problémy[]. Původu | Při aktuálním směrování, kde došlo k problému. Možné hodnoty:<br/> **Příchozí** - problém je na propojení z předchozího směrování na aktuální směrování<br/>**Odchozí** - Problém je na propojení z aktuálního směrování na další směrování<br/>**Místní** - Problém je na aktuální směrování.|
|Chmel[]. Problémy[]. Závažnosti | Závažnost zjištěného problému. Možné hodnoty jsou **Chyba** a **Upozornění**. |
|Chmel[]. Problémy[]. Typ |Byl nalezen typ nalezeného problému. Možné hodnoty: <br/>**Cpu**<br/>**Paměti**<br/>**Brána firewall pro hostování**<br/>**DnsResolution**<br/>**Pravidlo zabezpečení sítě**<br/>**UserDefinedRoute** |
|Chmel[]. Problémy[]. Kontextu |Podrobnosti týkající se nalezeného problému.|
|Chmel[]. Problémy[]. Kontext[].klíč |Klíč dvojice hodnot klíče vrácena.|
|Chmel[]. Problémy[]. Hodnota kontextu[]. |Vrácená hodnota páru hodnot klíče.|

Následuje příklad problému nalezeného v směrování.

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
## <a name="fault-types"></a>Typy poruch

Řešení potíží s připojením vrátí typy chyb o připojení. V následující tabulce je uveden seznam aktuálních typů chyb, které byly vráceny.

|Typ  |Popis  |
|---------|---------|
|Procesor     | Vysoké využití procesoru.       |
|Memory (Paměť)     | Vysoké využití paměti.       |
|Brána firewall pro hostování     | Provoz je blokován z důvodu konfigurace brány firewall virtuálního počítače.        |
|Rozlišení DNS     | Překlad DNS se nezdařil pro cílovou adresu.        |
|Pravidlo zabezpečení sítě    | Provoz je blokován pravidlem sítě sítě (pravidlo je vráceno)        |
|UserDefinedRoute|Provoz je zrušen z důvodu uživatelem definované nebo systémové trasy. |

### <a name="next-steps"></a>Další kroky

Zjistěte, jak řešit potíže s připojeními pomocí [portálu Azure](network-watcher-connectivity-portal.md), [PowerShellu](network-watcher-connectivity-powershell.md), [rozhraní API Azure](network-watcher-connectivity-cli.md)nebo rozhraní REST [API](network-watcher-connectivity-rest.md).