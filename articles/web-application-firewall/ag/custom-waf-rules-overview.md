---
title: Vlastní pravidla firewallu webových aplikací Azure (WAF) v2 pro Application Gateway
description: Tento článek poskytuje přehled o vlastních pravidlech firewallu webových aplikací (WAF) v2 na Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.openlocfilehash: 0ac37378797c59d79af5d026200b68154836c5ac
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585397"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Vlastní pravidla pro Firewall webových aplikací V2 v Azure Application Gateway

Firewall webových aplikací pro Azure Application Gateway (WAF) v2 přináší předem nakonfigurovanou RuleSet spravovanou platformou, která nabízí ochranu před mnoha různými typy útoků. Mezi tyto útoky patří skriptování mezi weby, vkládání SQL a další. Pokud jste správce WAF, možná budete chtít napsat vlastní pravidla pro rozšíření pravidel základní sady pravidel (počítačový systém). Vaše pravidla mohou buď blokovat nebo povolovat požadovaný provoz na základě kritérií porovnání.

Vlastní pravidla umožňují vytvořit vlastní pravidla vyhodnocená pro každý požadavek, který projde WAF. Tato pravidla mají vyšší prioritu než zbývající pravidla ve spravovaných sadách pravidel. Vlastní pravidla obsahují název pravidla, prioritu pravidla a pole podmínek, které se shodují. Pokud jsou splněny tyto podmínky, je provedena akce (pro povolení nebo blokování).

Můžete například blokovat všechny požadavky z IP adresy v rozsahu 192.168.5.4/24. V tomto pravidle je operátor *IPMatch*, matchValues je rozsah IP adres (192.168.5.4/24) a akce slouží k blokování provozu. Nastavíte také název a prioritu pravidla.

Vlastní pravidla podporují použití složené logiky k pokročilejším pravidlům, která řeší vaše požadavky na zabezpečení. Například (podmínka 1 **a** podmínka 2) **nebo** podmínka 3).  Tento příklad znamená, že pokud je splněna podmínka 1 **a** podmínka 2 **nebo** Pokud je splněna podmínka 3, WAF by měla provést akci určenou ve vlastním pravidle.

Různé podmínky shod v rámci stejného pravidla jsou vždy složené pomocí **a**. Například zablokujte provoz z konkrétní IP adresy a jenom v případě, že používá určitý prohlížeč.

Pokud chcete **nebo** dvě různé podmínky, musí být tyto dvě podmínky v různých pravidlech. Můžete například blokovat provoz z konkrétní IP adresy nebo blokovat provoz, pokud používají konkrétní prohlížeč.

> [!NOTE]
> Maximální počet vlastních pravidel WAF je 100. Další informace o omezeních Application Gateway najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-subscription-service-limits.md#application-gateway-limits).

