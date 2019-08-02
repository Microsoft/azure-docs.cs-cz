---
title: Vlastní pravidla firewallu webových aplikací Azure (WAF) v2
description: Tento článek poskytuje přehled vlastních pravidel firewallu webových aplikací (WAF) V2 v Azure Application Gateway.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 9c04f805cf410d2306eda76c84a201a67b022b84
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716621"
---
# <a name="custom-rules-for-web-application-firewall-v2"></a>Vlastní pravidla pro webový Application firewall v2

Firewall webových aplikací pro Azure Application Gateway (WAF) v2 přináší předem nakonfigurovanou RuleSet spravovanou platformou, která nabízí ochranu před mnoha různými typy útoků. Mezi tyto útoky patří skriptování mezi weby, vkládání SQL a další. Pokud jste správce WAF, možná budete chtít napsat vlastní pravidla pro rozšíření pravidel základní sady pravidel (počítačový systém). Vaše pravidla mohou buď blokovat nebo povolovat požadovaný provoz na základě kritérií porovnání.

Vlastní pravidla umožňují vytvořit vlastní pravidla vyhodnocená pro každý požadavek, který projde WAF. Tato pravidla mají vyšší prioritu než zbývající pravidla ve spravovaných sadách pravidel. Vlastní pravidla obsahují název pravidla, prioritu pravidla a pole podmínek, které se shodují. Pokud jsou splněny tyto podmínky, je provedena akce (pro povolení nebo blokování).

Můžete například blokovat všechny požadavky z IP adresy v rozsahu 192.168.5.4/24. V tomto pravidle je operátor *IPMatch*, matchValues je rozsah IP adres (192.168.5.4/24) a akce slouží k blokování provozu. Nastavíte také název a prioritu pravidla.

Vlastní pravidla podporují použití složené logiky k pokročilejším pravidlům, která řeší vaše požadavky na zabezpečení. Například (podmínka 1 **a** podmínka 2) **nebo** podmínka 3).  Tento příklad znamená, že pokud je splněna podmínka 1 **a** podmínka 2 **nebo** Pokud je splněna podmínka 3, WAF by měla provést akci určenou ve vlastním pravidle.

Různé podmínky shod v rámci stejného pravidla jsou vždy složené pomocí **a**. Například zablokujte provoz z konkrétní IP adresy a jenom v případě, že používá určitý prohlížeč.

Pokud chcete **nebo** dvě různé podmínky, musí být tyto dvě podmínky v různých pravidlech. Můžete například blokovat provoz z konkrétní IP adresy nebo blokovat provoz, pokud používají konkrétní prohlížeč.

> [!NOTE]
> Maximální počet vlastních pravidel WAF je 100. Další informace o omezeních Application Gateway najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-subscription-service-limits.md#application-gateway-limits).

