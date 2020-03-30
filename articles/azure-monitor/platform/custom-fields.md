---
title: Vlastní pole ve službě Azure Monitor (Preview) | Dokumenty společnosti Microsoft
description: Funkce Vlastní pole služby Azure Monitor umožňuje vytvářet vlastní prohledávatelná pole ze záznamů v pracovním prostoru Analýzy protokolů, které přidávají do vlastností shromážděného záznamu.  Tento článek popisuje proces vytvoření vlastního pole a poskytuje podrobný návod s ukázkovou událostí.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: bfb0a73631564c96a4af745fe9d7540a3a84f9c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655357"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor-preview"></a>Vytvoření vlastních polí v pracovním prostoru Analýzy protokolů v Azure Monitoru (preview)

> [!NOTE]
> Tento článek popisuje, jak analyzovat textová data v pracovním prostoru Analýzy protokolů při jejich shromažďování. Doporučujeme analyzovat textová data ve filtru dotazů po jejich shromáždění podle pokynů popsaných v [části Analyzovat textová data v Azure Monitoru](../log-query/parse-text.md). Poskytuje několik výhod oproti použití vlastních polí.

Funkce **Vlastní pole** služby Azure Monitor umožňuje rozšířit existující záznamy v pracovním prostoru Analýzy protokolů přidáním vlastních prohledávatelných polí.  Vlastní pole jsou automaticky vyplněna z dat extrahovaných z jiných vlastností ve stejném záznamu.

![Přehled](media/custom-fields/overview.png)

Například ukázkový záznam níže má užitečná data pohřbenv popisu události. Extrahování těchto dat do samostatné vlastnosti je k dispozici pro takové akce, jako je řazení a filtrování.

![Extrakt ze vzorku](media/custom-fields/sample-extract.png)

> [!NOTE]
> Ve verzi Preview jste omezeni na 100 vlastních polí v pracovním prostoru.  Toto omezení bude rozšířeno, jakmile tato funkce dosáhne obecné dostupnosti.

## <a name="creating-a-custom-field"></a>Vytvoření vlastního pole
Při vytváření vlastního pole musí služba Log Analytics pochopit, která data mají být naplnit jeho hodnotu.  Používá technologii z Microsoft Research s názvem FlashExtract rychle identifikovat tato data.  Místo toho, abyste museli zavést explicitní pokyny, Azure Monitor se dozví o datech, která chcete extrahovat z příkladů, které zadáte.

V následujících částech je uveden postup pro vytvoření vlastního pole.  V dolní části tohoto článku je návod extrakce vzorku.

> [!NOTE]
> Vlastní pole je vyplněno jako záznamy odpovídající zadaným kritériím jsou přidány do pracovního prostoru Analýzy protokolů, takže se zobrazí pouze u záznamů shromážděných po vytvoření vlastního pole.  Vlastní pole nebude přidáno do záznamů, které jsou již v úložišti dat při jeho vytvoření.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Krok 1 – Identifikace záznamů, které budou mít vlastní pole
Prvním krokem je identifikace záznamů, které získají vlastní pole.  Začnete se [standardním dotazem protokolu](../log-query/log-query-overview.md) a pak vyberte záznam, který bude fungovat jako model, ze kterého se Azure Monitor bude učit.  Pokud zadáte, že budete extrahovat data do vlastního pole, otevře se **Průvodce extrakcí polí,** kde ověříte a upřesníte kritéria.

1. Přejděte do **části Protokoly** a pomocí [dotazu načtěte záznamy,](../log-query/log-query-overview.md) které budou mít vlastní pole.
2. Vyberte záznam, který bude služba Log Analytics používat jako model pro extrahování dat k naplnění vlastního pole.  Identifikujete data, která chcete extrahovat z tohoto záznamu, a služba Log Analytics použije tyto informace k určení logiky k naplnění vlastního pole pro všechny podobné záznamy.
3. Rozbalte vlastnosti záznamu, klepněte na elipsu vlevo od horní vlastnosti záznamu a vyberte **Extrahovat pole z**.
4. Otevře se **Průvodce extrakcí polí** a vybraný záznam se zobrazí ve sloupci **Hlavní příklad.**  Vlastní pole bude definováno pro ty záznamy se stejnými hodnotami ve vybraných vlastnostech.  
5. Pokud výběr není přesně to, co chcete, vyberte další pole zúžit kritéria.  Chcete-li změnit hodnoty polí pro kritéria, musíte zrušit a vybrat jiný záznam odpovídající požadovaným kritériím.

