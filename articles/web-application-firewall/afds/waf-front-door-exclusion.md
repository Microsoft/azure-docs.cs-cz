---
title: Seznamy vyloučení brány firewall webových aplikací v frontě Azure – Azure Portal
description: Tento článek obsahuje informace o konfiguraci seznamů vyloučení v Azure předem s Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 1b94a3d8675461779fa9d543bf0153b165ab4fb4
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217580"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Firewall webových aplikací (WAF) se seznamy vyloučení služby front-dveří 

Někdy může firewall webových aplikací (WAF) blokovat požadavek, který chcete pro vaši aplikaci použít. Například služba Active Directory vloží tokeny, které se používají pro ověřování. Tyto tokeny mohou obsahovat speciální znaky, které mohou aktivovat falešně pozitivní hodnoty z pravidel WAF. Seznamy vyloučení WAF umožňují vynechat určité atributy žádostí z vyhodnocení WAF.  Seznam vyloučení se dá nakonfigurovat pomocí  [PowerShellu](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0), rozhraní příkazového [řádku Azure CLI](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), [rozhraní REST API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)nebo Azure Portal. Následující příklad ukazuje konfiguraci Azure Portal. 
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
- **Začíná** na: Tento operátor odpovídá všem polím, která začínají zadanou hodnotou selektoru.
- **Končí** na: Tento operátor odpovídá všem polím žádosti, která končí zadanou hodnotou selektoru.
- **Obsahuje**: Tento operátor odpovídá všem polím požadavku, která obsahují zadanou hodnotu selektoru.
- **Equals**: Tento operátor odpovídá všem polím žádosti. * je hodnota selektoru.

V názvech hlaviček a souborů cookie se nerozlišují malá a velká písmena.

Pokud hodnota záhlaví, hodnota souboru cookie, hodnota post argumentu nebo hodnota argumentu dotazu vytvoří pro některá pravidla falešně pozitivní hodnoty, můžete tuto část žádosti vyloučit z pravidla:


|matchVariableName z protokolů WAF  |Vyloučení pravidla na portálu  |
|---------|---------|
|CookieValue: SOME_NAME        |Název souboru cookie žádosti se rovná SOME_NAME|
|HeaderValue: SOME_NAME        |Název záhlaví žádosti se rovná SOME_NAME|
|PostParamValue: SOME_NAME     |Text žádosti post argumenty s názvem se rovná SOME_NAME|
|QueryParamValue: SOME_NAME    |Argumenty řetězce dotazu s názvem se rovná SOME_NAME|


V protokolech WAF aktuálně podporujeme jenom vyloučení pravidel pro výše uvedené matchVariableNames. Pro všechny ostatní matchVariableNames musíte buď zakázat pravidla, která poskytují falešně pozitivní výsledky, nebo vytvořit vlastní pravidlo, které tyto požadavky explicitně povoluje. Konkrétně, pokud je matchVariableName název souboru cookie, záhlaví, PostParamName nebo QueryParamName, znamená to, že toto pravidlo aktivuje samotné jméno. Vyloučení pravidla nemá v tuto chvíli žádnou podporu pro tyto matchVariableNames.


Pokud vyloučíte argument post požadavku s názvem *foo*, žádné pravidlo by v protokolech WAF obsahovat POSTPARAMVALUE: foo jako matchVariableName. Stále však můžete vidět pravidlo s matchVariableName InitialBodyContents, které odpovídá hodnotě parametru post FOO, protože hodnoty parametrů post jsou součástí InitialBodyContents.

Seznam vyloučení můžete použít pro všechna pravidla v rámci spravované sady pravidel, na pravidla pro konkrétní skupinu pravidel nebo na jedno pravidlo, jak je znázorněno v předchozím příkladu.

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>Definování vyloučení na základě protokolů brány firewall webových aplikací
 [Monitorování a protokolování brány firewall webových aplikací Azure](waf-front-door-monitor.md) zobrazuje odpovídající podrobnosti o blokované žádosti. Pokud hodnota záhlaví, hodnota souboru cookie, hodnota post argumentu nebo hodnota argumentu dotazu vytvoří pro některá pravidla falešně pozitivní hodnotu, můžete tuto část požadavku vyloučit z pravidla. V následující tabulce jsou uvedeny ukázkové hodnoty z protokolů WAF a příslušné podmínky vyloučení.

|matchVariableName z protokolů WAF    |Vyloučení pravidla na portálu|
|--------|------|
|CookieValue: SOME_NAME  |Název souboru cookie žádosti se rovná SOME_NAME|
|HeaderValue: SOME_NAME  |Název záhlaví žádosti se rovná SOME_NAME|
|PostParamValue: SOME_NAME|  Text žádosti post argumenty s názvem se rovná SOME_NAME|
|QueryParamValue: SOME_NAME| Argumenty řetězce dotazu s názvem se rovná SOME_NAME|


## <a name="next-steps"></a>Další kroky

Po konfiguraci nastavení WAF se dozvíte, jak zobrazit protokoly WAF. Další informace najdete v tématu [Diagnostika front-dveří](../afds/waf-front-door-monitor.md).