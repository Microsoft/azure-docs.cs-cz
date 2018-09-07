---
title: Nejčastější dotazy k převodu řeči na Text služby v Azure
description: Získejte odpovědi na oblíbené otázky o převod řeči na Text služby.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 31515d6867fc5524df1b081932dd2a28b0cf989c
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022154"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Převod řeči na Text – nejčastější dotazy

Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, projděte si [další možnosti podpory](support.md).

## <a name="general"></a>Obecné

**Otázka: co je rozdíl mezi modelem směrného plánu a custom Speech Text modelu?**

**A**: základní model vyškolila pomocí dat ve vlastnictví společnosti Microsoft a je už nasazená v cloudu.  Vlastní model můžete upravit model, aby lépe vyhovoval konkrétní prostředí, který má konkrétní hluk v jakémkoli jazyce. Výrobní prostory, dopravní, autech nebo na hlučného ulice by vyžadovaly upravit akustický model. Témata, jako je biologie, fyzika, radiologie, názvy produktů a vlastní zkratky by vyžadovaly upravit jazykový model.

**Otázka: kde můžu začít Pokud chci používat model směrného plánu?**

**A**: nejprve získejte [klíč předplatného](get-started.md). Pokud chcete provést volání REST pro predeployed základní modely, najdete v článku [rozhraní REST API](rest-apis.md). Pokud chcete použít objekty Websocket, [stáhnout sadu SDK](speech-sdk.md).

**Otázka: vždy potřebuji k sestavení vlastního modelu řeči?**

**A**: Ne. Pokud vaše aplikace používá Obecné, každodenní jazyka, nepotřebujete k přizpůsobení modelu. Pokud vaše aplikace je používána v prostředí, pokud neexistuje žádné nebo téměř žádné šum na pozadí, není nutné k přizpůsobení modelu. 

Můžete nasadit standardní hodnoty a vlastní modely na portálu a potom spustit testy přesnosti před nimi. Tato funkce slouží k měření přesnost modelu směrného plánu a vlastního modelu.

**Otázka: Jak poznám, že po dokončení zpracování pro datovou sadu nebo model?**

**A**: v současné době stav modelu nebo datovou sadu v tabulce je jediný způsob, jak zjistit. Po dokončení zpracování se stav **Succeeded**.

**Dotaz: lze vytvořit více než jeden model?**

**A**: neexistuje žádné omezení počtu modely můžete mít v kolekci.

**Otázka: nemůžu uvědomili si, že mám udělali chybu. Jak zrušit import dat nebo model vytváření probíhající?**

**A**: v současné době není možné vrátit zpět procesu přizpůsobení akustických a v jakémkoli jazyce. Když jsou ve stavu terminálu, mohou odstranit importovaných dat a modely.

**D: Jaký je rozdíl mezi modelem vyhledávání a diktování a konverzační modelu?**

**A**: můžete si vybrat z více než jeden model směrného plánu ve službě řeči. Konverzační model je užitečný pro rozpoznávání řeči, který je slyšet ve stylu konverzační. Tento model je ideální pro přepisování telefonních hovorů. Model hledání a diktování je ideální pro aplikace aktivované hlasu. Univerzální model je nový model, který řeší oba scénáře.

**Dotaz: lze aktualizovat mé existující model (model překrývání)?**

**A**: nelze aktualizovat existující model. Jako řešení kombinovat původní datové sady s novou datovou sadu a readapt.

Původní datové sady a nová datová sada musí zkombinovat do jednoho souboru .zip (pro akustických dat) nebo v souboru .txt (pro jazyk data). Po dokončení adaptace modelu nové a aktualizované je potřeba znovu nasadit k získání nového koncového bodu

**Otázka: Co když budu potřebovat vyšší souběžnosti pro moje nasazený model než nabízejí na portálu?** 

**A**: můžete vertikálně navýšit kapacitu váš model v přírůstcích po 20 souběžných požadavků. 

Pokud budete potřebovat větší měřítko, kontaktujte nás.

**Dotaz: lze stáhnout Moje modelu a spustit místně?**

**A**: modely nelze stáhnout a spustit místně.

**Otázka: jsou moje žádosti o přihlášení?**

**A**: máte možnost volby, když vytvoříte nasazení vypnutí trasování. V tu chvíli se žádné zvuk nebo přepisů, budou zaznamenány. V opačném případě požadavků obvykle přihlášení Azure v zabezpečeném úložišti. 

Pokud máte další aspekty ochrany osobních údajů, které neumožňují pomocí custom Speech service, obraťte se na jednu z kanály podpory.

## <a name="importing-data"></a>Importing data

**Otázka: co je omezení velikosti datové sady a proč je limit?**

**A**: aktuální limit pro datové sady je 2 GB. Limit je kvůli omezení velikosti souboru pro nahrání protokolu HTTP. 

