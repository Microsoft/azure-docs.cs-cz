---
title: Faktury za registraci do Azure Enterprise
description: V tomto článku je vysvětlená správa faktury za Azure Enterprise a její další použití.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/02/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: d6faf56554d666ce29b68ba90bb920b4e6c56c30
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644558"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Faktury za registraci do Azure Enterprise

V tomto článku je vysvětlená správa faktury za Azure Enterprise a její další použití. Faktura slouží jako vaše vyúčtování, a proto byste ji měli zkontrolovat, jestli je přesná. Měli byste se také seznámit s dalšími úkoly, které při správě faktury můžete potřebovat.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Změna čísla nákupní objednávky u faktury za nadlimitní využití

Portál Azure EA automaticky generuje výchozí číslo nákupní objednávky, pokud správce EA nenastavil před datem faktury jiné číslo. Správce EA může číslo nákupní objednávky aktualizovat až sedm dnů od přijetí automatického e-mailu s oznámením o faktuře.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Aktualizace čísla nákupní objednávky služeb Azure:

1. Na portálu Azure EA klikněte na **Sestava** a pak na **Souhrn využití**.
1. V pravém horním rohu vyberte **Upravit čísla nákupních objednávek**.
1. Vyberte přepínač **Služby Azure**.
1. V rozevírací nabídce pro výběr rozsahu dat vyberte **Fakturační období**. Čísla nákupních objednávek je možné upravit až sedm dnů od oznámení o faktuře nebo do zaplacení faktury (podle toho, co nastane dříve).
1. Do pole **Číslo nákupní objednávky** zadejte nové číslo nákupní objednávky.
1. Kliknutím na **Uložit** odešlete provedenou změnu.

### <a name="to-update-the-marketplace-purchase-order-number"></a>Aktualizace čísla nákupní objednávky na marketplace:

1. Na portálu Azure EA klikněte na **Sestava** a pak na **Souhrn využití**.
1. V pravém horním rohu vyberte **Upravit čísla nákupních objednávek**.
1. Vyberte přepínač **Marketplace**.
1. V rozevírací nabídce pro výběr rozsahu dat vyberte **Fakturační období**. Čísla nákupních objednávek je možné upravit až sedm dnů od oznámení o faktuře nebo do zaplacení faktury (podle toho, co nastane dříve).
1. Do pole **Číslo nákupní objednávky** zadejte nové číslo nákupní objednávky.
1. Kliknutím na **Uložit** odešlete provedenou změnu.

## <a name="cadence-of-azure-ea-billing"></a>Frekvence fakturace Azure EA

### <a name="billing-intervals"></a>Fakturační intervaly

Microsoft bude fakturovat všechny závazné nákupy služeb Microsoft Azure na roční bázi a veškeré využití nad rámec závazku bude fakturovat zpětně, a to k datu vstoupení smlouvy v platnost. Poplatky související se závazky se uvádějí na základě měsíční sazby a fakturují se předem za nadcházející rok. Poplatky za nadlimitní využití se počítají každý měsíc a fakturují se zpětně na konci fakturačního období.

V závislosti na tom, jakým způsobem se rozhodnete provádět závazné nákupy, bude mít váš roční závazek stejné datum ukončení, jako je datum výročí vaší registrace, nebo jako je datum vstoupení vašeho ročního doplňujícího předplatného v platnost.

Datum, kdy obdržíte fakturu za nadlimitní využití, závisí na počátečním datu a nastavení vaší registrace.

**Přímé registrace s počátečním datem před 1. květnem 2018:** Přímí zákazníci Enterprise Azure (EA) mají pro služby Azure (kromě služeb z Marketplace) nastavené roční fakturační období. Vaše fakturační období vychází z data výročí. Datum výročí je datum, kdy vaše smlouva vstoupila v platnost. První fakturu za nadlimitní využití služeb Azure obdržíte po překročení prahové hodnoty ve výši 150 % vašeho peněžního závazku.  Jakmile celková spotřeba služeb překročí prahovou hodnotu ve výši 150 % vašeho peněžního závazku, automaticky se převedete na čtvrtletní fakturační období na základě data výročí.  Pokud nepřekročíte prahovou hodnotu ve výši 150 % vašeho peněžního závazku, pro registraci bude dál platit roční fakturační období a fakturu za nadlimitní využití obdržíte na konci ročního období závazku.

**Přímé registrace s počátečním datem po 1. květnu 2018:** Pro faktury za využití Azure zákazníkem a poplatky fakturované samostatně bude platit měsíční fakturační období.  Všechny poplatky, které nepokryje peněžní závazek Azure, budete muset zaplatit jako nadlimitní využití.  

**Nepřímé registrace s počátečním datem před 1. květnem 2018:** Nepřímí zákazníci Enterprise Azure (EA) mají nastavené čtvrtletní fakturační období.  Distribuční partner bude fakturovat přímo zákazníkovi.  

**Nepřímé registrace s počátečním datem po 1. květnu 2018:** Všechny nepřímé smlouvy s počátečním datem po 1. květnu 2018 (včetně) se budou fakturovat měsíčně.  

### <a name="increasing-commitment"></a>Zvýšení závazku

Závazek je možné kdykoli zvýšit a toto zvýšení se bude fakturovat podle počtu zbývajících měsíců v období závazku pro daný rok. Například pokud si zaregistrujete roční doplňující předplatné a v šestém měsíci zvýšíte svůj závazek, bude se vám toto zvýšení fakturovat za zbývajících šest měsíců daného období. Na posledních šest měsíců období vašeho závazku se pak aktualizuje výše vašeho závazku, aby bylo možné určit případné poplatky za nadlimitní využití.

### <a name="overage"></a>Překročení

Jako nadlimitní využití se vám bude fakturovat využití rezervací, které během daného fakturačního období překročí váš závazek. Pokud chcete zobrazit rozpis výpočtu nadlimitního využití jednotlivých položek, projděte si sestavu Souhrn využití nebo se obraťte na svého distribučního partnera.

U každé položky na faktuře se zobrazí celkové poplatky (Rozšířená částka), výše závazku použitá k pokrytí těchto poplatků (Využití v rámci závazku) a částka, o kterou poplatky překračují závazek (Čistá částka).  Příslušné daně se počítají pouze z čisté částky nad rámec závazku.

Fakturace nadlimitního využití je automatizovaná.  Načasování oznámení a faktur závisí na koncovém datu fakturačního období zákazníka.  Oznámení o nadlimitním využití se obvykle zasílá sedm dnů po koncovém datu fakturace zákazníka. Během této doby můžou zákazníci přejít na portál, zkontrolovat poplatky a aktualizovat systémem vygenerovaná čísla nákupních objednávek (ta je možné aktualizovat také kdykoli před vystavením faktury za nadlimitní využití).  Faktury za nadlimitní využití se zasílají o 7 až 9 dnů později.

### <a name="azure-marketplace"></a>Azure Marketplace

Počínaje fakturačním cyklem, který začíná v dubnu 2019, začnou zákazníci dostávat jednu fakturu Azure, protože jsme veškeré poplatky za Azure a Azure Marketplace ze dvou různých faktur zkombinovali do jedné faktury. (Tato změna nemá vliv na zákazníky v Austrálii, Japonsku ani Singapuru.) Během přechodu na konsolidovanou fakturu obdržíte částečnou fakturu za Marketplace. Na této poslední samostatné faktuře se zobrazí poplatky za Marketplace před datem aktualizace fakturace. Poplatky za Marketplace po tomto datu se zobrazí na vaší faktuře Azure. Po skončení přechodného období se všechny poplatky za Azure a Marketplace budou zobrazovat na konsolidované faktuře.  