Regulární výrazy jsou také podporovány ve vlastních pravidlech, stejně jako v RuleSets počítačový počítač. Příklady těchto příkazů najdete v části Příklady 3 a 5 v tématu [Vytvoření a použití vlastních pravidel firewallu webových aplikací](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Povolení vs. blokování

Povolení a blokování provozu je jednoduché s vlastními pravidly. Můžete například blokovat veškerý provoz přicházející z rozsahu IP adres. Pokud požadavek pochází z konkrétního prohlížeče, můžete nastavit jiné pravidlo, které povolí provoz.

Pokud chcete něco dovolit, zajistěte `-Action` , aby byl parametr nastavený na hodnotu **povoleno**. Chcete-li něco zablokovat `-Action` , zajistěte, aby byl parametr nastaven na hodnotu **blokovat**.

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Předchozí `$BlockRule` mapování na následující vlastní pravidlo v Azure Resource Manager:

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

Toto vlastní pravidlo obsahuje název, prioritu, akci a pole podmínek porovnání, které musí být splněny, aby akce mohla probíhat. Další vysvětlení těchto polí naleznete v následujících popisech polí. Například vlastní pravidla najdete v tématu [Vytvoření a použití vlastních pravidel firewallu webových aplikací](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Pole pro vlastní pravidla

### <a name="name-optional"></a>Název [nepovinné]

Toto je název pravidla. Tento název se zobrazí v protokolech.

### <a name="priority-required"></a>Priorita [povinné]

- Určuje pořadí, ve kterém jsou pravidla vyhodnocována. Čím nižší hodnota, tím výše se vyhodnocuje pravidlo.
-Musí být jedinečné mezi všemi vlastními pravidly. Pravidlo s prioritou 100 bude vyhodnoceno dříve než pravidlo s prioritou 200.

### <a name="rule-type-required"></a>Typ pravidla [povinné]

V současné době musí být **MatchRule**.

### <a name="match-variable-required"></a>Match – proměnná [povinné]

Musí se jednat o jednu z proměnných:

- RemoteAddr – IP adresa/název hostitele připojení ke vzdálenému počítači
- RequestMethod – metoda požadavku HTTP (GET, POST, PUT, DELETE atd.)
- QueryString – proměnná v identifikátoru URI
- PostArgs – argumenty odeslané v těle příspěvku. Vlastní pravidla, která používají tuto proměnnou shody, se aplikují jenom v případě, že je záhlaví Content-Type nastavené na Application/x-www-form-urlencoded a multipart/form-data.
- RequestUri – identifikátor URI žádosti
- RequestHeaders hostitele – hlavičky žádosti
- Částmi – obsahuje celé tělo žádosti jako celek. Vlastní pravidla, která používají tuto proměnnou shody, se aplikují jenom v případě, že je záhlaví Content-Type nastavené na Application/x-www-form-urlencoded. 
- RequestCookies – soubory cookie žádosti

### <a name="selector-optional"></a>Selektor [nepovinné]

Popisuje pole kolekce matchVariable. Například pokud je matchVariable requestHeaders hostitele, selektor může být v hlavičce *User-Agent* .

### <a name="operator-required"></a>Operator [povinné]

Musí být jedním z následujících operátorů:

- IPMatch – používá se pouze v případě, že proměnná Match je *RemoteAddr* .
- Equals – vstup je stejný jako MatchValue
- Obsahuje
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- Filtr začíná na
- EndsWith
- Regulární

### <a name="negate-condition-optional"></a>Podmínka negace [nepovinné]

Negace aktuální podmínky.

### <a name="transform-optional"></a>Transformace [nepovinné]

Seznam řetězců s názvy transformací, které se mají provést před pokusem o shodu. Můžou to být následující transformace:

- Malá
- Oříznout
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Hodnoty shody [povinné]

Seznam hodnot, které mají být porovnány, což může být považováno za "je" *nebo*"Ed". Může to být například IP adresa nebo jiné řetězce. Formát hodnoty závisí na předchozím operátoru.

### <a name="action-required"></a>Action [povinné]

- Allow – autorizuje transakci a přeskočí všechna další pravidla. To znamená, že zadaný požadavek se přidá do seznamu povolených a po shodě se požadavek zastaví a pošle se do fondu back-end. Pravidla, která jsou na seznamu povolených, se nevyhodnotí pro žádná další vlastní pravidla ani spravovaná pravidla.
- Block – zablokuje transakci na základě *SecDefaultAction* (režim detekce nebo prevence). Stejně jako u akce Allow se po vyhodnocení žádosti a přidání do seznamu blokovaných hodnot zastaví a zablokuje se požadavek. Všechny žádosti, které splňují stejné podmínky, se nevyhodnotí a budou jenom blokované. 
- Log – umožňuje zapsání pravidla do protokolu, ale umožňuje, aby zbytek pravidel běžel pro vyhodnocení. Následná vlastní pravidla se vyhodnocují v pořadí podle priority, za kterými následuje spravovaná pravidla.

## <a name="next-steps"></a>Další postup

Jakmile se seznámíte s vlastními pravidly, [vytvořte vlastní pravidla](create-custom-waf-rules.md).
