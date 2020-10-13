---
title: Seznamy vyloučení brány firewall webových aplikací v frontě Azure – Azure Portal
description: Tento článek obsahuje informace o konfiguraci seznamů vyloučení v Azure předem s Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77925927"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Firewall webových aplikací (WAF) se seznamy vyloučení služby front-dveří 

Někdy může firewall webových aplikací (WAF) blokovat požadavek, který chcete pro vaši aplikaci použít. Například služba Active Directory vloží tokeny, které se používají pro ověřování. Tyto tokeny mohou obsahovat speciální znaky, které mohou aktivovat falešně pozitivní hodnoty z pravidel WAF. Seznamy vyloučení WAF umožňují vynechat určité atributy žádostí z vyhodnocení WAF.  Seznam vyloučení se dá nakonfigurovat pomocí  [PowserShell](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0), [Azure CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), [REST API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)nebo Azure Portal. Následující příklad ukazuje konfiguraci Azure Portal. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Konfigurace seznamů vyloučení pomocí Azure Portal
**Správa vyloučení** je přístupná z portálu WAF v části **spravovaná pravidla**

![Správa vyloučení ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ exclusion_add správy](../media/waf-front-door-exclusion/exclusion2.png)

 Příklad seznamu vyloučení: ![ správa exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

Tento příklad vylučuje hodnotu v poli záhlaví *uživatele* . Platný požadavek může obsahovat pole *uživatele* obsahující řetězec, který spouští pravidlo pro vložení SQL. V tomto případě můžete v tomto případě vyloučit parametr *uživatele* tak, aby pravidlo WAF nevyhodnotilo cokoli v poli.

Následující atributy lze přidat do seznamů vyloučení podle názvu. Hodnoty polí, která použijete, nejsou vyhodnocovány proti pravidlům WAF, ale jejich názvy jsou vyhodnocovány. Seznamy vyloučení odstraňují kontrolu hodnoty pole.

* Název hlavičky žádosti
* Název souboru cookie žádosti
* Název argumentů řetězce dotazu
* Název žádosti post argumenty pro tělo

Můžete zadat přesně takovou hlavičku požadavku, tělo, soubor cookie nebo atribut řetězce dotazu.  Případně můžete volitelně zadat částečné shody. Následující operátory jsou podporovaná kritéria shody:

- **Equals**: Tento operátor se používá pro přesnou shodu. Chcete-li například vybrat záhlaví s názvem **bearerToken**, použijte operátor Equals se sadou selektoru jako **bearerToken**.
- **Začíná**na: Tento operátor odpovídá všem polím, která začínají zadanou hodnotou selektoru.
- **Končí**na: Tento operátor odpovídá všem polím žádosti, která končí zadanou hodnotou selektoru.
- **Obsahuje**: Tento operátor odpovídá všem polím požadavku, která obsahují zadanou hodnotu selektoru.
- **Equals**: Tento operátor odpovídá všem polím žádosti. * je hodnota selektoru.

V názvech hlaviček a souborů cookie se nerozlišují malá a velká písmena.

Seznam vyloučení můžete použít pro všechna pravidla v rámci spravované sady pravidel, na pravidla pro konkrétní skupinu pravidel nebo na jedno pravidlo, jak je znázorněno v předchozím příkladu. 

## <a name="next-steps"></a>Další kroky

Po konfiguraci nastavení WAF se dozvíte, jak zobrazit protokoly WAF. Další informace najdete v tématu [Diagnostika front-dveří](../afds/waf-front-door-monitor.md).
