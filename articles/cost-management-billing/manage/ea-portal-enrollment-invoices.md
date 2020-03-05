---
title: Faktury za smlouvu Azure Enterprise
description: V tomto článku je vysvětlená správa faktury za Azure Enterprise a její další použití.
author: bandersmsft
ms.author: banders
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: df2d4eb1b28ac9f13511692ca7588414ea4fdff0
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78272385"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Faktury za smlouvu Azure Enterprise

V tomto článku je vysvětlená správa faktury za smlouvu Azure Enterprise (Azure EA) a její další použití. Vaše faktura představuje vyúčtování. Zkontrolujte její správnost. Měli byste se také seznámit s dalšími úkoly, které při správě faktury můžete potřebovat.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Změna čísla nákupní objednávky u faktury za nadlimitní využití

Portál Azure Enterprise Portal automaticky generuje výchozí číslo nákupní objednávky, pokud podnikový správce nenastavil před datem faktury jiné číslo. Podnikový správce může číslo nákupní objednávky aktualizovat až sedm dnů od přijetí automatického e-mailu s oznámením o faktuře.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Aktualizace čísla nákupní objednávky služeb Azure:

1. Na portálu Azure Enterprise Portal vyberte **Report** (Sestava) > **Usage Summary** (Souhrn využití).
1. V pravém horním rohu vyberte **Edit PO Numbers** (Upravit čísla nákupních objednávek).
1. Vyberte přepínač **Služby Azure**.
1. V rozevírací nabídce pro výběr rozsahu dat vyberte **Fakturační období**.

   Číslo nákupní objednávky můžete upravit během období 7 dnů po přijetí oznámení o faktuře, musí to ale být dříve, než fakturu uhradíte.
1. Do pole **PO Number**  (Číslo nákupní objednávky) zadejte nové číslo nákupní objednávky.
1. Pokud chcete změnu odeslat, vyberte **Save**  (Uložit).

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>Aktualizace čísla nákupní objednávky na Azure Marketplace:

1. Na portálu Azure Enterprise Portal vyberte **Report** (Sestava) > **Usage Summary** (Souhrn využití).
1. V pravém horním rohu vyberte **Edit PO Numbers** (Upravit čísla nákupních objednávek).
1. Vyberte přepínač **Marketplace**.
1. V rozevírací nabídce pro výběr rozsahu dat vyberte **Fakturační období**.

   Číslo nákupní objednávky můžete upravit během období 7 dnů po přijetí oznámení o faktuře, musí to ale být dříve, než fakturu uhradíte.
1. Do pole **PO Number**  (Číslo nákupní objednávky) zadejte nové číslo nákupní objednávky.
1. Pokud chcete změnu odeslat, vyberte **Save**  (Uložit).

## <a name="cadence-of-azure-enterprise-billing"></a>Četnost fakturace Azure Enterprise

Microsoft fakturuje všechny závazné nákupy služeb Microsoft Azure na roční bázi, a to k datu vstoupení smlouvy v platnost. Veškeré využití nad rámec závazku účtuje Microsoft v nedoplatcích.

- Poplatky související se závazky se uvádějí na základě měsíční sazby a fakturují se předem za nadcházející rok.
- Poplatky za nadlimitní využití se počítají každý měsíc a fakturují se zpětně na konci fakturačního období.

### <a name="billing-intervals"></a>Fakturační intervaly

Fakturační interval závisí na tom, jak se rozhodnete provádět závazné nákupy. Váš roční závazek se kryje s:

- datem výročí registrace,
- datem platnosti jednoročního doplňujícího předplatného.

Datum, kdy obdržíte fakturu za nadlimitní využití, závisí na počátečním datu a nastavení vaší registrace:

- **Přímé registrace s počátečním datem před 1. květnem 2018:**
  - Pokud jste přímým zákazníkem Enterprise Agreement (EA), máte pro služby Azure (kromě Azure Marketplace) nastavené roční fakturační období. Fakturační cyklus vychází z data výročí, což je datum, kdy vaše smlouva začala platit.
  - Pokud překročíte 150 % prahové hodnoty peněžního závazku smlouvy EA, budete automaticky převedeni na čtvrtletní fakturační cyklus, který je založený na vašem datu výročí. Obdržíte také fakturu za nadlimitní využití služeb Azure.
  - Pokud nepřekročíte 150 % prahové hodnoty peněžního závazku, zůstane registrace v ročním fakturačním cyklu. Fakturu za nadlimitní využití obdržíte na konci roku tohoto závazku.

- **Přímé registrace s počátečním datem po 1. květnu 2018:**
  - Pro faktury za využití Azure a poplatky fakturované samostatně platí měsíční fakturační období.
  - Jakékoli poplatky, které nejsou pokryté vaším peněžním závazkem, jsou splatné jako poplatek za nadlimitní využití.  

- **Nepřímé registrace s počátečním datem před 1. květnem 2018:**

  Pokud jste nepřímým zákazníkem se smlouvou Enterprise Agreement (EA) a počátečním datem před 1. květnem 2018, máte nastavený čtvrtletní fakturační cyklus. Distribuční partner bude fakturovat přímo vám.  

- **Nepřímé registrace s počátečním datem po 1. květnu 2018:**

  Máte měsíční fakturační cyklus.  

### <a name="increase-your-monetary-commitment"></a>Zvýšení peněžního závazku

