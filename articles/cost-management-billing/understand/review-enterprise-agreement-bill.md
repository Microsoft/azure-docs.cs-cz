---
title: Kontrola informací na faktuře za smlouvu Azure Enterprise
description: Zjistěte, jak číst informace o využití a fakturu za smlouvy Azure Enterprise a jak je chápat.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 5d945c421dc0a4abed17b3a355339c131dd922df
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728045"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Vysvětlení informací na faktuře za smlouvu Azure Enterprise

Když zákazníci Azure se smlouvou Enterprise překročí kredit své organizace nebo použijí služby, na které se kredit nevztahuje, dostanou fakturu.

Kredit vaší organizace zahrnuje Azure Prepayment (dřív označovaný jako peněžní závazek). Azure Prepayment je částka, kterou vaše organizace předem zaplatila za využívání služeb Azure. Pokud chcete do své smlouvy Enterprise přidat prostředky Azure Prepaymentu, obraťte se na prodejce nebo správce účtu Microsoft.

Tento kurz se týká zákazníků Azure, kteří mají smlouvu Azure Enterprise.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zkontrolovat fakturované poplatky
> * Zkontrolovat poplatky za nadlimitní využití služby
> * Zkontrolovat fakturu za Marketplace

## <a name="prerequisites"></a>Požadavky

Pokud chcete zkontrolovat a ověřit poplatky na své faktuře, musíte být podnikovým správcem. Další informace najdete v tématu [Principy rolí pro správu smlouvy Azure Enterprise v Azure](../manage/understand-ea-roles.md). Pokud nevíte, kdo je ve vaší organizaci podnikovým správcem, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Kontrola fakturovaných poplatků pro většinu zákazníků

Tato část se netýká zákazníků Azure v Austrálii, Japonsku a Singapuru.

Pokud během fakturačního cyklu dojde k některé z následujících situací, dostanete fakturu za Azure:

- **Nadlimitní využití služby:** Poplatky za využití pro vaši organizace překročí váš kredit.
- **Poplatky účtované zvlášť:** Služby použité vaší organizací, na které se nevztahuje kredit. Bez ohledu na zůstatek vašeho kreditu se vám budou fakturovat následující služby:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registrovaný uživatel
    - Openlogic
    - Registrovaný uživatel s oprávněním ke vzdálenému přístupu XenApp Essentials
    - Ubuntu Advantage
    - Visual Studio Enterprise (měsíční)
    - Visual Studio Enterprise (roční)
    - Visual Studio Professional (měsíční)
    - Visual Studio Professional (roční)
- **Poplatky za Marketplace:** Kredit vaší organizace se nevztahuje na nákupy na webu Azure Marketplace a jejich využívání. Poplatky za Marketplace se vám tedy naúčtují bez ohledu na zůstatek vašeho kreditu. Podnikový správce může povolit nebo zakázat nákupy na webu Marketplace na webu Enterprise Portal.

Na vaší faktuře se zobrazí poplatky za využití Azure s náklady, které jsou k nim přidružené, a za nimi následují případné poplatky za marketplace. Pokud máte kreditní zůstatek, použije se na využití Azure. Na faktuře se uvede využití Azure a využití marketplace bez jakýchkoli nákladů.

V části **Sestavy** > **Souhrn využití** můžete porovnat souhrnnou celkovou částku uvedenou na webu Enterprise Portal s vaší fakturou za Azure. Částky v části **Souhrn využití** jsou uvedené bez daně.