### <a name="purchase-order-numbers"></a>Čísla nákupních objednávek

Výchozí hodnota nákupní objednávky je systémem vygenerované číslo nákupní objednávky. Uživatel může číslo nákupní objednávky aktualizovat na portálu Enterprise Portal tím, že se přihlásí jako podnikový správce a přejde do části Sestavy. V pravém horním rohu tohoto okna se nachází pole pro číslo nákupní objednávky, které může podnikový správce po kliknutí na ikonu tužky upravit.

## <a name="adjust-billing-frequency"></a>Úprava frekvence fakturace

Vystavování faktur zákazníkům probíhá ročně, čtvrtletně nebo měsíčně. Fakturační cyklus si zákazník volí při podpisu smlouvy. Nejkratším fakturačním intervalem je měsíc.

Změnu fakturačního cyklu u přímých registrací z ročního na čtvrtletní musí odsouhlasit podnikový správce. U nepřímých registrací musí změnu schválit správce partnera. Změna se uskuteční na konci aktuálního fakturačního čtvrtletí.

Ke změně ročního nebo čtvrtletního fakturačního cyklu na měsíční je nutný dodatek smlouvy.  Jakékoli změny fakturačního cyklu u stávající registrace vyžadují souhlas podnikového správce nebo jiné osoby, která je ve smlouvě označená jako _kontakt pro fakturaci_. Žádost o schválení můžete adresovat [podpoře portálu Azure EA](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Jako kategorii problému vyberte **Vyúčtování a fakturace**.  Změna se uskuteční na konci aktuálního fakturačního čtvrtletí.

Pokud jste podepsali dodatek č. M503, můžete frekvenci kterékoli smlouvy změnit na měsíční fakturaci. 

## <a name="credits-and-adjustments"></a>Kredity a úpravy

Všechny kredity a úpravy, které se týkají vaší registrace, si můžete prohlédnout na adrese [https://ea.azure.com](https://ea.azure.com) v části **Sestavy**.

Pokud chcete zobrazit kredity, postupujte následovně:

1. Vyberte část **Sestavy**.
1. Klikněte na **Souhrn využití**.
1. V pravém horním rohu změňte zobrazení z _M_ na _C_.
1. V tabulce závazku služby Azure rozšiřte pole pro úpravy.
1. Na tomto řádku se zobrazí kredity uplatněné na vaši registraci a stručný popis, například: Kredit smlouvy SLA

## <a name="request-an-invoice-copy"></a>Žádost o kopii faktury

Pokud chcete požádat o kopii faktury, obraťte se na partnera.

## <a name="overage-offset-by-customers"></a>Vyrovnání nadlimitního využití zákazníky

Pokud má zákazník nadlimitní využití, pro které chce použít peněžní závazek, musí být splněny následující podmínky:

- Zákazník musí mít neuhrazené poplatky za nadlimitní využití, které mu vznikly, nejsou zaplacené a současně nejsou starší než jeden rok od data ukončení fakturované služby.
- Dostupný peněžní závazek musí pokrývat celou částku účtovaných poplatků, včetně všech předchozích neuhrazených faktur za Azure.
- Termín fakturace, o jejíž dokončení zákazník žádá, musí být plně uzavřený. Fakturace se plně uzavírá po pátém dni v měsíci.
- Fakturační období, do kterého požadavek na vyrovnání spadá, musí být úplně uzavřené.
- Sleva ACD vychází ze skutečného nového závazku po odečtení prostředků naplánovaných k předchozí spotřebě. Tento požadavek se týká jenom skutečně vzniklých poplatků za nadlimitní využití. Funguje jenom u služeb, které vyžadují peněžní závazek, takže ho nemůžete použít na poplatky za Marketplace. Poplatky za Marketplace jsou fakturované zvlášť.
- Pokud zákazník chce vyrovnat nadlimitní využití, může otevřít žádost o podporu. Žádost o podporu také může otevřít tým účtu. K dokončení celého postupu je nutný souhlas správce EA zákazníka nebo souhlas kontaktu pro fakturaci.

## <a name="view-price-sheet-information"></a>Zobrazení informací z ceníku

Podnikoví správci můžou zobrazit ceník, který platí pro zaregistrované služby Azure.

Zobrazení aktuálního ceníku:

1. Na webu Azure EA Portal klikněte na **Sestavy** a pak klikněte na **Ceník**.
2. Prohlédněte si ceník nebo si ho kliknutím **stáhněte**.

![Příklad informací z ceníku](./media/billing-ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Stažení historického ceníku:

1. Na webu Azure EA Portal klikněte na **Sestavy** a pak klikněte na **stažení údajů o využití**.
2. Stáhněte si ceník.

![Příklad stažení staršího ceníku](./media/billing-ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

Pokud se chcete zeptat na příčinu rozdílů v cenách, tady jsou některé důvody:

Změna cen mezi předchozí a novou registrací. Ke změnám cen dochází, protože jde o ceny smluvní platné pro konkrétní registraci, a to od data zahájení do data ukončení smlouvy. Při převodu registrace ze staré na novou se použijí ceny podle nové smlouvy. Ceny jsou definované v ceníku zákazníka. To znamená, že ceny můžou být v nové registraci vyšší.

Ceny se mění i při prodloužení lhůty registrace. Ceny se mění na sazby pro průběžné platby.

## <a name="request-detailed-usage-information"></a>Žádost o podrobné informace o využití

Podnikoví správci si na portálu Azure EA můžou zobrazit souhrnná data o využití, spotřebovanou výši peněžního závazku a poplatky za další použití. Tyto poplatky jsou vyjádřené souhrnně za všechny účty a všechna předplatná.

Pokud si chcete prohlédnout podrobné údaje o využití konkrétních účtů, stáhněte si sestavu podrobných informací o využití tím, že přejdete na **Sestavy** > **Stáhnout údaje o využití**. Sestava neobsahuje platné daně. Mezi časem skutečného použití a promítnutím tohoto údaje do sestavy může vzniknout zpoždění až osm hodin.

U nepřímých registrací je potřeba, aby partner před zobrazením informací o cenách nejprve zapnul funkci přirážky.

## <a name="reports"></a>Sestavy

Podnikoví správci si na portálu Enterprise Portal můžou zobrazit souhrnné údaje o využití, výši spotřebovaného peněžního závazku a poplatky za další využití. Tyto poplatky budou vyjádřené souhrnně za všechny účty a všechna předplatná.

**Sestavy EA**

- Souhrn a grafy využití
- Sestava využití služeb
- Sestava zůstatků a poplatků
- Sestava s podrobnostmi o využití
- Sestava poplatků za marketplace
- Ceník
- Stažení rozšířené sestavy
- Formátování sestav CSV

**Zobrazení sestav a grafů se souhrnem využití**

1. Na portálu Azure EA klikněte v levém navigačním panelu na **Sestavy** a zobrazte kartu **Souhrn využití**.
1. V rozevírací nabídce rozsahů dat v levém horním rohu vyberte požadované období závazku.
1. Výběrem požadovaného období nebo měsíce v grafu zobrazíte další podrobnosti.
1. Zobrazte graf meziměsíčního využití s rozpisem spotřebovaného využití, nadměrného vyúčtování služeb, poplatků fakturovaných samostatně a poplatků za marketplace.
1. Pro vybraný měsíc můžete pod grafem filtrovat podle oddělení, účtů a předplatných.
1. Můžete přepínat mezi rozpisem vyúčtování podle služeb a rozpisem vyúčtování podle hierarchie.
1. Podrobně si prohlédněte služby Azure, samostatně fakturované poplatky a poplatky za Azure Marketplace.

## <a name="service-usage-report"></a>Sestava využití služeb

Na stránce Sestava využití služeb můžou podnikoví správci zobrazit souhrn údajů o využití služeb. Využití se bude zobrazovat na úrovni souhrnu napříč všemi účty a předplatnými. Pokud však chcete zobrazit podrobné informace o využití, můžete sestavu také filtrovat podle účtů nebo předplatných.

Mějte na paměti, že mezi datem skutečného využití a jeho zobrazením v této sestavě může docházet ke zpoždění až 5 dnů.

### <a name="to-view-the-report"></a>Zobrazení sestavy:

1. Přihlaste se k portálu Enterprise Portal.
1. V levém navigačním panelu klikněte na **Sestavy**.
1. Klikněte na kartu **Souhrn využití**.
1. Klikněte na požadovaný rozsah dat.
1. Zvolte účty nebo předplatná, která chcete zobrazit.
1. Změnou zobrazení z Vyúčtování podle služeb na Vyúčtování podle hierarchie můžete zobrazit různé rozpisy.
1. Prohlédněte si podrobnosti, včetně názvu služby, měrné jednotky, spotřebovaných jednotek, skutečné sazby a rozšířených nákladů.

## <a name="download-csv-reports"></a>Stahování sestav CSV

Podnikoví správci si můžou ze stránky s měsíčními sestavami stáhnout několik sestav ve formátu CSV, včetně sestavy zůstatků a poplatků, sestavy s podrobnostmi o využití, sestavy poplatků za Marketplace a ceníku.

## <a name="to-download-reports"></a>Jak sestavy stáhnout:

1. Na portálu Azure EA klikněte na **Sestava**.
1. Na horním pásu karet vyberte **Stáhnout informace o využití**.
1. Vedle sestavy pro odpovídající měsíc vyberte **Stáhnout**.



## <a name="csv-report-formatting"></a>Formátování sestav CSV

Zákazníci, kteří zobrazují sestavy CSV na portálu Azure EA v eurech, můžou narazit na problémy s formátováním související s čárkami a tečkami.

Například se může zobrazit následující:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Hodiny | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Měli byste vidět tohle:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Hodiny | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

### <a name="root-cause"></a>Původní příčina

Excel importuje všechna pole jako obecný text a předpokládá, že čísla jsou oddělená v matematickém standardů: 1,000.00.  Evropská měna, která jako oddělovač tisíců používá tečku (.) a jako oddělovač desetinných míst čárku (,), tzn. 1.000,00, se zobrazí nesprávně. To se může lišit v závislosti na vašem nastavení místního jazyka.

### <a name="when-importing-the-csv-use-the-following-steps"></a>Při importu souboru CSV postupujte následovně:

1.    Otevřete Excel a přejděte do části Soubor > Otevřít.
1.    Zobrazí se Průvodce importem textu.
1.    V části Zdrojový datový typ zvolte _Oddělovač_.  Výchozí hodnota je _Pevná šířka_.
1.    Klikněte na **Další**.
1.    V části Oddělovače zaškrtněte políčko u možnosti Čárka. Výchozí možnost je Tabulátor (zrušte její zaškrtnutí).
1.    Klikněte na **Další**.
1.    Posuňte se ke sloupcům ResourceRate a ExtendedCost.
1.    Vyberte sloupec ResourceRate (zobrazí se zvýrazněný černě).
9.    V části Formát dat ve sloupcích místo možnosti Obecný vyberte Text.  Uvidíte, že se záhlaví sloupce změní z hodnoty Obecný na Text.
10. Zopakujte kroky 8 a 9 i pro sloupec ExtendedCost. Vyberte **Finish** (Dokončit).

Pokud máte aplikaci Excel přidruženou k automatickému otevírání souborů \*.csv, musíte místo toho použít funkci Otevřít v aplikaci Excel. Otevřete Excel a přejděte do části Soubor > Otevřít.

## <a name="balance-and-charge-report"></a>Sestava zůstatků a poplatků

Sestava zůstatků a poplatků nabízí měsíční souhrn informací o zůstatcích, nových nákupech, poplatcích za služby z Azure Marketplace, vyrovnáních a poplatcích za nadlimitní využití. Všechny řádky v tabulce se souhrnem závazku služby Azure budou každý měsíc statické, zatímco v částech Podrobnosti o novém nákupu a Podrobnosti o vyrovnání se zobrazí podrobnosti o všech nákupech a vyrovnáních.

### <a name="download-the-balance-and-charge-report"></a>Stažení sestavy zůstatků a poplatků

1. Přihlaste se k portálu Azure EA jako podnikový správce.
1. V levém navigačním panelu klikněte na **Sestavy**.
1. Klikněte na kartu **Stažení sestavy**.
1. Ve sloupci _Zůstatek a poplatky_ vyberte odpovídající měsíc a kliknutím stáhněte sestavu.

## <a name="usage-detail-report"></a>Sestava s podrobnostmi o využití

Sestava s podrobnostmi o využití nabízí měsíční souhrn služeb a jejich množství spotřebovaného v rámci registrace, včetně informací o názvech měřičů, typech měřičů a spotřebovaném množství.

### <a name="download-the-usage-detail-report"></a>Stažení sestavy s podrobnostmi o využití

1. Přihlaste se k portálu Azure EA jako podnikový správce.
1. V levém navigačním panelu klikněte na **Sestavy**.
1. Klikněte na kartu **Stažení údajů o využití**.
1. Ve sloupci _Podrobnosti o využití_ vyberte odpovídající měsíc a kliknutím stáhněte sestavu.

## <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Poplatky za marketplace v sestavách na portálu Azure EA

Další informace o poplatcích za marketplace najdete [tady](https://azure.microsoft.com/marketplace/faq/).

Existují dva typy poplatků za marketplace:

- **Podle využití:** Produkty měřené v transakčních jednotkách.  Například virtuální počítače se účtují po hodinách a vyhledávání přes rozhraní API Bingu se účtuje podle počtu hledání.
- **Měsíční poplatek:** Účtuje se měsíčně na základě využití nebo přístupu.

Zobrazení různých poplatků na portálu Enterprise Portal:

1. **Sestava Souhrn využití** – Ukazuje poplatky podle využití **i** měsíční poplatky za marketplace.
1. **Sestava poplatků za Marketplace** – Ukazuje **pouze** poplatky za marketplace na základě využití.  Jednorázové poplatky se nezobrazují.

Mějte na paměti, že Azure Marketplace není k dispozici pro registrace MPSA.

## <a name="advanced-report-download"></a>Stažení rozšířené sestavy

Při generování sestav pro konkrétní rozsahy dat nebo účty je možné využít stažení rozšířené sestavy. Od 30. srpna 2016 se změnil formát výstupního souboru z .xslx na .csv, který podporuje větší sady záznamů.

1. Vyberte **Stažení rozšířené sestavy**.
1. Vyberte **odpovídající rozsah dat**.
1. Vyberte **odpovídající účty**.
1. Vyberte **Požádat o data o využití**.
1. Opakovaně vyberte tlačítko **Aktualizovat**, dokud se stav sestavy neaktualizuje na Stáhnout.
1. Stáhněte si sestavu.

## <a name="reporting-for-non-enterprise-administrators"></a>Generování sestav pro nepodnikové správce

Podnikoví správci můžou povolit přístup k zobrazení nákladů správcům oddělení a vlastníkům účtů v rámci registrace. Po povolení si vlastník účtu může v části Generování sestav na portálu Enterprise Portal stahovat sestavy .csv specifické pro jeho účet a předplatná a zobrazovat vizuální informace.

### <a name="to-enable-access"></a>Povolení přístupu:

 1. Přihlaste se jako podnikový správce.
 1. V levém navigačním panelu klikněte na **Spravovat**.
 1. Klikněte na kartu**Registrace**.
 1. V části Podrobnosti o registraci vyberte ikonu tužky vedle následujících položek:
    - Správce oddělení může zobrazit náklady
    - Vlastník účtu může zobrazit náklady
 1. Vyberte **Povoleno**.
 1. Klikněte na **Uložit**.

### <a name="to-view-reports"></a>Zobrazení sestav:

1. Přihlaste se k portálu Azure EA jako správce oddělení nebo vlastník účtu.
1. V levém navigačním panelu klikněte na **Sestavy**.
1. Kliknutím na kartu **Souhrn využití** zobrazíte vizuální informace o účtu a předplatném.
1. Kliknutím na **Stažení údajů o využití** zobrazíte sestavy .csv.

Pokud správci oddělení a vlastníci účtů používají funkci _Stažení rozšířené sestavy_, nemůžou zobrazit poplatky. V nákladech se zobrazí 0 USD.

Možnost Vlastník účtu může zobrazit poplatky se vztahuje na vlastníky účtů a všechny uživatele, kteří mají oprávnění k přidruženým předplatným. Pokud jste nepřímý zákazník, váš distribuční partner musí povolit funkce nákladů.

## <a name="move-usage-data-to-another-enrollment"></a>Přesun dat o využití do jiné registrace

Data o využití se přesouvají jenom v případě, že se převod provádí zpětně ke staršímu datu. Existují dvě možnosti, jak přesunout data o využití z jedné registrace do druhé:

- Převody účtů z jedné registrace do druhé
- Převody registrací z jedné registrace do druhé

V obou případech musíte týmu podpory EA poslat [žádost o podporu](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) a požádat je o pomoc. 

## <a name="azure-ea-pricing-overview"></a>Přehled cen Azure EA

Tento dokument obsahuje podrobnosti o způsobu výpočtu využití a odpovědi na řadu nejčastějších dotazů souvisejících s cenami různých služeb Azure v programech Enterprise, kde je výpočet složitější.

### <a name="price-protection"></a>Ochrana cen

Zákazníci a distribuční partneři (v případě našich nepřímých kanálů) mají záruku, že pro ně budou platit stejné (nebo nižší) ceny, které jsou uvedené v jejich ceníku pro zákazníky, nebo ceny platné k datu vstoupení jejich nákupu Azure v platnost. Tato cena se označuje jako základní cena. V případě služeb zavedených po tomto nákupu se jedná o cenu s odpovídající úrovní slevy platnou při prvním představení dané služby. Ochrana cen platí pro celé období závazku, které může v závislosti na programu Enterprise trvat jeden nebo tři roky.

### <a name="price-changes"></a>Změny cen

Microsoft může během období registrace snížit aktuální cenu programu Enterprise pro jednotlivé služby Azure.  Po dobu platnosti nižších cen rozšíříme tyto snížené sazby na stávající zákazníky.  Pokud se tyto sazby později zvýší, ceny služeb v rámci registrace se nezvýší nad rámec výše definované ochrany cen zákazníka, ale můžou se zvýšit vzhledem k předchozím nižším cenám.  Microsoft v každém případě bude informovat zákazníky a nepřímé distribuční partnery o připravovaných změnách cen prostřednictvím e-mailu zaslaného podnikovým správcům a správcům partnerů přidruženým k dané registraci.

### <a name="current-effective-pricing"></a>Aktuálně platné ceny

Zákazníci a distribuční partneři můžou zobrazit aktuální ceny pro určitou registraci tak, že se přihlásí k portálu [Azure Enterprise Portal](https://ea.azure.com/) a přejdou na stránku ceníku pro danou registraci.  Pokud si Azure zakoupíte přímo přes některého z našich distribučních partnerů a váš distribuční partner nepovolí zobrazení cen s přirážkou pro vaši registraci, budete muset o aktualizace cen požádat svého distribučního partnera.

### <a name="introduction-of-new-azure-services"></a>Zavádění nových služeb Azure

Neustále Azure vylepšujeme a pravidelně přidáváme nové služby, jejichž ceny se stanovují odděleně od existujících služeb.  Některé služby ve verzi Preview jsou dostupné automaticky, zatímco jiné vyžadují akci zákazníka na [Portálu účtů Azure](https://account.windowsazure.com/PreviewFeatures). Mějte také na paměti, že při přechodu služeb z verze Preview na obecně dostupnou verzi může dojít ke zvýšení cen, protože začnou platit úplné smlouvy SLA o výkonu a spolehlivosti. A konečně, některé služby se po zavedení nabízejí s propagačními cenami, které se v budoucnu můžou zvýšit. Na takové zvýšení v důsledku přechodu z verze Preview nebo propagačních cen na obecně dostupnou verzi se normální ochrana základní ceny nevztahuje a bude platit pro budoucí využití těchto služeb. Zákazníci můžou zabránit poplatkům souvisejícím s těmito službami tím, že se rozhodnou nové služby nevyužívat.

### <a name="introduction-of-regional-pricing"></a>Zavedení regionálních cen

Kromě zavedení nových služeb se také v souvislosti s rozšiřováním regionální podpory služeb pravidelně mění globální charakter těchto služeb na regionálnější model. Po prvním zavedení regionalizace určité služby se ochrana cen vztahuje na nové oblasti, a ne na předchozí globální cenu platnou pro registraci. Další oblasti zavedené pro stejnou službu později se však považují za nové služby a nabízejí se s vlastními individuálními sazbami, které jsou nezávislé na ochraně základních cen.

### <a name="enterprise-msdn-devtest"></a>Enterprise MSDN pro vývoj/testování

Podnikoví správci můžou vlastníkům účtů umožnit vytvářet předplatná na základě nabídky EA MSDN pro vývoj/testování. Aby to fungovalo správně, vlastník účtu musí pro předplatitele základní nabídky MSDN nastavit potřebná předplatná EA MSDN pro vývoj/testování. Aktivní předplatitelé MSDN díky tomu můžou spouštět v Azure vývojové a testovací úlohy za speciální sazby pro vývoj/testování. Další informace najdete na stránce nabídky [EA MSDN pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="enterprise-program-usage-calculation-guidelines"></a>Pokyny k výpočtu využití programu Enterprise

Informace o základních cenách pro veřejnost, měrných jednotkách, nejčastějších dotazech a pokynech ke generování sestav využití pro jednotlivé služby najdete na stránkách [Služby Azure](https://azure.microsoft.com/services/) a [Ceny Azure](https://azure.microsoft.com/pricing/). Při výpočtu využití služeb byste navíc měli využít následující pokyny k programu Enterprise.

### <a name="enterprise-program-units-of-measure"></a>Měrné jednotky programu Enterprise

Měrné jednotky pro programy Enterprise se často liší od jednotek, které znáte z jiných programů, jako je například program Microsoft Online Services Agreement (MOSA). To znamená, že u řady služeb se měrná jednotka agreguje, aby bylo možné poskytovat normalizované ceny. Měrná jednotka, která se na portálu Enterprise Portal zobrazuje v zobrazení Souhrn využití, je vždy měrnou jednotkou programu Enterprise. Úplný seznam aktuálních měrných jednotek a převodech pro jednotlivé služby najdete v souboru [Friendly Service Names](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) (Popisné názvy služeb).

### <a name="rounding-rules"></a>Pravidla zaokrouhlování

Při zaokrouhlování na portálu Enterprise Portal se využívá logika statistického zaokrouhlování neboli Gaussova zaokrouhlování podle standardu IEEE. Podle této logiky se čísla s polovinou zaokrouhlují na nejbližší celé sudé číslo, zatímco obvyklejší funkce zaokrouhlování nahoru vždy čísla s polovinou zaokrouhlují nahoru na nejbližší celé číslo. Tato metoda ve skutečnosti v porovnání se standardní logikou aplikace Excel poskytuje přesnější celkový součet skupiny.

Pokud je první vynechanou číslicí číslice 5, za kterou nenásledují žádné další číslice nebo jen samé nuly, předchozí číslice se převede na sudou (tj. zaokrouhlí se na nejbližší sudou číslici). Např. čísla 2,315 i 2,325 se při zaokrouhlení na setiny zaokrouhlí na 2,32.

Při modelování pravidel zaokrouhlování a převodu používaných na portálu Enterprise Portal v aplikaci Excel doporučujeme používat vzorce MROUND, jak je znázorněno níže.

| Scénář | Logický vzorec statistického zaokrouhlování |
| --- | --- |
| Zaokrouhlování využití | =MROUND({_zdroj_}, 0.0002) |
| Zaokrouhlování cen (na 2 desetinná místa) | =MROUND({_zdroj_}, 0.02) |
| Zaokrouhlování cen (na 0 desetinných míst) | =MROUND({_zdroj_}, 2) |

**Tabulka** **2** **– Převod cloudových služeb a hodin virtuálních počítačů**

### <a name="conversion-between-download-usage-file-and-usage-summary-portal-view"></a>Převod mezi souborem s informacemi o využití ke stažení a zobrazením Souhrn využití na portálu

Nezpracovaná data o využití prostředků se uvádí s přesností na šest desetinných čísel, jak je vidět v sestavě údajů o využití ke stažení. Portál Azure Enterprise Portal však údaje o využití zaokrouhluje na čtyři desetinná čísla v případě jednotek závazku a zkracuje na nula desetinných míst v případě jednotek nadlimitního využití. Nezpracovaná data o využití se nejprve zaokrouhlí na čtyři desetinná místa, pak se převedou na měrnou jednotku programu Enterprise a výsledné jednotky programu Enterprise se znovu zaokrouhlí na čtyři desetinná místa. Skutečně využité hodiny před převodem se zobrazí pouze v sestavě údajů o využití ke stažení, a nikoli v samotném uživatelském rozhraní.

Předpokládejme například, že sestava údajů o využití ke stažení hlásí 694,533404 skutečně využitých hodin SQL Serveru. Tyto jednotky se pak převedou na 6,94533404 jednotek 100 výpočetních hodin, které se pak zaokrouhlí na 6,9453 a tato hodnota se zobrazí na portálu Enterprise Portal.

Tyto jednotky se pak jednoduše vynásobí cenou jednotky závazku, výsledek se zkrátí na dvě desetinná místa a použije se k určení rozšířené fakturované částky. V případě japonského jenu (JPY) a korejského wonu (KRW) se rozšířená částka zaokrouhlí na nula desetinných míst.

V případě nadlimitního využití ve stejném příkladu by se rozšířená fakturovaná částka určila tak, že by se fakturovatelné jednotky zkrátily na šest desetinných míst a pak by se vynásobily cenou jednotky nadlimitního využití. Pro účely fakturace poskytovatelů spravovaných služeb (MSP) se veškeré využití přidružené k oddělení a označené jako MSP po převodu na měrnou jednotku programu EA zkrátí na nula desetinných míst, stejně jako v případě generování sestav o nadlimitním využití. V závislosti na tom, jestli poskytovatel ještě nepřekročil svůj zůstatek peněžního závazku, nebo jestli už služby využívá nadlimitně, může být součet tohoto využití ve výsledku nižší než celkový součet veškerého využití hlášeného v uživatelském rozhraní.

### <a name="graduated-pricing"></a>Odstupňované ceny

Ceny programu Enterprise v současné době nezahrnují odstupňované ceny (kdy se se zvyšujícím využitím snižuje cena za jednotku). Při přechodu z programu MOSA s odstupňovanými cenami na program Enterprise se ceny upraví o případné slevy v rámci programu Enterprise a nastaví se úměrně základní úrovni dané služby.

### <a name="partial-hour-billing"></a>Fakturace po zlomcích hodin

Veškeré využití se fakturuje podle počtu minut převedených na zlomky hodin, a ne v přírůstcích po celých hodinách.  Podnikové sazby uvedené jako hodinové se jednoduše použijí na celkový počet hodin včetně případných zlomků hodin.

### <a name="average-daily-consumption"></a>Průměrná denní spotřeba

Když se služba účtuje měsíčně, ale využití se hlásí každý den, jednou denně se toto využití vyhodnotí, vydělí se číslem 31 a sečte se pro všechny dny v daném fakturačním měsíci. Výsledkem jsou sazby, které v žádném měsíci nepřesáhnou očekávání a v měsících, které mají méně než 31 dnů, jsou mírně nižší.

### <a name="compute-hours-conversion"></a>Převod výpočetního času

Dříve se veškeré využití pro cloudové služby a virtuální počítače úrovně Standard a Basic A0, A2, A3 a A4 generovalo jako podíly (pro A0) nebo násobky (pro A2, A3 a A4) minut měřiče služby Virtual Machines A1. Tento způsob byl pro některé zákazníky nejasný, proto implementujeme změnu a přiřazujeme vyhrazeným měřičům A0, A2, A3 a A4 využití po minutách.

Nový způsob měření začne platit mezi 27. a 28. lednem 2016. V souboru .csv ke stažení pro vaše nasazení během tohoto přechodného období uvidíte dvě řádkové položky: Jedna je pro využití generované měřičem A1 a druhá je pro využití generované na novém vyhrazeném měřiči odpovídajícím velikosti vašeho nasazení.

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

**Tabulka** **4** **– Oblasti pro přenos dat**

Za výchozí přenos dat mezi službami (např. mezi O365 a Azure) hostovanými ve stejném datacentru se neúčtují žádné poplatky.

### <a name="monetary-commitment-and-unbilled-usage"></a>Peněžní závazek a nefakturované použití

Peněžní závazek za Azure je částka, která se za služby Azure platí předem. Peněžní závazek se spotřebovává používáním služeb. Peněžní závazek se používá u vlastních služeb Azure. Existují ale výjimky, které se týkají poplatků fakturovaných samostatně a služeb Marketplace.

### <a name="charges-billed-separately"></a>Poplatky fakturované samostatně

Některé produkty a služby od třetích stran nespotřebovávají peněžní závazek Azure. Místo toho se tyto položky fakturují samostatně v rámci standardní faktury za nadlimitní využití ve fakturačním období.

Všechny poplatky za Azure a Marketplace jsme zkombinovali do jedné faktury, která odpovídá fakturačnímu období registrace. (Toto se netýká zákazníků v Austrálii, Japonsku ani Singapuru.)

Na konsolidované faktuře se jako první zobrazí využití Azure následované případnými poplatky za Marketplace. Zákazníkům v Austrálii, Japonsku nebo Singapuru se poplatky za Marketplace budou dál zobrazovat na samostatné faktuře.

Pokud ke konci standardního fakturačního období nedojde k žádnému nadlimitnímu využití, za poplatky fakturované samostatně se vystaví samostatná faktura. (Platí pro zákazníky v Austrálii, Japonsku a Singapuru.)

**Mezi služby fakturované samostatně patří:**

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

Přímí zákazníci můžou poplatky za marketplace zobrazit na portálu Azure EA. Nákupy na marketplace a související spotřeba se budou účtovat mimo peněžní závazek, a to zpětně za předchozí čtvrtletí nebo měsíc.

Nepřímí zákazníci najdou svá předplatná Azure Marketplace na stránce Správa předplatných na portálu Azure EA Portal, ale ceny budou skryté. Pokud zákazníci chtějí získat informace o poplatcích za marketplace, měli by se obrátit na svého poskytovatele LSP.

Nové pravidelné měsíční nebo roční nákupy na marketplace se budou fakturovat v plné výši v období, kdy došlo k nákupu příslušných položek na marketplace. Platnost těchto položek se v následujícím období automaticky prodlouží ke stejnému dni, kdy došlo k původnímu nákupu.

Na stávající pravidelné poplatky za marketplace to nebude mít vliv. Pravidelné měsíční poplatky se budou dál obnovovat k prvnímu dni každého kalendářního měsíce a roční poplatky se budou obnovovat ke dni výročí nákupu.

Některé služby prodejců třetích stran dostupné na Azure Marketplace teď spotřebovávají zůstatek peněžního závazku smlouvy Enterprise (EA). Dříve se tyto služby účtovaly mimo peněžní závazek EA a fakturovaly se samostatně. Použití peněžního závazku EA pro tyto služby na Marketplace pomáhá zákazníkům zjednodušit nákup a správu plateb. Úplný seznam služeb, které teď spotřebovávají peněžní závazek, najdete na [webu Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/?WT.mc_id=azurebg_email_Trans_33771_1695_Release_Direct_Tier2_March14).

### <a name="partners"></a>Partneři

Partneři LSP si můžou stáhnout ceník specifický pro marketplace tak, že na portálu Azure EA přejdou na ceník a v pravém horním rohu kliknou na odkaz **Ceník Marketplace**. Ceník marketplace bude obsahovat všechny dostupné služby a jejich ceny.

**Pokud si chcete ceník stáhnout, postupujte následovně:**

1. Přejděte do části Sestavy > Ceník.
1. V pravém horním rohu najdete pod svým uživatelským jménem odkaz na ceník Azure Marketplace.
1. Klikněte na odkaz pravým tlačítkem a vyberte **Uložit cíl jako**.
1. V okně Uložit změňte název dokumentu na _AzureMarketplacePricelist.zip_. Tím se soubor xlsx změní na soubor zip.
1. Po dokončení stahování budete mít soubor zip s ceníky pro jednotlivé země.
1. Partneři LSP by ke zjištění cen pro konkrétní zemi měli použít soubor pro konkrétní zemi. Na kartě Oznámení můžou partneři LSP zobrazit zcela nové skladové položky na marketplace i odebrané skladové položky.
1. Ke změnám cen nebude docházet často, ale když k nim dojde, partnerům LSP přijde s 30denním předstihem e-mail s oznámením o zvýšení cen a změnách směnných kurzů.
1. Partneři LSP obdrží jednu fakturu za každou registraci, každého nezávislého výrobce softwaru a každé čtvrtletí.

### <a name="enabling-marketplace-purchases"></a>Povolení nákupů na marketplace

Podnikoví správci mají možnost zakázat nebo povolit nákupy na marketplace pro všechna předplatná v rámci dané registrace. Pokud podnikový správce zakáže nákupy a nějaká předplatná Azure již obsahují předplatná marketplace, tato předplatná marketplace se nezruší, ani to na ně nebude mít žádný vliv.

Přestože zákazníci můžou převést svá přímá předplatná Azure na smlouvu EA tím, že je na portálu Azure EA přidruží ke své registraci, podřízená předplatná marketplace se tím automaticky nepřevedou.

**Povolení nákupů na marketplace:**

1. Přihlaste se k portálu Azure EA jako podnikový správce.
1. Přejděte do části _Správa_.
1. V části _Podrobnosti o registraci_ klikněte na ikonu tužky vedle řádkové položky _Azure Marketplace_.
1. Podle potřeby přepněte přepínač _Povoleno/zakázáno_ nebo _Jen skladové položky Free nebo BYOL\*_ .
1. Klikněte na **Uložit**.

### <a name="marketplace-charges-in-azure-ea-portal-reports"></a>Poplatky za marketplace v sestavách na portálu Azure EA

Další informace o poplatcích za marketplace najdete [tady](https://azure.microsoft.com/marketplace/faq/).

Existují dva typy poplatků za marketplace:

- **Založené na využití:** Produkty se měří v transakčních jednotkách.  Například virtuální počítače se účtují po hodinách a vyhledávání přes rozhraní API Bingu se účtuje podle počtu hledání.
- **Nezaložené na využití:** Jednorázové poplatky nebo pravidelné měsíční poplatky, které nejsou závislé na využití.

V sestavě poplatků za marketplace budou uvedené poplatky založené na využití i poplatky nezaložené na využití.

Mějte na paměti, že Azure Marketplace není k dispozici pro registrace MPSA.

\* Použitím možnosti Jen skladové položky Free nebo BYOL (používání vlastní licence) se omezí nákupy a pořizování skladových položek Azure Marketplace pouze na skladové položky BYOL a Free.

### <a name="services-billed-hourly-for-ea"></a>Služby účtované po hodinách v programu EA

Application Delivery Network a Firewall webových aplikací se v programu EA účtují po hodinách, zatímco v programu MOSP se účtují měsíční sazbou.

### <a name="remote-app"></a>Vzdálená aplikace

Zákazníci EA platí za vzdálenou aplikaci podle cenové úrovně jejich smlouvy EA a neúčtují se jim žádné další poplatky. Standardní cenová hladina zahrnuje prvních 40 hodin, zatímco neomezená cenová hladina pokrývá prvních 80 hodin. Vzdálená aplikace po uplynutí 80 hodin přestane generovat data o využití.

## <a name="azure-marketplace-faq"></a>Nejčastější dotazy k Azure Marketplace

Tento dokument s nejčastějšími dotazy se věnuje aktualizacím možnosti použití peněžního závazku Azure na některé služby publikované třetími stranami na Azure Marketplace.

### <a name="what-are-we-changing-with-respect-to-marketplace-services-and-azure-monetary-commitment"></a>Jaké změny týkající se služeb z marketplace a peněžního závazek Azure provádíme?

Kromě rezervovaných instancí virtuálních počítačů Azure zákazníci za všechny služby zakoupené na Azure Marketplace obdrží samostatnou fakturu. Rozšiřujeme možnosti použití peněžního závazku Azure, který je teď možné využít i na některé služby publikované na Azure Marketplace třetími stranami, které si naši zákazníci kupují nejčastěji.

### <a name="why-are-we-making-this-change"></a>Proč tuto změnu provádíme?

Zákazníci neustále hledají další způsoby, jak využít svou platbu předem v podobě peněžního závazku Azure.  Touto změnou reagujeme na časté žádosti zákazníků a rozšíření peněžního závazku na tyto služby má vliv na velkou část našich zákazníků na Azure Marketplace.

### <a name="what-is-the-customer-benefit"></a>Jakou výhodu to má pro zákazníka?

Zákazníci získají jednodušší prostředí fakturace a možnost zajistit, že využijí svůj peněžní závazek Azure.  Přidáním této výhody k předplacenému peněžnímu závazku a rezervovaným instancím využívajícím peněžní závazek se ještě více zvyšuje hodnota peněžního závazku Azure.

### <a name="what-services-will-deduct-from-azure-monetary-commitment-and-how-will-my-customer-know"></a>Jaké služby se budou odečítat od peněžního závazku Azure a jak se o tom dozví zákazníci?

Během nákupu na Azure Marketplace budou všechny služby, které budou využívat peněžní závazek, odlišené upozorněním. Mezi aktuálně podporované vydavatele patří určité služby publikované společnostmi Red Hat, SUSE, Autodesk a Oracle. Služby s podobnými názvy, které jsou však publikované jinými stranami, než jsou výše uvedené, se od peněžního závazku odečítat nebudou. Úplný seznam najdete na konci těchto nejčastějších dotazů.

### <a name="what-if-my-customer-runs-out-of-monetary-commitment"></a>Co když zákazník vyčerpá peněžní závazek?

Zákazníkům, kteří využijí celý svůj peněžní závazek a služby teď využívají nadlimitně, se poplatky související s těmito službami zobrazí na další faktuře za nadlimitní využití společně se všemi dalšími službami založenými na spotřebě.  Jedná se o změnu, protože dříve by se tyto poplatky účtovaly na samostatné faktuře společně s dalšími nabídkami Azure Marketplace.

### <a name="why-are-we-not-enabling-azure-monetary-commitment-for-all-marketplace-purchases"></a>Proč nepovolíme peněžní závazek Azure pro všechny nákupy na marketplace?

Často pracujeme na tom, abychom zákazníkům v souvislosti s peněžním závazkem Azure poskytovali co nejlepší služby. Tato změna se bude týkat velkého množství zákazníků a významné části celkové útraty na Azure Marketplace. V budoucnu se možná přidají další služby.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Jaký to bude mít vliv na nepřímé registrace a partnery?

Na naše zákazníky ani partnery s nepřímou registrací to nebude mít žádný vliv. Na tyto služby se vztahují stejné možnosti přirážky partnera jako na ostatní služby založené na spotřebě. Jedinou změnou bude, že se budou zobrazovat na jiné faktuře, a poplatky se budou platit z peněžního závazku.

### <a name="list-of-services-that-will-deduct-from-azure-monetary-commitment"></a>Seznam služeb, které se budou odečítat od peněžního závazku Azure

Pro konkrétní nabídky Azure Marketplace je možné využít prostředky peněžních závazků. Úplný seznam produktů zapojených do tohoto programu najdete v tématu [Peněžní závazek Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment).

## <a name="additional-information"></a>Další informace

Další podrobnosti najdete v těchto zdrojích dalších informací. Tyto soubory se aktualizují dvakrát měsíčně, vždy 6. a 20. den v měsíci. Podrobnosti k jednotlivým souborům:

| Název přílohy | Popis | Zásady vytváření názvů adres URL |
| --- | --- | --- |
| [**Friendly Service Names**](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) (Popisné názvy služeb) | Obsahuje seznam všech aktivních služeb společně s kategorií služby, popisným názvem služby, názvem a číslem produktu závazku, názvem a číslem části spotřeby, měrnými jednotkami a převodním koeficientem mezi nahlášeným využitím a zobrazeným využitím na portálu Enterprise Portal. | Friendly\_Service\_Names.xlsx |
| [**Service Download Fields**](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) (Pole ve stažených informacích o službách) | Tato tabulka obsahuje seznam všech možných kombinací polí souvisejících se službami v sestavě využití ke stažení. | Service\_Download\_Fields.xlsx |

**Tabulka** **5** **– Další zdroje informací**

## <a name="power-bi-reporting"></a>Generování sestav Power BI

### <a name="power-bi-pro"></a>Power BI Pro

Power BI Pro je teď k dispozici pro zákazníky EA. Pomocí Power BI Pro můžete generovat a sdílet sestavy a efektivně tak spravovat údaje o nákladech s využitím dalších funkcí pro spolupráci a aktualizaci dat. Power BI Pro nabízí vyšší datovou kapacitu a limity streamování dat. Pro zákazníky se smlouvou Azure Enterprise připravujeme nové zajímavé funkce pro správu nákladů.

Aktuální uživatelé Power BI úrovně Free využívající balíček obsahu Microsoft Azure Consumption Insights mají nárok na 60denní bezplatnou zkušební verzi Power BI Pro. Pokud budete chtít používat Power BI Pro i po skončení bezplatné zkušební verze, můžete přidat licenci.

Pokud si chcete zaregistrovat bezplatnou zkušební verzi, klikněte na ikonu ozubeného kola a vyberte **Spravovat osobní úložiště**. Pak na pravé straně vyberte **Vyzkoušet verzi Pro zdarma**. Další informace o bezplatné zkušební verzi Power BI Pro najdete v tématu popisujícím [samoobslužnou registraci Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial).

### <a name="microsoft-azure-ea-power-bi-pro-trial-terms"></a>Podmínky zkušební verze Power BI Pro v programu Microsoft Azure EA

- **Obecné účely:** Nabídka rozšířené bezplatné zkušební verze Power BI Pro pro balíček obsahu Microsoft Azure Enterprise („nabídka“) je dostupná pro stávající oprávněné uživatele během období platnosti nabídky a umožňuje jim získat přístup k určitým přehledům souvisejícím s jejich využitím Microsoft Azure prostřednictvím používání konkrétního balíčku obsahu Power BI.
- **Nárok:** Nabídku můžou využít uživatelé se smlouvou Enterprise (EA), kteří mají funkci související s fakturací Microsoft Azure, službami, správou služeb nebo nákladů jejich organizace.
- **Vyloučení:**
  - Uživatelé, kteří se již účastní rozšířené bezplatné zkušební verze Power BI Pro, budou mít dál nárok na stávající nabídku, ale nebudou moct využít nabídku bezplatné zkušební verze Power BI Pro v programu Azure EA.
  - Uživatelé využívající tuto nabídku můžou Power BI Pro používat pouze s balíčkem obsahu Microsoft Azure Enterprise. Jakékoli jiné použití Power BI Pro je zakázané.
  - Období: Nabídka začne platit 1. června 2017 a skončí 31. května 2018.  Nabídku je možné přijmout kdykoli během 12měsíčního období, ale 31. května 2018 se nabídka ukončí pro všechny uživatele bez ohledu na to, kdy ji přijali.

### <a name="to-access-the-microsoft-azure-consumption-insights-content-pack"></a>Přístup k balíčku obsahu Microsoft Azure Consumption Insights:

1. Přejděte na [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Klikněte na **Získat**.
1. Zadejte číslo registrace a počet měsíců. Klikněte na **Další**.
1. Zadejte svůj přístupový klíč rozhraní API pro připojení. Klíč pro vaši registraci najdete na portálu [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Klikněte na **Přihlásit se**.
1. Automaticky se spustí proces importu. Po dokončení se v navigačním podokně zobrazí nový řídicí panel, sestava a model. Kliknutím na řídicí panel zobrazíte importovaná data.

Další informace o možnostech vygenerování klíče rozhraní API pro vaši registraci najdete v souboru nápovědy k sestavám rozhraní API na portálu [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Pokud chcete získat další informace o novém balíčku obsahu, stáhněte si dokument o [Microsoft Azure Consumption Insights](https://automaticbillingspec.blob.core.windows.net/spec/Microsoft%20Azure%20Consumption%20Insights.docx?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Získání přístupu ke starší verzi balíčku obsahu EA pro Power BI:

 1. Přejděte na [web Power BI](https://app.powerbi.com/getdata/services/azure-enterprise).
 1. Přihlaste se pomocí platného pracovního nebo školního účtu.
    - Může se jednat o stejný pracovní nebo školní účet, který používáte pro přístup k registraci prostřednictvím portálu Azure EA, nebo jiný pracovní nebo školní účet.
 1. Na řídicím panelu služeb vyberte **Microsoft Azure Enterprise** a klikněte na **Připojit**.
 1. Na obrazovce Připojit k Azure Enterprise zvolte:
    - adresu URL prostředí Azure: [https://ea.azure.com](https://ea.azure.com/).
    - počet měsíců: vyberte hodnotu v rozmezí 1 až 36.
    - číslo registrace: zadejte číslo registrace.
    - Klikněte na **Další**.
 1. Do pole Ověřovací klíč zadejte klíč rozhraní API. Klíč rozhraní API můžete získat tady na portálu Azure EA na kartě Stažení údajů o využití kliknutím na Přístupový klíč rozhraní API.
    - Klíč zkopírujte a vložte ho do pole Klíč účtu.
 1. Načtení dat v Power BI bude v závislosti na velikosti datových sad trvat přibližně 5 až 30 minut.

Generování sestav Power BI je k dispozici pro přímé a nepřímé zákazníky a partnery EA, kteří mají přístup k zobrazení fakturačních údajů.

## <a name="report-faq"></a>Nejčastější dotazy k sestavám

Tato část článku obsahuje odpovědi na nejčastější dotazy související s interpretací sestav.

### <a name="why-is-my-cost-showing-as-0"></a>Proč se mi v nákladech zobrazuje 0 USD?

**Přímá registrace:** Pokud jste vlastníkem účtu nebo správcem oddělení, obraťte se na správce EA s žádostí o povolení funkce cen:

1. V levém navigačním panelu klikněte na **Spravovat**.
1. Klikněte na modrou tužku vedle možnosti Správce oddělení může zobrazit náklady.
1. Vyberte **Povoleno** a uložte provedenou změnu.
1. Klikněte na modrou tužku vedle možnosti Vlastník účtu může zobrazit náklady.
1. Vyberte **Povoleno** a uložte provedenou změnu.

Touto akcí získají vlastnící účtů a správci oddělení přístup k informacím o nákladech a cenách v sestavách využití.

**Nepřímá registrace:** Ověřte u svého partnera, že pro vás povolil funkci cen. To může udělat pouze partner a jakmile tuto funkci zapne, budete moct zobrazit náklady a ceny ve vaší registraci jako správce EA.

Pokud chcete funkci zobrazení poplatků povolit pro vaše vlastníky účtů a správce oddělení, postupujte podle výše uvedených kroků v části **Přímá registrace**.

### <a name="there-is-no-sku-information-on-the-usage-detail-report"></a>Sestava s podrobnostmi o využití neobsahuje žádné informace o skladových položkách

Sestava s podrobnostmi o využití neobsahuje informace o skladových položkách, můžete v ní však zobrazit informace o využitých službách. Pak si můžete stáhnout příslušnou sestavu ceníku, kde najdete informace o skladových položkách.

### <a name="the-total-amount-on-marketplace-does-not-match-on-usage-summary-and-csv-report"></a>Celková částka na Marketplace neodpovídá souhrnu využití a sestavě CSV

Sestava poplatků za Marketplace ukazuje pouze poplatky za marketplace na základě využití. Jednorázové poplatky se nezobrazují. Nejaktuálnější využití s poplatky na základě využití i jednorázovými poplatky najdete na stránce se souhrnem využití.

### <a name="there-is-no-information-on-my-api-report"></a>Sestava rozhraní API neobsahuje žádné informace

Platnost klíčů rozhraní API vyprší každých šest měsíců. Pod dochází k nějakému problému, vygenerujte nový klíč rozhraní API. Také je důležité požádat vašeho správce EA, aby vygeneroval nové klíče rozhraní API a postupoval podle kroků v nejčastějších dotazech k sestavě rozhraní API.

### <a name="my-power-bi-report-isnt-working"></a>Sestava Power BI nefunguje

V případě problémů s Power BI na stránce [https://support.powerbi.com](https://support.powerbi.com) odešlete technický lístek týmu Power BI, aby vám mohl pomoct.

### <a name="my-resource-tags-arent-showing-up-on-my-reports"></a>V sestavách se nezobrazují značky prostředků

Značky prostředků se spravují na webu Azure Portal. Tým předplatného Azure můžete kontaktovat na stránce [https://portal.azure.com](https://portal.azure.com). Při vytváření lístku podpory postupujte podle pokynů na [tomto odkazu](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

### <a name="why-does-my-resource-rate-change-every-day"></a>Proč se mi každý den mění sazba za prostředek?

Sazba za prostředek v podrobné sestavě využití je vypočítaná hodnota, která představuje průměrnou měsíční sazbu účtovanou za službu. Tato sazba se počítá na základě průměru měsíčního závazku a měsíčních poplatků za nadlimitní využití jednotky služby. Část poplatků účtovaná vůči závazku a sazby za nadlimitní využití se budou až do konce měsíce měnit. Z tohoto důvodu se v průběhu měsíce bude měnit také sazba za prostředek. Sazba za prostředek se uzamkne pátý den po konci měsíce.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Glosář procesů pro výpočet sazby za prostředek

**Celkový počet jednotek RAW:** Spotřebované množství v rámci podrobné sestavy využití.
**Prostředky MOCP na jednotku:** Upstreamový systém využití pro každou službu generuje údaje o využití v různých jednotkách. (Přednastavená hodnota) **Spotřeba na jednotku:** Měrná jednotka EA. (Přednastavená hodnota) **Cena:** Jednotková cena z portálu Azure EA.
**Celkové náklady:** Rozšířené náklady z podrobné sestavy využití nebo využití závazku + nadlimitní využití z portálu Azure EA.


### <a name="charges-calculation"></a>Výpočet poplatků

**Převod na prostředky MOCP na jednotku** = ROUND(celkový počet jednotek RAW × prostředky MOCP na jednotku,4); **převod na jednotky** = jednotky po převodu na prostředky MOCP na jednotku / spotřeba na jednotku; **celkové náklady** = jednotky × cena

### <a name="download-usage-calculation-logic"></a>Logika výpočtu při stažení údajů o využití

**Sazba za prostředek** = celkové náklady / (celkový počet jednotek RAW / prostředky MOCP na jednotku)

Sazba za prostředek se odvozuje na základě poplatků a často neodpovídá skutečné jednotkové ceně v ceníku.

Pro účely výpočtů poplatků za nadlimitní využití se nezpracovaná data o využití prostředků uvádí s přesností na šest desetinných čísel, jak je vidět v sestavě údajů o využití ke stažení. Portál Azure EA však údaje o využití zaokrouhluje na čtyři desetinná čísla v případě jednotek závazku a zkracuje na nula desetinných míst v případě jednotek nadlimitního využití. To znamená, že na portálu Azure EA veškeré využití účtované jako nadlimitní účtujeme pouze po celých jednotkách. U využití, které se účtuje jako nadlimitní nebo ve smíšených měsících, bude mezi jednotkovou cenou a sazbou za prostředek výrazný rozdíl.

## <a name="next-steps"></a>Další kroky
- Informace, které vysvětlují fakturu a poplatky, najdete v tématu, ve kterém [je vysvětlená faktura ke smlouvě Azure Enterprise](billing-understand-your-bill-ea.md).
- Pokud chcete začít používat portál Azure EA, přečtěte si, [jak začít používat portál Azure EA](billing-ea-portal-get-started.md).
