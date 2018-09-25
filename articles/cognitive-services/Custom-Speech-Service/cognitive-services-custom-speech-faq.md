---
title: Nejčastější dotazy ke službě Custom Speech Service v Azure | Dokumentace Microsoftu
description: Tady najdete odpovědi na Nejoblíbenější dotazy týkající se služby Custom Speech Service.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/21/2016
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 5ab4adefbfbfa1a295f980fd31bffaeaa957ce0c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955278"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Custom Speech Service – nejčastější dotazy

Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, zkuste se zeptat komunity služby Custom Speech Service na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) a [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Obecné

**Dotaz**: Jak zjistím, že po dokončení zpracování sady dat nebo model?

**Odpověď**: v současné době je pouze chcete vědět, stav modelu nebo sadu dat v tabulce.
Po dokončení zpracování se stav bude "Připraveno".
Pracujeme na vylepšení metody pro komunikaci zpracování stavu, jako jsou e-mailové oznámení.

**Dotaz**: můžete vytvořit více než jeden model v čase?

**Odpověď**: neexistuje žádné omezení počtu modely jsou ve vaší kolekci, ale pouze jeden lze vytvořit v čas na každé stránce.
Například nelze spustit proces vytváření language model, pokud ve fázi procesu je aktuálně jazykový model.
Můžete však použít akustický model a jazykový model zpracování ve stejnou dobu. 

**Dotaz**: Mohu uvědomili si můžu udělali chybu. Jak zrušit import dat nebo model vytváření probíhající? 

**Odpověď**: aktuálně není možné vrátit zpět proces přizpůsobení akustických a v jakémkoli jazyce.
Importovaná data lze odstranit po dokončení importu

**Dotaz**: Jaký je rozdíl mezi hledání & diktování modely a konverzační modely?

**Odpověď**: existují dvě základní akustických a jazykových modelů lze vybírat ve službě Custom Speech Service.
vyhledávací dotazy, nebo diktování. Microsoft Conversational AM je vhodný pro rozpoznávání řeči, kterým se mluví ve stylu konverzační.
Tento typ řeči se obvykle přesměruje na jinou osobu, například v centrech volání nebo schůzky.

**Dotaz**: můžete aktualizovat mé existující model (model překrývání)?

**Odpověď**: nenabízíme možnost aktualizovat existující model s novými daty.
Pokud máte novou sadu dat a chcete upravit existující model se musí znovu přizpůsobovat na nová data a původní datové sady, který jste použili.
Staré a nové sady dat musí lze spojit do jednoho ZIP (je-li akustických dat) nebo souboru .txt, pokud je jazyk data jednou přizpůsobení se provádí nové aktualizace modelu musí být zrušit nasazené získat nový koncový bod

**Dotaz**: Co když budu potřebovat vyšší souběžnosti, než je výchozí hodnota. 

**Odpověď**: můžete vertikálně navýšit kapacitu váš model v přírůstcích po 5 souběžných žádostí, které označujeme jako jednotky škálování. Každá jednotka škálování garantuje, že váš model současně může zpracovat 5 zvukový datový proud. Můžete si koupit 100 jednotek škálování (nebo 500 souběžných požadavků).

Kontaktujte nás prosím Pokud vyžadujete vyšší než.

**Dotaz**: můžu my model stáhnout a spustit místně?

**Odpověď**: nepovolíte modely ke stažení a spuštěn lokálně.

**Dotaz**: jsou moje žádosti o přihlášení?

**Odpověď**: máte možnost volby při vytváření nasazení vypnutí trasování, nezůstane žádný zvukový nebo zaprotokoluje se přepisy. Jinak požadavky jsou obvykle protokoluje v Azure v zabezpečeném úložišti. Pokud máte další aspekty ochrany osobních údajů, které můžete používání služby Custom Speech Service prosím kontaktujte nás.

## <a name="importing-data"></a>Import dat

**Dotaz**: Jaký je limit velikosti datové sady? Proč? 

**Odpověď**: aktuální limit pro datové sady je 2 GB, kvůli omezení velikosti souboru pro nahrání protokolu HTTP. 

**Dotaz**: můžete k nahrání větší textový soubor zip Moje textové soubory? 

**Odpověď**: Ne, aktuálně jsou povoleny pouze nekomprimované textové soubory.

**Dotaz**: říká dat sestavy došlo k selhání projevy. Jde o problém?

**Odpověď**: Pokud jenom pár projevy se nedají úspěšně importovat, to není problém.
Pokud většinu projevy akustických nebo jazyk dat. Nastavte (například > 95 %) jsou úspěšně importovány, datová sada může být použitelné. Doporučuje se však pokusí zjistit, proč se nezdařilo projevy a opravit problémy.
Nejčastější problémy, například formátování chyby, se snadno to vyřešíme. 

## <a name="creating-am"></a>Vytváření AM

**Dotaz**: kolik akustických dat musím udělat?

**Odpověď**: doporučujeme začít se 30 minut na jednu hodinu akustických dat

**Dotaz**: jaký druh dat shromažďovat?

**Odpověď**: by shromažďovat data, která je nejblíž scénář aplikací a případ použití nejvíce.
To znamená, že shromažďování dat by měl odpovídat cílové aplikace a uživatele z hlediska zařízení nebo zařízení, prostředí a typy mluvčích. Obecně platí by měly shromažďovat data z co nejširším rozsahu mluvčích, jako je to možné. 

**Dotaz**: jak se mají shromažďovat? 

**Odpověď**: můžete vytvořit samostatné aplikace shromažďování dat nebo použít některé vypnout police záznam zvuků software.
Můžete také vytvořit verzi aplikace, která protokoluje zvukových dat a, který používá. 

**Dotaz**: Potřebuji přepisy přizpůsobení dat, mi připomínku? 

**Odpověď**: data musí být přepsána. Můžete přepisy sami nebo použít službu profesionální určené k transkripci. Některé z těchto přepisovatelé použití a dalšími lidmi pomocí crowdsourcingu. Můžete také doporučujeme určené k transkripci služby na vyžádání.

**Dotaz**: jak dlouho trvá vytvoření vlastního akustického modelu?

**Odpověď**: doba zpracování pro vytvoření vlastního akustického modelu je stejné jako délku akustický datové sady.
Ano, přizpůsobený akustický model vytvořený z datové sady pět hodin bude trvat přibližně pět hodin k procesu. 

## <a name="offline-testing"></a>Testování offline

**Dotaz**: provést offline testování Moje vlastního akustického modelu pomocí vlastního jazykového modelu?

**Odpověď**: Ano, stačí vybrat vlastní jazyk modelu v rozevíracím seznamu při nastavení offline testu

**Dotaz**: provést offline testování Moje vlastního jazykového modelu s použitím vlastního akustického modelu?

**Odpověď**: Ano, stačí vybrat vlastního akustického modelu v rozevírací nabídce při nastavování testů offline.

**Dotaz**: co je míra chyb aplikace Word a jak se počítá?

**Odpověď**: míra chyb aplikace Word je metrika hodnocení pro rozpoznávání řeči. Počítá se jako celkový počet chyb, která zahrnuje vložení, odstranění a nahrazení rozdělené podle celkového počtu slov ve přepis odkaz.

**Dotaz**: Mám teď znáte výsledky testu pro Moje vlastního modelu, je to dobrý nebo chybné číslo?

**Odpověď**: Výsledkem porovnání mezi modelem směrného plánu a jste si přizpůsobili.
Bychom se měli snažit beat základního modelu provádět přizpůsobení smysl

**Dotaz**: jak I přijít na to, zasílání základní modely, tak se zobrazí, pokud bylo zlepšení? 

**Odpověď**: výsledky testů offline zobrazit přesnost přesnost směrného plánu vlastního modelu a vylepšování prostřednictvím standardních hodnot

## <a name="creating-lm"></a>Vytváření LM

**Dotaz**: kolik textová data je potřeba nahrát?

**Odpověď**: Toto je obtížné dotaz poskytnout přesné odpovědi, protože závisí na tom, jak různé slovník a frází ve vaší aplikaci využívá jsou z výchozí jazykové modely. Pro všechny nové slova je užitečný k zadání počtu příklady nejvíce využití těchto slov. Běžné vět, které se používají ve vaší aplikaci včetně programů v jazyce data je také užitečné jako říká systému pro naslouchání i tyto podmínky.
Je běžné mít alespoň 100 a obvykle několik stovek projevy v datové sadě jazyka nebo více.
Také pokud určité typy * z dotazů jsou má být častější než jiné, můžete vložit více kopií běžných dotazů v datové sadě.

**Dotaz**: Mohu stačí načíst seznam slov?

**Odpověď**: nahrávání seznam slov, získejte slova do slovníku ale neučí systému, jak se obvykle používají slova.
Tím, že poskytuje úplné nebo částečné projevy (větách nebo frázích věcí, které uživatelé můžou Řekněme, že) jazykový model další nové slova a způsob jejich použití. Vlastní jazykový model je dobrým způsobem nejen pro získávání nových slov v systému, ale také pro úpravu pravděpodobnost známé slova pro vaši aplikaci. Poskytuje úplné projevy pomáhá systému zjistěte to. 

-----

 * [Přehled](cognitive-services-custom-speech-home.md)
 * [Spuštění](cognitive-services-custom-speech-get-started.md)
 * [Glosář](cognitive-services-custom-speech-glossary.md)