Přihlaste se k [portálu Azure EA](https://ea.azure.com). Vyberte **Reports** (Sestavy). V pravém horním rohu karty přepněte zobrazení z **M** na **C** a nastavte stejné období jako na faktuře.  

![Snímek obrazovky zobrazující možnost M + C v souhrnu využití](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Souhrnná částka za **Celkové využití** a **Azure Marketplace** by měla odpovídat hodnotě **Celková rozšířená částka** na vaší faktuře. Další podrobnosti o vašich poplatcích najdete v části **Stažení dat o využití**.  

![Snímek obrazovky s kartou pro stažení dat o využití](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Kontrola fakturovaných poplatků pro ostatní zákazníky

Tato část se týká jenom zákazníků Azure v Austrálii, Japonsku a Singapuru.

Když nastane některá z následujících situací, dostanete jednu nebo více faktur Azure:

- **Nadlimitní využití služby:** Poplatky za využití pro vaši organizace překročí váš kredit.
- **Poplatky účtované zvlášť:** Služby použité vaší organizací, na které se nevztahuje kredit. Fakturují se vám následující služby:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registrovaný uživatel
    - Openlogic
    - Registrovaný uživatel s oprávněním ke vzdálenému přístupu XenApp Essentials
    - Ubuntu Advantage
    - Visual Studio Enterprise (měsíční)
    - Visual Studio Enterprise (roční)
    - Visual Studio Professional (měsíční)
    - Visual Studio Professional (roční)
- **Poplatky za Marketplace:** Kredit vaší organizace se nevztahuje na nákupy na webu Azure Marketplace a jejich využívání a tyto nákupy a využívání se budou fakturovat zvlášť. Podnikový správce může povolit nebo zakázat nákupy na webu Marketplace na webu Enterprise Portal.

Pokud se vám během jednoho fakturačního období naúčtují poplatky za nadlimitní využití služeb a poplatky účtované zvlášť, dostanete jednu fakturu. Ta bude obsahovat oba typy poplatků. Poplatky za Marketplace se vždycky fakturují samostatně.

## <a name="review-service-overage-charges-for-other-customers"></a>Kontrola poplatků za nadlimitní využití služby pro ostatní zákazníky

Tato část se vás týká jenom v případě, že se nacházíte v Austrálii, Japonsku nebo Singapuru.

V části **Sestavy** > **Souhrn využití** můžete porovnat souhrnnou celkovou částku za využití uvedenou na webu Enterprise Portal s vaší fakturou za nadlimitní využití služby. Faktura za nadlimitní využití služby zahrnuje využití, které překračuje kredit vaší organizace, a služby, na které se kredit nevztahuje. Částky v části **Souhrn využití** jsou uvedené bez daně.

Přihlaste se k [portálu Azure EA](https://ea.azure.com) a potom vyberte **Reports** (Sestavy). V pravém horním rohu karty přepněte zobrazení z **M** na **C** a nastavte stejné období jako na faktuře.  

![Snímek obrazovky zobrazující možnost M + C v souhrnu využití](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Částka **Celkové využití** by měla odpovídat hodnotě **Celková rozšířená částka** na vaší faktuře za nadlimitní využití služby. Další informace o poplatcích najdete v části **Stažení dat o využití** > **Stažení rozšířené sestavy**. Tato sestava nezahrnuje daně, poplatky za rezervace ani poplatky za Marketplace.  

![Snímek obrazovky ukazující možnost stažení rozšířené sestavy na kartě pro stažení dat o využití](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

Následující tabulka obsahuje výrazy uvedené na faktuře a v části **Souhrn využití** na webu Enterprise Portal a jejich popisy:

|Výraz na faktuře|Výraz v souhrnu využití|Popis|
|---|---|---|
|Celková rozšířená částka|Celkové využití|Celkový poplatek za využití před zdaněním za konkrétní období před uplatněním kreditu.|
|Využití v rámci závazku|Využití v rámci závazku|Kredit uplatněný za dané období.|
|Celkový prodej|Celkové nadlimitní využití|Celkový poplatek za využití, které překračuje částku kreditu. Tato částka nezahrnuje daň.|
|Částka daně|Neuvedeno|Daň, která se uplatňuje na částku celkového prodeje za dané období.|
|Celková částka|Neuvedeno|Splatná částka faktury po uplatnění kreditu a přičtení daně.|

### <a name="review-marketplace-invoice"></a>Kontrola faktury za Marketplace

Tato část se vás týká jenom v případě, že se nacházíte v Austrálii, Japonsku nebo Singapuru.

V části **Sestavy** > **Souhrn využití** na portálu Enterprise Portal můžete porovnat celkovou částku za Azure Marketplace s vaší fakturou za Marketplace. Faktura za marketplace je pouze za nákupy a využití na Azure Marketplace. Částky v **souhrnu využití** už zahrnují daň, kterou určuje vydavatel.

Přihlaste se k [portálu Enterprise](https://ea.azure.com) a potom vyberte **Reports** (Sestavy). V pravém horním rohu karty přepněte zobrazení z **M** na **C** a nastavte stejné období jako na faktuře.  

![Snímek obrazovky zobrazující možnost M + C v souhrnu využití](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

Celková částka za **Azure Marketplace** by měla odpovídat hodnotě **Celkový prodej** na vaší faktuře za Marketplace. Další informace o účtovaných poplatcích podle využití najdete v části **Stažení dat o využití**. V části **Poplatky za Marketplace** vyberte **Stáhnout**. Cena za Marketplace zahrnuje daň, jak stanovuje vydavatel. Zákazníci nedostanou samostatnou fakturu od vydavatele pro shromáždění daně transakce.

![Snímek obrazovky s možností stažení v části poplatků za Marketplace](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="view-price-sheet-information"></a>Zobrazení informací z ceníku

Podnikoví správci můžou zobrazit ceník, který platí pro zaregistrované služby Azure.

Zobrazení aktuálního ceníku:

1. Na portálu Azure Enterprise Portal vyberte **Reports** (Sestavy) a pak vyberte **Price Sheet** (Ceník).
2. Prohlédněte si ceník nebo vyberte **Download** (Stáhnout).

![Příklad informací z ceníku](./media/review-enterprise-agreement-bill/ea-create-view-price-sheet-information.png)

Stažení historického ceníku:

1. Na portálu Azure Enterprise Portal vyberte **Reports** (Sestavy) a pak vyberte **Download Usage** (Stáhnout údaje o využití).
2. Stáhněte si ceník.

![Příklad stažení staršího ceníku](./media/review-enterprise-agreement-bill/create-ea-view-price-sheet-download-historical-price-list.png)

Tady jsou některé důvody rozdílů v cenách:

- Změna cen mezi předchozí a novou registrací. Ke změnám cen může dojít, protože jde o ceny smluvní platné pro konkrétní registraci, a to od data zahájení do data ukončení smlouvy.
- Při přechodu na novou registraci se ceny změní na novou smlouvu. Ceny jsou definovány vaším ceníkem, který může být u nové registrace vyšší.
- Ceny se mění i při prodloužení lhůty registrace. Ceny se mění na sazby pro průběžné platby.

## <a name="request-detailed-usage-information"></a>Žádost o podrobné informace o využití

Podnikoví správci si na portálu Azure Enterprise Portal můžou zobrazit souhrnné údaje o využití, výši spotřebované zálohy na Azure a poplatky za další využití. Tyto poplatky jsou vyjádřené souhrnně za všechny účty a všechna předplatná.

Pokud si chcete prohlédnout podrobné údaje o využití konkrétních účtů, stáhněte si sestavu podrobných informací o využití tím, že přejdete na **Reports** (Sestavy)  >  **Download Usage** (Stáhnout údaje o využití).

> [!NOTE]
> Sestava s podrobnostmi využití nezahrnuje příslušné daně. Mezi časem skutečného použití a promítnutím tohoto údaje do sestavy může vzniknout zpoždění až osm hodin.

U nepřímých registrací je potřeba, aby partner před zobrazením informací o cenách nejprve zapnul funkci přirážky.

## <a name="reports"></a>Sestavy

Podnikoví správci si na portálu Azure Enterprise Portal můžou zobrazit souhrnné údaje o využití, výši spotřebované zálohy na Azure a poplatky za další využití. Tyto poplatky jsou vyjádřené souhrnně za všechny účty a všechna předplatná.

### <a name="azure-enterprise-reports"></a>Sestavy Azure Enterprise

- Souhrn a grafy využití
- Sestava využití služeb
- Sestava zůstatků a poplatků
- Sestava s podrobnostmi o využití
- Sestava poplatků za Azure Marketplace
- Ceník
- Stažení rozšířené sestavy
- Formátování sestav CSV

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>Zobrazení sestav a grafů se souhrnem využití:

1. Přejděte na portál Azure Enterprise Portal.
1. V levém podokně vyberte **Reports** (Sestavy).
1. Klikněte na kartu **Usage Summary** (Souhrn využití).
1. V nabídce rozsahů dat v levém horním rohu vyberte období závazku.
1. Výběrem období nebo měsíce v grafu si zobrazíte další podrobnosti.
1. Na této kartě:
   - Můžete zobrazit graf meziměsíčního využití s rozpisem využití, nadměrného vyúčtování služeb, poplatků fakturovaných samostatně a poplatků za Azure Marketplace.
   - Pod grafem můžete filtrovat podle oddělení, účtů a předplatných.
   - Můžete přepínat mezi rozpisem **vyúčtování podle služeb** a rozpisem **vyúčtování podle hierarchie**.
   - Můžete si podrobně prohlédnout služby Azure, samostatně fakturované poplatky a poplatky za Azure Marketplace.

## <a name="service-usage-report"></a>Sestava využití služeb

Na stránce sestavy využití služeb můžou podnikoví správci zobrazit souhrn údajů o využití služeb. Využití je vyjádřené souhrnně za všechny účty a všechna předplatná. Pokud chcete zobrazit podrobné využití, můžete sestavu filtrovat podle účtů nebo předplatných.

> [!NOTE]
> Mezi datem skutečného využití a jeho zobrazením v této sestavě může docházet ke zpoždění až 5 dnů.

### <a name="to-view-the-report"></a>Zobrazení sestavy:

1. Přihlaste se k portálu Azure Enterprise Portal.
1. V levém navigačním panelu vyberte **Reports** (Sestavy).
1. Klikněte na kartu **Usage Summary** (Souhrn využití).
1. Vyberte rozsah dat.
1. Zvolte účty nebo předplatná, která chcete zobrazit.
1. Volitelně:
   - Můžete změnit zobrazení **Vyúčtování podle služeb** na **Vyúčtování podle hierarchie** a zobrazit různé rozpisy.
   - Prohlédněte si podrobnosti názvu služby, měrné jednotky, spotřebovaných jednotek, skutečné sazby a rozšířených nákladů.

## <a name="download-csv-reports"></a>Stahování sestav CSV

Stránka s měsíčními sestavami umožňuje podnikovým správcům stáhnout několik sestav ve formátu CSV. Sestavy ke stažení zahrnují:

- Sestava zůstatků a poplatků
- Sestava s podrobnostmi o využití
- Sestava poplatků za Azure Marketplace
- Ceník

### <a name="to-download-reports"></a>Jak sestavy stáhnout:

1. Na portálu Azure Enterprise Portal vyberte **Report** (Sestava).
1. Na horním pásu karet vyberte **Stáhnout informace o využití**.
1. Vedle sestavy pro odpovídající měsíc vyberte **Stáhnout**.

### <a name="csv-report-formatting-issues"></a>Problémy s formátováním sestav CSV

Zákazníci, kteří zobrazují sestavy CSV na portálu Azure Enterprise Portal v eurech, můžou zaznamenat problémy s formátováním související s čárkami a tečkami.

Například se může zobrazit následující:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Hodiny | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Měli byste vidět tohle:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Hodiny | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

Tento problém s formátováním způsobuje výchozí nastavení funkce importu aplikace Excel. Excel importuje všechna pole jako obecný text a předpokládá, že čísla jsou oddělená v matematickém standardu. Příklad: 1,000.00.

Pokud evropská měna používá jako oddělovač tisíců tečku (.) a jako oddělovač desetinných míst čárku (,), zobrazí se nesprávně. Příklad: 1.000,00. Výsledky importu se mohou lišit v závislosti na vašem nastavení místního jazyka.

### <a name="to-import-the-csv-file-without-formatting-issues"></a>Import souboru CSV bez problémů s formátováním:

1. V Microsoft Excelu přejděte na **Soubor** > **Otevřít**.
   Zobrazí se Průvodce importem textu.
1. V části **Zdrojový datový typ** zvolte **Oddělovač**.  Výchozí hodnota je **Pevná šířka**.
1. Vyberte **Další**.
1. V části Oddělovače zaškrtněte políčko u možnosti **Čárka**. Pokud je vybraná možnost **Tabulátor**, zrušte zaškrtnutí tohoto políčka.
1. Vyberte **Další**.
1. Posuňte se ke sloupcům **ResourceRate** a **ExtendedCost**.
1. Vyberte sloupec **ResourceRate**. Zobrazuje se černě zvýrazněný.
1. V části **Formát dat ve sloupcích** místo možnosti **Obecný** vyberte **Text**. Záhlaví sloupce se změní z **Obecný** na **Text**.
1. Zopakujte kroky 8 a 9 i pro sloupec **ExtendedCost** a pak vyberte **Dokončit**.

> [!TIP]
> Pokud máte nastaveno, aby se soubory CSV otevíraly automaticky v Excelu, musíte místo toho použít funkci **Otevřít** v Excelu. V Excelu přejděte na **Soubor** > **Otevřít**.

## <a name="balance-and-charge-report"></a>Sestava zůstatků a poplatků

Sestava zůstatků a poplatků nabízí měsíční souhrn informací o zůstatcích, nových nákupech, poplatcích za služby z Azure Marketplace, vyrovnáních a poplatcích za nadlimitní využití.

Všechny řádky v tabulce souhrnu závazku služby Azure zůstávají v průběhu měsíců statické. Podrobnosti pro všechny nákupy a úpravy najdete v částech **New Purchase Details** (Podrobnosti nového nákupu) a **Adjustment Details** (Podrobnosti úpravy).

### <a name="download-the-balance-and-charge-report"></a>Stažení sestavy zůstatků a poplatků

1. Přihlaste se k portálu Azure Enterprise Portal jako podnikový správce.
1. V levém podokně vyberte **Reports** (Sestavy).
1. Klikněte na kartu **Report Download** (Stažení sestavy).
1. Ve sloupci **Balance and Charge** (Zůstatek a poplatky) vyberte odpovídající měsíc a výběrem stáhněte sestavu.

## <a name="usage-detail-report"></a>Sestava s podrobnostmi o využití

Sestava s podrobnostmi o využití nabízí měsíční souhrn spotřeby služeb a množství v registraci. Obsahuje informace o názvech měřičů, typech měřičů a spotřebovaných množstvích.

### <a name="download-the-usage-detail-report"></a>Stažení sestavy s podrobnostmi o využití

1. Přihlaste se k portálu Azure Enterprise Portal jako podnikový správce.
1. V levém navigačním panelu vyberte **Reports** (Sestavy).
1. Vyberte kartu **Download Usage** (Stáhnout data o využití).
1. Ve sloupci **Usage Detail** (Podrobnosti o využití) vyberte odpovídající měsíc a výběrem stáhněte sestavu.

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Poplatky za Azure Marketplace v sestavách na portálu Azure Enterprise

Existují dva typy poplatků za Azure Marketplace:

- **Podle využití:** Produkty měřené v transakčních jednotkách.  Například virtuální počítače se účtují po hodinách a vyhledávání přes rozhraní API Bingu se účtuje podle počtu hledání.
- **Měsíční poplatek:** Účtuje se měsíčně na základě využití nebo přístupu.

Další informace o poplatcích za Azure Marketplace najdete v [nejčastější dotazech k Azure Marketplace](https://azure.microsoft.com/marketplace/faq/).

Zobrazení různých poplatků na portálu Azure Enterprise Portal:

- **Sestava souhrnu využití:** Ukazuje poplatky podle využití **i** měsíční poplatky za Azure Marketplace.
- **Sestava poplatků za marketplace:** Zobrazuje **pouze** poplatky za Azure Marketplace založené na využití.  Jednorázové poplatky se nezobrazují.

> [!NOTE]
> Azure Marketplace není k dispozici pro registrace MPSA.

## <a name="advanced-report-download"></a>Stažení rozšířené sestavy

Při generování sestav pro konkrétní rozsahy dat nebo účty je možné využít stažení rozšířené sestavy. Od 30. srpna 2016 je formátem výstupního souboru formát CSV, který podporuje větší sady záznamů.

1. Na portálu Azure Enterprise Portal vyberte **Advanced Report Download** (Stažení rozšířené sestavy).
1. Vyberte **odpovídající rozsah dat**.
1. Vyberte **odpovídající účty**.
1. Vyberte **Požádat o data o využití**.
1. Opakovaně vyberte tlačítko **Update** (Aktualizovat), dokud se stav sestavy neaktualizuje na **Download** (Stáhnout).
1. Stáhněte sestavu.

## <a name="reporting-for-non-enterprise-administrators"></a>Generování sestav pro nepodnikové správce

Podnikoví správci můžou udělit oprávnění zobrazit poplatky za registraci správcům oddělení a vlastníkům účtů v rámci registrace. Vlastníci účtů s přístupem můžou stahovat sestavy CSV specifické pro jejich účet a předplatná. Informace si můžou zobrazit také vizuálně v části sestav na portálu Azure Enterprise Portal.

### <a name="to-enable-access"></a>Povolení přístupu:

 1. Přihlaste se k portálu Azure Enterprise Portal jako podnikový správce.
 1. V levém navigačním panelu vyberte **Manage** (Spravovat).
 1. Klikněte na kartu **Enrollment** (Registrace).
 1. V části **Enrollment Detail** (Podrobnosti o registraci) vyberte ikonu tužky vedle **DA View Charges** (Zobrazení poplatků pro správce oddělní) nebo **AO View Charges** (Zobrazení poplatků pro vlastníky účtů).
 1. Vyberte **Povoleno**.
 1. Vyberte **Uložit**.

### <a name="to-view-reports"></a>Zobrazení sestav:

1. Přihlaste se k portálu Azure Enterprise Portal jako správce oddělení nebo vlastník účtu.
1. V levém navigačním panelu vyberte **Reports** (Sestavy).
1. Výběrem karty **Usage Summary** (Souhrn využití) zobrazte vizuální informace o účtu a předplatném.
1. Výběrem možnosti **Usage Download** (Stažení údajů o využití) zobrazte sestavy CSV.

Pokud správci oddělení a vlastníci účtů používají funkci **Stažení rozšířené sestavy**, nemůžou zobrazit poplatky. V nákladech se zobrazí 0 USD.

Oprávnění vlastníka účtu zobrazit poplatky se vztahuje na vlastníky účtů a všechny uživatele, kteří mají oprávnění k přidruženým předplatným. Pokud jste nepřímý zákazník, váš distribuční partner musí povolit funkce nákladů.

## <a name="power-bi-reporting"></a>Generování sestav Power BI

Generování sestav Power BI je k dispozici pro přímé a nepřímé zákazníky a partnery EA, kteří mají přístup k zobrazení fakturačních údajů.

### <a name="power-bi-pro"></a>Power BI Pro

Power BI Pro je k dispozici pro zákazníky EA. Pomocí Power BI Pro můžete generovat a sdílet sestavy a efektivně tak spravovat údaje o nákladech. Má také další funkce pro spolupráci a aktualizaci dat. Power BI Pro nabízí vyšší datovou kapacitu a limity streamování dat.

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer.
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>Přístup k Microsoft Azure Consumption Insights:

1. Přejděte na [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Vyberte **Get It Now** (Získat).
1. Zadejte číslo registrace a počet měsíců a potom vyberte **Next** (Další).
1. Zadejte svůj přístupový klíč rozhraní API pro připojení. Klíč pro vaši registraci najdete na portálu [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Výběrem **Sign In** (Přihlásit se) automaticky spusťte proces importu.
1. Po dokončení se v navigačním podokně zobrazí nový řídicí panel, sestava a model. Výběrem řídicího panelu zobrazte importovaná data.

> [!TIP]
>
> - Informace o možnostech vygenerování klíče rozhraní API pro vaši registraci najdete v souboru nápovědy k sestavám rozhraní API na portálu [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
> - Další informace o připojení Power BI k vašemu využití Azure najdete v tématu [Microsoft Azure Consumption Insights](/power-bi/desktop-connect-azure-cost-management).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Získání přístupu ke starší verzi balíčku obsahu EA pro Power BI:

1. Přejděte na [web Power BI](https://app.powerbi.com/getdata/services/azure-enterprise).
1. Přihlaste se pomocí platného pracovního nebo školního účtu.

   Může se jednat o stejný pracovní nebo školní účet, který používáte pro přístup k registraci prostřednictvím portálu Azure Enterprise Portal, nebo jiný pracovní nebo školní účet.
1. Na řídicím panelu služeb vyberte **Microsoft Azure Enterprise** a vyberte **Připojit**.
1. Na obrazovce **Připojit k Azure Enterprise** vyplňte pole:
    - Adresa URL prostředí Azure: [https://ea.azure.com](https://ea.azure.com/)
    - Počet měsíců: od 1 do 36
    - Číslo registrace: číslo vaší registrace
1. Vyberte **Další**.
1. Do pole **Authentication Key** (Ověřovací klíč) zadejte klíč rozhraní API.

    Klíč rozhraní API můžete získat na portálu Azure Enterprise Portal na kartě **Download Usage** (Stažení údajů o využití). Vyberte **API Access Key** (Přístupový klíč rozhraní API) a potom tento klíč vložte do pole **Account Key** (Klíč účtu).
1. Načtení dat v Power BI bude v závislosti na velikosti datových sad trvat přibližně 5 až 30 minut.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Glosář procesů pro výpočet sazby za prostředek

- **Celkový počet jednotek RAW:** Spotřebované množství v podrobné sestavě využití.
- **Prostředky MOCP na jednotku:** Upstreamový systém využití pro každou službu generuje údaje o využití v různých jednotkách. (Přednastavená hodnota)
- **Spotřeba na jednotku:** Měrná jednotka Azure Enterprise. (Přednastavená hodnota)
- **Cena:** Jednotková cena na portálu Azure Enterprise Portal.
- **Celkové náklady:** Rozšířené náklady z podrobné sestavy využití nebo využití závazku + nadlimitní využití z portálu Azure Enterprise Portal.

### <a name="charges-calculations"></a>Výpočty poplatků

- **Převod na spotřebované prostředky MOCP** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **Převod na spotřebované jednotky** = `Consumed MOCP Resources / Consumption per Unit`
- **Výpočet celkových nákladů** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>Logika v logice výpočtu využití

**Sazba za prostředek** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

Sazba za prostředek se odvozuje podle vašich poplatků. Nemusí se shodovat se skutečnou jednotkovou cenou v ceníku.

V sestavě údajů o využití ke stažení můžete vidět nezpracované využití prostředků s až šesti desetinnými místy. Tato data se používají pro výpočty poplatků za nadlimitní využití. Portál Azure Enterprise Portal ale údaje o využití zaokrouhluje na čtyři desetinná čísla v případě jednotek závazku a zkracuje na nula desetinných míst v případě jednotek nadlimitního využití. Na portálu Azure Enterprise Portal se všechna nadlimitní využití účtují jenom za celé jednotky. U využití, které se účtuje jako nadlimitní nebo ve smíšených měsících, bude mezi jednotkovou cenou a sazbou za prostředek velký rozdíl.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Zkontrolovat fakturované poplatky
> * Zkontrolovat poplatky za nadlimitní využití služby
> * Zkontrolovat fakturu za Marketplace

Další informace o použití portálu Azure EA najdete v dalším článku.

> [!div class="nextstepaction"]
> [Začínáme s portálem Azure EA](../manage/ea-portal-get-started.md)