Regulární výrazy jsou také podporovány ve vlastních pravidlech, stejně jako v RuleSets počítačový počítač. Příklady těchto příkazů najdete v části Příklady 3 a 5 v tématu [Vytvoření a použití vlastních pravidel firewallu webových aplikací](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Povolení vs. blokování

Povolení a blokování provozu je jednoduché s vlastními pravidly. Můžete například blokovat veškerý provoz přicházející z rozsahu IP adres. Pokud požadavek pochází z konkrétního prohlížeče, můžete nastavit jiné pravidlo, které povolí provoz.

Chcete-li něco připustit, zajistěte, aby byl parametr `-Action` nastaven na hodnotu **Allow**. Chcete-li něco zablokovat, zajistěte, aby byl parametr `-Action` nastaven na hodnotu **blokovat**.

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

Předchozí `$BlockRule` se v Azure Resource Manager mapuje na následující vlastní pravidlo:

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

- Určuje pořadí hodnocení pravidel. Čím nižší hodnota, tím výše se vyhodnocuje pravidlo. Povolený rozsah je od 1-100. 
- Musí být jedinečné napříč všemi vlastními pravidly. Pravidlo s prioritou 40 je vyhodnoceno před pravidlem s prioritou 80.

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
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- Filtr začíná na
- EndsWith
- regulární
- Porovnat (Náhled)

### <a name="negate-condition-optional"></a>Podmínka negace [nepovinné]

Negace aktuální podmínky.

### <a name="transform-optional"></a>Transformace [nepovinné]

Seznam řetězců s názvy transformací, které se mají provést před pokusem o shodu. Můžou to být následující transformace:

- Malá písmena
- Sklon
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

## <a name="geomatch-custom-rules-preview"></a>Vlastní pravidla pro neshodu (Preview)

Vlastní pravidla umožňují vytvářet upravená pravidla tak, aby vyhovovala přesným potřebám vašich aplikací a vašim zásadám zabezpečení. Nyní můžete omezit přístup k vašim webovým aplikacím podle země nebo oblasti, která je k dispozici ve verzi Public Preview. Stejně jako u všech vlastních pravidel může být tato logika složená s jinými pravidly, aby vyhovovala potřebám vaší aplikace. 

   > [!NOTE]
   > Vlastní pravidla pro inshodě jsou dostupná v Střed USA – jih a Severní Evropa. Pokud k nim chcete přistupovat na portálu, použijte prosím [Tento odkaz](https://aka.ms/AppGWWAFGeoMatch) , dokud nebude aktivní pro všechny. 

Pokud používáte operátor geografického porovnávání, mohou být selektory libovolné z následujících dvoumístné kódů zemí. 

|Kód země | Název země |
| ----- | ----- |
| AD | Andorra |
| AE | Spojené arabské emiráty|
| AF | Afghánistán|
| AG | Antigua a Barbuda|
| AL | Albánie|
| AM | Arménie|
| AO | Angola|
| AR | Argentina|
| AS | Americká Samoa|
| AT | Rakousko|
| AU | Austrálie|
| AZ | Ázerbájdžán|
| BA | Bosna a Hercegovina|
| BB | Barbados|
| BD | Bangladéš|
| BE | Belgie|
| BF | Burkina Faso|
| BG | Bulharsko|
| BH | Bahrajn|
| BI | Burundi|
| BJ | Benin|
| BL | Svatý Bartoloměj|
| BN | Sultanát Brunej|
| BO | Bolívie|
| BR | Brazílie|
| BS | Bahamy|
| BT | Bhútán|
| BW | Botswana|
| BY | Bělorusko|
| BZ | Belize|
| CA | Kanada|
| CD | Demokratická republika Kongo|
| CF | Středoafrická republika|
| CH | Švýcarsko|
| CI | Pobřeží slonoviny|
| CL | Chile|
| CM | Kamerun|
| CN | Čína|
| CO | Kolumbie|
| CR | Kostarika|
| CU | Kuba|
| CV | Cabo Verde|
| CY | Kypr|
| CZ | Česká republika|
| DE | Německo|
| DK | Dánsko|
| DO | Dominikánská republika|
| DZ | Alžírsko|
| EC | Ekvádor|
| EE | Estonsko|
| EG | Egypt|
| ES | Španělsko|
| ET | Etiopie|
| FI | Finsko|
| FJ | Fidži|
| FM | Federativní státy Mikronésie|
| FR | Francie|
| GB | Spojené království|
| GE | Gruzie|
| GF | Francouzská Guyana|
| GH | Ghana|
| GN | Guinea|
| GP | Guadeloupe|
| GR | Řecko|
| GT | Guatemala|
| GY | Guyana|
| HK | Hongkong – zvláštní správní oblast|
| HN | Honduras|
| HR | Chorvatsko|
| HT | Haiti|
| HU | Maďarsko|
| ID | Indonésie|
| IE | Irsko|
| IL | Izrael|
| IN | Indie|
| IQ | Irák|
| IR | Íránská islámská republika|
| IS | Island|
| IT | Itálie|
| JM | Jamajka|
| JO | Jordánsko|
| JP | Japonsko|
| KE | Keňa|
| KG | Kyrgyzstán|
| KH | Kambodža|
| KI | Kiribati|
| KN | Svatý Kryštof a Nevis|
| KP | Korejská lidově demokratická republika|
| KR | Korejská republika|
| KW | Kuvajt|
| KY | Kajmanské ostrovy|
| KZ | Kazachstán|
| LA | Laoská lidově demokratická republika|
| LB | Libanon|
| LI | Lichtenštejnsko|
| LK | Srí Lanka|
| LR | Libérie|
| LS | Lesotho|
| LT | Litva|
| LU | Lucembursko|
| LV | Lotyšsko|
| LY | Libye |
| MA | Maroko|
| MD | Moldavská republika|
| MG | Madagaskar|
| MK | Severní Makedonie|
| ML | Mali|
| MM | Myanmar|
| MN | Mongolsko|
| MO | Macao – zvláštní správní oblast|
| MQ | Martinik|
| MR | Mauritánie|
| MT | Malta|
| MV | Maledivy|
| MW | Malawi|
| MX | Mexiko|
| MY | Malajsie|
| MZ | Mosambik|
| Není k dispozici | Namibie|
| NE | Niger|
| NG | Nigérie|
| NI | Nikaragua|
| NL | Nizozemsko|
| NO | Norsko|
| NP | Nepál|
| NR | Nauru|
| NZ | Nový Zéland|
| OM | Omán|
| PA | Panama|
| PE | Peru|
| PH | Filipíny|
| PK | Pákistán|
| PL | Polsko|
| PR | Portoriko|
| PT | Portugalsko|
| PW | Palau|
| PY | Paraguay|
| QA | Katar|
| RE | Réunion|
| RO | Rumunsko|
| RS | Srbsko|
| RU | Ruská federace|
| RW | Rwanda|
| SA | Saúdská Arábie|
| SD | Súdán|
| SE | Švédsko|
| SG | Singapur|
| SI | Slovinsko|
| SK | Slovensko|
| SN | Senegal|
| SO | Somálsko|
| SR | Surinam|
| SS | Jižní Súdán|
| SV | Salvador|
| SY | Syrská arabská republika|
| SZ | Svazijsko|
| TC | Turks a Caicos|
| TG | Togo|
| TH | Thajsko|
| TN | Tunisko|
| TR | Turecko|
| TT | Trinidad a Tobago|
| TW | Tchaj-wan|
| TZ | Sjednocená tanzanská republika|
| UA | Ukrajina|
| UG | Uganda|
| USA | Spojené státy|
| UY | Uruguay|
| UZ | Uzbekistán|
| VC | Svatý Vincenc a Grenadiny|
| VE | Venezuela|
| VG | Britské Panenské ostrovy|
| VI | Americké Panenské ostrovy|
| VN | Vietnam|
| ZA | Jižní Afrika|
| ZM | Zambie|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Další kroky

Jakmile se seznámíte s vlastními pravidly, [vytvořte vlastní pravidla](create-custom-waf-rules.md).
