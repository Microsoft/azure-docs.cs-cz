---
title: Vlastní pole v Azure Monitor (Preview) | Microsoft Docs
description: Funkce vlastní pole Azure Monitor umožňuje vytvořit vlastní hledaná pole ze záznamů v pracovním prostoru Log Analytics, který se přidává do vlastností shromážděného záznamu.  Tento článek popisuje proces vytvoření vlastního pole a poskytuje podrobný návod s ukázkovou událostí.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: e7ddeaf1273a9e3b5608336b24c07fad3c3f4525
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031034"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor-preview"></a>Vytváření vlastních polí v pracovním prostoru Log Analytics v Azure Monitor (Preview)

> [!NOTE]
> Tento článek popisuje, jak analyzovat textová data v Log Analytics pracovním prostoru při jejich shromažďování. Po shromáždění textu podle pokynů popsaných v tématu [Analýza textových dat v Azure monitor](./parse-text.md)doporučujeme analyzovat textová data ve filtru dotazu. Nabízí několik výhod používání vlastních polí.

> [!IMPORTANT]
> Vlastní pole zvyšují množství dat shromažďovaných v pracovním prostoru Log Analytics, což může zvýšit vaše náklady. Podrobnosti najdete v tématu [Správa využití a nákladů pomocí protokolů Azure monitor](./manage-cost-storage.md#pricing-model) .

Funkce **vlastní pole** Azure monitor umožňuje rozšíření existujících záznamů v pracovním prostoru Log Analytics přidáním vlastních vyhledávacích polí.  Vlastní pole jsou automaticky vyplněna z dat extrahovaných z jiných vlastností ve stejném záznamu.

![Diagram znázorňuje původní záznam přidružený k upravenému záznamu v pracovním prostoru Log Analytics s páry hodnoty vlastností přidané do původní vlastnosti v upravovaném záznamu.](media/custom-fields/overview.png)

Například následující ukázkový záznam obsahuje užitečné ukryto dat v popisu události. Extrakce těchto dat do samostatné vlastnosti je dostupná pro tyto akce jako řazení a filtrování.

![Ukázkový extrakce](media/custom-fields/sample-extract.png)

> [!NOTE]
> Ve verzi Preview můžete v pracovním prostoru omezit na 100 vlastních polí.  Toto omezení se rozšíří, když tato funkce dosáhne všeobecné dostupnosti.

## <a name="creating-a-custom-field"></a>Vytvoření vlastního pole
Když vytvoříte vlastní pole, Log Analytics musí pochopit, která data se mají použít k naplnění její hodnoty.  K rychlé identifikaci těchto dat používá technologii z Microsoft Research s názvem FlashExtract.  Místo toho, abyste museli poskytnout explicitní pokyny, Azure Monitor se naučíte, jak se data, která chcete extrahovat, z příkladů, které poskytnete.

Následující části obsahují postup pro vytvoření vlastního pole.  V dolní části tohoto článku se jedná o návod k extrakci ukázek.

> [!NOTE]
> Vlastní pole je vyplněno jako záznamy, které odpovídají zadaným kritériím, jsou přidány do pracovního prostoru Log Analytics, takže se zobrazí pouze u záznamů shromážděných po vytvoření vlastního pole.  Vlastní pole nebude přidáno k záznamům, které jsou již v úložišti dat, když je vytvořen.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Krok 1 – identifikace záznamů, které budou mít vlastní pole
Prvním krokem je identifikace záznamů, které získají vlastní pole.  Začnete se [standardním dotazem protokolu](./log-query-overview.md) a pak vyberete záznam, který se bude chovat jako model, ze kterého Azure monitor vědět.  Pokud určíte, že budete chtít extrahovat data do vlastního pole, otevře se **Průvodce extrakcí polí** , kde ověříte a upřesníte kritéria.

1. Přejít na **protokoly** a použít [dotaz k načtení záznamů](./log-query-overview.md) , které budou mít vlastní pole.
2. Vyberte záznam, který Log Analytics použít jako model pro extrakci dat, aby bylo možné naplnit vlastní pole.  Budete identifikovat data, která chcete extrahovat z tohoto záznamu, a Log Analytics bude tyto informace používat k určení logiky pro naplnění vlastního pole pro všechny podobné záznamy.
3. Rozbalte vlastnosti záznamu, klikněte na elipsu nalevo od vlastnosti horní části záznamu a vyberte **extrahovat pole z**.
4. Otevře se **Průvodce extrakcí polí** a vybraný záznam se zobrazí ve sloupci **hlavní příklad** .  Vlastní pole bude definováno pro tyto záznamy se stejnými hodnotami ve vlastnostech, které jsou vybrány.  
5. Pokud výběr přesně nechcete, vyberte další pole a upřesněte kritéria.  Aby bylo možné změnit hodnoty polí pro kritéria, je nutné zrušit a vybrat jiný záznam, který odpovídá požadovaným kritériím.

### <a name="step-2---perform-initial-extract"></a>Krok 2 – proveďte počáteční extrakci.
Jakmile identifikujete záznamy, které budou mít vlastní pole, identifikujte data, která chcete extrahovat.  Log Analytics budou tyto informace používat k identifikaci podobných vzorů v podobných záznamech.  V tomto kroku budete moci ověřit výsledky a poskytnout další podrobnosti, které Log Analytics použít při analýze.

1. Zvýrazněte text v ukázkovém záznamu, který chcete vyplnit vlastní pole.  Pak se zobrazí dialogové okno, kde můžete zadat název a datový typ pro pole a provést počáteční extrakci.  Znaky **\_ CR** budou automaticky připojeny.
2. Kliknutím na **extrahovat** proveďte analýzu shromážděných záznamů.  
3. V sekcích **Souhrn** a **výsledky hledání** se zobrazují výsledky extrakce, abyste mohli zkontrolovat jeho přesnost.  **Souhrn** zobrazuje kritéria sloužící k identifikaci záznamů a počtu identifikovaných hodnot dat.  **Výsledky hledání** obsahují podrobný seznam záznamů, které odpovídají kritériím.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Krok 3 – ověření správnosti extrakce a vytvoření vlastního pole
Jakmile provedete počáteční extrakci, Log Analytics zobrazí výsledky na základě dat, která již byla shromážděna.  Pokud výsledky vypadají přesně, můžete vytvořit vlastní pole bez další práce.  Pokud ne, můžete výsledky Upřesnit tak, aby Log Analytics mohli vylepšit jeho logiku.

1. Pokud některé hodnoty v počátečním extrakci nejsou správné, klikněte na ikonu **Upravit** vedle nepřesného záznamu a výběrem **změnit toto zvýraznění** , aby se výběr změnil.
2. Položka se zkopíruje do části **Další příklady** pod **hlavním příkladem**.  Zvýraznění zde můžete nastavit tak, aby bylo možné Log Analytics porozumět výběru, který by měl být proveden.
3. Kliknutím na **extrahovat** můžete tyto nové informace použít k vyhodnocení všech stávajících záznamů.  Výsledky se můžou upravovat pro jiné záznamy než ta, kterou jste právě změnili v závislosti na této nové inteligentní službě.
4. Pokračujte v přidávání oprav, dokud všechny záznamy v extrahování správně neidentifikují data k naplnění nového vlastního pole.
5. Až budete s výsledky spokojeni, klikněte na **Uložit extrakci** .  Vlastní pole je nyní definováno, ale ještě nebude přidáno do žádných záznamů.
6. Počkejte na shromáždění nových záznamů, které odpovídají zadaným kritériím, a poté znovu spusťte hledání protokolu. Nové záznamy by měly mít vlastní pole.
7. Použijte vlastní pole jako jakoukoli jinou vlastnost záznamu.  Můžete ji použít k agregaci a seskupení dat a dokonce ji použít k vytvoření nových přehledů.

## <a name="viewing-custom-fields"></a>Zobrazení vlastních polí
Seznam všech vlastních polí ve skupině pro správu můžete zobrazit v nabídce **Upřesnit nastavení** v pracovním prostoru Log Analytics v Azure Portal.  Vyberte **data** a potom **vlastní pole** pro seznam všech vlastních polí v pracovním prostoru.  

![Vlastní pole](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Odebrání vlastního pole
Existují dva způsoby, jak odebrat vlastní pole.  První je možnost **Odebrat** pro každé pole při zobrazení úplného seznamu, jak je popsáno výše.  Druhou metodou je načíst záznam a kliknout na tlačítko nalevo od pole.  Nabídka bude mít možnost odebrat vlastní pole.

## <a name="sample-walkthrough"></a>Ukázkový názorný postup
Následující část vás provede úplným příkladem vytvoření vlastního pole.  Tento příklad extrahuje název služby v událostech systému Windows, které označují stav změny služby.  To se spoléhá na události vytvořené nástrojem řízení služeb během spouštění systému v počítačích se systémem Windows.  Pokud chcete postupovat podle tohoto příkladu, musíte [shromažďovat události informací pro systémový protokol](../agents/data-sources-windows-events.md).

Do následujícího dotazu zadáte následující dotaz, který vrátí všechny události ze Správce řízení služeb, který má ID události 7036, což je událost, která označuje, že služba se spouští nebo zastavuje.

![Snímek obrazovky zobrazuje dotaz na zdroj a ID události.](media/custom-fields/query.png)

Pak vybereme a rozbalíme libovolný záznam s ID události 7036.

![Zdrojový záznam](media/custom-fields/source-record.png)

Vlastní pole definujeme tak, že kliknete na tři tečky vedle vlastnosti Top (začátek).

![Extrahovat pole](media/custom-fields/extract-fields.png)

Otevře se **Průvodce extrakcí polí** a pole **EventLog** a **ID události** jsou vybrána v **hlavním příkladu** sloupce.  To znamená, že vlastní pole bude definováno pro události ze systémového protokolu s ID události 7036.  To je dostačující, takže nemusíme vybírat žádná další pole.

![Hlavní příklad](media/custom-fields/main-example.png)

Ve vlastnosti **RenderedDescription** se zvýrazní název služby a k identifikaci názvu služby používá **službu** .  Vlastní pole bude voláno **Service_CF**. Typ pole v tomto případě je řetězec, takže ho můžeme nechat beze změny.

![Název pole](media/custom-fields/field-title.png)

Uvidíme, že název služby je pro některé záznamy správně identifikovaný, ale ne pro jiné.   Ve **výsledcích hledání** se nevybere část názvu **adaptéru výkonu služby WMI** .  V **souhrnu** se zobrazí, že místo **instalačního programu pro moduly systému Windows** jeden záznam identifikoval **moduly** .  

![Snímek obrazovky znázorňující části názvu služby zvýrazněné v podokně výsledků hledání a jeden nesprávný název služby zvýrazněný v souhrnu.](media/custom-fields/search-results-01.png)

Začneme záznamem **adaptéru výkonu služby WMI** .  Kliknu na ikonu pro úpravu a pak na **změnit toto zvýraznění**.  

![Změnit zvýraznění](media/custom-fields/modify-highlight.png)

Zvětšili jsme zvýraznění tak, aby obsahovalo **rozhraní WMI** pro Word, a pak znovu spustit extrakci.  

![Další příklad](media/custom-fields/additional-example-01.png)

Vidíte, že položky pro **adaptér výkonu WMI** byly opraveny, a Log Analytics také tyto informace použít k opravě záznamů pro **Instalační službu modulu systému Windows**.

![Snímek obrazovky zobrazující úplný název služby zvýrazněný v podokně výsledků hledání a správné názvy služeb zvýrazněné v souhrnu](media/custom-fields/search-results-02.png)

Teď můžeme spustit dotaz, který ověří **Service_CF** je vytvořený, ale ještě není přidaný do žádných záznamů. Důvodem je skutečnost, že vlastní pole nefunguje u stávajících záznamů, takže musíme počkat na shromáždění nových záznamů.

![Počáteční počet](media/custom-fields/initial-count.png)

Po uplynutí určité doby se budou shromažďovat nové události, můžeme vidět, že pole **Service_CF** se teď přidávají do záznamů, které odpovídají kritériím.

![Konečné výsledky](media/custom-fields/final-results.png)

Nyní můžeme použít vlastní pole jako jakoukoli jinou vlastnost záznamu.  Pro ilustraci vytvoříme dotaz, který seskupí podle pole New **Service_CF** , aby se zkontrolovaly, které služby jsou v nejvyšším případě aktivní.

![Seskupit podle dotazu](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Další kroky
* Přečtěte si o [dotazech protokolu](./log-query-overview.md) pro vytváření dotazů pomocí vlastních polí pro kritéria.
* Monitorujte [vlastní soubory protokolů](../agents/data-sources-custom-logs.md) , které analyzujete pomocí vlastních polí.