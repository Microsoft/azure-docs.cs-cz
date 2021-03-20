---
title: Referenční informace k modulu Azure CDN Rules | Microsoft Docs
description: Referenční dokumentace ke službě Azure CDN Rules Engine podmínky a funkce.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 295bc0a20a547bf944f48af6711b18af34571b02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91362576"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Odkazy na modul Azure CDN z Verizon Premium Rules

V tomto článku jsou uvedené podrobné popisy dostupných podmínek a funkcí shody pro [modul pravidel](cdn-verizon-premium-rules-engine.md)Azure Content DELIVERY Network (CDN).

Modul pravidel je navržený tak, aby měl koncovou autoritu týkající se způsobu, jakým služba CDN zpracovává konkrétní typy požadavků.

**Běžné použití**:

- Přepsat nebo definovat vlastní zásadu mezipaměti.
- Zabezpečte nebo zakažte požadavky na citlivý obsah.
- Přesměrování požadavků.
- Ukládat data vlastního protokolu.
## <a name="key-concepts"></a>Klíčové koncepty
Klíčové pojmy pro nastavení stroje pravidel jsou popsané níže.
### <a name="draft"></a>Koncept
Koncept zásady se skládá z jednoho nebo více pravidel, která jsou určena k identifikaci požadavků a sadě akcí, které budou pro ně aplikovány. Koncept je Nedokončená práce, která umožňuje časté aktualizace konfigurace bez ovlivnění provozu lokality. Jakmile je koncept připravený k finalizaci, měl by být převeden na zásadu jen pro čtení.

### <a name="rule"></a>Pravidlo
Pravidlo identifikuje jeden nebo více typů požadavků a sadu akcí, které se na ně budou aplikovat.

Skládá se z těchto: 

- Sada podmíněných výrazů, které definují logiku, prostřednictvím které jsou identifikovány požadavky.
- Sada podmínek shody definující kritéria používaná k identifikaci požadavků.
- Sada funkcí definujících způsob, jakým bude CDN zpracovávat výše uvedené požadavky.
Tyto prvky jsou identifikovány na následujícím obrázku.

![Snímek obrazovky s popisky zobrazuje podmíněný výraz, shodu a funkce pravidla.](./media/cdn-verizon-premium-rules-engine-reference/verizon-rules-engine-reference.png)

### <a name="policy"></a>Zásady
Zásady, které se skládají ze sady pravidel jen pro čtení, poskytují prostředky pro:

- Umožňuje vytvářet, ukládat a spravovat víc variant vašich pravidel.
- Vraťte se k dříve nasazené verzi.
- Předem připravte pravidla specifická pro událost (například pravidlo, které přesměruje provoz v důsledku údržby zdroje zákazníka).

> [!NOTE]
> I když je povolená jenom jedna zásada pro každé prostředí, můžou se podle potřeby nasadit zásady.

### <a name="deploy-request"></a>Nasadit požadavek
Žádost o nasazení poskytuje jednoduchý a zjednodušený postup, pomocí kterého lze zásadu rychle použít v pracovním nebo produkčním prostředí. K dispozici je historie žádostí o nasazení, které usnadňují sledování změn použitých v těchto prostředích.

> [!NOTE]
> Pouze požadavky, které neprošly naším automatizovaným ověřováním a systémem detekce chyb, budou vyžadovat ruční kontrolu a schválení.

### <a name="rule-precedence"></a>Priorita pravidla
Pravidla obsažená v zásadách jsou obvykle zpracovávána v pořadí, ve kterém jsou uvedena (tj. shora dolů). Pokud žádost odpovídá konfliktním pravidlům, bude mít poslední zpracovávané pravidlo přednost.

### <a name="policy-deployment-workflow"></a>Pracovní postup nasazení zásad
Pracovní postup, pomocí kterého se dá zásady použít v produkčním nebo přípravném prostředí, je znázorněný níže.

![Pracovní postup nasazení zásad](./media/cdn-verizon-premium-rules-engine-reference/policy-deployment-workflow.png)

