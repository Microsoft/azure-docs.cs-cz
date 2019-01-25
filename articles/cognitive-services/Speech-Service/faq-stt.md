---
title: Nejčastější dotazy k převodu řeči na Text služby v Azure
titleSuffix: Azure Cognitive Services
description: Získejte odpovědi na oblíbené otázky o převod řeči na Text služby.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 21f7733f3f63fd4d7567830ab4c98ea1b04bf32e
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904127"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Převod řeči na Text – nejčastější dotazy

Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, projděte si [další možnosti podpory](support.md).

## <a name="general"></a>Obecné

**Otázka: Jaký je rozdíl mezi modelem základní a vlastní převod řeči na textový model?**

**A**: Základní model vyškolila pomocí dat ve vlastnictví společnosti Microsoft a je už nasazená v cloudu.  Vlastní model můžete upravit model, aby lépe vyhovoval konkrétní prostředí, který má konkrétní hluk v jakémkoli jazyce. Výrobní prostory, dopravní, autech nebo na hlučného ulice by vyžadovaly upravit akustický model. Témata, jako je biologie, fyzika, radiologie, názvy produktů a vlastní zkratky by vyžadovaly upravit jazykový model.

**Otázka: Kde mám začít Pokud chci používat model směrného plánu?**

**A**: Nejprve získejte [klíč předplatného](get-started.md). Pokud chcete provést volání REST pro predeployed základní modely, najdete v článku [rozhraní REST API](rest-apis.md). Pokud chcete použít objekty Websocket, [stáhnout sadu SDK](speech-sdk.md).

**Otázka: Vždy je potřeba sestavení vlastního modelu řeči?**

**A**: Ne. Pokud vaše aplikace používá Obecné, každodenní jazyka, nepotřebujete k přizpůsobení modelu. Pokud vaše aplikace je používána v prostředí, pokud neexistuje žádné nebo téměř žádné šum na pozadí, není nutné k přizpůsobení modelu.

Můžete nasadit standardní hodnoty a vlastní modely na portálu a potom spustit testy přesnosti před nimi. Tato funkce slouží k měření přesnost modelu směrného plánu a vlastního modelu.

**Otázka: Jak poznám, že po dokončení zpracování pro datovou sadu nebo model?**

**A**: Stav modelu nebo datové sady v tabulce v současné době je jediný způsob, jak zjistit. Po dokončení zpracování se stav **Succeeded**.

**Otázka: Můžete vytvořit více než jeden model?**

**A**: Neexistuje žádné omezení počtu modely, které máte v kolekci.

**Otázka: Můžu uvědomili si, že mám udělali chybu. Jak zrušit import dat nebo model vytváření probíhající?**

**A**: V současné době není možné vrátit zpět procesu přizpůsobení akustických a v jakémkoli jazyce. Když jsou ve stavu terminálu, mohou odstranit importovaných dat a modely.

**Otázka: Jaký je rozdíl mezi modelem vyhledávání a diktování a konverzační modelu?**

**A**: Můžete vybrat z více než jeden model směrného plánu ve službě řeči. Konverzační model je užitečný pro rozpoznávání řeči, který je slyšet ve stylu konverzační. Tento model je ideální pro přepisování telefonních hovorů. Model hledání a diktování je ideální pro aplikace aktivované hlasu. Univerzální model je nový model, který řeší oba scénáře. Univerzální model právě nebo vyšší úroveň kvality konverzační modelu ve většině národní prostředí.

**Otázka: Můžete aktualizovat mé existující model (model překrývání)?**

**A**: Nelze aktualizovat existující model. Jako řešení kombinovat původní datové sady s novou datovou sadu a readapt.

Původní datové sady a nová datová sada musí zkombinovat do jednoho souboru .zip (pro akustických dat) nebo v souboru .txt (pro jazyk data). Po dokončení adaptace modelu nové a aktualizované je potřeba znovu nasadit k získání nového koncového bodu

**Otázka: Pokud je dostupná nová verze směrného plánu je Moje nasazení automaticky aktualizuje?**

**A**: Nasazení není aktualizují automaticky.

Pokud máte upravit a nasadit model se standardními hodnotami V1.0, zůstane tohoto nasazení je. Zákazníci mají decommision nasazený model znovu přizpůsobit pomocí novější verze směrný plán a znovu nasadit.

**Otázka: Co když budu potřebovat vyšší souběžnosti pro moje nasazený model než nabízejí na portálu?**

**A**: Můžete vertikálně navýšit kapacitu váš model v přírůstcích po 20 souběžných požadavků.

Pokud budete potřebovat větší měřítko, kontaktujte nás.

**Otázka: Můžete stáhnout Moje modelu a spustit místně?**

**A**: Modely nelze stáhnout a spustit místně.

**Otázka: Jsou moje žádosti o přihlášení?**

**A**: Máte možnost volby, když vytvoříte nasazení vypnutí trasování. V tu chvíli se žádné zvuk nebo přepisů, budou zaznamenány. V opačném případě požadavků obvykle přihlášení Azure v zabezpečeném úložišti.

**Otázka: Jsou moje žádosti o omezený?**

**A**: Rozhraní REST API omezuje požadavky na 25 na 5 sekund. Podrobnosti najdete na naší stránce pro [převod řeči na text](speech-to-text.md).

