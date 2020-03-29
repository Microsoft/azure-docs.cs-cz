---
title: Seznamy vyloučení brány firewall webových aplikací v Azure Front Door – portál Azure
description: Tento článek obsahuje informace o konfiguraci seznamů vyloučení v Azure Front s portálem Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925927"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Brána firewall webových aplikací (WAF) se seznamy vyloučení služby front door 

V některých skutečnostech může brána firewall webových aplikací (WAF) blokovat požadavek, který chcete povolit pro vaši aplikaci. Služba Active Directory například vloží tokeny, které se používají k ověřování. Tyto tokeny mohou obsahovat speciální znaky, které mohou vyvolat falešně pozitivní z pravidel WAF. Seznamy vyloučení WAF umožňují vynechat určité atributy požadavku z vyhodnocení WAF.  Seznam vyloučení lze nakonfigurovat pomocí [PowserShell](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0), [Azure CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), [Rest API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)nebo portálu Azure. Následující příklad ukazuje konfiguraci portálu Azure. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Konfigurace seznamů vyloučení pomocí portálu Azure
**Správa vyloučení** je přístupná z portálu WAF v části **Spravovaná pravidla**

![Správa](../media/waf-front-door-exclusion/exclusion1.png)
![vyloučení Správa exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 Příklad vyloučení: ![Správa exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

Tento příklad vylučuje hodnotu v poli hlavičky *uživatele.* Platný požadavek může obsahovat *uživatelské* pole, které obsahuje řetězec, který aktivuje pravidlo vkládání SQL. V tomto případě můžete vyloučit *parametr uživatele,* aby pravidlo WAF nevyhodnotilo nic v poli.

Následující atributy lze přidat do seznamů vyloučení podle názvu. Hodnoty polí, která používáte, nejsou vyhodnoceny podle pravidel WAF, ale jejich názvy jsou vyhodnoceny. Seznamy vyloučení odeberou kontrolu hodnoty pole.

* Název hlavičky požadavku
* Žádost o název souboru cookie
* Název řetězce dotazu args
* Požadavek tělo post args jméno

Můžete zadat přesnou shodu záhlaví, těla, souboru cookie nebo řetězce dotazu.  Nebo můžete volitelně zadat částečné shody. Podporovaná kritéria shody jsou následující operátory:

- **Rovná se**: Tento operátor se používá pro přesnou shodu. Chcete-li například vybrat hlavičku s názvem **bearerToken**, použijte operátor rovná se selektoru nastaveným jako **bearerToken**.
- **Začíná na**: Tento operátor odpovídá všem polím, která začínají zadanou hodnotou voliče.
- **Končí :** Tento operátor porovnává všechna pole požadavku, která končí se zadanou hodnotou voliče.
- **Obsahuje**: Tento operátor odpovídá všem polím požadavku, která obsahují zadanou hodnotu voliče.
- **Rovná se všem**: Tento operátor odpovídá všem polím požadavku. * je hodnota voliče.

Názvy záhlaví a souborů cookie nerozlišují malá a velká písmena.

Seznam vyloučení můžete použít na všechna pravidla v rámci sady spravovaných pravidel, na pravidla pro určitou skupinu pravidel nebo na jedno pravidlo, jak je znázorněno v předchozím příkladu. 

## <a name="next-steps"></a>Další kroky

Po konfiguraci nastavení WAF se dozvíte, jak zobrazit protokoly WAF. Další informace naleznete [v tématu Diagnostika předních dveří](../afds/waf-front-door-monitor.md).