Závazek můžete kdykoli zvýšit. Bude se vám účtovat počet měsíců zbývajících v období závazku daného roku. Například pokud si zaregistrujete roční doplňující předplatné a v šestém měsíci zvýšíte svůj závazek, bude se vám toto zvýšení fakturovat za zbývajících šest měsíců daného období. Na posledních šest měsíců období vašeho závazku se pak aktualizuje výše vašeho závazku. Tato nová množství se použijí pro stanovení poplatků za nadlimitní využití.

### <a name="overage"></a>Překročení

Jako nadlimitní využití se vám bude fakturovat využití rezervací, které během daného fakturačního období překročí váš závazek. Pokud chcete zobrazit rozpis výpočtu nadlimitního využití jednotlivých položek, projděte si sestavu souhrnu využití nebo se obraťte na svého distribučního partnera.

U každé položky na faktuře uvidíte položky:

- **Rozšířená částka:** poplatky celkem
- **Využití závazku:** množství závazku použité na uhrazení poplatků
- **Čistá částka**: poplatky, které překračují váš závazek

Příslušné daně se počítají pouze z čisté částky nad rámec závazku.

Fakturace nadlimitního využití je automatizovaná. Načasování oznámení a faktur závisí na koncovém datu fakturačního období.

- Oznámení o nadlimitním využití se obvykle posílá sedm dnů po koncovém datu fakturace.
- Faktury za nadlimitní využití se odesílají sedm až devět dnů po oznámení.
- Během sedmi dnů mezi oznámením a fakturací za nadlimitní využití si můžete prohlédnout poplatky a aktualizovat systémem vygenerovaná čísla nákupních objednávek.

### <a name="azure-marketplace"></a>Azure Marketplace

Počínaje fakturačním cyklem od dubna 2019 začali zákazníci dostávat jednu fakturu Azure kombinující v jedné faktuře veškeré poplatky za Azure a Azure Marketplace ze dvou různých faktur. Tato změna nemá vliv na zákazníky v Austrálii, Japonsku ani Singapuru.

Během přechodu na kombinovanou fakturu obdržíte částečnou fakturu za Azure Marketplace. Na této poslední samostatné faktuře se zobrazí poplatky za Azure Marketplace účtované před datem aktualizace fakturace. Poplatky za Azure Marketplace účtované po tomto datu se zobrazí na vaší faktuře Azure. Po skončení přechodného období se všechny poplatky za Azure a Azure Marketplace budou zobrazovat na kombinované faktuře.  

## <a name="adjust-billing-frequency"></a>Úprava frekvence fakturace

Vystavování faktur zákazníkům probíhá ročně, čtvrtletně nebo měsíčně. Fakturační cyklus si zákazník volí při podpisu smlouvy. Nejkratším fakturačním intervalem je měsíc.