### <a name="step-2---perform-initial-extract"></a>Krok 2 - Proveďte počáteční výpis.
Jakmile identifikujete záznamy, které budou mít vlastní pole, identifikujete data, která chcete extrahovat.  Log Analytics použije tyto informace k identifikaci podobných vzorů v podobných záznamech.  V kroku po tomto budete moci ověřit výsledky a poskytnout další podrobnosti pro Log Analytics použít v jeho analýze.

1. Zvýrazněte text v ukázkovém záznamu, který chcete naplnit vlastním polem.  Poté se zobrazí dialogové okno, které zadá název a datový typ pole a provede počáteční výpis.  Znaky ** \_CF** budou automaticky připojeny.
2. Chcete-li provést analýzu shromážděných záznamů, klepněte na tlačítko **Extrahovat.**  
3. V částech **Souhrn** a **Výsledky hledání** se zobrazí výsledky extraktu, takže můžete zkontrolovat jeho přesnost.  **Souhrn** zobrazuje kritéria použitá k identifikaci záznamů a počet pro každou z identifikovaných datových hodnot.  **Výsledky hledání** poskytují podrobný seznam záznamů odpovídajících kritériím.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Krok 3 – Ověření přesnosti extraktu a vytvoření vlastního pole
Po provedení počátečního výpisu se služba Log Analytics zobrazí její výsledky na základě dat, která již byla shromážděna.  Pokud výsledky vypadají přesně, můžete vytvořit vlastní pole bez další práce.  Pokud ne, pak můžete upřesnit výsledky tak, aby Log Analytics můžete zlepšit jeho logiku.

1. Pokud některé hodnoty v počátečním extraktu nejsou správné, klepněte na ikonu **Upravit** vedle nepřesného záznamu a vyberte **Změnit toto zvýraznění,** abyste mohli změnit výběr.
2. Položka je zkopírována do oddílu **Další příklady** pod **hlavním příkladem**.  Zde můžete upravit zvýraznění, které vám pomůže službě Log Analytics pochopit výběr, který měl provést.
3. Kliknutím na **Extrahovat** použijte tyto nové informace k vyhodnocení všech existujících záznamů.  Výsledky mohou být upraveny pro jiné záznamy, než ten, který jste právě upravili na základě této nové inteligence.
4. Pokračujte v přidávání oprav, dokud všechny záznamy v extraktu správně neidentifikují data k naplnění nového vlastního pole.
5. Pokud jste s výsledky spokojeni, klikněte na **Uložit výpis.**  Vlastní pole je nyní definováno, ale zatím nebude přidáno do žádných záznamů.
6. Počkejte na nové záznamy odpovídající zadaným kritériím, které mají být shromážděny, a potom znovu spusťte hledání protokolu. Nové záznamy by měly mít vlastní pole.
7. Použijte vlastní pole jako všechny ostatní vlastnosti záznamu.  Můžete ji použít k agregaci a seskupení dat a dokonce k vytvoření nových přehledů.

## <a name="viewing-custom-fields"></a>Zobrazení vlastních polí
Seznam všech vlastních polí ve skupině pro správu můžete zobrazit v nabídce **Upřesnit nastavení** pracovního prostoru Log Analytics na webu Azure Portal.  Vyberte **Data** a potom **vlastní pole** pro seznam všech vlastních polí v pracovním prostoru.  

