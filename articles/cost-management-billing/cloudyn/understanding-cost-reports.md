---
title: Principy sestav Cloudyn Cost Management v Azure
description: Tento článek vám pomůže pochopit základní strukturu a funkce sestav Cloudyn Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 9afe34097d005daeedf46fbaf30b37e53a8e3658
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132036"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>Principy sestav Cloudyn Cost Management

Tento článek vám pomůže pochopit základní strukturu a funkce sestav Cloudyn Cost Management. Většina sestav Cloudyn je intuitivní a má jednotný vzhled a chování. Po přečtení tohoto článku budete připraveni použít všechny sestavy pro řízení nákladů. Sestavy jsou různorodé, ale ve všech je k dispozici mnoho standardních funkcí, které vám usnadní orientaci. Sestavy jsou přizpůsobitelné a můžete vybírat z několika možností pro výpočet a zobrazení výsledků.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="report-fields-and-options"></a>Pole a možnosti sestav

Tady se můžete podívat na příklad sestavy Cost Over Time (Náklady v průběhu času). Většina sestav Cloudyn má podobné rozložení.

![Příklad sestavy nákladů v průběhu času s očíslovanými oblastmi, které odpovídají popisům](./media/understanding-cost-reports/sample-report.png)

Každá číslovaná oblast na předchozím obrázku je podrobněji popsána níže:

1. **Rozsah dat**

    Seznam Date Range (Rozsah dat) slouží k definování přednastaveného nebo vlastního časového intervalu sestavy.
2. **Uložený filtr**

    Pomocí seznamu Saved Filter (Uložený filtr) uložte aktuální skupiny a filtry použité v sestavě. Uložené filtry jsou k dispozici ve všech sestavách nákladů a výkonu a zahrnují tyto položky:

      - Analýza nákladů
      - Přidělování
      - Správa aktiv
      - Optimalizace

   Zadejte název filtru a potom klikněte na **Save** (Uložit).

3. **Značky**

    Oblast Tags (Značky) slouží k seskupení podle kategorií značek. Značky, které jsou uvedené v nabídce, jsou značky oddělení Azure nebo nákladového střediska nebo jsou to značky nákladových entit a předplatného Cloudyn. Vybráním značek vyfiltrujete výsledky. Výsledky můžete také filtrovat zadáním názvu značky (klíčového slova).

    ![Příklad seznamu značek pro filtrování výsledků](./media/understanding-cost-reports/select-options.png)

    Kliknutím na **Add** (Přidat) přidejte nový filtr.

    ![Okno přidání filtru zobrazující možnosti a podmínky pro filtrování](./media/understanding-cost-reports/add-filter.png)

    Seskupení značek nebo filtrování se netýká značek prostředků nebo skupin prostředků Azure.

    Seskupení a filtrování značek pro přidělování nákladů je k dispozici jako možnost nabídky **Groups** (Skupiny).