- Změnu fakturačního cyklu u přímých registrací z ročního na čtvrtletní musí **odsouhlasit** podnikový správce. U nepřímých registrací musí změnu schválit správce partnera. Změna se uskuteční na konci aktuálního fakturačního čtvrtletí.
- Ke změně ročního nebo čtvrtletního fakturačního cyklu na měsíční je nutný **dodatek** smlouvy.  Jakékoli změny fakturačního cyklu u stávající registrace vyžadují souhlas podnikového správce nebo příjemce faktury.
- Souhlas **odešlete** na [podporu portálu Azure Enterprise Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Vyberte kategorii problému: **Fakturace**.

Změna se uskuteční na konci aktuálního fakturačního čtvrtletí.

Pokud jste podepsali dodatek č. M503, můžete frekvenci kterékoli smlouvy změnit na měsíční fakturaci. 

## <a name="credits-and-adjustments"></a>Kredity a úpravy

Všechny kredity a úpravy použité na vaši registraci zobrazíte v části **Reports** (Sestavy) na [portálu Azure Enterprise Portal](https://ea.azure.com).

Zobrazení kreditů:

1. Na [portálu Azure Enterprise Portal](https://ea.azure.com) vyberte část **Reports** (Sestavy).
1. Vyberte **Usage Summary** (Souhrn využití).
1. V pravém horním rohu změňte zobrazení z **M** na **C**.
1. V tabulce závazku služby Azure rozšiřte pole pro úpravy.
1. Zobrazí se kredity, které se vztahují k vaší registraci, a krátké vysvětlení. Příklad: Kredit smlouvy SLA

## <a name="request-an-invoice-copy"></a>Žádost o kopii faktury

Pokud chcete požádat o kopii faktury, obraťte se na partnera.

## <a name="overage-offset"></a>Posun nadlimitního využití

Pokud chcete peněžní závazek použít na nadlimitní využití, musíte splnit následující podmínky:

- Musíte mít neuhrazené poplatky za nadlimitní využití, které nejsou starší než jeden rok od data ukončení fakturované služby.
- Dostupný peněžní závazek pokrývá celou částku účtovaných poplatků, včetně všech předchozích neuhrazených faktur za Azure.
- Termín fakturace, který chcete dokončit, musí být plně uzavřený. Fakturace se plně uzavírá po pátém dni v měsíci.
- Fakturační období, které chcete posunout, musí být plně uzavřené.
- Sleva ACD (Azure Commitment Discount) vychází ze skutečného nového závazku po odečtení prostředků naplánovaných k předchozí spotřebě. Tento požadavek se týká jenom skutečně vzniklých poplatků za nadlimitní využití. Je platný jenom u služeb, které vyžadují peněžní závazek, takže ho nemůžete použít na poplatky za Azure Marketplace. Poplatky za Azure Marketplace se fakturují zvlášť.

Pokud chcete vyrovnat nadlimitní využití, můžete vy nebo tým účtu otevřít žádost o podporu. Požaduje se e-mail se schválením od vašeho podnikového správce nebo příjemce faktury.

## <a name="view-price-sheet-information"></a>Zobrazení informací z ceníku

Podnikoví správci můžou zobrazit ceník, který platí pro zaregistrované služby Azure.

Zobrazení aktuálního ceníku:

1. Na portálu Azure Enterprise Portal vyberte **Reports** (Sestavy) a pak vyberte **Price Sheet** (Ceník).
2. Prohlédněte si ceník nebo vyberte **Download** (Stáhnout).

![Příklad informací z ceníku](./media/ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Stažení historického ceníku:

1. Na portálu Azure Enterprise Portal vyberte **Reports** (Sestavy) a pak vyberte **Download Usage** (Stáhnout údaje o využití).
2. Stáhněte si ceník.

![Příklad stažení staršího ceníku](./media/ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

Tady jsou některé důvody rozdílů v cenách:

- Změna cen mezi předchozí a novou registrací. Ke změnám cen může dojít, protože jde o ceny smluvní platné pro konkrétní registraci, a to od data zahájení do data ukončení smlouvy.
- Při přechodu na novou registraci se ceny změní na novou smlouvu. Ceny jsou definovány vaším ceníkem, který může být u nové registrace vyšší.
- Ceny se mění i při prodloužení lhůty registrace. Ceny se mění na sazby pro průběžné platby.

## <a name="request-detailed-usage-information"></a>Žádost o podrobné informace o využití

Podnikoví správci si na portálu Azure Enterprise Portal můžou zobrazit souhrnné údaje o využití, výši spotřebovaného peněžního závazku a poplatky za další využití. Tyto poplatky jsou vyjádřené souhrnně za všechny účty a všechna předplatná.

Pokud si chcete prohlédnout podrobné údaje o využití konkrétních účtů, stáhněte si sestavu podrobných informací o využití tím, že přejdete na **Reports** (Sestavy)  >  **Download Usage** (Stáhnout údaje o využití).

> [!NOTE]
> Sestava s podrobnostmi využití nezahrnuje příslušné daně. Mezi časem skutečného použití a promítnutím tohoto údaje do sestavy může vzniknout zpoždění až osm hodin.

U nepřímých registrací je potřeba, aby partner před zobrazením informací o cenách nejprve zapnul funkci přirážky.

## <a name="reports"></a>Sestavy

Podnikoví správci si na portálu Azure Enterprise Portal můžou zobrazit souhrnné údaje o využití, výši spotřebovaného peněžního závazku a poplatky za další využití. Tyto poplatky jsou vyjádřené souhrnně za všechny účty a všechna předplatná.

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

## <a name="move-usage-data-to-another-enrollment"></a>Přesun dat o využití do jiné registrace

Data o využití se přesouvají jenom v případě, že se převod provádí zpětně ke staršímu datu. Existují dvě možnosti, jak přesunout data o využití z jedné registrace do druhé:

- Převody účtů z jedné registrace do druhé
- Převody registrací z jedné registrace do druhé

V obou případech musíte týmu podpory EA poslat [žádost o podporu](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) a požádat je o pomoc. 

## <a name="azure-ea-pricing-overview"></a>Přehled cen Azure EA

V této části najdete podrobné informace o tom, jak se počítá využití. Najdete zde odpovědi na mnoho častých dotazů týkajících se poplatků za různé služby Azure ve smlouvě Enterprise, kde jsou výpočty složitější.

### <a name="price-protection"></a>Ochrana cen

Jako zákazník nebo distribuční partner máte garantované ceny na úrovni nebo pod úrovní cen zobrazených v ceníku zákazníka (CPS) nebo ceny platné k datu platnosti vašeho nákupu Azure. Tato cena se označuje jako základní cena. V případě služeb zavedených po vašem nákupu Azure se vám účtuje cena s odpovídající úrovní slevy platnou při prvním představení dané služby. Ochrana cen platí pro celé období závazku, které může v závislosti na smlouvě Enterprise Agreement trvat jeden nebo tři roky.

### <a name="price-changes"></a>Změny cen

Microsoft může během období registrace snížit aktuální cenu Enterprise Agreement pro jednotlivé služby Azure. Po dobu platnosti nižších cen rozšíříme tyto snížené sazby na stávající zákazníky. Pokud se tyto sazby zvýší později, cena za službu se u vaší registrace nezvýší nad horní mez ochrany cen, jak je definováno výše. Sazba se ale může zvýšit vzhledem k předchozí snížené ceně. V každém případě budeme informovat zákazníky a nepřímé distribuční partnery o připravovaných změnách cen prostřednictvím e-mailu zaslaného podnikovým správcům a správcům partnerů přidruženým k dané registraci.

### <a name="current-effective-pricing"></a>Aktuálně platné ceny

Zákazníci a distribuční partneři můžou zobrazit aktuální ceny pro určitou registraci tak, že se přihlásí k portálu [Azure Enterprise Portal](https://ea.azure.com/) a zobrazí si stránku ceníku pro danou registraci. Pokud si Azure zakoupíte přímo přes některého z našich distribučních partnerů a váš distribuční partner nepovolí zobrazení cen s přirážkou pro vaši registraci, budete muset o aktualizace cen požádat svého distribučního partnera.

### <a name="introduction-of-new-azure-services"></a>Zavádění nových služeb Azure

Neustále Azure vylepšujeme a pravidelně přidáváme nové služby, jejichž ceny se stanovují odděleně od existujících služeb. Některé služby ve verzi Preview jsou dostupné automaticky, zatímco jiné vyžadují akci zákazníka na [Portálu účtů Azure](https://account.windowsazure.com/PreviewFeatures).

Některé služby po zavedení začínají s propagačními cenami, které se v budoucnu můžou zvýšit.

Při přechodu služeb z verze Preview na obecně dostupnou verzi (GA) může dojít ke zvýšení cen, protože začnou platit úplné smlouvy SLA o výkonu a spolehlivosti. Takové zvýšení není normální ochranou základní ceny omezeno. Používání těchto služeb se účtuje zvýšenou sazbou. Abyste se vyhnuli poplatkům souvisejícím s těmito novými službami, budete muset odhlásit jejich používání.

### <a name="introduction-of-regional-pricing"></a>Zavedení regionálních cen

Kromě zavedení nových služeb se také v souvislosti s rozšiřováním regionální podpory služeb pravidelně mění globální charakter těchto služeb na regionálnější model.

Po prvním zavedení regionalizace určité služby se ochrana základních cen vztahuje na tyto nové oblasti. Pokud se ale pro stejnou službu zavedou další oblasti později, považují se za nové služby a nabízejí se s vlastními individuálními sazbami, které jsou nezávislé na ochraně základních cen.

### <a name="enterprise-devtest"></a>Enterprise pro vývoj/testování

Podnikoví správci můžou vlastníkům účtů umožnit vytvářet předplatná na základě nabídky Enterprise pro vývoj/testování. Vlastník účtu musí nastavit předplatná Enterprise pro vývoj/testování, která potřebují základní uživatelé. Tato konfigurace umožňuje aktivním předplatitelům sady Visual Studio spouštět v Azure vývojové a testovací úlohy za speciální sazby Enterprise pro vývoj/testování. Další informace najdete v tématu věnovaném [Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="enterprise-agreement-usage-calculations"></a>Výpočty využití smlouvy Enterprise

Informace o základních cenách pro veřejnost, měrných jednotkách, nejčastějších dotazech a pokynech ke generování sestav využití pro jednotlivé služby najdete na stránkách [Služby Azure](https://azure.microsoft.com/services/) a [Ceny Azure](https://azure.microsoft.com/pricing/). Další informace o výpočtech Enterprise Agreement najdete v následujících částech.

### <a name="enterprise-agreement-units-of-measure"></a>Měrné jednotky smlouvy Enterprise

Měrné jednotky pro smlouvy Enterprise se často liší od jednotek, které znáte z jiných programů, jako je například program Microsoft Online Services Agreement (MOSA). To znamená, že u řady služeb se měrná jednotka agreguje, aby bylo možné poskytovat normalizované ceny. Měrná jednotka, která se na portálu Azure Enterprise Portal zobrazuje v zobrazení Usage Summary (Souhrn využití), je vždy měrnou jednotkou smlouvy Enterprise. Úplný seznam aktuálních měrných jednotek a převodů pro jednotlivé služby najdete v excelovém souboru [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) (Popisné názvy služeb).

### <a name="rounding-rules"></a>Pravidla zaokrouhlování

Portál Azure Enterprise Portal využívá logiku statistického zaokrouhlování neboli Gaussova zaokrouhlování podle standardu IEEE. Podle této logiky se čísla s polovinou zaokrouhlí na nejbližší sudé číslo. Obvyklejší logika zaokrouhlování polovin směrem nahoru vždy zaokrouhlí čísla s polovinou na další nejvyšší číslo. Tato metoda portálu Azure Enterprise Portal ve skutečnosti v porovnání se standardní logikou aplikace Excel poskytuje přesnější celkový součet skupiny.

Pro ilustraci: Pokud je první vynechaná číslice 5 a nenásledují žádné další číslice nebo jsou následující číslice nuly, zaokrouhlí se na nejbližší sudé číslo. Příklad: 2,315 i 2,325 se zaokrouhlí na nejbližší setinu, tzn. 2,32.

V následující tabulce najdete excelové vzorce, které můžete použít k modelování pravidel portálu Azure Enterprise Portal pro zaokrouhlování a převod:

| Scénář | Logický vzorec statistického zaokrouhlování |
| --- | --- |
| Zaokrouhlování využití | =MROUND({_zdroj_}, 0.0002) |
| Zaokrouhlování cen (na 2 desetinná místa) | =MROUND({_zdroj_}, 0.02) |
| Zaokrouhlování cen (na 0 desetinných míst) | =MROUND({_zdroj_}, 2) |

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Převod mezi sestavou podrobností o využití a stránkou souhrnu využití

V sestavě údajů o využití ke stažení můžete vidět nezpracované využití prostředků s až šesti desetinnými místy. Portál Azure Enterprise Portal ale údaje o využití zaokrouhluje na čtyři desetinná čísla v případě jednotek závazku a zkracuje na nula desetinných míst v případě jednotek nadlimitního využití. Nezpracované údaje o využití se před převodem na jednotky používané na portálu Azure Enterprise nejprve zaokrouhlí na čtyři číslice. Převedené jednotky Enterprise se pak znovu zaokrouhlí na čtyři číslice. Skutečně využité hodiny před převodem můžete zobrazit pouze v sestavě údajů o využití ke stažení, a nikoli na portálu Azure Enterprise Portal.

Příklad: V sestavě podrobností o využití je uvedeno 694,533404 skutečných hodin SQL Serveru. Tyto jednotky se pak převedou na 6,94533404 100 výpočetních hodin, které se pak zaokrouhlí na 6,9453 a zobrazí se na portálu Azure Enterprise Portal.

- K určení rozšířené fakturované částky se pak tyto jednotky vynásobí cenou jednotky závazku a výsledek se zkrátí na dvě desetinná místa. V případě japonského jenu (JPY) a korejského wonu (KRW) se rozšířená částka zaokrouhlí na nula desetinných míst.
- V případě nadlimitního využití se rozšířená fakturovaná částka určí tak, že se fakturovatelné jednotky zkrátí na šest číslic a pak se vynásobí cenou jednotky nadlimitního využití.
- Pro účely fakturace poskytovatelů spravovaných služeb (MSP) se veškeré využití přidružené k oddělení a označené jako MSP po převodu na měrnou jednotku smlouvy Enterprise zkrátí na nula desetinných míst. V důsledku toho by součet tohoto využití mohl být nižší než celkový součet veškerého využití vykázaného na portálu Azure Enterprise Portal. Záleží na tom, jestli je MSP v rámci zůstatku peněžního závazku nebo se nachází v nadlimitním využití.

### <a name="graduated-pricing"></a>Odstupňované ceny

Ceny smlouvy Enterprise v současné době nezahrnují odstupňované ceny, kdy se se zvyšujícím se využitím snižuje cena za jednotku. Když přecházíte z programu MOSA s odstupňovanými cenami na smlouvu Enterprise, nastaví se ceny úměrně základní úrovni služby snížené o veškeré odpovídající úpravy pro slevy smlouvy Enterprise.

### <a name="partial-hour-billing"></a>Fakturace po zlomcích hodin

Veškeré fakturované využití je založeno na počtu minut převedených na zlomky hodin, a ne na přírůstcích po celých hodinách. Uvedené hodinové sazby Enterprise se použijí na celkové hodiny plus částečné hodiny.

### <a name="average-daily-consumption"></a>Průměrná denní spotřeba

Některé služby se účtují měsíčně, využití se ale vykazuje denně. V těchto případech se využití vyhodnocuje jednou denně, vydělí se 31 a shrne se v rámci počtu dnů v daném fakturačním měsíci. Sazby proto v žádném měsíci nepřesáhnou očekávání a v měsících, které mají méně než 31 dnů, jsou mírně nižší.

### <a name="compute-hours-conversion"></a>Převod výpočetního času

Před 28. lednem 2016 se veškeré využití pro cloudové služby a virtuální počítače úrovně Standard a Basic A0, A2, A3 a A4 generovalo jako minuty měřiče služby Virtual Machines A1. Virtuální počítače A0 se počítaly jako zlomky minut virtuálního počítače A1. A2, A3 a A4 se převedly na násobky. Vzhledem k tomu, že tyto zásady byly pro některé zákazníky nejasné, implementovali jsme změnu a přiřadili vyhrazeným měřičům A0, A2, A3 a A4 využití po minutách.

Nový způsob měření začal platit mezi 27. a 28. lednem 2016. V souboru CSV ke stažení zobrazujícím využití během tohoto období přechodu můžete vidět obojí:

- Využití generované na měřiči A1
- Využití generované na novém vyhrazeném měřiči odpovídajícím velikosti vašeho nasazení

| **Velikost nasazení** | **Využití generované jako násobky A1 do 26. ledna 2016** | **Využití generované s vyhrazeným měřičem od 27. ledna 2016** |
| --- | --- | --- |
| A0 | 0,25 hodin A1 | 1 hodina A0 |
| A2 | 2 hodiny A1 | 1 hodina A2 |
| A3 | 4 hodiny A1 | 1 hodina A3 |
| A4 | 8 hodin A1 | 1 hodina A4 |

### <a name="regions"></a>Oblasti

Následující tabulka ukazuje mapování geografických oblastí a oblastí pro služby, na jejichž ceny má vliv jejich zóna a oblast:

| Geografická oblast | Oblasti pro přenos dat | Oblasti CDN |
| --- | --- | --- |
| Zóna 1 | Evropa – sever <br> Evropa – západ <br> USA – západ <br> USA – východ <br> USA (střed) – sever <br> USA (střed) – jih <br> USA – východ 2 <br> USA – střed | Severní Amerika <br> Evropa |
| Zóna 2 | Asie a Tichomoří – východ <br> Asie a Tichomoří – jihovýchod <br> Japonsko – východ <br> Japonsko – západ <br> Austrálie – východ <br> Austrálie – jihovýchod | Asie a Tichomoří <br> Japonsko <br> Latinská Amerika <br> Střední východ / Afrika <br> Austrálie – východ <br> Austrálie – jihovýchod |
| Zóna 3 | Brazílie – jih |   |

Za výchozí přenos dat mezi službami hostovanými ve stejném datacentru se neúčtují žádné poplatky. Jedná se například o Office 365 a Azure.

### <a name="monetary-commitment-and-unbilled-usage"></a>Peněžní závazek a nefakturované použití

Peněžní závazek za Azure je částka, která se za služby Azure platí předem. Peněžní závazek se spotřebovává používáním služeb. Peněžní závazek se účtuje u vlastních služeb Azure. Některé poplatky se ale účtují samostatně a služby Azure Marketplace peněžní závazek nespotřebovávají.

### <a name="charges-billed-separately"></a>Poplatky fakturované samostatně

Některé produkty a služby od třetích stran nespotřebovávají peněžní závazek Azure. Místo toho se tyto položky fakturují samostatně v rámci standardní faktury za nadlimitní využití ve fakturačním období.

Všechny poplatky za Azure a Azure Marketplace jsme zkombinovali do jedné faktury, která odpovídá fakturačnímu období registrace. Kombinovaná faktura se netýká zákazníků v Austrálii, Japonsku a Singapuru.

Kombinovaná faktura ukazuje nejprve využití Azure a potom poplatky za Azure Marketplace. Zákazníkům v Austrálii, Japonsku nebo Singapuru se poplatky za Marketplace zobrazují na samostatné faktuře.

Pokud ke konci standardního fakturačního období nedojde k žádnému nadlimitnímu využití, za poplatky fakturované samostatně se vystaví samostatná faktura. Tento proces platí pro zákazníky v Austrálii, Japonsku a Singapuru.

Následující služby se účtují samostatně:

- Canonical
- Citrix XenApp Essentials
- Registrovaný uživatel Citrix XenDesktop
- Openlogic
- Registrovaný uživatel s oprávněním ke vzdálenému přístupu XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (měsíční)
- Visual Studio Enterprise (roční)
- Visual Studio Professional (měsíční)
- Visual Studio Professional (roční)

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace pro zákazníky EA

Přímí zákazníci můžou poplatky za Azure Marketplace zobrazit na portálu Azure Enterprise Portal. Nákupy na Azure Marketplace a související spotřeba se účtují mimo peněžní závazek, a to čtvrtletně nebo měsíčně a v nedoplatcích.

Nepřímí zákazníci najdou svá předplatná Azure Marketplace na stránce **Manage Subscriptions** (Správa předplatných) na portálu Azure Enterprise Portal, ale ceny budou skryté. Pokud zákazníci chtějí získat informace o poplatcích za Azure Marketplace, měli by se obrátit na svého poskytovatele LSP (Licensing Solutions Provider).

Nové měsíčně či ročně se opakující nákupy na Azure Marketplace se plně účtují během období, kdy se položky z Azure Marketplace kupují. Platnost těchto položek se v následujícím období automaticky prodlouží ke stejnému dni, kdy došlo k původnímu nákupu.

Stávající měsíční poplatky se budou i nadále obnovovat k prvnímu dni každého kalendářního měsíce. Roční poplatky se obnoví k datu výročí nákupu.

Některé služby prodejců třetích stran dostupné na Azure Marketplace teď spotřebovávají zůstatek peněžního závazku smlouvy Enterprise (EA). Dříve se tyto služby účtovaly mimo peněžní závazek EA a fakturovaly se samostatně. Použití peněžního závazku EA pro tyto služby na Azure Marketplace pomáhá zákazníkům zjednodušit nákup a správu plateb. Úplný seznam služeb, které teď využívají peněžní závazek, najdete na v [aktualizaci k 6. březnu 2018 na webu Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Partneři

Partneři LSP můžou stáhnout ceník Azure Marketplace ze stránky ceníku na portálu Azure Enterprise Portal. V pravém horním rohu vyberte odkaz **Marketplace Price list** (Ceník Marketplace). Ceník Azure Marketplace zobrazuje všechny dostupné služby a jejich ceny.

Stažení ceníku:

1. Na portálu Azure Enterprise Portal přejděte na **Reports** (Sestavy)  >  **Price Sheet** (Ceník).
1. V pravém horním rohu najdete pod svým uživatelským jménem odkaz na ceník Azure Marketplace.
1. Klikněte na odkaz pravým tlačítkem a vyberte **Save Target As** (Uložit cíl jako).
1. V okně **Save** (Uložit) změňte název dokumentu na `AzureMarketplacePricelist.zip`. Tím se soubor .xlsx změní na soubor .zip.
1. Po dokončení stahování budete mít soubor zip s ceníky pro jednotlivé země.
1. Partneři LSP by ke zjištění cen pro konkrétní zemi měli použít soubor pro konkrétní zemi. Partneři LSP můžou použít kartu **Notifications** (Oznámení) k tomu, aby se dozvěděli o skladových položkách, které jsou nové nebo vyřazené.
1. Změny cen se objevují zřídka. Partneři LSP obdrží e-mailová oznámení o zvýšení ceny a devizových změnách 30 dní předem.
1. Partneři LSP obdrží jednu fakturu za každou registraci, každého nezávislého výrobce softwaru a každé čtvrtletí.

### <a name="enabling-azure-marketplace-purchases"></a>Povolení nákupů na Azure Marketplace

Podnikoví správci můžou zakázat nebo povolit nákupy na Azure Marketplace pro všechna předplatná Azure v rámci dané registrace. Pokud podnikový správce zakáže nákupy a nějaká předplatná Azure již obsahují předplatná Azure Marketplace, tato předplatná Azure Marketplace se nezruší, ani to na ně nebude mít žádný vliv.

Přestože zákazníci můžou převést svá přímá předplatná Azure na smlouvu Azure EA tím, že je na portálu Azure Enterprise Portal přidruží ke své registraci, podřízená předplatná se tím automaticky nepřevedou.

Povolení nákupů na Azure Marketplace:

1. Přihlaste se k portálu Azure Enterprise Portal jako podnikový správce.
1. Přejděte do části **Správa**.
1. V části **Enrollment Detail** (Podrobnosti o registraci) vyberte ikonu tužky vedle řádkové položky **Azure Marketplace**.
1. Podle potřeby přepněte přepínač **Enabled/Disabled** (Povoleno/zakázáno) nebo Free **BYOL SKUs Only** (Jen skladové položky Free nebo BYOL).
1. Vyberte **Uložit**.

> [!NOTE]
> Použitím možnosti Jen skladové položky Free nebo BYOL (používání vlastní licence) se omezí nákupy a pořizování skladových položek Azure Marketplace pouze na skladové položky BYOL a Free.

Přečtěte si další informace o [poplatcích za Azure Marketplace v sestavách na portálu Azure Enterprise Portal](#azure-marketplace-charges-in-azure-enterprise-portal-reports).

### <a name="services-billed-hourly-for-azure-ea"></a>Služby účtované po hodinách v Azure EA

Následující služby se ve smlouvě Enterprise účtují po hodinách namísto měsíčních sazeb v MOSP:

- Application Delivery Network
- Web Application Firewall (Brána firewall webových aplikací)

### <a name="azure-remoteapp"></a>Azure RemoteApp

Pokud máte smlouvu Enterprise, platíte za službu Azure RemoteApp na základě vaší cenové úrovně smlouvy Enterprise. Neúčtují se žádné další poplatky. Standardní cena zahrnuje počátečních 40 hodin. Neomezená cena pokrývá počátečních 80 hodin. RemoteApp přestane generovat data o využití po uplynutí 80 hodin.

## <a name="azure-marketplace-faq"></a>Nejčastější dotazy k Azure Marketplace

Tato část vysvětluje, jak se peněžní závazek Azure může použít na některé služby prodejců třetích stran na webu Azure Marketplace.

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>Co se u služeb Azure Marketplace a peněžního závazku smlouvy EA změnilo?

Od 1. března 2018 využívají některé služby prodejců třetích stran peněžní závazek EA. S výjimkou rezervovaných instancí virtuálního počítače Azure byly služby dříve fakturovány mimo peněžní závazek EA a byly účtovány samostatně.

Rozšířili jsme možnosti použití peněžního závazku tak, aby zahrnoval některé služby publikované na Azure Marketplace třetími stranami, které si naši zákazníci kupují nejčastěji. Použití peněžního závazku EA pro tyto služby na Azure Marketplace pomáhá zjednodušit nákup a správu plateb.

### <a name="why-did-we-make-this-change"></a>Proč jsme tuto změnu provedli?

Zákazníci neustále hledají další způsoby, jak využít platbu peněžního závazku předem. Zákazníci tuto změnu často vyžadovali a měla vliv na velkou část zákazníků Azure Marketplace.

### <a name="how-do-you-benefit"></a>Jaké přináší výhody?

Fakturace se zjednoduší a budete moct lépe využít peněžní závazek EA. Vzhledem k tomu, že jsou tyto služby zahrnuté do předem placeného peněžního závazku, bude váš peněžní závazek se smlouvou EA cennější.

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>Jaké služby Azure Marketplace peněžní závazek EA používají a jak to zjistím?

Když si koupíte službu, která peněžní závazek používá, Azure Marketplace zobrazí právní omezení. Podporují se některé služby publikované společnostmi Red Hat, SUSE, Autodesk a Oracle. Podobně pojmenované služby publikované jinými stranami se v současné době od peněžního závazku neodečtou. Úplný seznam najdete na konci těchto nejčastějších dotazů.

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>Co když peněžní závazek EA vyčerpám?

Pokud využijete celý svůj peněžní závazek a služby teď využívají nadlimitně, poplatky související s těmito službami se zobrazí na další faktuře za nadlimitní využití společně se všemi dalšími službami založenými na spotřebě. Před změnou, ke které došlo 1. března 2018, se tyto poplatky fakturovaly s ostatními službami Azure Marketplace.

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>Proč peněžní závazek EA nevyužívají všechny služby Azure Marketplace?

Často pracujeme na tom, abychom zákazníkům v souvislosti s peněžním závazkem Azure poskytovali co nejlepší služby. Tato změna se týkala velkého množství zákazníků a významné části celkové útraty na Azure Marketplace. V budoucnu se možná přidají další služby.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Jaký to bude mít vliv na nepřímé registrace a partnery?

Na naše zákazníky ani partnery s nepřímou registrací to nebude mít žádný vliv. Na tyto služby se vztahují stejné možnosti přirážky partnera jako na ostatní služby založené na spotřebě. Jedinou změnou je, že se poplatky objevují na jiné faktuře a že platba poplatků pochází z peněžního závazku zákazníka EA.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>Existuje seznam služeb Azure Marketplace, které využívají peněžní závazek EA?

Pro konkrétní nabídky Azure Marketplace je možné využít prostředky peněžních závazků. Úplný seznam produktů zapojených do tohoto programu najdete v tématu týkajícím se [služeb třetích stran využívajících peněžní závazek](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment).

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

## <a name="reports-faq"></a>Nejčastější dotazy k sestavám

Tato část se zabývá běžnými dotazy k sestavám.

### <a name="why-is-my-cost-showing-as-0"></a>Proč se mi v nákladech zobrazuje 0 USD?

U zákazníků s **přímou registrací** můžou podnikoví správci poskytnout vlastníkům účtů a správcům oddělení přístup k informacím o nákladech a cenách v sestavách využití. Postupujte následovně:

1. V levém navigačním panelu portálu Azure Enterprise vyberte **Manage** (Spravovat).
1. Vyberte modrou tužku vedle možnosti DA (department administrator) view charges (Správce oddělení může zobrazit náklady).
1. Vyberte **Povoleno** a uložte provedenou změnu.
1. Vyberte modrou tužku vedle možnosti AO (account owner) view charges (Vlastník účtu může zobrazit náklady).
1. Vyberte **Povoleno** a uložte provedenou změnu.

> [!NOTE]
> Pokud jste vlastníkem účtu nebo správcem oddělení, obraťte se na podnikového správce s žádostí o povolení funkce cen.

Zákazníci s **nepřímou registrací** ověří u svého partnera, že pro ně povolil funkci cen. To může udělat jenom partner. Po povolení můžete zobrazit náklady a ceny u vaší registrace jako podnikový správce.

Pokud chtějí partneři povolit funkci zobrazení poplatků pro vlastníka účtu nebo správce oddělení, postupují podle pokynů v části s **přímou registrací**.

### <a name="why-is-there-no-sku-information-on-my-usage-detail-report"></a>Proč se v sestavě s podrobnostmi o využití nezobrazují žádné informace o skladových položkách?

Sestava s podrobnostmi o využití neobsahuje žádné informace o skladových položkách. Tato sestava ale obsahuje informace o využití, abyste si mohli stáhnout sestavu ceníku a získat tak informace o skladových položkách.

### <a name="why-doesnt-the-total-amount-on-azure-marketplace-match-the-reports-for-usage-summary-and-detail"></a>Proč celková částka u Azure Marketplace neodpovídá sestavám pro shrnutí využití a podrobnosti?

Sestava poplatků za Azure Marketplace ukazuje pouze poplatky na základě využití. Jednorázové poplatky se nezobrazují. Nejaktuálnější poplatky na základě využití i jednorázové poplatky najdete na stránce se souhrnem využití.

### <a name="why-is-there-no-information-on-my-api-report"></a>Proč sestava rozhraní API neobsahuje žádné informace?

Platnost klíčů rozhraní API vyprší každých šest měsíců. Pokud máte problém, měl by podnikový správce vygenerovat nový klíč rozhraní API. Nezapomeňte postupovat podle kroků v nejčastějších dotazech k sestavě rozhraní API.

### <a name="why-isnt-my-power-bi-report-working"></a>Proč sestava Power BI nefunguje?

V případě problémů s Power BI zaprotokolujte lístek pomocí [týmu podpory Power BI](https://support.powerbi.com).

### <a name="why-dont-my-resource-tags-show-on-my-reports"></a>Proč se značky prostředků nezobrazují u sestav?

Značky prostředků se spravují na webu Azure Portal. Obraťte se na tým předplatného Azure na webu [Azure Portal](https://portal.azure.com). Postupujte podle kroků v článku [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

### <a name="why-does-my-resource-rate-change-every-day"></a>Proč se mi každý den mění sazba za prostředek?

Sazba za prostředek uvedená v podrobné sestavě využití je vypočtená hodnota. Představuje průměrnou měsíční sazbu, která se účtovala za službu. Sazba za prostředek se počítá na základě průměru měsíčního závazku a měsíčních poplatků za nadlimitní využití jednotky služby. Část poplatků účtovaná vůči závazku a sazby za nadlimitní využití se budou až do konce měsíce měnit. Proto se v průběhu měsíce mění i uvedená sazba za prostředek. Sazba za prostředek se uzamkne pátý den po konci měsíce.

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

- Následující excelové soubory obsahují podrobnosti o službách Azure a aktualizují se 6. a 20. dne každého měsíce:

   | Nadpis | Popis | Název souboru |
   | --- | --- | --- |
   | [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) (Popisné názvy služeb) | Obsahuje seznam všech aktivních služeb a zahrnuje: <br>  <ul><li>kagorii služby</li>   <li>popisný název služby</li>   <li>název závazku a číslo součásti</li> <li>název spotřeby a číslo součásti</li>   <li>měrné jednotky</li>   <li>faktory převodu mezi vykázaným využitím a využitím zobrazeným na portálu Enterprise</li></ul> | Friendly\_Service\_Names.xlsx |
   | [Service Download Fields](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) (Pole ve stažených informacích o službách) | Tato tabulka obsahuje seznam všech možných kombinací polí souvisejících se službami v sestavě využití ke stažení. | Service\_Download\_Fields.xlsx |

- Informace, které vysvětlují fakturu a poplatky, najdete v tématu, ve kterém [je vysvětlená faktura ke smlouvě Azure Enterprise](../understand/review-enterprise-agreement-bill.md).
- Pokud chcete začít používat portál Azure Enterprise Portal, přečtěte si, [jak začít používat portál Azure EA](ea-portal-get-started.md).
