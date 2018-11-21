---
title: Principy Cloudyn nákladů sestavy správy v Azure | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit základní struktura Cloudyn náklady na správu sestav a funkce.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: cost-management
manager: benshy
ms.custom: ''
ms.openlocfilehash: b4e1ffa0b7bd524fc9029b21e77b09ea07e1f202
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52276648"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>Principy Cloudyn sestav správy nákladů

Tento článek vám pomůže pochopit základní struktura Cloudyn náklady na správu sestav a funkce. Většina sestav Cloudyn jsou výsledkem je intuitivní a mít jednotný vzhled a chování. Po přečtení tohoto článku, jsou připravené k použití všech sestavách nákladů správy. Mnoho standardních funkcí jsou k dispozici v rámci různých sestav, abyste mohli přejít sestavy s lehkostí a elegancí. Sestavy jsou přizpůsobitelné, a můžete vybrat z několika možností pro výpočet a zobrazit výsledky.

## <a name="report-fields-and-options"></a>Pole sestavy a možnosti

Tady se můžete podívat na příklad sestavy Cost Over Time. Většina sestav Cloudyn mají podobné rozložení.

![Ukázková sestava](./media/understanding-cost-reports/sample-report.png)

Jednotlivé očíslované oblasti v předchozím obrázku je podrobně popsaný v následující informace:

1. **Rozsah kalendářních dat**

    Seznam rozsah kalendářních dat slouží k určení časového intervalu sestavy pomocí vlastní nebo přednastavený kontext.
2. **Uložený filtr**

    Pomocí seznamu filtru Uložit uložte aktuální skupiny a filtry, které se použijí k sestavě. Uložené filtry jsou dostupné v rámci náklady a výkon sestavy, včetně:

      - Analýza nákladů
      - přidělení
      - Správa majetku
      - Optimalizace

  Zadejte název filtru a kliknutím na **Uložit**.

3. **Značky**

    Použijte oblast značky do skupiny podle kategorií značky. Klíčová slova uvedená v nabídce jsou Azure oddělení nebo cost center značky nebo se jedná značky entity a předplatné náklady od Cloudyn. Vyberte tagy, které chcete filtrovat výsledky. Můžete také zadat název značky (klíčové slovo) pro filtrování výsledků.

    ![Vyberte možnosti](./media/understanding-cost-reports/select-options.png)

    Klikněte na tlačítko **přidat** přidat nový filtr.

    ![Přidat filtr](./media/understanding-cost-reports/add-filter.png)

    Značka seskupování a filtrování se nevztahuje na prostředky Azure nebo značky skupiny prostředků.

    Jsou k dispozici v přidělení nákladů značky seskupování a filtrování **skupiny** nabídky.

