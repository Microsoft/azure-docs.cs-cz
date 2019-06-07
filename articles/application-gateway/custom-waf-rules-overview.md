---
title: Vlastní pravidla brány Firewall webových aplikací (WAF) Azure
description: Tento článek obsahuje přehled firewallu webových aplikací (WAF) vlastní pravidla ve službě Azure Application Gateway.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: d35e1dc96f65e51ab14c4962d1824334cbdb1616
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752026"
---
# <a name="custom-rules-for-web-application-firewall"></a>Vlastní pravidla pro Firewall webových aplikací

Firewall webových aplikací (WAF) Azure Application Gateway se dodává s předem nakonfigurované, spravované platformy sady pravidel, který nabízí ochranu z mnoha různých typů útoků. Tyto útoky patří mezi skriptování, útok prostřednictvím injektáže SQL a další. Pokud jste správce WAF, můžete k zapsání vlastní pravidla, která rozšiřují základní pravidla nastavení pravidel (CRS). Pravidla můžete blokovat nebo povolit požadovaný provoz na základě kritérií porovnání.

Vlastní pravidla umožňují vytvářet vlastní pravidla, která se vyhodnotí pro každý požadavek, který prochází WAF. Tato pravidla uchování vyšší prioritu než zbytek pravidla v sadách pravidel spravovaná. Vlastní pravidla obsahují název pravidla, Priorita pravidla a podmínky odpovídající pole. Pokud jsou tyto podmínky splněny, akci (na povoleného i blokovaného).

Například můžete blokovat všechny požadavky z IP adresy v rozsahu 192.168.5.4/24. V tomto pravidle operátor je *IPMatch*matchValues je rozsah IP adres (192.168.5.4/24) a akce se zamítne. Můžete také nastavit prioritu a název pravidla.

Vlastní pravidla podporovat použití logiky úročených provádět více rozšířených pravidel tuto adresu, které vašim požadavkům na zabezpečení. For example (1 podmínku **a** podmínka 2) **nebo** podmínka 3).  V tomto příkladu znamená, že pokud podmínka 1 **a** 2 podmínky jsou splněny, **nebo** Pokud je splněna podmínka 3, WAF zabere akce určená ve vlastní pravidlo.

Různé odpovídající podmínky v rámci stejného pravidla jsou vždy compounded pomocí **a**. Například blokovat provoz z konkrétní IP adresu, a pouze v případě, že používají některé prohlížeče.

Pokud chcete **nebo** dvě různé podmínky, dvě podmínky musí být v různých pravidel. Například blokovat provoz z konkrétní IP adresa nebo blok přenosem, pokud používá určitého webového prohlížeče.

> [!NOTE]
> Maximální počet vlastní pravidla firewallu webových aplikací je 100. Další informace o omezeních Application Gateway najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md#application-gateway-limits).

Regulární výrazy jsou podporovány také v vlastní pravidla, stejně jako v sady pravidel CRS. Příklady z nich najdete v tématu příklady 3 a 5 v [vytvoření a použití pravidel brány firewall na vlastních webových aplikací](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Povolení a blokování

Povolení a blokování provozu je jednoduchý s vlastní pravidla. Například můžete zablokovat veškerý provoz pocházející z rozsahu IP adres. Můžete vytvořit další pravidlo pro povolení provozu, pokud požadavek pochází z určitého webového prohlížeče.

Pokud chcete povolit něco, ujistěte se, že `-Action` parametr je nastaven na **povolit**. Pokud chcete zablokovat možnost něco, ujistěte se, že `-Action` parametr je nastaven na **bloku**.

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

Předchozí `$BlockRule` mapuje na vlastní pravidlo, v Azure Resource Manageru:

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

Tohoto vlastního pravidla obsahuje název, priority, akci a pole odpovídající podmínky, které musí být splněny pro akci k provedení. Další vysvětlení těchto polí najdete v následujících popisech pole. Například vlastní pravidla, najdete v článku [vytvoření a použití pravidel brány firewall na vlastních webových aplikací](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Pole pro vlastní pravidla

### <a name="name-optional"></a>[Volitelné] název

Toto je název pravidla. Tento název se zobrazí v protokolech.

### <a name="priority-required"></a>Priorita [povinné]

- Určuje pořadí, ve kterém jsou pravidla vyhodnocována v. Čím nižší hodnota, na dřívější vyhodnocení pravidla.
– Musí být jedinečný mezi všechna vlastní pravidla. Pravidlo s prioritou 100 se vyhodnotí před pravidlem s prioritou 200.

### <a name="rule-type-required"></a>Typ pravidla [povinné]

V současné době musí být **MatchRule**.

### <a name="match-variable-required"></a>Proměnné shody [povinné]

Musí být jedna z proměnných:

- RemoteAddr – IP adresa nebo název hostitele připojení vzdáleného počítače
- RequestMethod – metoda požadavku HTTP (GET, POST, PUT, DELETE a atd.)
- Řetězec dotazu – proměnné v identifikátoru URI
- PostArgs – argumenty poslala v těle příspěvku
- RequestUri – identifikátor URI žádosti
- RequestHeaders – hlavičky požadavku
- Includesearchresults: true – text žádosti
- RequestCookies – soubory cookie požadavku

### <a name="selector-optional"></a>Selektor [volitelný]

Popisuje pole matchVariable kolekce. Například pokud matchVariable RequestHeaders, modulu pro výběr může být na *User-Agent* záhlaví.

### <a name="operator-required"></a>[Povinné] – operátor

Musí být jedna z následujících operátorů:

- IPMatch - používá pouze v případě shody proměnné je *RemoteAddr*
- Equals – vstup je stejné jako MatchValue
- Obsahuje
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- Začíná na
- endsWith
- Regex

### <a name="negate-condition-optional"></a>Negate – podmínku [volitelný]

Neguje aktuální stav.

### <a name="transform-optional"></a>Transformace [volitelný]

Dojde k pokusu o seznam řetězců s názvy transformací provedete před shodou. Může jít o následující transformace:

- Malá
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Porovnání hodnot [povinné]

Seznam hodnot k porovnání, které si lze představit jako *nebo*"ed. Například může být IP adresy nebo jiných řetězců. Formát hodnoty závisí na předchozím operátor.

### <a name="action-required"></a>Akce [povinné]

- Povolit – autorizuje transakce, přeskakuje se všechny další pravidla. To znamená, že zadaný požadavek se přidá do seznamu povolených a po shoda, vyhodnocení další žádost o zastavení a jsou odeslána do back-endový fond. Pravidla, která jsou na seznamu povolených položek se nevyhodnocují pro všechny další vlastní pravidla nebo spravovaný.
- Blokovat – brání transakce v závislosti na *SecDefaultAction* (režim detekce a ochrany před únikem informací). Stejně jako akce Povolit Jakmile požadavek je vyhodnocen a přidat do seznamu zakázaných položek, vyhodnocování se zastaví a požadavek je blokován. Jakoukoli žádost po stejných podmínek, která splňuje nebude se vyhodnocovat a právě se zablokuje. 
- Log – umožňuje pravidlo zaznamená do protokolu, ale umožňuje zbývající pravidla spuštění pro vyhodnocení. Další vlastní pravidla se vyhodnocují v pořadí podle priority, za nímž následuje spravované pravidla.

## <a name="next-steps"></a>Další postup

Po informace o vlastních pravidel [vytvořit vaše vlastní pravidla](create-custom-waf-rules.md).