4. **Skupiny v sestavách**

    Pomocí skupin v sestavách analýzy nákladů můžete zobrazit standardní kategorie s položkami z fakturačních údajů.  Skupiny v sestavách přidělení nákladů ale ukazují kategorie zobrazení založené na značkách. Kategorie založené na značkách jsou definované v modelu přidělování nákladů a ve standardních položkách kategorií z fakturačních údajů.

    ![První příklad – seznam značek, podle kterých můžete seskupovat](./media/understanding-cost-reports/groups-tags01.png)

    ![Druhý příklad – seznam značek, podle kterých můžete seskupovat](./media/understanding-cost-reports/groups-tags02.png)

    V sestavách přidělování nákladů mohou skupiny v kategoriích skupin založených na značkách zahrnovat:
      - Značky
      - Značky skupiny prostředků
      - Značky nákladových entit Cloudyn
      - Kategorie značek předplatného pro účely přidělení nákladů

   Příklady:
   - Nákladové středisko
   - Oddělení
   - Aplikace
   - Prostředí
   - Nákladový kód

     Tady je seznam předdefinovaných skupin, které jsou k dispozici v sestavách:

     - **Typ nákladů**
     - Vyberte typ nákladů nebo více typů nákladů nebo vyberte vše. Typy nákladů zahrnují:
       - Jednorázové poplatky
       - Podpora
       - Náklady na využití
     - **Zákazník**
       - Vyberte konkrétního zákazníka, více zákazníků nebo vyberte všechny zákazníky.
     - **Název účtu**
       - Název účtu nebo předplatného. V Azure se jedná o název předplatného Azure.
     - **Číslo účtu**
       - Vyberte účet, více účtů nebo všechny účty. V Azure je to identifikátor GUID předplatného Azure.
     - **Nadřazený účet**
       - Vyberte nadřazený účet, více účtů nebo vyberte vše.
     - **Služba**
       - Vyberte službu, několik služeb nebo vyberte všechny služby.
     - **Poskytovatel**
       - Poskytovatel cloudu, ve kterém se spojují prostředky a výdaje.
     - **Oblast**
       - Oblast, ve které je prostředek hostovaný.
     - **Zóna dostupnosti**
       - Izolovaná umístění AWS v rámci jedné oblasti.
     - **Typ prostředku**
       - Typ používaného prostředku.
     - **Podtyp**
       - Vyberte podtyp.
     - **Operace**
       - Vyberte operaci nebo možnost **Show all** (Zobrazit všechno).
     - **Cenový model**
       - Všechno předem
       - Nic předem
       - Část předem
       - Na vyžádání
       - Rezervace
       - Na místě
     - **Typ poplatku**
       - Vyberte typ záporný nebo kladný poplatek nebo obojí.
     - **Architektura tenantů**
       - Rozlišuje, zda se počítač používá jako vyhrazený počítač.
     - **Typ použití**
       - Typ použití může být jednorázové nebo periodické poplatky.

5. **Filtry**

    K nastavení rozsahů vybraných hodnot použijte filtry s jedním nebo vícenásobným výběrem. Filtr nastavíte tak, že kliknete **Add** (Přidat) a pak vyberete kategorie a hodnoty filtru.

