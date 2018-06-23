---
title: Nejčastější dotazy pro převod řeči na Text služby v Azure | Microsoft Docs
description: Tady najdete odpovědi na otázky nejoblíbenější o převod řeči na Text.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: fa25f16d3ef7ee8c7ec4c0c9ee17ccc89e8c52a2
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321852"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Vlastní rozpoznávání řeči služby nejčastější dotazy

Pokud nenajdete odpovědi na otázky v tohoto článku, zkuste žádostí služby řeči vlastní komunity na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) a [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Obecné

**Otázka**: Jaký je rozdíl mezi základní a vlastní řeči na Text modely?

**Odpověď**: modely směrného plánu byla trénink na data ve vlastnictví společnosti Microsoft a jsou už nasazené v cloudu. Vlastní modely povolit uživatelům přizpůsobit model, aby lépe vyhovoval konkrétní prostředí s konkrétní vedlejším šumu a jazyka. Objekt pro vytváření podlah, aut, aktivní ulice by vyžadovaly přizpůsobena akustickými modelu, zatímco konkrétní oblastech, jako je biologie, fyziky, radiologie, názvy produktů a vlastní režim by vyžadovaly jazyk modelu.

**Otázka**: kde mám začít Pokud chcete použít model směrného plánu?

**Odpověď**: nejprve je nutné získat [klíč předplatného](get-started.md). Pokud chcete provádět volání REST modely predeployed směrného plánu, projděte si [podrobnosti zde](rest-apis.md). Pokud chcete použít objekty WebSockets stahování [SDK](speech-sdk.md)

**Otázka**: do I vždy potřebujete k vytvoření vlastních řeči modelu?

**Odpověď**: Ne, pokud vaše aplikace používá obecné jazyk den bez vlastní slovník nebo že výjimečných potom možná není nutné k přizpůsobení modelu. Kromě toho, pokud je vaše aplikace, který se má použít v prostředí, kde je hluku na pozadí nebo téměř žádné pak ho použít nechcete muset přizpůsobit buď. Na portálu umožňuje uživatelům nasadit standardní hodnoty a vlastní modely a spuštění testů přesnost je. Uživatelé mohou pomocí této funkce lze měřit přesnost směrného plánu vs vlastní modelu.

**Otázka**: Jak poznám, že po dokončení zpracování Moje sady dat nebo model?

**Odpověď**: v současné době stav modelu nebo sady dat v tabulce je jenom chcete vědět.
Po dokončení zpracování bude stav "Připravena".

**Otázka**: můžete vytvořit více než jeden model v čase?

**Odpověď**: není omezen na tom, kolik modely jsou v kolekci, ale současně na každé stránce lze vytvořit pouze jeden.
Pokud ve fázi procesu je aktuálně jazyk modelu, například nelze spustit proces vytvoření modelu jazyka.
Můžete však mít akustickými modelu a jazykový model zpracování ve stejnou dobu. 

**Otázka**: I uvědomili si I se jedná o chybu. Jak zrušit Moje importu dat nebo model vytváření, který je v průběhu? 

**Odpověď**: aktuálně není možné vrátit zpět přizpůsobení procesu acoustic a jazyka.
Po dokončení importu lze odstranit importovaných dat

**Otázka**: Jaký je rozdíl mezi hledání & diktování modely a konverzačního modely?

**Odpověď**: existují dva základní akustickými & jazyk modely zvolit ve službě řeči vlastní.
vyhledávací dotazy nebo diktování. Microsoft Conversational AM jsou vhodné pro rozpoznávání řeči používaný v konverzačního stylu.
Tento typ řeči se obvykle přesměruje na jinou osobu, například v centrech volání nebo schůzek.

**Otázka**: můžete aktualizovat mé existující model (model překrývání)?

**Odpověď**: existující modely nelze aktualizovat. Vyřešit kombinovat původní datové sady s novým a readapt.

Staré a nové sady dat musí být zadán společně v jednom .zip (je-li akustickými data) nebo soubor .txt, pokud je jazyk data. Po přizpůsobení se provádí nové aktualizovaném modelu, musí být nasazené zrušte získat nový koncový bod

**Otázka**: Co když je potřeba souběžnosti vyšší než je výchozí hodnota nebo co je k dispozici v portálu. 

**Odpověď**: můžete postupně škálovat modelu v přírůstcích po 20 souběžnými požadavky. 

Pokud požadujete vyšší škálování, kontaktujte nás.

**Otázka**: lze stáhnout Moje modelu a spustit místně?

**Odpověď**: modely nelze stáhnout a spustit místně.

**Otázka**: Moje žádosti o přihlášení?

**Odpověď**: máte možnost volby při vytváření nasazení vypnout trasování, které okamžiku žádné zvuk nebo transcriptions bude do protokolu. V opačném případě požadavky jsou obvykle protokolovány v Azure v zabezpečeném úložišti. Pokud máte další aspekty ochrany osobních údajů, které je mají zakázáno používat službu vlastní rozpoznávání řeči, obraťte se na jednu z kanály podpory.

## <a name="importing-data"></a>Import dat

**Otázka**: co je limit na velikosti datových sad? Proč? 

**Odpověď**: aktuální limit pro datové sady je 2 GB, z důvodu omezení velikosti souboru k odeslání protokolu HTTP. 

**Otázka**: můžete aby bylo možné nahrát větší textový soubor zip Moje textových souborů? 

**Odpověď**: Ne, aktuálně povolené jsou jenom soubory nekomprimované text.

**Otázka**: uvádí sestavu dat došlo k selhání utterances. Co je problém?

**Odpověď**: selhání nahrát 100 % utterences v souboru se nejedná o problém.
Nastaveného valná většina utterances ve model acoustic nebo jazyk data (například > 95 %) úspěšně importovat, datová sada může být použitelná. Doporučuje se však pokusí pochopit, proč se nepodařilo utterances a opravit problémy. Nejběžnější problémy, například chyby, formátování lze snadno opravit. 

## <a name="creating-am"></a>Vytváření AM

**Otázka**: množství dat, akustickými potřebuji?

**Odpověď**: doporučujeme začít s 30 minut na jednu hodinu akustickými dat

**Otázka**: Jaká data shromažďovat?

**Odpověď**: shromažďování dat, který je co nejblíže aplikační scénář a případ použití míře.
Shromažďování dat by měl odpovídat cílová aplikace a uživatelé z hlediska zařízení nebo zařízení, prostředí a typy mluvčí. Obecně platí by měl shromažďovat data z jako široký rozsah mluvčí nejblíže. 

**Otázka**: jak se mají shromažďovat? 

**Odpověď**: můžete vytvořit samostatná dat kolekce aplikace nebo používat některé vypnout police záznamu zvuku softwaru.
Můžete také vytvořit verzi aplikace, který protokoluje zvuk data a použije tento. 

**Otázka**: muset transcribe mne přizpůsobení dat? 

**Odpověď**: data musí být přepsána. Můžete transcribe sami nebo použít službu professional přepis. Některé z těchto přepisovatelé použití a ostatní uživatele pomocí crowdsourcingu.

**Otázka**: jak dlouho trvá pro vytvoření vlastní akustickými modelu?

**Odpověď**: doba zpracování pro vytvoření vlastní akustickými modelu se o stejný jako délka akustickými datové sady.
Ano, přizpůsobené akustickými model vytvořený z datové sady pět hodin bude trvat přibližně pět hodin procesu. 

## <a name="offline-testing"></a>Testování offline

**Otázka**: je možné provést offline testování Mé vlastní akustickými model použití vlastní jazyk modelu?

**Odpověď**: Ano, stačí vybrat vlastní jazyk modelu v rozevíracím seznamu při nastavování offline testu

**Otázka**: je možné provést offline testování Mé vlastní jazykový model použití vlastní akustickými modelu?

**Odpověď**: Ano, stačí vybrat vlastní akustickými modelu v rozevírací nabídce při nastavování offline test.

**Otázka**: co je míra chyb aplikace Word a jak je položka počítaný?

**Odpověď**: vyhodnocení Metrika pro rozpoznávání řeči je míra chyb aplikace Word. Je považován za celkový počet chyb, která zahrnuje vložení, odstranění a nahrazení, dělený celkový počet slova v přepis odkaz.

**Otázka**: Jak zjistím, pokud je vhodný pro výsledky test přesnost?

**Odpověď**: Zobrazit výsledky porovnání mezi modelem směrného plánu a ten, který jste si přizpůsobili.
Zaměřena porazit modelu směrného plánu aby smysl přizpůsobení

**Otázka**: jak I zjistěte WER základní modelů, zobrazují se, pokud byl zlepšování? 

**Odpověď**: výsledky testů offline zobrazit přesnost směrného plánu přesnost vlastní modelu a zkvalitňování prostřednictvím standardních hodnot

## <a name="creating-lm"></a>Vytváření LM

**Otázka**: kolik textová data je nutné odeslat?

**Odpověď**: závisí na různých termínů a frází použít v aplikaci z výchozí jazyk modelů. Pro všechny nové slova je užitečné příklady tolik o použití těchto slova. Pro běžné frází, které se používají ve vaší aplikaci včetně frází v datech jazyk je užitečné také jako tento řekne systému naslouchat také tyto podmínky. Je běžné mít alespoň jeden 100 a obvykle několik set utterances v datové sadě jazyk nebo více. Také pokud se očekává, že některé typy dotazů být častější než jiné, můžete vložit více kopií běžné dotazy v datové sadě.

**Otázka**: Mohu právě načíst seznam slov?

**Odpověď**: odesílání seznam slov bude získat slova do slovníku ale není naučit systému obvykle použití slova.
Tím, že poskytuje úplné nebo částečné utterances (věty či fráze věcí, které budou uživatelé k vyslovení) jazykový model další nové slova a jak, se používají. Vlastní jazyk modelu je vhodný, nejen pro získávání nových slov v systému, ale také pro úpravu pravděpodobnost známé slova pro vaši aplikaci. Poskytování úplné utterances pomáhá další ebtter systému. 

## <a name="next-steps"></a>Další postup

* [Řešení potíží](troubleshooting.md)
* [Poznámky k verzi](releasenotes.md)