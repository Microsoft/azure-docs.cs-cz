---
title: Vlastní pravidla brány webových aplikací Azure Web Application Firewall (WAF) v2 na aplikační bráně
description: Tento článek obsahuje přehled vlastních pravidel brány firewall webových aplikací (WAF) v2 v bráně aplikace Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: c0f802f5113e38e811c110ee913099e76fa7be0b
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383814"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Vlastní pravidla pro bránu firewall webových aplikací v2 v bráně aplikace Azure

Brána webových aplikací Azure Application Gateway v2 je dodávána s předem nakonfigurovanou sadou pravidel spravovanou platformou, která nabízí ochranu před mnoha různými typy útoků. Tyto útoky zahrnují skriptování mezi weby, vkládání SQL a další. Pokud jste správcem WAF, můžete napsat vlastní pravidla, abyste rozšířili základní pravidla sady pravidel (CRS). Pravidla mohou blokovat nebo povolit požadovaný provoz na základě odpovídajících kritérií.

Vlastní pravidla umožňují vytvořit vlastní pravidla, která jsou vyhodnocována pro každý požadavek, který prochází WAF. Tato pravidla mají vyšší prioritu než ostatní pravidla ve spravovaných sadách pravidel. Vlastní pravidla obsahují název pravidla, prioritu pravidla a pole odpovídajících podmínek. Pokud jsou tyto podmínky splněny, je provedena akce (povolit nebo blokovat).

Můžete například blokovat všechny požadavky z adresy IP v rozsahu 192.168.5.4/24. V tomto pravidle je operátor *IPMatch*, matchValues je rozsah IP adres (192.168.5.4/24) a akce je blokovat přenosy. Můžete také nastavit název a prioritu pravidla.

Vlastní pravidla podporují použití složené logiky, aby se pokročilejší pravidla, která řeší vaše potřeby zabezpečení. Například (podmínka 1 **a** podmínka 2) **nebo** podmínka 3). To znamená, že pokud jsou splněny podmínky 1 **a** podmínka 2 **nebo** pokud je splněna podmínka 3, waf by měla provést akci zadanou ve vlastním pravidle.

Různé podmínky párování v rámci stejného pravidla jsou vždy slohozovány pomocí **a**. Můžete například blokovat provoz z určité IP adresy a pouze v případě, že používají určitý prohlížeč.

Pokud chcete **nebo** dvě různé podmínky, musí být dvě podmínky v různých pravidlech. Můžete například blokovat provoz z určité IP adresy nebo blokovat provoz, pokud používají konkrétní prohlížeč.

