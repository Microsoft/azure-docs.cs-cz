---
title: Vlastní pravidla firewallu webových aplikací Azure (WAF) v2
description: Tento článek poskytuje přehled vlastních pravidel firewallu webových aplikací (WAF) V2 v Azure Application Gateway.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 2e96a2a2dd5504c906b5fb84b643467a83518f21
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027582"
---
# <a name="overview-custom-rules-for-web-application-firewall-v2"></a>Přehled: vlastní pravidla pro webový Application firewall v2

Firewall webových aplikací v Azure Application Gateway (WAF) v2 obsahuje předem konfigurovanou sadu pravidel spravovanou platformou, která nabízí ochranu z mnoha různých typů útoků. Mezi tyto útoky patří skriptování mezi weby, vkládání SQL a další. Pokud jste správce WAF, možná budete chtít napsat vlastní pravidla, která budou rozšiřovat pravidla základní sady pravidel. Vaše pravidla mohou buď blokovat nebo povolovat požadovaný provoz na základě kritérií porovnání.

Pomocí vlastních pravidel můžete vytvořit vlastní pravidla, která se vyhodnotí pro každý požadavek, který projde přes WAF. Tato pravidla mají vyšší prioritu než zbývající pravidla ve spravovaných sadách pravidel. Vlastní pravidla obsahují název pravidla, prioritu pravidla a pole podmínek, které se shodují. Pokud jsou splněny tyto podmínky, je provedena akce pro povolení nebo blokování.

Můžete například vytvořit pravidlo pro blokování všech požadavků z IP adresy v rozsahu 192.168.5.4/24. V tomto pravidle je operátor *IPMatch*, *MATCHVALUES* je rozsah IP adres (192.168.5.4/24) a *Akce* slouží k blokování provozu. Nastavíte také název a prioritu pravidla.

Vlastní pravidla podporují použití složené logiky k pokročilejším pravidlům, která řeší vaše požadavky na zabezpečení. Například "(podmínka 1 *a* podmínka 2) *nebo* podmínka 3)" znamená, že pokud je splněna podmínka 1 *a* podmínka 2 *nebo* Pokud je splněna podmínka 3, WAF by měla provést akci, která je zadána ve vlastním pravidle.

Různé podmínky shod v rámci stejného pravidla jsou vždy složené pomocí *a*. Například pravidlo, které používá *a* může určit pro blokování provozu z určité IP adresy a jenom v případě, že se používá určitý prohlížeč.

Pokud chcete použít *nebo* pro dvě různé podmínky, musí být tyto dvě podmínky v různých pravidlech. Například pravidlo, které používá *nebo* může určit pro blokování provozu z určité IP adresy nebo blokování provozu v případě, že se používá určitý prohlížeč.

> [!NOTE]
> Maximální počet vlastních pravidel WAF je 100. Další informace o omezeních Application Gateway najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-subscription-service-limits.md#application-gateway-limits).

Regulární výrazy jsou také podporovány ve vlastních pravidlech, stejně jako jsou v základních sadách pravidel. Příklady těchto pravidel naleznete v části "Příklad 3" a "Příklad 5" v tématu [Vytvoření a použití vlastních pravidel firewallu webových aplikací](create-custom-waf-rules.md).

> [!NOTE]
> Vlastní pravidla nejsou k dispozici v WAF SKU v1.

## <a name="allowing-or-blocking-traffic"></a>Povolení nebo blokování provozu

Povolení nebo blokování provozu je jednoduché s vlastními pravidly. Můžete například blokovat veškerý provoz, který pochází z rozsahu IP adres. Pokud požadavek pochází z určitého prohlížeče, můžete nastavit jiné pravidlo, které povolí provoz.

Chcete-li něco připustit, zajistěte, aby byl parametr `-Action` nastaven na hodnotu **Allow**. Chcete-li něco zablokovat, zajistěte, aby byl parametr `-Action` nastaven na **blokování**, jak je znázorněno v následujícím kódu:

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

Předchozí `$BlockRule` se mapuje na následující vlastní pravidlo v Azure Resource Manager:

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

Toto vlastní pravidlo obsahuje název, prioritu, akci a pole podmínek, které je potřeba splnit, aby se akce mohla uskutečnit. Popisy polí vlastního pravidla najdete v následujících oddílech. Příklady vlastních pravidel najdete v tématu [Vytvoření a použití vlastních pravidel firewallu webových aplikací](create-custom-waf-rules.md).