|Krok |Description |
|---------|---------|
|[Vytvořit koncept](https://docs.vdms.com/cdn/index.html#HRE/AdministeringDraftsandRules.htm#Create)    |    Koncept se skládá ze sady pravidel definujících způsob, jakým by měly být požadavky na obsah zpracovávány přes CDN.     |
|Zamknout koncept   |     Po dokončení konceptu by měl být uzamčen a převeden na zásadu jen pro čtení.    |
|[Odeslat žádost o nasazení](https://docs.vdms.com/cdn/index.html#HRE/DeployRequest.htm)   |   <br> Požadavek na nasazení umožňuje použít zásady na testovací nebo provozní provoz.</br> <br>Odešlete žádost o nasazení do pracovního nebo produkčního prostředí.</br>     |
|Nasadit revizi žádosti   |    <br>Požadavek na nasazení přechází k automatizovanému ověřování a detekci chyb.</br><br>I když je většina žádostí o nasazení automaticky schválena, pro složitější zásady se vyžaduje ruční kontrola.</br>   |
|Nasazení zásad ([fázování](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Staging))   |  <br> Po schválení žádosti o nasazení do přípravného prostředí se zásada použije pro pracovní prostředí. Toto prostředí umožňuje testování zásad proti provozu lokality.</br><br>Jakmile je zásada připravena k použití pro provoz živého webového serveru, měla by se odeslat nová žádost o nasazení v produkčním prostředí.</br>      |
|Nasazení zásad ([produkční](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Producti))   |  Po schválení žádosti o nasazení v produkčním prostředí se zásada použije v produkčním prostředí. Toto prostředí umožňuje zásadám fungovat jako koncová autorita pro určení způsobu, jakým má síť CDN pracovat s živým provozem.     |
## <a name="syntax"></a>Syntax

Způsob, jakým jsou zpracovány speciální znaky, se liší podle toho, jak podmínka shody nebo funkce zpracovává textové hodnoty. Podmínka nebo funkce shody může interpretovat text jedním z následujících způsobů:

- [**Hodnoty literálu**](#literal-values)
- [**Zástupné hodnoty**](#wildcard-values)
- [**Regulární výrazy**](#regular-expressions)

### <a name="literal-values"></a>Hodnoty literálu

Text, který je interpretován jako hodnota literálu, zpracovává všechny speciální znaky s výjimkou% symbol, jako součást hodnoty, která musí být shodná. Jinými slovy podmínka shody literálů nastavená na `\'*'\` je splněná jenom v případě, že se najde tato přesná hodnota (tj `\'*'\` .).

Symbol procenta se používá k označení kódování adresy URL (například `%20` ).

### <a name="wildcard-values"></a>Zástupné hodnoty

Text, který je interpretován jako zástupná hodnota, přiřadí další význam pro speciální znaky. Následující tabulka popisuje, jak je interpretována následující sada znaků:

Znak | Description
----------|------------
\ | Zpětné lomítko se používá k úniku znaků určených v této tabulce. Zpětné lomítko musí být zadáno přímo před speciálním znakem, který by měl být uvozen řídicím znakem.<br/>Například následující syntaxe řídí hvězdičku: `\*`
% | Symbol procenta se používá k označení kódování adresy URL (například `%20` ).
\* | Hvězdička je zástupný znak, který reprezentuje jeden nebo více znaků.
Místo | Znak mezery označuje, že podmínka shody může být splněna některou ze zadaných hodnot nebo vzorů.
osa | Jedna nabídka nemá zvláštní význam. Sada jednoduchých uvozovek však používá k označení toho, že by měla být hodnota považována za hodnotu literálu. Dá se použít následujícími způsoby:<br><br/>– Umožňuje splnění podmínky shody vždy, když zadaná hodnota odpovídá jakékoli části hodnoty porovnání.  Například `'ma'` by odpovídaly jakémukoli z následujících řetězců: <br/><br/>/Business/**ma** rathon/asset.htm<br/>**ma** p.gif<br/>/business/template. **ma** p<br /><br />– Umožňuje zadat speciální znak jako literální znak. Například můžete zadat literální znak mezery ohraničujícím znakem mezery v rámci sady jednoduchých uvozovek (tj `' '` `'sample value'` . nebo).<br/>– Povoluje zadání prázdné hodnoty. Zadáním sady jednoduchých uvozovek (tj.) zadejte prázdnou hodnotu.<br /><br/>**Důležité:**<br/>– Pokud zadaná hodnota neobsahuje zástupný znak, je automaticky považována za literálovou hodnotu, což znamená, že není nutné zadávat sadu jednoduchých uvozovek.<br/>– Pokud zpětné lomítko neřídí jiný znak v této tabulce, je ignorováno, pokud je zadáno v rámci sady jednoduchých uvozovek.<br/>– Jiný způsob, jak zadat speciální znak jako literální znak, je vystavení pomocí zpětného lomítka (tj `\` .).

### <a name="regular-expressions"></a>Regulární výrazy

Regulární výrazy definují vzor, který je prohledáván v rámci textové hodnoty. Zápis regulárního výrazu definuje konkrétní význam pro nejrůznější symboly. Následující tabulka uvádí, jak jsou speciální znaky ošetřeny podmínkami shody a funkcemi, které podporují regulární výrazy.

Speciální znak | Description
------------------|------------
\ | Zpětné lomítko řídí znak, který následuje, což způsobí, že tento znak bude považován za hodnotu literálu namísto převzetí jeho regulárního výrazu. Například následující syntaxe řídí hvězdičku: `\*`
% | Význam symbolu procenta závisí na jeho využití.<br/><br/> `%{HTTPVariable}`: Tato syntaxe identifikuje proměnnou HTTP.<br/>`%{HTTPVariable%Pattern}`: Tato syntaxe používá symbol procenta k identifikaci proměnné HTTP a jako oddělovače.<br />`\%`: Uvozovací znak v procentech umožňuje použití jako literálové hodnoty nebo k označení kódování adresy URL (například `\%20` ).
\* | Hvězdička umožňuje, aby předchozí znak odpovídal nule nebo vícekrát.
Místo | Znak mezery je obvykle považován za literální znak.
osa | Jednoduché uvozovky se považují za literální znaky. Sada jednoduchých uvozovek nemá zvláštní význam.

Podmínky shody a funkce, které podporují regulární výrazy, přijímají vzory definované běžnými výrazy kompatibilními s Perl (PCRE).



## <a name="next-steps"></a>Další kroky

- [Podmínky shody stroje pravidel](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Podmíněné výrazy stroje pravidel](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkce stroje pravidel](cdn-verizon-premium-rules-engine-reference-features.md)
- [Přepsání chování HTTP pomocí modulu pravidel](cdn-verizon-premium-rules-engine.md)
- [Přehled Azure CDN](cdn-overview.md)