6. **Nákladový model**

    Vyberte nákladový model, který jste dříve vytvořili pomocí nástroje Cost Allocation 360. Podle požadavků na přidělení nákladů můžete mít několik nákladových modelů Cloudyn. Některé z vašich organizačních týmů můžou mít požadavky na přidělení nákladů, které se liší od ostatních. Každý tým může mít svůj vlastní vyhrazený nákladový model.

    Informace o vytvoření definice modelu přidělení nákladů najdete v tématu [Použití vlastních značek k přidělování nákladů](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortizace**

    Pomocí amortizace v sestavách přidělení nákladů můžete zobrazit linearizované poplatky za služby nesouvisející s využitím nebo jednorázově splatné náklady a rovnoměrně je rozložit na dobu jejich životnosti. Příklady jednorázových poplatků:
    - Roční poplatky za podporu
    - Roční poplatky za komponenty zabezpečení
    - Poplatky za nákup rezervovaných instancí
    - Některé položky z Azure Marketplace

   V části Amortization (Amortizace) vyberte **Amortized Cost** (Amortizované náklady) nebo **Actual Cost** (Skutečné náklady).

8. **Rozlišení**

    Zde můžete vybrat časové rozlišení v rámci vybraného rozsahu dat. Vaše časové rozlišení určuje, jak se budou jednotky zobrazovat v sestavě:
    - denně
    - týdně
    - měsíčně
    - čtvrtletně
    - ročně

9. **Pravidla přidělení**

    Pomocí pravidel přidělení můžete použít nebo zakázat přepočítání nákladů na přidělení nákladů. Můžete povolit nebo zakázat přepočítání přidělení nákladů pro fakturační údaje. Přepočítání se vztahuje na vybrané kategorie v sestavě. Umožňuje vyhodnotit dopad přepočtu přidělení nákladů na nezpracované fakturační údaje.

10. **Nezařazeno do kategorie**

    Možnost Nezařazeno do kategorie dovoluje v sestavě zahrnout nebo vyloučit náklady nezařazené do kategorií.

11. **Zobrazit/skrýt pole**

    Možnost zobrazit/skrýt nemá v sestavách žádný vliv.

12. **Formáty zobrazení**

    Formáty zobrazení slouží k výběru různých zobrazení grafu nebo tabulky.

    ![Symboly formátů zobrazení, které lze vybrat](./media/understanding-cost-reports/display-formats.png)

13. **Více barev**

    Díky možnosti více barev nastavíte barvu grafů v sestavě.

14. **Akce**

    Akce slouží k uložení, exportu nebo naplánování sestavy.

15. **Zásady**

    I když to není vidět, obsahují některé sestavy zásady výpočtu předpokládaných nákladů. V těchto sestavách zobrazují **Konsolidované** zásady doporučení pro všechny účty a předplatná v rámci aktuální entity, jako je například registrace do Microsoftu nebo plátce AWS. **Samostatné** zásady zobrazují doporučení pro jeden účet nebo předplatné, jako kdyby žádné jiné předplatné neexistovalo. Zásady, které vyberete, se liší podle strategie optimalizace používané ve vaší organizaci. Projekce nákladů jsou založené na posledních 30 dnech využití.

## <a name="save-and-schedule-reports"></a>Ukládání a plánování sestav

Po vytvoření sestavy ji můžete uložit pro pozdější použití. Uložené sestavy jsou k dispozici v **My Tools** > **My Reports** (Moje nástroje > Moje sestavy). Pokud provedete změny ve stávající sestavě a uložíte ji, sestava se uloží jako nová verze. Případně ji můžete uložit jako novou sestavu.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Uložení sestavy na portál Cloudyn

Při prohlížení jakékoli sestavy klikněte na **Actions** (Akce) a potom vyberte **Save to my reports** (Uložit do mých sestav). Pojmenujte sestavu a pak buď přidejte svoji vlastní adresu URL, nebo použijte automaticky vytvořenou adresu URL. Volitelně můžete sestavu nasdílet (**Share**) veřejně s ostatními uživateli ve vaší organizaci nebo ji můžete sdílet ve vaší entitě. Pokud sestavu nesdílíte, bude existovat jako osobní sestava, kterou si můžete prohlédnout jenom vy. Uložte sestavu.


### <a name="save-a-report-to-cloud-provider-storage"></a>Uložení sestavy do úložiště poskytovatele cloudu

Aby bylo možné sestavu uložit u poskytovatele cloudových služeb, musíte již mít nakonfigurovaný účet úložiště. Během prohlížení libovolné sestavy klikněte na **Actions** (Akce) a potom vyberte **Schedule report** (Naplánovat sestavu). Pojmenujte sestavu a pak buď přidejte svoji vlastní adresu URL, nebo použijte automaticky vytvořenou adresu URL. Vyberte **Save to storage** (Uložit do úložiště) a vyberte účet úložiště nebo přidejte nový. Zadejte předponu, která se připojí k názvu souboru sestavy. Vyberte formát souboru CSV nebo JSON a pak tuto sestavu uložte.

### <a name="schedule-a-report"></a>Naplánování sestavy

Sestavy můžete spouštět v naplánovaných intervalech a můžete je odeslat na seznam příjemců nebo do účtu úložiště poskytovatele cloudových služeb. Během prohlížení libovolné sestavy klikněte na **Actions** (Akce) a potom vyberte **Schedule report** (Naplánovat sestavu). Sestavu můžete odeslat e-mailem a uložit ji do účtu úložiště. V části **Schedule** (Plánování) vyberte interval (denně, týdně nebo měsíčně). V poli týdně a měsíčně vyberte den nebo datum, kdy chcete sestavu dodat, a vyberte čas. Uložte naplánovanou sestavu. Pokud jako formát sestavy vyberete Excel, bude sestava odeslána jako příloha. Pokud vyberete e-mailový formát, doručí se výsledky sestavy zobrazené ve formátu grafu jako graf.

### <a name="export-a-report-as-a-csv-file"></a>Export sestavy do souboru CSV

Během prohlížení libovolné sestavy klikněte na **Actions** (Akce) a potom vyberte **Export all report data** (Exportovat všechna data sestavy). Zobrazí se automaticky otevírané okno a stáhne se soubor CSV.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o sestavách, které jsou součástí služby Cloudyn, v tématu [Použití sestav Cloudyn](./use-reports.md).
- Přečtěte si, jak pomocí sestav vytvořit [řídicí panely](./dashboards.md).