## <a name="custom-rule-fields"></a>Pole vlastního pravidla

### <a name="name-optional"></a>Název (volitelné)

Toto je název pravidla. Název se zobrazí v protokolech.

### <a name="priority-required"></a>Priorita (povinné)

- Priorita určuje pořadí, ve kterém jsou pravidla vyhodnocována. Čím nižší hodnota, tím výše se vyhodnocuje pravidlo. Povolený rozsah je od 1 do 100.
- Priorita musí být jedinečná mezi všemi vlastními pravidly. Pravidlo s prioritou 40 je vyhodnoceno před pravidlem s prioritou 80.

### <a name="rule-type-required"></a>Typ pravidla (povinné)

V současné době musí být typ pravidla **MatchRule**.

### <a name="match-variable-required"></a>Proměnná shody (povinné)

Proměnná shody musí být jedna z následujících:

- RemoteAddr: IP adresa nebo název hostitele připojení ke vzdálenému počítači
- RequestMethod: metoda požadavku HTTP (GET, POST, PUT, DELETE a tak dále).
- QueryString: proměnná v identifikátoru URI.
- PostArgs: argumenty, které jsou odeslány v těle příspěvku. Vlastní pravidla, která používají tuto proměnnou shody, se použijí pouze v případě, že je záhlaví Content-Type nastaveno na "application/x-www-form-urlencoded" a "multipart/form-data".
- RequestUri: identifikátor URI požadavku.
- RequestHeaders hostitele: hlavičky žádosti.
- Částmi: proměnná, která obsahuje celý text žádosti jako celek. Vlastní pravidla, která používají tuto proměnnou shody, se použijí jenom v případě, že je záhlaví Content-Type nastavené na Application/x-www-form-urlencoded. 
- RequestCookies: soubory cookie žádosti.

### <a name="selector-optional"></a>Selektor (volitelné)

Selektor popisuje pole kolekce matchVariable. Například pokud má matchVariable hodnotu "requestHeaders hostitele", selektor může být v hlavičce User-Agent.

### <a name="operator-required"></a>Operátor (povinné)

Operátor musí být jeden z následujících:

- IPMatch: Tento operátor se používá pouze v případě, že je proměnná porovnávání *RemoteAddr*.
- Equals: vstup je stejný jako MatchValue.
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- Filtr začíná na
- EndsWith
- regulární

### <a name="negate-condition-optional"></a>Podmínka negace (volitelné)

Negace aktuální podmínky.

### <a name="transform-optional"></a>Transformace (volitelné)

Seznam řetězců s názvy transformací, které mají být dokončeny před pokusem o shodu. Mezi transformace patří:

- Malá písmena
- Sklon
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Porovnat hodnoty (povinné)

Pole matchValues je seznam hodnot, na které se má shoda, což může být považovat *za "Ed*". Hodnoty mohou být například IP adresy nebo jiné řetězce. Formát hodnoty závisí na předchozím operátoru.

### <a name="action-required"></a>Akce (povinné)

Pole akce nabízí následující možnosti: 

- Povolit: autorizuje transakci a přeskočí všechna následující pravidla. To znamená, že zadaný požadavek se přidá do seznamu povolených a po jeho porovnání se požadavek zastaví a pošle se do fondu back-end. Pravidla, která jsou na seznamu povolených, se nevyhodnotí pro další vlastní pravidla ani spravovaná pravidla.

- Blok: blokuje transakci na základě *SecDefaultAction* (režim detekce nebo prevence). Podobně jako u akce povolení se po vyhodnocení žádosti a přidání do seznamu blokovaných hodnot zastaví vyhodnocení a zablokuje se požadavek. Následné požadavky, které splňují stejné podmínky, se nevyhodnotí. Zablokují se jenom. 

- Log: umožňuje do protokolu zapsat pravidlo a umožňuje, aby zbytek pravidel běžel pro vyhodnocení. Následná vlastní pravidla se vyhodnocují v pořadí podle priority, za kterými následuje spravovaná pravidla.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s vlastními pravidly, můžete [vytvořit vlastní pravidla](create-custom-waf-rules.md).
