---
title: Analýzy hrozeb v Azure brány Firewall na základě filtrování
description: Seznamte se s bránou Firewall služby Azure threat intelligence filtrování
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 3/2/2019
ms.author: victorh
ms.openlocfilehash: 38b60536a05fc726ddcd06b631078c0a5f3d89ce
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248836"
---
# <a name="azure-firewall-threat-intelligence-based-filtering---public-preview"></a>Brána Firewall před internetovými útoky Azure na základě logiky filtrování – ve verzi Public Preview

Filtrování podle intelligence hrozeb je možné povolit pro bránu firewall, aby upozornění a zakazují provoz z/do známé škodlivé IP adresy a domény. IP adresy a domény pocházejí z analýzy hrozeb Microsoft informačního kanálu. [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) využívá Microsoft analýzu hrozeb a používá víc služeb, včetně Azure Security Center.

![Analýza hrozeb brány firewall](media/threat-intel/firewall-threat.png)

> [!IMPORTANT]
> Threat intelligence na základě filtrování je aktuálně ve verzi public preview a je k dispozici ve verzi preview smlouvu o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti.  Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>Threat intelligence filtrování je k dispozici ve všech veřejných oblastech. Uživatelské rozhraní přidružené konfigurace portálu je přidáte do oblastí postupně a bude k dispozici ve všech oblastech v blízké budoucnosti.

Pokud je povolené filtrování podle intelligence před internetovými útoky, přidružená pravidla se zpracovávají před jakoukoli pravidel NAT, pravidla nebo pravidla aplikací. Během období preview jsou zahrnuty pouze nejvyšší spolehlivosti záznamy.

Můžete se jenom přihlásit výstrahu, když se aktivuje pravidlo, nebo můžete vyberte výstrahu a zakázat režim.

Ve výchozím nastavení je filtrování threat intelligence podle povolené v režimu výstrah. Nejde tuto funkci vypnout nebo změňte režim, dokud nebude ve vaší oblasti k dispozici rozhraní portálu.

![Analýza hrozeb na základě filtrování rozhraní portálu](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Logs

Následující úryvek protokolu ukazuje aktivovaných pravidlo:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testování

- **Odchozí testování** -upozornění na přerušené přenosy odchozích by měl být výjimečné události, protože to znamená, že došlo k napadení o vašem prostředí. Abychom odchozí výstrahy test práci, test, který se vytvořil plně kvalifikovaný název domény, který aktivuje výstrahu. Použití **testmaliciousdomain.eastus.cloudapp.azure.com** pro odchozí testy.

- **Příchozí testování** – můžete chtít zobrazit výstrahy pro příchozí provoz, pokud jsou nakonfigurovaná pravidla DNAT v bráně firewall. To platí i v případě, že pro pravidla DNAT jsou povolené jenom konkrétní zdroje a provoz jinak byl odepřen. Brány Firewall Azure nebude upozorňovat na všechny skenery portů; pouze na skenery, které jsou známé také zapojit do škodlivých aktivit.

## <a name="next-steps"></a>Další postup

- Zobrazit [ukázek Azure bránu Firewall Log Analytics](log-analytics-samples.md)
- Zjistěte, jak [nasazení a konfiguraci brány Firewall Azure](tutorial-firewall-deploy-portal.md)
- Zkontrolujte [Microsoft Security intelligence report](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)