![Vlastní pole](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Odebrání vlastního pole
Vlastní pole lze odebrat dvěma způsoby.  První je **možnost Odebrat** pro každé pole při zobrazení úplného seznamu, jak je popsáno výše.  Druhou metodou je načtení záznamu a klepnutí na tlačítko nalevo od pole.  Nabídka bude mít možnost odebrat vlastní pole.

## <a name="sample-walkthrough"></a>Ukázkový názorný postup
V následující části se provede úplný příklad vytvoření vlastního pole.  Tento příklad extrahuje název služby v událostech systému Windows, které označují stav změny služby.  To závisí na událostech vytvořených Správcem řízení služeb při spuštění systému v počítačích se systémem Windows.  Chcete-li postupovat podle tohoto [příkladu,](data-sources-windows-events.md)je nutné shromažďovat informační události pro systémový protokol .

Zadejte následující dotaz vrátit všechny události ze Správce řízení služeb, které mají ID události 7036, což je událost, která označuje spuštění nebo zastavení služby.

![Dotaz](media/custom-fields/query.png)

Poté vybereme a rozbalíme libovolný záznam s ID události 7036.

![Zdrojový záznam](media/custom-fields/source-record.png)

Vlastní pole definujeme kliknutím na elipsu vedle horní vlastnosti.

![Extrahování polí](media/custom-fields/extract-fields.png)

Otevře se **Průvodce extrakcí polí** a pole **EventLog** a **EventID** jsou vybrána ve sloupci **Hlavní příklad.**  To znamená, že vlastní pole bude definováno pro události z systémového protokolu s ID události 7036.  To je dostačující, takže nemusíme vybírat žádná další pole.

![Hlavní příklad](media/custom-fields/main-example.png)

Zvýrazníme název služby ve **vlastnosti RenderedDescription** a k identifikaci názvu služby použijeme **službu.**  Vlastní pole se bude volat **Service_CF**. Typ pole v tomto případě je řetězec, takže můžeme nechat beze změny.

![Název pole](media/custom-fields/field-title.png)

Vidíme, že název služby je správně identifikován pro některé záznamy, ale ne pro jiné.   **Výsledky hledání** ukazují, že část názvu **adaptéru výkonu služby WMI** nebyla vybrána.  **Souhrn** ukazuje, že jeden záznam identifikoval **Instalační službu modulů** namísto **Instalační služby modulů systému Windows**.  

![Výsledky hledání](media/custom-fields/search-results-01.png)

Začneme se záznamem **adaptéru výkonu služby WMI.**  Klikneme na jeho ikonu upravit a pak **upravit toto zvýraznění**.  

![Změnit zvýraznění](media/custom-fields/modify-highlight.png)

Zvýšíme zvýraznění zahrnout slovo **WMI** a potom znovu spustit extrakt.  

![Další příklad](media/custom-fields/additional-example-01.png)

Vidíme, že položky **pro adaptér výkonu služby WMI** byly opraveny a služba Log Analytics tyto informace také použila k opravě záznamů instalační **služby modulu systému Windows**.

![Výsledky hledání](media/custom-fields/search-results-02.png)

Nyní můžeme spustit dotaz, který ověří, **Service_CF** je vytvořen, ale ještě není přidán do žádné záznamy. Je to proto, že vlastní pole nefunguje proti existujícím záznamům, takže musíme počkat na sběr nových záznamů.

![Počáteční počet](media/custom-fields/initial-count.png)

Po uplynutí určité doby, aby byly shromažďovány nové události, můžeme vidět, že **pole Service_CF** je nyní přidáváno do záznamů, které odpovídají našim kritériím.

![Konečné výsledky](media/custom-fields/final-results.png)

Nyní můžeme použít vlastní pole jako všechny ostatní vlastnosti záznamu.  Pro ilustraci vytvoříme dotaz, který seskupuje podle pole nové **ho Service_CF,** aby se zkontrolovaly, které služby jsou nejaktivnější.

![Seskupit podle dotazu](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Další kroky
* Informace o [dotazech protokolu](../log-query/log-query-overview.md) pro vytváření dotazů pomocí vlastních polí pro kritéria.
* Sledujte [vlastní soubory protokolu,](data-sources-custom-logs.md) které analyzujete pomocí vlastních polí.