Pokud máte další aspekty ochrany osobních údajů, které neumožňují pomocí custom Speech service, obraťte se na jednu z kanály podpory.

## <a name="importing-data"></a>Importing data

**Otázka: Co je omezení velikosti datové sady a proč je limit?**

**A**: Aktuální limit pro datové sady je 2 GB. Limit je kvůli omezení velikosti souboru pro nahrání protokolu HTTP. 

**Otázka: Můžete zip Moje textové soubory, tak můžu nahrát větší textový soubor?** 

**A**: Ne. V současné době jsou povoleny pouze nekomprimované textové soubory.

**Otázka: Data sestavy uvádí, že došlo k selhání projevy. V čem je problém?**

**A**: Selhání nahrání 100 procent projevy v souboru není problém. Pokud většinu projevy v akustických a v jakémkoli jazyce datová sada služby (pro příklad, více než 95 %) jsou úspěšně importovány, může být použitelné datové sady. Doporučujeme však, že pokusíte zjistit, proč se nezdařilo projevy a opravit problémy. Nejčastější problémy, například formátování chyby, se snadno to vyřešíme. 

## <a name="creating-an-acoustic-model"></a>Vytváření akustický model

**Otázka: Kolik akustických dat budu potřebovat?**

**A**: Doporučujeme začít se 30 minut až jednu hodinu akustických dat.

**Otázka: Jaká data shromažďuje**

**A**: Shromažďovat data, která je nejblíž scénář aplikací a případ použití nejvíce. Shromažďování dat by měl odpovídat cílové aplikace a uživatele z hlediska zařízení nebo zařízení, prostředí a typy mluvčích. Obecně platí by měly shromažďovat data z co nejširším rozsahu mluvčích, jako je to možné. 

**Otázka: Jak se mají shromažďovat akustických dat?**

**A**: Můžete vytvořit samostatné aplikace kolekci dat nebo použít softwaru u zvukového záznamu. Můžete také vytvořit verzi aplikace, který se přihlásí zvukových dat a pak používá data. 

**Otázka: Je potřeba přepisy přizpůsobení dat ručně?**

**A**: Ano! Můžete přepisy sami nebo použít službu profesionální určené k transkripci. Někteří uživatelé dávají přednost přepisovatelé a ostatních pomocí crowdsourcingu nebo proveďte přepisů sami.

## <a name="accuracy-testing"></a>Testování přesnosti

**Otázka: Můžete provést offline testování Moje vlastního akustického modelu s použitím vlastního jazykového modelu?**

**A**: Ano, stačí vyberte vlastní jazyk modelu v rozevírací nabídce při nastavování testů offline.

**Otázka: Můžete provádět testování offline Moje vlastního jazykového modelu s použitím vlastního akustického modelu?**

**A**: Ano, stačí vyberte vlastního akustického modelu v rozevírací nabídce při nastavování testů offline.

**Otázka: Co je míra chyb aplikace word (zasílání) a jak se počítá?**

**A**: Zasílání je metrika hodnocení pro rozpoznávání řeči. Zasílání se počítá jako celkový počet chyb, která zahrnuje vložení, odstranění a nahrazení rozdělené podle celkového počtu slov ve přepis odkaz. Další informace najdete v tématu [míra chyb slovo](https://en.wikipedia.org/wiki/Word_error_rate).

**Otázka: Jak zjistím, zda jsou výsledky testu přesnost dobré?**

**A**: Ve výsledcích zobrazí porovnání mezi základní model a model, který jste si přizpůsobili. Bychom se měli snažit beat základního modelu provádět přizpůsobení smysl.

**Otázka: Jak zjistím zasílání základní model, takže se zobrazí, pokud bylo zlepšení?** 

**A**: Výsledky testů offline zobrazit základní přesnost vlastního modelu a vylepšování nad směrného plánu.

## <a name="creating-a-language-model"></a>Vytváří se jazykový model

**Otázka: Kolik dat textu je nutné odeslat?**

**A**: To závisí na různých slovníku a frází ve vaší aplikaci využívá pocházejí z výchozí jazykové modely. Pro všechny nové slova je užitečný k zadání počtu příklady nejvíce využití těchto slov. Běžné vět, které se používají ve vaší aplikaci včetně fráze v datech jazyk je také užitečné protože říká systému také naslouchat tyto podmínky. Je běžné mít alespoň 100 a obvykle několik stovek nebo více projevy v datové sadě jazyka. Navíc pokud se očekává, že některé typy dotazů být častější než jiné, můžete vložit více kopií běžných dotazů v datové sadě.

**Otázka: Mohu jen seznam slov načíst?**

**A**: Odesílání seznamu slov slova přidá do slovníku, ale to nebude představuje systém obvykle používání slova. Poskytnutím celé nebo jeho část projevy (větách nebo frázích, které uživatelé můžou Řekněme, že) jazykový model další nové slova a způsob jejich použití. Vlastní jazykový model je vhodný, nejen pro přidání nových slov k systému, ale také pro úpravu pravděpodobnost známé slova pro vaši aplikaci. Poskytuje úplné projevy pomáhá systému získat lepší informace. 

## <a name="next-steps"></a>Další postup

* [Řešení potíží](troubleshooting.md)
* [Poznámky k verzi](releasenotes.md)
