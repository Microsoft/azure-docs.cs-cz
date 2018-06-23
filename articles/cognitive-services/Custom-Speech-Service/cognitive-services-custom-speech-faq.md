---
title: Nejčastější dotazy pro službu vlastní řeči v Azure | Microsoft Docs
description: Tady najdete odpovědi na nejoblíbenější otázky týkající se služby řeči vlastní.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 11/21/2016
ms.author: panosper
ms.openlocfilehash: a929869b36387b3257b672308ceca36c84ff8cae
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342523"
---
# <a name="custom-speech-service-frequently-asked-questions"></a>Vlastní rozpoznávání řeči služby nejčastější dotazy

Pokud nenajdete odpovědi na otázky v tohoto článku, zkuste žádostí služby řeči vlastní komunity na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) a [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Obecné

**Otázka**: Jak poznám, že po dokončení zpracování Moje sady dat nebo model?

**Odpověď**: v současné době stav modelu nebo sady dat v tabulce je jenom chcete vědět.
Po dokončení zpracování bude stav "Připravena".
Pracujeme na vylepšené metody pro komunikaci zpracování stavu, jako je například e-mailové oznámení.

**Otázka**: můžete vytvořit více než jeden model v čase?

**Odpověď**: není omezen na tom, kolik modely jsou v kolekci, ale současně na každé stránce lze vytvořit pouze jeden.
Pokud ve fázi procesu je aktuálně jazyk modelu, například nelze spustit proces vytvoření modelu jazyka.
Můžete však mít akustickými modelu a jazykový model zpracování ve stejnou dobu. 

**Otázka**: I uvědomili si I se jedná o chybu. Jak zrušit Moje importu dat nebo model vytváření, který je v průběhu? 

**Odpověď**: aktuálně není možné vrátit zpět acoustic nebo jazyk přizpůsobení procesu.
Po dokončení importu lze odstranit importovaných dat

**Otázka**: Jaký je rozdíl mezi hledání & diktování modely a konverzačního modely?

**Odpověď**: existují dva základní akustickými & jazyk modely zvolit ve službě řeči vlastní.
vyhledávací dotazy nebo diktování. Microsoft Conversational AM jsou vhodné pro rozpoznávání řeči používaný v konverzačního stylu.
Tento typ řeči se obvykle přesměruje na jinou osobu, například v centrech volání nebo schůzek.

**Otázka**: můžete aktualizovat mé existující model (model překrývání)?

**Odpověď**: nenabízíme schopnosti aktualizovat existující model nová data.
Pokud máte novou sadu dat a chcete přizpůsobit existující model se musí znovu upravit s nová data a původní datové sady, který jste použili.
Staré a nové sady dat musí lze spojit do jednoho .zip (je-li akustickými data) nebo soubor .txt. Pokud je jazyk data po přizpůsobení se provádí nové aktualizovaném modelu, musí být nasazené zrušte získat nový koncový bod

**Otázka**: Co když je potřeba souběžnosti vyšší než je výchozí hodnota. 

**Odpověď**: můžete postupně škálovat modelu v přírůstcích po 5 souběžných požadavků, které říkáme jednotek škálování. Jednotlivé jednotky škálování zaručuje, že váš model současně zpracovávat 5 zvukový stream. Můžete zakoupit 100 jednotek škálování (nebo 500 souběžných požadavků).

Kontaktujte nás Pokud požadujete vyšší než.

**Otázka**: lze stáhnout Moje modelu a spustit místně?

**Odpověď**: jsme nepovolujte modely, které je možné stáhnout a spustit místně.

**Otázka**: Moje žádosti o přihlášení?

**Odpověď**: máte možnost volby při vytváření nasazení vypnout trasování, které okamžiku žádné zvuk nebo transcriptions bude do protokolu. V opačném případě požadavky jsou obvykle protokolovány v Azure v zabezpečeném úložišti. Pokud máte další aspekty ochrany osobních údajů, které je mají zakázáno používat službu řeči vlastní prosím kontaktujte nás.

## <a name="importing-data"></a>Import dat

**Otázka**: co je limit na velikosti datových sad? Proč? 

**Odpověď**: aktuální limit pro datové sady je 2 GB, z důvodu omezení velikosti souboru k odeslání protokolu HTTP. 

**Otázka**: můžete aby bylo možné nahrát větší textový soubor zip Moje textových souborů? 

**Odpověď**: Ne, aktuálně povolené jsou jenom soubory nekomprimované text.

**Otázka**: uvádí sestavu dat došlo k selhání utterances. Se tento problém?

**Odpověď**: Pokud jenom pár utterances se nepodařilo úspěšně importovat, se nejedná o problém.
Nastaveného valná většina utterances ve model acoustic nebo jazyk data (například > 95 %) úspěšně importovat, datová sada může být použitelná. Doporučuje se však pokusí pochopit, proč se nepodařilo utterances a opravit problémy.
Nejběžnější problémy, například chyby, formátování lze snadno opravit. 

## <a name="creating-am"></a>Vytváření AM

**Otázka**: množství dat, akustickými potřebuji?

**Odpověď**: doporučujeme začít s 30 minut na jednu hodinu akustickými dat

**Otázka**: Jaký způsob data shromažďovat?

**Odpověď**: doporučujeme shromažďování dat, který je co nejblíže aplikační scénář a případ použití nejdříve.
To znamená, že shromažďování dat by měl odpovídat cílová aplikace a uživatelé z hlediska zařízení nebo zařízení, prostředí a typy mluvčí. Obecně platí by měl shromažďovat data z jako široký rozsah mluvčí nejblíže. 

**Otázka**: jak se mají shromažďovat? 

**Odpověď**: můžete vytvořit samostatná dat kolekce aplikace nebo používat některé vypnout police záznamu zvuku softwaru.
Můžete také vytvořit verzi aplikace, který protokoluje zvuk data a použije tento. 

**Otázka**: muset transcribe mne přizpůsobení dat? 

**Odpověď**: data musí být přepsána. Můžete transcribe sami nebo použít službu professional přepis. Některé z těchto přepisovatelé použití a ostatní uživatele pomocí crowdsourcingu. Můžete také doporučujeme přepis služba na vyžádání.

**Otázka**: jak dlouho trvá pro vytvoření vlastní akustickými modelu?

**Odpověď**: doba zpracování pro vytvoření vlastní akustickými modelu se o stejný jako délka akustickými datové sady.
Ano, přizpůsobené akustickými model vytvořený z datové sady pět hodin bude trvat přibližně pět hodin procesu. 

## <a name="offline-testing"></a>Testování offline

**Otázka**: je možné provést offline testování Mé vlastní akustickými model použití vlastní jazyk modelu?

**Odpověď**: Ano, stačí vybrat vlastní jazyk modelu v rozevíracím při nastavení offline testu

**Otázka**: je možné provést offline testování Mé vlastní jazykový model použití vlastní akustickými modelu?

**Odpověď**: Ano, stačí vybrat vlastní akustickými modelu v rozevírací nabídce při nastavování offline test.

**Otázka**: co je míra chyb aplikace Word a jak je položka počítaný?

**Odpověď**: vyhodnocení Metrika pro rozpoznávání řeči je míra chyb aplikace Word. Je považován za celkový počet chyb, která zahrnuje vložení, odstranění a nahrazení, dělený celkový počet slova v přepis odkaz.

**Otázka**: I nyní vědět výsledky testů Mé vlastní modelu, je to dobrý nebo chybné číslo?

**Odpověď**: Zobrazit výsledky porovnání mezi modelem směrného plánu a ten, který jste si přizpůsobili.
Zaměřena porazit modelu směrného plánu aby smysl přizpůsobení

**Otázka**: jak I zjistěte WER základní modelů, zobrazují se, pokud byl zlepšování? 

**Odpověď**: výsledky testů offline zobrazit přesnost směrného plánu přesnost vlastní modelu a zkvalitňování prostřednictvím standardních hodnot

## <a name="creating-lm"></a>Vytváření LM

**Otázka**: kolik textová data je nutné odeslat?

**Odpověď**: Toto je obtížné otázku k na přesné odpovídaly, závisí na tom, jak různých termínů a frází použít v aplikaci jsou z výchozí jazyk modelů. Pro všechny nové slova je užitečné příklady tolik o použití těchto slova. Pro běžné frází, které se používají ve vaší aplikaci včetně těch, které v datech jazyk je užitečné také jako tento řekne systému naslouchat také tyto podmínky.
Je běžné nejméně 100 a obvykle několik set utterances v datové sadě jazyk nebo více.
Také pokud určité typy * z očekávány častější než jiné dotazy, můžete vložit více kopií běžné dotazy v datové sadě.

**Otázka**: Mohu právě načíst seznam slov?

**Odpověď**: odesílání seznam slov bude získat slova do slovníku ale není naučit systému obvykle použití slova.
Tím, že poskytuje úplné nebo částečné utterances (věty či fráze věcí, které budou uživatelé k vyslovení) jazykový model další nové slova a způsobu jejich použití. Vlastní jazyk modelu je vhodný, nejen pro získávání nových slov v systému, ale také pro úpravu pravděpodobnost známé slova pro vaši aplikaci. Poskytování úplné utterances pomáhá to další systému. 

-----

 * [Přehled](cognitive-services-custom-speech-home.md)
 * [Spuštění](cognitive-services-custom-speech-get-started.md)
 * [Glosář](cognitive-services-custom-speech-glossary.md)