4. **Skupiny v sestavách**

    Používání skupin v Cost Analysis sestavy, které ukazují na úrovni standard rozpis kategorie z fakturační data v sestavě.  Nicméně podle klíčových slov kategorií zobrazení skupin v zobrazení sestavy přidělování nákladů. Podle klíčových slov kategorie jsou definovány v modelu přidělování nákladů a standardní rozepsané kategorie z fakturačních údajů.

    ![značky skupin](./media/understanding-cost-reports/groups-tags01.png)

    ![značky skupin](./media/understanding-cost-reports/groups-tags02.png)

    V sestavách přidělování nákladů mohou zahrnovat skupiny do kategorií podle klíčových slov skupiny:
      - Značky
      - značky skupiny prostředků
      - Značky entit nákladů Cloudyn
      - Kategorie značky předplatného pro účely přidělování nákladů

  Příklady můžou zahrnovat:
     - Nákladové středisko
     - Oddělení
     - Aplikace
     - Prostředí
     - Cenově kódu

    Tady je seznam předdefinovaných skupin v sestavách dostupná:

    - **Typ nákladu pro přenos**
      - Vyberte typ nákladů nebo více typů náklady, nebo všechny. Typy nákladů patří:
        - Jednorázový poplatek
        - Podpora
        - Poplatky za využití
    - **Zákazníka**
        - Vyberte konkrétního zákazníka, více zákazníků, nebo všechny zákazníky.
    - **Název účtu**
        - Název účtu nebo předplatného. V Azure je název předplatného Azure.
    - **Číslo účtu**
        - Vyberte účet, více účtů nebo pro všechny účty. V Azure je GUID předplatného Azure.
    - **Nadřazené účtu**
        - Vyberte účet nadřazené, více účtů nebo select.
    - **Služba**
        - Vyberte službu, více služeb, nebo všechny služby.
    - **Poskytovatel**
        - Rozhraní poskytovatele cloudu, kde jsou přidružené prostředky a náklady.
    - **Oblast**
        - Oblast, ve které je hostitelem prostředku.
    - **Zóna dostupnosti**
        - AWS izolované umístění v rámci oblasti.
    - **Typ prostředku**
        - Typ prostředku používá.
    - **Dílčí typ**
        - Výběr dílčí typu.
    - **Operace**
        - Vyberte operaci, nebo **Zobrazit vše**.
    - **Cenový Model**
        - Všechny předem
        - Bez předem
        - Částečné předem
        - Na vyžádání
        - Rezervace
        - Místo
    - **Poplatek za typ**
        - Vyberte typ poplatku záporné nebo kladné nebo obojí.
    - **Tenantů**
        - Určuje, zda počítač běží jako vyhrazený počítač.
    -   **Typ využití**
          - Typ využití může být jednorázové poplatky nebo opakované poplatky.

5. **Filtry**

    Pomocí jednoduchého nebo vícenásobného výběru filtrů nastavit rozsahy vybraných hodnot. Pokud chcete nastavit filtr, klikněte na tlačítko **přidat** a potom vyberte filtr kategorií a hodnot.