**Otázka: zip Moje textové soubory, tak můžu nahrát větší textový soubor?** 

**A**: Ne. V současné době jsou povoleny pouze nekomprimované textové soubory.

**Otázka: data sestavy uvádí, že došlo k selhání projevy. V čem je problém?**

**A**: selhání nahrání 100 procent projevy v souboru nepředstavuje žádný problém. Pokud většinu projevy v akustických a v jakémkoli jazyce datová sada služby (pro příklad, více než 95 %) jsou úspěšně importovány, může být použitelné datové sady. Doporučujeme však, že pokusíte zjistit, proč se nezdařilo projevy a opravit problémy. Nejčastější problémy, například formátování chyby, se snadno to vyřešíme. 

## <a name="creating-an-acoustic-model"></a>Vytváření akustický model

**Otázka: kolik akustických dat budu potřebovat?**

**A**: doporučujeme začít se 30 minut až jednu hodinu akustických dat.

**D: Jaká data shromažďuje**

**A**: shromažďovat data, která je nejblíž scénář aplikací a případ použití nejvíce. Shromažďování dat by měl odpovídat cílové aplikace a uživatele z hlediska zařízení nebo zařízení, prostředí a typy mluvčích. Obecně platí by měly shromažďovat data z co nejširším rozsahu mluvčích, jako je to možné. 

**Otázka: Jak mohu shromažďovat akustických dat?**

**A**: můžete vytvořit samostatné aplikace kolekci dat nebo použít softwaru u zvukového záznamu. Můžete také vytvořit verzi aplikace, který se přihlásí zvukových dat a pak používá data. 

**Otázka: je potřeba přepisy přizpůsobení dat ručně?**

**A**: Ano! Můžete přepisy sami nebo použít službu profesionální určené k transkripci. Někteří uživatelé dávají přednost přepisovatelé a ostatních pomocí crowdsourcingu nebo proveďte přepisů sami.

## <a name="accuracy-testing"></a>Testování přesnosti

**Dotaz: lze provést offline testování Moje vlastního akustického modelu s použitím vlastního jazykového modelu?**

**A**: Ano, stačí vybrat vlastní jazyk modelu v rozevírací nabídce při nastavování testů offline.

**Dotaz: lze provést offline testování Moje vlastních jazykových modelů pomocí vlastního akustického modelu?**

**A**: Ano, stačí vybrat vlastního akustického modelu v rozevírací nabídce při nastavování testů offline.

**Otázka: co je míra chyb aplikace word (zasílání) a jak se počítá?**

**A**: zasílání je metrika hodnocení pro rozpoznávání řeči. Zasílání se počítá jako celkový počet chyb, která zahrnuje vložení, odstranění a nahrazení rozdělené podle celkového počtu slov ve přepis odkaz. Další informace najdete v tématu [míra chyb slovo](https://en.wikipedia.org/wiki/Word_error_rate).

**Dotaz: jak určit, zda jsou dobré výsledky testu přesnost?**

**A**: ve výsledcích zobrazí porovnání mezi základní model a model, který jste si přizpůsobili. Bychom se měli snažit beat základního modelu provádět přizpůsobení smysl.

**Otázka: jak se tak, že se zobrazí, pokud bylo zlepšení určit zasílání základního modelu?** 

**A**: výsledky testů offline zobrazit základní přesnost vlastního modelu a vylepšování prostřednictvím standardních hodnot.

## <a name="creating-a-language-model"></a>Vytváří se jazykový model

**Otázka: kolik textová data je nutné odeslat?**

**A**: závisí na různých slovníku a frází ve vaší aplikaci využívá pocházejí z výchozí jazykové modely. Pro všechny nové slova je užitečný k zadání počtu příklady nejvíce využití těchto slov. Běžné vět, které se používají ve vaší aplikaci včetně fráze v datech jazyk je také užitečné protože říká systému také naslouchat tyto podmínky. Je běžné mít alespoň 100 a obvykle několik stovek nebo více projevy v datové sadě jazyka. Navíc pokud se očekává, že některé typy dotazů být častější než jiné, můžete vložit více kopií běžných dotazů v datové sadě.

**Otázka: Mohu právě načíst seznam slov?**

**A**: nahrávání seznam slov slova přidá do slovníku, ale to nebude představuje systém obvykle používání slova. Poskytnutím celé nebo jeho část projevy (větách nebo frázích, které uživatelé můžou Řekněme, že) jazykový model další nové slova a způsob jejich použití. Vlastní jazykový model je vhodný, nejen pro přidání nových slov k systému, ale také pro úpravu pravděpodobnost známé slova pro vaši aplikaci. Poskytuje úplné projevy pomáhá systému získat lepší informace. 

## <a name="next-steps"></a>Další postup

* [Řešení potíží](troubleshooting.md)
* [Poznámky k verzi](releasenotes.md)