> [!NOTE]
> Maximální počet vlastních pravidel WAF je 100. Další informace o omezeních aplikační brány najdete v [tématu Omezení předplatného a služeb Azure, kvóty a omezení](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

Regulární výrazy jsou také podporovány ve vlastních pravidlech, stejně jako v sadách pravidel crs. Příklady najdete v tématu Příklady 3 a 5 v [tématu Vytvoření a použití vlastních pravidel brány firewall webové aplikace](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Povolení vs. blokování

Povolení a blokování provozu je jednoduché s vlastními pravidly. Můžete například zablokovat veškerý provoz přicházející z rozsahu ADRES IP. Můžete vytvořit jiné pravidlo pro povolení provozu, pokud požadavek pochází z určitého prohlížeče.

Chcete-li něco povolit, ujistěte se, že `-Action` parametr je nastaven na **Povolit**. Chcete-li něco zablokovat, ujistěte se, že je `-Action` parametr nastaven na **blokovat**.

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

Předchozí `$BlockRule` mapy na následující vlastní pravidlo ve Správci prostředků Azure:

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

Toto vlastní pravidlo obsahuje název, prioritu, akci a pole odpovídajících podmínek, které musí být splněny, aby se akce uskutečnila. Další vysvětlení těchto polí naleznete v následujících popisech polí. Například vlastní pravidla naleznete v [tématu Vytvoření a použití vlastních pravidel brány firewall webové aplikace](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Pole pro vlastní pravidla

### <a name="name-optional"></a>Název [nepovinné]

Název pravidla.  Zobrazí se v protokolech.

### <a name="priority-required"></a>Priorita [povinné]

- Určuje pořadí ocenění pravidla. Čím nižší hodnota, tím dříve hodnocení pravidla. Povolený rozsah je od 1 do 100. 
- Musí být jedinečný ve všech vlastních pravidlech. Pravidlo s prioritou 40 je vyhodnoceno před pravidlem s prioritou 80.

### <a name="rule-type-required"></a>Typ pravidla [povinné]

V současné době musí být **MatchRule**.

### <a name="match-variable-required"></a>Proměnná shoda [povinné]

Musí být jedna z proměnných:

- RemoteAddr – IP adresa/název hostitele připojení vzdáleného počítače
- RequestMethod – metoda požadavku HTTP (GET, POST, PUT, DELETE a tak dále.)
- QueryString – proměnná v identifikátoru URI
- PostArgs – Argumenty odeslané v těle POST. Vlastní pravidla používající tuto proměnnou shody se použijí pouze v případě, že je hlavička typu Content-Type nastavena na "application/x-www-form-urlencoded" a "multipart/form-data".
- RequestUri – identifikátor URI požadavku
- RequestHeaders – záhlaví požadavku
- RequestBody – Obsahuje celý tělo požadavku jako celek. Vlastní pravidla používající tuto proměnnou shody se použijí pouze v případě, že je hlavička typu content-type nastavena na "application/x-www-form-urlencoded". 
- RequestCookies – Cookies žádosti

### <a name="selector-optional"></a>Volič [volitelné]

Popisuje pole matchVariable kolekce. Například pokud matchVariable je RequestHeaders, selektor může být v záhlaví *User-Agent.*

### <a name="operator-required"></a>Operátor [povinné]

Musí se jednat o jeden z následujících operátorů:

- IPMatch - používá se pouze v případě, že proměnná shoda je *RemoteAddr*
- Equal – vstup je stejný jako MatchValue
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex
- Geomatch (náhled)

### <a name="negate-condition-optional"></a>Negátová podmínka [nepovinné]

Neguje aktuální stav.

### <a name="transform-optional"></a>Transformace [volitelné]

Seznam řetězců s názvy transformací, které je třeba provést před pokusem o shodu. Mohou to být následující transformace:

- Malá písmena
- Trim
- UrlDecode
- Urlencode 
- Odebrat nulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Shodovat hodnoty [povinné]

Seznam hodnot, které mají odpovídat, které lze považovat za *OR*'ed. Může to být například IP adresy nebo jiné řetězce. Formát hodnoty závisí na předchozím operátoru.

### <a name="action-required"></a>Akce [povinné]

- Povolit – autorizuje transakci, přeskočení všech ostatních pravidel. Zadaný požadavek je přidán do seznamu povolených položek a po odpovídající, požadavek zastaví další hodnocení a je odeslán do back-endového fondu. Pravidla, která jsou uvedena v seznamu povolených položek, nejsou vyhodnocována pro žádná další vlastní pravidla ani pro spravovaná pravidla.
- Blok – blokuje transakci na základě *SecDefaultAction* (režim detekce/prevence). Stejně jako povolit akce, jakmile je požadavek vyhodnocen a přidán do seznamu blokování, vyhodnocení je zastaveno a požadavek je blokován. Jakýkoli požadavek poté, co splňuje stejné podmínky, nebudou vyhodnoceny a budou pouze blokovány. 
- Protokol – umožňuje pravidlo zapisovat do protokolu, ale umožňuje zbývající pravidla spustit pro hodnocení. Ostatní vlastní pravidla jsou vyhodnocována v pořadí podle priority následovaná spravovanými pravidly.

## <a name="geomatch-custom-rules-preview"></a>Vlastní pravidla geomatch (náhled)

Vlastní pravidla umožňují vytvářet přizpůsobené pravidla tak, aby vyhovovala přesným potřebám vašich aplikací a zásad zabezpečení. Přístup k webovým aplikacím můžete omezit podle země nebo oblasti. Další informace naleznete v tématu [Geomatch custom rules (preview)](geomatch-custom-rules.md).

## <a name="next-steps"></a>Další kroky

Po sešlápnout o vlastních pravidlech [vytvořte vlastní pravidla](create-custom-waf-rules.md).