6. **Model nákladů**

    Vyberte model nákladů, které jste předtím vytvořili pomocí Cost Allocation 360 pomocí modelu nákladů. Můžete mít více modelů nákladů Cloudyn, v závislosti na požadavcích přidělení nákladů. Některé týmy na jednom místě organizace může mít náklady požadavky na přidělení, které se liší od ostatních. Každý tým může mít vlastní vyhrazený nákladový model.

    Informace o vytváření definice modelu přidělování nákladů, naleznete v tématu [použití vlastních značek k přidělování nákladů](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortizace**

    Amortizace použití v sestavách přidělení nákladů k zobrazení bez použití na základě poplatků za služby nebo jednorázové závazků náklady a náklady na jejich rozloženy rovnoměrně dobu jejich životnosti. Příklady jednorázové poplatky můžou zahrnovat:
    - Roční poplatky za podporu
    - Roční poplatky součásti zabezpečení
    - Poplatky za nákupy rezervovaných instancí
    - Některé položky Azure Marketplace.

  V části amortizace, vyberte **Amortizovaných nákladů** nebo **skutečné náklady**.

8. **Řešení**

    Pomocí řešení vyberte řešení čas ve vybraném rozsahu dat. Vaše doba řešení určuje, jak jednotky se zobrazí v sestavě a může být:
    - Denně
    - Týdně
    - Měsíční
    - Čtvrtletně
    - Roční

9. **Pravidla přidělování**

    Použití přidělení pravidel k použití nebo zakázat přidělování nákladů náklady přepočítání. Můžete povolit nebo zakázat přepočet přidělení nákladů pro fakturačních údajů. Přepočítání se vztahuje na vybraných kategorií v sestavě. Umožňuje vám posoudit dopad přepočet přidělení nákladů na nezpracovaných datech fakturace.

10. **Nezařazeno do kategorie**

    Nezařazeno můžete zahrnout nebo vyloučit Nezařazeno do kategorie nákladů v sestavě.

11. **Zobrazit/skrýt pole**

    Zobrazit/Skrýt možnost nemá žádný efekt v sestavách.

12.   **Formáty zobrazení**

    Pomocí formátů zobrazení vyberte různá zobrazení grafu nebo tabulce.

    ![Formáty zobrazení](./media/understanding-cost-reports/display-formats.png)

13. **Více barva**

    Použití více barvy k nastavení barvy grafy v sestavě.

14. **Akce**

    Pomocí akcí můžete uložit, exportujte nebo plánování sestavy.

15. **Zásady**

    I když nejsou na obrázku, některé sestavy zahrnují zásadu výpočtu odhadovaných nákladů. V těchto sestavách **konsolidované** zásad můžete vidět doporučení pro všechny účty a předplatná v rámci aktuální entity, jako je například Microsoft registrace nebo plátce AWS. **Samostatné** zásady vidět doporučení pro jednoho účtu nebo předplatného, jakoby žádná předplatná neexistují. Zásady, kterou jste vybrali, se liší optimalizace strategie, které vaše organizace používá. Projekce nákladů jsou založeny na posledních 30 dní používání.

## <a name="save-and-schedule-reports"></a>Uložit a plánování sestav

Po vytvoření sestavy, můžete ho uložit pro budoucí použití. Uložené sestavy jsou k dispozici v **Moje nástroje** > **Moje sestavy**. Pokud provedete změny do stávající sestavy a uložte ho, sestava uložena jako novou verzi. Nebo ho můžete uložit jako novou sestavu.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Uložení sestavy na portálu Cloudyn

Při zobrazení všech sestav, klikněte na tlačítko **akce** a pak vyberte **uložit Moje sestavy**. Název sestavy a pak přidejte své vlastní adresu URL nebo použít automaticky vytvořený adresu URL. Volitelně můžete **sdílet** veřejně s jinými uživateli ve vaší organizaci nebo můžete sestavy můžete sdílet do entity. Pokud není sdílet sestavy, zůstává osobní zprávu a pouze se zobrazí. Uložte sestavu.


### <a name="save-a-report-to-cloud-provider-storage"></a>Uložení sestavy do zprostředkovatele úložiště v cloudu

Pokud chcete uložit sestavu do poskytovatele cloudových služeb, jste museli už nakonfigurovat účet úložiště. Při zobrazení všech sestav, klikněte na tlačítko **akce** a pak vyberte **Naplánování sestavy**. Název sestavy a pak přidejte své vlastní adresu URL nebo použít automaticky vytvořený adresu URL. Vyberte **uložit do úložiště** a potom vyberte účet úložiště nebo přidat nový. Zadejte předponu, která získá připojeným k názvu souboru sestavy. Vyberte formát souboru CSV nebo JSON a uložte sestavu.

### <a name="schedule-a-report"></a>Naplánování sestavy

Můžete vytvářet sestavy v naplánovaných intervalech a odesílat je do příjemce seznamu nebo cloudové služby poskytovatele účtu úložiště. Při zobrazení všech sestav, klikněte na tlačítko **akce** a pak vyberte **Naplánování sestavy**. Můžete odeslat zprávu o e-mailem a uložit do účtu úložiště. V části **plán**, vyberte interval (denní, týdenní nebo měsíční frekvencí). Pro týdenní a měsíční vyberte den nebo datum poskytovat a vyberte čas. Uložte naplánovanou sestavu. Pokud vyberete formát sestavy aplikace Excel, sestava se odešle jako příloha. Když vyberete formát obsahu e-mailu, výsledky sestavy, které jsou zobrazeny ve formátu grafu jsou dodávány jako graf.

### <a name="export-a-report-as-a-csv-file"></a>Export sestavy do souboru .csv

Při zobrazení všech sestav, klikněte na tlačítko **akce** a pak vyberte **exportovat všechna data sestavy**. Automaticky otevírané okno se zobrazí a stáhne soubor CSV.

## <a name="next-steps"></a>Další postup

- Další informace o sestavách, které jsou zahrnuté ve službě Cloudyn na [sestavy použijte Cloudyn](use-reports.md).
- Další informace o tom, jak můžete vytvořit sestavy [řídicí panely](dashboards.md).
