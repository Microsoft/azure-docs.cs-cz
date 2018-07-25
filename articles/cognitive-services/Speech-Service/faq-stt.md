---
title: Nejčastější dotazy k převodu řeči na Text služby v Azure | Dokumentace Microsoftu
description: Tady najdete odpovědi na Nejoblíbenější dotazy týkající se převod řeči na Text.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 543e8d6fb68a351dfe75c962debaf15eeb080a3f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223883"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Převod řeči na Text – nejčastější dotazy

Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, zkuste se zeptat komunity služby Custom Speech Service na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) a [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Obecné

**Dotaz**: Jaký je rozdíl mezi základní a vlastní převod řeči na Text modely?

**Odpověď**: základní modely byly školení s Microsoftem vlastní data a jsou už nasazené v cloudu. Vlastní modely umožňují uživatelům přizpůsobit model, aby lépe vyhovovaly konkrétním prostředí pomocí konkrétní hluk nebo jazyka. Výrobní prostory, dopravní, auta, hlučného ulice by vyžadovaly upravit akustický model, že určitá témata, jako je například biologie, fyzika, radiologie, názvy produktů a vlastní zkratky by vyžadovaly upravit jazykový model.

**Dotaz**: kdy začnu Pokud chci používat model směrného plánu?

**Odpověď**: nejdřív je potřeba získat [klíč předplatného](get-started.md). Pokud chcete provést volání REST pro predeployed základní modely, obraťte se [podrobnosti tady](rest-apis.md). Pokud chcete použít objekty Websocket stahování [SDK](speech-sdk.md)

**Dotaz**: to vždy muset sestavit vlastního modelu řeči?

**Odpověď**: Ne, pokud vaše aplikace používá obecné jazyk každodenní potom není potřeba přizpůsobení modelu. Kromě toho, pokud vaše aplikace se používá v prostředí, kde je šum na pozadí nebo vůbec žádné potom není potřeba buď přizpůsobit. Na portálu umožňuje uživatelům nasadit standardní hodnoty a vlastní modely a spustit testy přesnosti před nimi. Uživatele můžete použít tuto funkci k měření přesnost vs směrného plánu vlastního modelu.

**Dotaz**: Jak zjistím, že po dokončení zpracování sady dat nebo model?

**Odpověď**: v současné době stav modelu nebo sadu dat v tabulce je jediný způsob, jak zjistit.
Po dokončení zpracování se stav bude mít "bylo dokončeno".

**Dotaz**: můžete vytvořit více než jeden model?

**Odpověď**: neexistuje žádné omezení počtu modely jsou ve vaší kolekci.

**Dotaz**: Mohu uvědomili si můžu udělali chybu. Jak zrušit import dat nebo model vytváření probíhající? 

**Odpověď**: aktuálně není možné vrátit zpět procesu přizpůsobení akustických a v jakémkoli jazyce. Importovaná data a modely můžete odstranit až do konečného stavu.

**Dotaz**: Jaký je rozdíl mezi hledání & diktování modely a konverzační modely?

**Odpověď**: existuje více než jedna základní modely na výběr v Speech Service. Konverzační model je vhodný pro rozpoznávání řeči, kterým se mluví ve stylu konverzační. Tento model by byla ideální pro přepisování volání při vyhledávání a je ideální pro aplikace aktivované hlasem diktování. Univerzální je nový model, který řeší oba scénáře.

**Dotaz**: můžete aktualizovat mé existující model (model překrývání)?

**Odpověď**: nelze aktualizovat stávající modely. Jako alternativní řešení kombinovat původní datové sady s novými a readapt.

Staré a nové datové sady musí zkombinovat v jedné ZIP (je-li akustických dat) nebo soubor .txt, pokud je jazyk data. Po přizpůsobení se provádí nové aktualizace modelu musí být zrušit nasazené získat nový koncový bod

**Dotaz**: Co když budu potřebovat vyšší souběžnosti pro moje nasazený model než nabízejí na portálu. 

**Odpověď**: můžete vertikálně navýšit kapacitu váš model v přírůstcích po 20 souběžných požadavků. 

Pokud potřebujete škálování ve větším měřítku, kontaktujte nás.

**Dotaz**: můžu my model stáhnout a spustit místně?

**Odpověď**: modely nelze stáhnout a spustit místně.

**Dotaz**: jsou moje žádosti o přihlášení?

**Odpověď**: máte možnost volby při vytváření nasazení vypnutí trasování, nezůstane žádný zvukový nebo zaprotokoluje se přepisy. Jinak požadavky jsou obvykle protokoluje v Azure v zabezpečeném úložišti. Pokud máte další aspekty ochrany osobních údajů, které můžete používání služby Custom Speech Service, obraťte se na jednu z kanály podpory.

## <a name="importing-data"></a>Import dat

**Dotaz**: Jaký je limit velikosti datové sady? Proč? 

**Odpověď**: aktuální limit pro datové sady je 2 GB, kvůli omezení velikosti souboru pro nahrání protokolu HTTP. 

**Dotaz**: můžete k nahrání větší textový soubor zip Moje textové soubory? 

**Odpověď**: Ne, aktuálně jsou povoleny pouze nekomprimované textové soubory.

**Dotaz**: říká dat sestavy došlo k selhání projevy. V čem je problém?

**Odpověď**: selhání nahrání 100 % projevy v souboru nepředstavuje žádný problém.
Pokud většinu projevy a v jakémkoli jazyce akustických dat (například > 95 %) se úspěšně naimportovány, datová sada může být použitelné. Doporučuje se však pokusí zjistit, proč se nezdařilo projevy a opravit problémy. Nejčastější problémy, například formátování chyby, se snadno to vyřešíme. 

## <a name="creating-am"></a>Vytváření AM

**Dotaz**: kolik akustických dat musím udělat?

**Odpověď**: doporučujeme začít se 30 minut na jednu hodinu akustických dat.

**Dotaz**: Jaká data shromažďuje?

**Odpověď**: shromažďovat data, která je nejblíž scénář aplikací a případ použití nejvíce.
Shromažďování dat by měl odpovídat cílové aplikace a uživatele z hlediska zařízení nebo zařízení, prostředí a typy mluvčích. Obecně platí by měly shromažďovat data z co nejširším rozsahu mluvčích, jako je to možné. 

**Dotaz**: jak se mají shromažďovat? 

**Odpověď**: můžete vytvořit samostatné aplikace shromažďování dat nebo použít některé vypnout police záznam zvuků software.
Můžete také vytvořit verzi aplikace, která protokoluje zvukových dat a, který používá. 

**Dotaz**: Potřebuji přepisy přizpůsobení dat, mi připomínku? 

**Odpověď**: Ano! Můžete přepisy sami nebo použít službu profesionální určené k transkripci. Někteří uživatelé dávají přednost přepisovatelé, zatímco jiné pomocí crowdsourcingu nebo proveďte přepisů sami.

## <a name="accuracy-testing"></a>Testování přesnosti

**Dotaz**: provést offline testování Moje vlastního akustického modelu pomocí vlastního jazykového modelu?

**Odpověď**: Ano, stačí vybrat vlastní jazyk modelu v rozevíracím seznamu při nastavování offline testu

**Dotaz**: provést offline testování Moje vlastního jazykového modelu s použitím vlastního akustického modelu?

**Odpověď**: Ano, stačí vybrat vlastního akustického modelu v rozevírací nabídce při nastavování testů offline.

**Dotaz**: co je míra chyb aplikace Word (zasílání) a jak se počítá?

**Odpověď**: míra chyb aplikace Word (zasílání) je metrika hodnocení pro rozpoznávání řeči. Počítá se jako celkový počet chyb, která zahrnuje vložení, odstranění a nahrazení rozdělené podle celkového počtu slov ve přepis odkaz. Další podrobnosti najdete [tady](https://en.wikipedia.org/wiki/Word_error_rate).

**Dotaz**: Jak zjistím, pokud je dobré výsledky testu přesnost?

**Odpověď**: Výsledkem porovnání mezi modelem směrného plánu a jste si přizpůsobili.
Bychom se měli snažit beat základního modelu provádět přizpůsobení smysl.

**Dotaz**: Jak můžu přijít na to, míra chyb slovo základní modelů, proto se zobrazí, pokud bylo zlepšení? 

**Odpověď**: výsledky testů offline zobrazit přesnost přesnost směrného plánu vlastního modelu a vylepšování prostřednictvím standardních hodnot.

## <a name="creating-lm"></a>Vytváření LM

**Dotaz**: kolik textová data je potřeba nahrát?

**Odpověď**: závisí na různých slovníku a frází ve vaší aplikaci využívá pocházejí z výchozí jazykové modely. Pro všechny nové slova je užitečný k zadání počtu příklady nejvíce využití těchto slov. Běžné vět, které se používají ve vaší aplikaci včetně fráze v datech jazyk je také užitečné jako říká systému pro naslouchání i tyto podmínky. Je běžné mít alespoň 100 a obvykle několik stovek projevy v datové sadě jazyka nebo více. Také pokud se očekává, že některé typy dotazů být častější než jiné, můžete vložit více kopií běžných dotazů v datové sadě.

**Dotaz**: Mohu stačí načíst seznam slov?

**Odpověď**: nahrávání seznam slov, získejte slova do slovníku ale neučí systému, jak se obvykle používají slova.
Tím, že poskytuje úplné nebo částečné projevy (větách nebo frázích věcí, které uživatelé můžou Řekněme, že) jazykový model další nové slova a, jejich použití. Vlastní jazykový model je dobrým způsobem nejen pro získávání nových slov v systému, ale také pro úpravu pravděpodobnost známé slova pro vaši aplikaci. Poskytuje úplné projevy pomáhá systému získat lepší informace. 

## <a name="next-steps"></a>Další postup

* [Řešení potíží](troubleshooting.md)
* [Poznámky k verzi](releasenotes.md)
