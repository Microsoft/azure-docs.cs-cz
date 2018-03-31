---
title: Azure Mobile Engagement implementace pro mediální aplikace
description: Scénář aplikace média k implementaci Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 48201cc8-4e04-485c-a8dc-d6406d23f3ed
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8e5d4585b47a4ca26b81b7168aa499f25722415a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="implement-mobile-engagement-with-media-app"></a>Implementace s mediální aplikace Mobile Engagement
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

## <a name="overview"></a>Přehled
Jan je správcem mobilní projektu pro společnost big média. Zadá nedávno spuštění nové aplikace, který má stažení velmi vysoký počet. Zadá narazil na jeho cíle pro stahování, ale stále jeho vrátit na Investment(ROI) na uživatele nesplňuje požadavky na jeho. 

Jan již nalezla proto jeho návratnost investic je příliš nízká. Uživatelé často zastavit pomocí jeho aplikace jenom 2 týdny a většina z nich nikdy vraťte. Chce zvýšit míru uchování jeho aplikace.

Po některé počáteční testování, mohl se naučili při mu zapojí svých uživatelů s nabízenými oznámeními, budou většinou dál používat své aplikace. Také uživatelé, které byly neaktivní často vrátí do aplikace v závislosti na oznámení, která mu odešle je. Jan rozhodne investovat do určitého druhu Program zapojení pro svou aplikaci, která využívá rozšířené cílení pomocí nabízených oznámení.

Jan má nedávno ke čtení [Azure Mobile Engagement – Příručka Začínáme s osvědčenými postupy](mobile-engagement-getting-started-best-practices.md) a se rozhodla doporučení z příručky jsou implementovat.

## <a name="objectives-and-kpis"></a>Cíle a klíčové ukazatele výkonu.
Klíčových zúčastněných stran Jan pro aplikace splňovat. Obchodní je generováno ze služby Active Directory uživatelům využívat jeho média. Zvýšením obsahu využívat na uživatele, se zvyšuje Jan jeho příjmů. Všechny odsouhlasení jedním z hlavních cílů: zvýšit prodej ze služby Active Directory o 25 %. Vytvoří obchodní klíčové ukazatele výkonu (KPI) k měření a disku tohoto cíle

* Počet kliknutí na jednotlivé uživatele služby Active Directory
* Počet stránek článku navštívené (na uživatele / relace / za týden / měsíčně...)
* Jaké jsou oblíbených kategorií

Na základě Jan pro schůzky s klíčových zúčastněných stran mu nemá definovánu jeho klíčové ukazatele výkonu. Mu odpovídá části 1 [Azure Mobile Engagement – Příručka Začínáme s osvědčenými postupy](mobile-engagement-getting-started-best-practices.md). 

V dalším kroku vytvoří následující klíčové ukazatele zapojení zajistit, že se dosáhne cíle:

* Monitorování uchování mezi následujícími intervaly: denně, týdně, jednou za dva týdny a měsíční.
* Počet aktivních uživatelů
* Ukládá hodnocení aplikace v aplikaci

Na základě doporučení od týmu IT, byly přidány následující technické klíčové ukazatele výkonu zodpovědět následující otázky:

* Co je cesta k uživatele (je navštívené stránky, která, kolik času uživatelé vynaložit na jeho)
* Počet havárií nebo chyby došlo k relace?
* Jaké verze operačního systému jsou mé uživatelé používají?
* Jaká je průměrná velikost obrazovky pro svoje uživatele?
* Jaký druh připojení k Internetu Moji uživatelé mají?

Pro každý ukazatel KPI mu klasifikuje data, vyžaduje a mohl zaznamenává do příslušného umístění jeho scénářem.

## <a name="engagement-program-and-integration"></a>Integrace produktů a program zapojení
Teď tento Jan dokončí, definování jeho klíčových ukazatelů výkonu, začne jeho fáze strategie zapojení definováním 4 programy zapojení a jejich cíle: ![][1]

Jan přejde poté hlubší podle s podrobnostmi o nabízená oznámení pro každý program. Nabízená oznámení jsou definovány pět prvky:

1. Cíl: co je cílem oznámení
2. Jak bude dosaženo cíle
3. Cíl: kdo obdrží oznámení?
4. Obsah: Co je pomocí jiné volby slov a formát oznámení (v App/Out z aplikace)
5. Kdy: co je nejlepší chvíli k odesílání této nabízených oznámení
   
    ![][2]

Další informace najdete v části [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

Podle části 2 v dokumentu white paper Jan používá cílový oddíl pro definování jaká data má ke shromažďování a zapíše jeho plánu značek společně s IT tým k implementaci řešení. Po 1 týden implementace a testování přijetí uživateli Jan nakonec spusťte jeho programy.

## <a name="program-results"></a>Výsledky programu
4 měsíců později, Jan zkontroluje přínos programů. Uvítací Program a týdenní programu jsou splňuje jeho cíle. Snižuje počet uživatelů s pouze jednu relaci, se používají další funkce aplikace a má dvojnásobný počet připojení za týden.

**Neaktivní Program** pomáhá pochopit tendence uživatele Jan. Zdá se, že 15 % neaktivní uživatele se vraťte k aplikaci. Ale většina z nich není zůstávají aktivní, více než 1 měsíc. Jan předpokládá potenciální optimalizace toto pořadí s další oznámení a rozšíření možnosti jeho obsahu.

**Zjistit Program** nefunguje správně. Jeho hodnota se zvyšuje křížové prodávané ale není dostatek dosáhne své cíle. Jan identifikuje, že mu nemá dostatek dat, aby relevantní cílení a navrhnout příslušný obsah. Mu tento program zastaví a se zaměřuje na odesílání "redakční nabízených oznámení" pomocí Azure Mobile Engagement. Jeho novináři už máte řešení CMS k odesílání nabízených oznámení a nechcete použít změnit.

Jan rozhodne použít rozhraní Reach API, což je rozhraní REST API HTTP, která umožňuje správu kampaně Reach bez nutnosti použití AZME webové rozhraní. S tímto přístupem Jan může shromažďovat data mu potřebuje a povolit jeho zapisovateli za účelem řešení CMS můžete dál používat.

K zajištění, že tato funkce funguje správně, požádá Jan IT tým být opatrná na následující body:

1. **Operační systémy** : mají všechna vlastní pravidla k administraci nabízená oznámení, tak, aby Jan rozhodne seznam všech případech a zkontroluje, pokud rozhraní API je potřeba ho.
   Např: Systému Android nabízené umožňuje velký obrázek, který není v případě s iOS.
2. **Časový rámec**: John chce rozhraní API, který je nastavený časový rámec a zakončení na kampaně. Chce zachovat uživatele z jakékoli bombing rušivý oznámení.
3. **Kategorie**: Marketing team připraví šablony pro každý typ výstrahy. Jan požádá IT tým nastavit kategorie uvnitř rozhraní API.

Některé testy Jan po splněna. Díky toto rozhraní API může novináři i dál posílat nabízená oznámení s jejich CMS a Azure Mobile Engagement shromažďuje všechna data chování pro ně

Za těchto 4 nejprve měsíců, výsledky odrážel dobrý celkový výkon a poskytuje jistotu Jan a jeho Tabule, návratnost investic za uživatele zvyšuje za 15 % a mobilní prodej představují 17.5 % celkový prodej, 7.5 % zvýšení pouze čtyř měsíců.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
