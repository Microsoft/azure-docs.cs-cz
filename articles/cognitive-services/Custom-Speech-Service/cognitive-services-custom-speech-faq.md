---
title: Nejčastější dotazy ke službě Custom Speech Service v Azure | Dokumentace Microsoftu
description: Tady najdete odpovědi na Nejoblíbenější dotazy týkající se služby Custom Speech Service.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 11/21/2016
ms.author: nitinme
ms.openlocfilehash: 757fb6c1315940b73a8f9d7d28f8926cb7ccfd7b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884405"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Custom Speech Service – nejčastější dotazy

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)] 

Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, zkuste se zeptat komunity služby Custom Speech Service na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) a [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Obecné

**Dotaz**: Jak poznám, že po dokončení zpracování sady dat nebo model?

**Odpověď**: Stav modelu nebo sadu dat v tabulce je v současné době pouze chcete vědět.
Po dokončení zpracování se stav bude "Připraveno".
Pracujeme na vylepšení metody pro komunikaci zpracování stavu, jako jsou e-mailové oznámení.

**Dotaz**: Můžete vytvořit více než jeden model v čase?

**Odpověď**: Neexistuje žádné omezení počtu modely jsou ve vaší kolekci, ale pouze jeden lze vytvořit v čas na každé stránce.
Například nelze spustit proces vytváření language model, pokud ve fázi procesu je aktuálně jazykový model.
Můžete však použít akustický model a jazykový model zpracování ve stejnou dobu. 

**Dotaz**: Můžu uvědomili si, že mám udělali chybu. Jak zrušit import dat nebo model vytváření probíhající? 

**Odpověď**: Aktuálně není možné vrátit zpět proces přizpůsobení akustických a v jakémkoli jazyce.
Importovaná data lze odstranit po dokončení importu

**Dotaz**: Jaký je rozdíl mezi hledání & diktování modely a konverzační modely?

**Odpověď**: Existují dvě základní akustických a jazykových modelů lze vybírat ve službě Custom Speech Service.
vyhledávací dotazy, nebo diktování. Microsoft Conversational AM je vhodný pro rozpoznávání řeči, kterým se mluví ve stylu konverzační.
Tento typ řeči se obvykle přesměruje na jinou osobu, například v centrech volání nebo schůzky.

**Dotaz**: Můžete aktualizovat mé existující model (model překrývání)?

**Odpověď**: Nenabízíme možnost aktualizovat existující model s novými daty.
Pokud máte novou sadu dat a chcete upravit existující model se musí znovu přizpůsobovat na nová data a původní datové sady, který jste použili.
Staré a nové sady dat musí lze spojit do jednoho ZIP (je-li akustických dat) nebo souboru .txt, pokud je jazyk data jednou přizpůsobení se provádí nové aktualizace modelu musí být zrušit nasazené získat nový koncový bod

**Dotaz**: Co když budu potřebovat vyšší souběžnosti, než je výchozí hodnota. 

**Odpověď**: Můžete vertikálně navýšit kapacitu váš model v přírůstcích po 5 souběžných žádostí, které označujeme jako jednotky škálování. Každá jednotka škálování garantuje, že váš model současně může zpracovat 5 zvukový datový proud. Můžete si koupit 100 jednotek škálování (nebo 500 souběžných požadavků).

Kontaktujte nás prosím Pokud vyžadujete vyšší než.

**Dotaz**: Můžete stáhnout Moje modelu a spustit místně?

**Odpověď**: Než povolíte modely ke stažení a spuštěn lokálně.

**Dotaz**: Jsou moje žádosti o přihlášení?

**Odpověď**: Máte možnost volby při vytváření nasazení vypnutí trasování, v tomto okamžiku se protokolovat žádné zvuk nebo přepisů. Jinak požadavky jsou obvykle protokoluje v Azure v zabezpečeném úložišti. Pokud máte další aspekty ochrany osobních údajů, které můžete používání služby Custom Speech Service prosím kontaktujte nás.

## <a name="importing-data"></a>Import dat

**Dotaz**: Jaký je limit velikosti datové sady? Proč? 

**Odpověď**: Aktuální limit pro datové sady je 2 GB, nahrát kvůli omezení velikosti souboru pro protokol HTTP. 

**Dotaz**: Můžete k nahrání větší textový soubor zip Moje textové soubory? 

**Odpověď**: Ne, momentálně se podporuje jenom nekomprimované textové soubory jsou povoleny.

**Dotaz**: Data sestavy uvádí, že došlo k selhání projevy. Jde o problém?

**Odpověď**: Pokud pouze několik projevy se nedají úspěšně importovat, to není problém.
Pokud většinu projevy akustických nebo jazyk dat. Nastavte (například > 95 %) jsou úspěšně importovány, datová sada může být použitelné. Doporučuje se však pokusí zjistit, proč se nezdařilo projevy a opravit problémy.
Nejčastější problémy, například formátování chyby, se snadno to vyřešíme. 

## <a name="creating-am"></a>Vytváření AM

**Dotaz**: Kolik akustických dat budu potřebovat?

**Odpověď**: Doporučujeme začít se 30 minut na jednu hodinu akustických dat

**Dotaz**: Jaký druh dat shromažďování

**Odpověď**: By měl shromažďovat data, která je nejblíž scénář aplikací a případ použití nejvíce.
To znamená, že shromažďování dat by měl odpovídat cílové aplikace a uživatele z hlediska zařízení nebo zařízení, prostředí a typy mluvčích. Obecně platí by měly shromažďovat data z co nejširším rozsahu mluvčích, jako je to možné. 

**Dotaz**: Jak by měl shromažďování vyžadováno? 

**Odpověď**: Můžete vytvořit samostatné aplikace shromažďování dat nebo použít některé vypnout police záznam zvuků software.
Můžete také vytvořit verzi aplikace, která protokoluje zvukových dat a, který používá. 

**Dotaz**: Je potřeba přepisy přizpůsobení dat ručně? 

**Odpověď**: Data musí být přepsána. Můžete přepisy sami nebo použít službu profesionální určené k transkripci. Některé z těchto přepisovatelé použití a dalšími lidmi pomocí crowdsourcingu. Můžete také doporučujeme určené k transkripci služby na vyžádání.

**Dotaz**: Jak dlouho trvá vytvoření vlastního akustického modelu?

**Odpověď**: Doba zpracování pro vytvoření vlastního akustického modelu je stejné jako délku akustický datové sady.
Ano, přizpůsobený akustický model vytvořený z datové sady pět hodin bude trvat přibližně pět hodin k procesu. 

## <a name="offline-testing"></a>Testování offline

**Dotaz**: Můžete provádět testování offline Moje vlastního akustického modelu pomocí vlastního jazykového modelu?

**Odpověď**: Ano, stačí vyberte vlastní jazyk modelu v rozevíracím seznamu při nastavení offline testu

**Dotaz**: Můžete provádět testování offline Moje vlastního jazykového modelu s použitím vlastního akustického modelu?

**Odpověď**: Ano, stačí vyberte vlastního akustického modelu v rozevírací nabídce při nastavování testů offline.

**Dotaz**: Co je míra chyb aplikace Word a jak se počítá?

**Odpověď**: Míra chyb aplikace Word je metrika hodnocení pro rozpoznávání řeči. Počítá se jako celkový počet chyb, která zahrnuje vložení, odstranění a nahrazení rozdělené podle celkového počtu slov ve přepis odkaz.

**Dotaz**: Mám teď znáte výsledky testu pro Moje vlastního modelu, je to dobrý nebo chybné číslo?

**Odpověď**: Ve výsledcích zobrazí porovnání mezi modelem směrného plánu a ten, který jste si přizpůsobili.
Bychom se měli snažit beat základního modelu provádět přizpůsobení smysl

**Dotaz**: Jak I přijít na to, zasílání základní modely, tak se zobrazí, pokud bylo zlepšení? 

**Odpověď**: Výsledky testů offline zobrazit přesnost přesnost směrného plánu vlastního modelu a vylepšování prostřednictvím standardních hodnot

## <a name="creating-lm"></a>Vytváření LM

**Dotaz**: Kolik dat textu je nutné odeslat?

**Odpověď**: To je obtížné dotaz poskytnout přesné odpověď na, protože závisí na různých slovník a frází ve vaší aplikaci využívá pocházejí z výchozí jazykové modely. Pro všechny nové slova je užitečný k zadání počtu příklady nejvíce využití těchto slov. Běžné vět, které se používají ve vaší aplikaci včetně programů v jazyce data je také užitečné jako říká systému pro naslouchání i tyto podmínky.
Je běžné mít alespoň 100 a obvykle několik stovek projevy v datové sadě jazyka nebo více.
Také pokud určité typy * z dotazů jsou má být častější než jiné, můžete vložit více kopií běžných dotazů v datové sadě.

**Dotaz**: Mohu jen seznam slov načíst?

**Odpověď**: Nahrává se seznam slov, získat slova do slovníku ale neučí systému, jak se obvykle používají slova.
Tím, že poskytuje úplné nebo částečné projevy (větách nebo frázích věcí, které uživatelé můžou Řekněme, že) jazykový model další nové slova a způsob jejich použití. Vlastní jazykový model je dobrým způsobem nejen pro získávání nových slov v systému, ale také pro úpravu pravděpodobnost známé slova pro vaši aplikaci. Poskytuje úplné projevy pomáhá systému zjistěte to. 

-----

 * [Přehled](cognitive-services-custom-speech-home.md)
 * [Spuštění](cognitive-services-custom-speech-get-started.md)
 * [Glosář](cognitive-services-custom-speech-glossary.md)
