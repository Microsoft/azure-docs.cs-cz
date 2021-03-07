---
title: Faktury za smlouvu Azure Enterprise
description: V tomto článku je vysvětlená správa faktury za Azure Enterprise a její další použití.
author: bandersmsft
ms.author: banders
ms.date: 03/05/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: ruturajd
ms.custom: contperf-fy21q1
ms.openlocfilehash: 71ba1d6e1e45c5c2e72e2be620158b1aa4b71582
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430801"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Faktury za smlouvu Azure Enterprise

V tomto článku je vysvětlená správa faktury za smlouvu Azure Enterprise (Azure EA) a její další použití. Vaše faktura představuje vyúčtování. Zkontrolujte její správnost. Měli byste se také seznámit s dalšími úkoly, které při správě faktury můžete potřebovat.

## <a name="view-usage-summary-and-download-reports"></a>Zobrazení souhrnu využití a stahování sestav

Podnikoví správci si na portálu Azure Enterprise Portal můžou zobrazit souhrnné údaje o využití, výši spotřebované zálohy na Azure a poplatky za další využití. Poplatky jsou vyjádřené souhrnně za všechny účty a všechna předplatná.

Pokud se chcete podívat na podrobné využití u konkrétních účtů, stáhněte si sestavu s podrobnostmi o využití:

1. Přihlaste se k portálu Azure Enterprise.
1. Vyberte **Sestavy**.
1. Vyberte kartu **Download Usage** (Stáhnout data o využití).
1. V seznamu sestav vyberte **Download** (Stáhnout) u měsíční sestavy, kterou chcete získat.

   > [!NOTE]
   > Sestava s podrobnostmi využití nezahrnuje příslušné daně.
   >
   > Mezi časem skutečného využití a promítnutím tohoto údaje do sestavy může vzniknout zpoždění až 8 hodin.

Zobrazení sestav a grafů se souhrnem využití:

1. Přihlaste se k portálu Azure Enterprise Portal.
1. Vyberte období zálohy.
   Pokud chcete změnit rozsah data pro **Usage Summary** (Souhrn využití), můžete vpravo nahoře na stránce přepnout z **M** (Monthly) (Měsíčně) na **C** (Custom) (Vlastní) a zadat vlastní počáteční a koncové datum.  
   ![Vytvoření a zobrazení souhrnu využití a stahování sestav ve vlastním zobrazení](./media/ea-portal-enrollment-invoices/create-ea-view-usage-summary-and-download-reports-custom-view.png)
1. Pokud se chcete podívat na další podrobnosti, můžete vybrat období nebo měsíc v grafu.
   - V grafu se zobrazuje meziměsíční využití s rozpisem spotřebovaného využití, nadměrného vyúčtování služeb, poplatků fakturovaných samostatně a poplatků z Azure Marketplace.
   - Pro vybraný měsíc můžete pomocí polí pod grafem filtrovat podle oddělení, účtů a předplatných.
   - Můžete přepínat mezi volbami **Charge by Services** (Vyúčtování podle služeb) a **Charge by Hierarchy** (Vyúčtování podle hierarchie).
   - Rozbalením a sbalením příslušných oddílů **Azure Service** (Služba Azure), **Charges Billed Separately** (Samostatně fakturované poplatky) a **Azure Marketplace** si můžete zobrazit podrobnosti.

Podívejte se na toto video, ze kterého se dozvíte, jak zobrazit informace o využití:

> [!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>Stahování sestav CSV

Podnikoví správci si mohou ze stránky s měsíčními sestavami stáhnout následující sestavy ve formátu CSV:

- Zůstatek a poplatky
- Podrobnosti o využití
- Poplatky za Azure Marketplace
- Ceník

Jak sestavy stáhnout:

1. Na portálu Azure Enterprise vyberte **Reports** (Sestavy).
2. V horní části stránky vyberte **Download Usage** (Stáhnout data o využití).
3. Vyberte tlačítko **Download** (Stáhnout) vedle sestavy pro požadovaný měsíc.

   > [!NOTE]
   > Mezi datem skutečného využití a jeho zobrazením v sestavě může docházet ke zpoždění až 72 hodin.
   >
   > U uživatelů, kteří si soubory CSV stahují přes Safari do Excelu, může docházet k chybám formátování. Chybám se vyhnete tak, že soubor otevřete pomocí textového editoru.

![Příklad ukazující stránku pro stažení údajů o využití](./media/ea-portal-enrollment-invoices/create-ea-download-csv-reports.png)

Podívejte se na toto video, které ukazuje, jak si stáhnout informace o využití:

> [!VIDEO https://www.youtube.com/embed/eY797htT1qg]

### <a name="advanced-report-download"></a>Stažení rozšířené sestavy

Pomocí stažení rozšířené sestavy můžete získat sestavy, které pokrývají konkrétní rozsahy kalendářních dat nebo účty. Výstupní soubor je ve formátu CSV, aby pojal rozsáhlé sady záznamů.

1. Na portálu Azure Enterprise vyberte **Advanced Report Download** (Stažení rozšířené sestavy).
1. Vyberte příslušný rozsah data a příslušné účty.
1. Vyberte **Požádat o data o využití**.
1. Opakovaně vyberte tlačítko **Refresh** (Aktualizovat), dokud se stav sestavy neaktualizuje na **Download** (Stáhnout).
1. Stáhněte sestavu.

### <a name="download-usage-reports-and-billing-information-for-a-prior-enrollment"></a>Stažení sestav využití a fakturačních údajů pro předchozí registraci

Sestavy využití a fakturační údaje pro předchozí registraci si můžete stáhnout až po přenosu registrace. Portál Azure Enterprise i správa nákladů umožňuje generování historických sestav.

Portál Azure Enterprise vyfiltruje neaktivní registrace, aby nebyly vidět. Aby se zobrazily neaktivní přenesené registrace, budete muset zrušit zaškrtnutí políčka **Active** (Aktivní).  

![Zrušení zaškrtnutí políčka Active (Aktivní) umožní uživateli zobrazit neaktivní registrace](./media/ea-portal-enrollment-invoices/unchecked-active-box.png)

## <a name="change-a-po-number-for-an-upcoming-overage-invoice"></a>Změna čísla nákupní objednávky pro chystanou fakturu za nadlimitní využití

Portál Azure Enterprise Portal automaticky generuje výchozí číslo nákupní objednávky, pokud podnikový správce nenastavil před datem faktury jiné číslo. Podnikový správce může číslo nákupní objednávky aktualizovat až sedm dnů od přijetí automatického e-mailu s oznámením o faktuře. 

Chcete-li zabránit automatické generaci čísel nákupních objednávek každý měsíc, můžete číslo nákupní objednávky uzamknout. Viz [číslo pro zámek](#lock-po-number-to-prevent-automatic-update-in-upcoming-billing-cycles).

### <a name="update-the-azure-services-purchase-order-number"></a>Aktualizace čísla nákupní objednávky služeb Azure

1. Na portálu Azure Enterprise Portal vyberte **Report** (Sestava) > **Usage Summary** (Souhrn využití).
1. V pravém horním rohu vyberte **Edit PO Numbers** (Upravit čísla nákupních objednávek).
1. Vyberte přepínač **Služby Azure**.
1. V rozevírací nabídce pro výběr rozsahu dat vyberte **Fakturační období**.
   Číslo nákupní objednávky můžete upravit během období 7 dnů po přijetí oznámení o faktuře, musí to ale být dříve, než fakturu uhradíte.
1. Do pole **Číslo objednávky** zadejte nové číslo nákupní objednávky.
1. Pokud chcete změnu odeslat, vyberte **Uložit**.

### <a name="update-the-azure-marketplace-purchase-order-number"></a>Aktualizace čísla nákupní objednávky Azure Marketplace

1. Na portálu Azure Enterprise Portal vyberte **Report** (Sestava) > **Usage Summary** (Souhrn využití).
1. V pravém horním rohu vyberte **Edit PO Numbers** (Upravit čísla nákupních objednávek).
1. Vyberte přepínač **Marketplace**.
1. V rozevírací nabídce pro výběr rozsahu dat vyberte **Fakturační období**.  
    Číslo nákupní objednávky můžete upravit během období 7 dnů po přijetí oznámení o faktuře, musí to ale být dříve, než fakturu uhradíte.
1. Do pole **Číslo objednávky** zadejte nové číslo nákupní objednávky.
1. Pokud chcete změnu odeslat, vyberte **Uložit**.

### <a name="lock-po-number-to-prevent-automatic-update-in-upcoming-billing-cycles"></a>Umožňuje uzamknout číslo a zabránit automatické aktualizaci v nadcházejících fakturačních cyklech.

Po uzamknutí čísla No zůstane tato hodnota uzamčená pro všechny nové faktury a nemusíte aktualizovat číslo No.

1.  Na portálu Azure Enterprise vyberte   >  **Souhrn využití** sestav.
2.  V pravém horním rohu vyberte **Edit PO Numbers** (Upravit čísla nákupních objednávek).
3.  Do pole **číslo nákupní objednávky** zadejte nové číslo objednávky.
4.  Vyberte pole **zámek pro číslo objednávky** .
5.  Pokud chcete změnu odeslat, vyberte **Uložit**.  
    :::image type="content" source="./media/ea-portal-enrollment-invoices/lock-po.png" alt-text="Snímek obrazovky s polem Zobrazit/upravit čísla objednávek" lightbox="./media/ea-portal-enrollment-invoices/lock-po.png" :::


## <a name="azure-enterprise-billing-frequency"></a>Četnost fakturace Azure Enterprise

Microsoft fakturuje všechny nákupy záloh na služby Microsoft Azure na roční bázi, a to k datu vstoupení smlouvy v platnost. Veškeré využití nad rámec výše zálohy vyúčtovává Microsoft zpětně.

- Poplatky za zálohu se uvádějí na základě měsíční sazby a fakturují se ročně předem.
- Poplatky za nadlimitní využití se počítají každý měsíc a fakturují se zpětně na konci fakturačního období.

### <a name="billing-intervals"></a>Fakturační intervaly

Fakturační interval závisí na tom, jak se rozhodnete provádět nákupy záloh. Vaše roční záloha se kryje s:

- datem výročí registrace,
- datem platnosti jednoročního doplňujícího předplatného.

Datum, kdy obdržíte fakturu za nadlimitní využití, závisí na počátečním datu a nastavení vaší registrace:

- **Přímé registrace s počátečním datem před 1. květnem 2018:**
  - Pokud jste přímým zákazníkem Enterprise Agreement (EA), máte pro služby Azure (kromě Azure Marketplace) nastavené roční fakturační období. Fakturační cyklus vychází z data výročí, což je datum, kdy vaše smlouva začala platit.
  - Pokud překročíte 150 % prahové hodnoty zálohy na Azure se smlouvou EA, budete automaticky převedeni na čtvrtletní fakturační cyklus, který je založený na vašem datu výročí. Obdržíte také fakturu za nadlimitní využití služeb Azure.
  - Pokud nepřekročíte 150 % prahové hodnoty zálohy na Azure, zůstane registrace v ročním fakturačním cyklu. Fakturu za nadlimitní využití obdržíte na konci roku zálohy.

- **Přímé registrace s počátečním datem po 1. květnu 2018:**
  - Pro faktury za využití Azure a poplatky fakturované samostatně platí měsíční fakturační období.
  - Jakékoli poplatky, které nejsou pokryté vaší zálohou na Azure, jsou splatné jako poplatek za nadlimitní využití.  

- **Nepřímé registrace s počátečním datem před 1. květnem 2018:**

  Pokud jste nepřímým zákazníkem se smlouvou Enterprise Agreement (EA) a počátečním datem před 1. květnem 2018, máte nastavený čtvrtletní fakturační cyklus. Distribuční partner bude fakturovat přímo vám.  

- **Nepřímé registrace s počátečním datem po 1. květnu 2018:**

  Máte měsíční fakturační cyklus.  

### <a name="increase-your-azure-prepayment"></a>Zvýšení zálohy na Azure

Zálohu můžete kdykoli zvýšit. Bude se vám účtovat počet měsíců zbývajících v období zálohy daného roku. Pokud si například zaregistrujete roční doplňující předplatné a v šestém měsíci zvýšíte svoji zálohu, bude se vám toto zvýšení fakturovat za zbývajících šest měsíců daného období. Na posledních šest měsíců období vaší zálohy se pak výše zálohy aktualizuje. Tato nová množství se použijí pro stanovení poplatků za nadlimitní využití.

### <a name="overage"></a>Překročení

Jako nadlimitní využití se vám bude fakturovat využití rezervací, které během daného fakturačního období překročí výši vaší zálohy. Pokud chcete zobrazit rozpis výpočtu nadlimitního využití jednotlivých položek, projděte si sestavu souhrnu využití nebo se obraťte na svého distribučního partnera.

U každé položky na faktuře uvidíte položky:

- **Rozšířená částka:** poplatky celkem
- **Využití zálohy:** Výše zálohy využitá k uhrazení poplatků
- **Čistá částka:** Poplatky, které překračují výši vaší zálohy

Příslušné daně se počítají pouze z čisté částky nad rámec zálohy.

Fakturace nadlimitního využití je automatizovaná. Načasování oznámení a faktur závisí na koncovém datu fakturačního období.

- Oznámení o nadlimitním využití se obvykle posílá sedm dnů po koncovém datu fakturace.
- Faktury za nadlimitní využití se odesílají sedm až devět dnů po oznámení.
- Během sedmi dnů mezi oznámením a fakturací za nadlimitní využití si můžete prohlédnout poplatky a aktualizovat systémem vygenerovaná čísla nákupních objednávek.

### <a name="azure-marketplace"></a>Azure Marketplace

Počínaje fakturačním cyklem od dubna 2019 začali zákazníci dostávat jednu fakturu Azure kombinující v jedné faktuře veškeré poplatky za Azure a Azure Marketplace ze dvou různých faktur. Tato změna nemá vliv na zákazníky v Austrálii, Japonsku ani Singapuru.

Během přechodu na kombinovanou fakturu obdržíte částečnou fakturu za Azure Marketplace. Na této poslední samostatné faktuře se zobrazí poplatky za Azure Marketplace účtované před datem aktualizace fakturace. Poplatky za Azure Marketplace účtované po tomto datu se zobrazí na vaší faktuře Azure. Po skončení přechodného období se všechny poplatky za Azure a Azure Marketplace budou zobrazovat na kombinované faktuře.  

### <a name="adjust-billing-frequency"></a>Úprava frekvence fakturace

Vystavování faktur zákazníkům probíhá ročně, čtvrtletně nebo měsíčně. Fakturační cyklus si zákazník volí při podpisu smlouvy. Nejkratším fakturačním intervalem je měsíc.

- Změnu fakturačního cyklu u přímých registrací z ročního na čtvrtletní musí **odsouhlasit** podnikový správce. U nepřímých registrací musí změnu schválit správce partnera. Změna se uskuteční na konci aktuálního fakturačního čtvrtletí.
- Ke změně ročního nebo čtvrtletního fakturačního cyklu na měsíční je nutný **dodatek** smlouvy.  Jakékoli změny fakturačního cyklu u stávající registrace vyžadují souhlas podnikového správce nebo příjemce faktury.
- Souhlas **odešlete** na [podporu portálu Azure Enterprise Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Vyberte kategorii problému: **Fakturace**.

Změna se uskuteční na konci aktuálního fakturačního čtvrtletí.

Pokud jste podepsali dodatek č. M503, můžete frekvenci kterékoli smlouvy změnit na měsíční fakturaci. 

### <a name="request-an-invoice-copy"></a>Žádost o kopii faktury

Pokud chcete požádat o kopii faktury, obraťte se na partnera.

## <a name="credits-and-adjustments"></a>Kredity a úpravy

Všechny kredity a úpravy použité na vaši registraci zobrazíte v části **Reports** (Sestavy) na [portálu Azure Enterprise Portal](https://ea.azure.com).

Zobrazení kreditů:

1. Na [portálu Azure Enterprise Portal](https://ea.azure.com) vyberte část **Reports** (Sestavy).
1. Vyberte **Usage Summary** (Souhrn využití).
1. V pravém horním rohu změňte zobrazení z **M** na **C**.
1. V tabulce záloha na služby Azure rozšiřte pole pro úpravy.
1. Zobrazí se kredity, které se vztahují k vaší registraci, a krátké vysvětlení. Příklad: Kredit smlouvy SLA

## <a name="pay-your-overage-with-your-azure-prepayment"></a>Úhrada nadlimitního využití pomocí zálohy na Azure

Pokud chcete zálohu na Azure použít na nadlimitní využití, musíte splnit následující podmínky:

- Musíte mít neuhrazené poplatky za nadlimitní využití, které nejsou starší než jeden rok od data ukončení fakturované služby.
- Dostupná záloha na Azure pokrývá celou částku účtovaných poplatků, včetně všech předchozích neuhrazených faktur za Azure.
- Termín fakturace, který chcete dokončit, musí být plně uzavřený. Fakturace se plně uzavírá po pátém dni v měsíci.
- Fakturační období, které chcete posunout, musí být plně uzavřené.
- Sleva ACD (Azure Prepayment Discount) vychází ze skutečné nové zálohy po odečtení prostředků naplánovaných k předchozí spotřebě. Tento požadavek se týká jenom skutečně vzniklých poplatků za nadlimitní využití. Je platný jenom u služeb, které využívají zálohu na Azure, takže ho nemůžete použít na poplatky za Azure Marketplace. Poplatky za Azure Marketplace se fakturují zvlášť.

Pokud chcete vyrovnat nadlimitní využití, můžete vy nebo tým účtu otevřít žádost o podporu. Požaduje se e-mail se schválením od vašeho podnikového správce nebo příjemce faktury.

## <a name="move-charges-to-another-enrollment"></a>Přesun poplatků na jinou registraci

Data o využití se přesouvají jenom v případě, že se převod provádí zpětně ke staršímu datu. Existují dvě možnosti, jak přesunout data o využití z jedné registrace do druhé:

- Převody účtů z jedné registrace do druhé
- Převody registrací z jedné registrace do druhé

V obou případech musíte týmu podpory EA poslat [žádost o podporu](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) a požádat je o pomoc. 

## <a name="enterprise-agreement-usage-calculations"></a>Výpočty využití smlouvy Enterprise

Informace o základních cenách pro veřejnost, měrných jednotkách, nejčastějších dotazech a pokynech ke generování sestav využití pro jednotlivé služby najdete na stránkách [Služby Azure](https://azure.microsoft.com/services/) a [Ceny Azure](https://azure.microsoft.com/pricing/). Další informace o výpočtech Enterprise Agreement najdete v následujících částech.

### <a name="enterprise-agreement-units-of-measure"></a>Měrné jednotky smlouvy Enterprise

Měrné jednotky pro smlouvy Enterprise se často liší od jednotek, které znáte z jiných programů, jako je například program Microsoft Online Services Agreement (MOSA). To znamená, že u řady služeb se měrná jednotka agreguje, aby bylo možné poskytovat normalizované ceny. Měrná jednotka, která se na portálu Azure Enterprise Portal zobrazuje v zobrazení Usage Summary (Souhrn využití), je vždy měrnou jednotkou smlouvy Enterprise. Úplný seznam aktuálních měrných jednotek a převodech pro jednotlivé služby získáte odesláním [žádosti o podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Převod mezi sestavou podrobností o využití a stránkou souhrnu využití

V sestavě údajů o využití ke stažení můžete vidět nezpracované využití prostředků s až šesti desetinnými místy. Portál Azure Enterprise Portal ale údaje o využití zaokrouhluje na čtyři desetinná čísla v případě jednotek zálohy a ořízne na nula desetinných míst v případě jednotek nadlimitního využití. Nezpracované údaje o využití se před převodem na jednotky používané na portálu Azure Enterprise nejprve zaokrouhlí na čtyři číslice. Převedené jednotky Enterprise se pak znovu zaokrouhlí na čtyři číslice. Skutečně využité hodiny před převodem můžete zobrazit pouze v sestavě údajů o využití ke stažení, a nikoli na portálu Azure Enterprise Portal.

Příklad: V sestavě podrobností o využití je uvedeno 694,533404 skutečných hodin SQL Serveru. Tyto jednotky se pak převedou na 6,94533404 100 výpočetních hodin, které se pak zaokrouhlí na 6,9453 a zobrazí se na portálu Azure Enterprise Portal.

- K určení rozšířené fakturované částky se pak tyto jednotky vynásobí cenou jednotky zálohy a výsledek se ořízne na dvě desetinná místa. V případě japonského jenu (JPY) a korejského wonu (KRW) se rozšířená částka zaokrouhlí na nula desetinných míst.
- V případě nadlimitního využití se rozšířená fakturovaná částka určí tak, že se fakturovatelné jednotky zkrátí na šest číslic a pak se vynásobí cenou jednotky nadlimitního využití.
- Pro účely fakturace poskytovatelů spravovaných služeb (MSP) se veškeré využití přidružené k oddělení a označené jako MSP po převodu na měrnou jednotku smlouvy Enterprise zkrátí na nula desetinných míst. V důsledku toho by součet tohoto využití mohl být nižší než celkový součet veškerého využití vykázaného na portálu Azure Enterprise Portal. Záleží na tom, jestli je MSP v rámci zůstatku zálohy na Azure, nebo jestli jde o nadlimitní využití.

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

Za výchozí přenos dat mezi službami hostovanými ve stejném datacentru se neúčtují žádné poplatky. Například Microsoft 365 a Azure.

### <a name="azure-prepayment-and-unbilled-usage"></a>Záloha na Azure a nevyfakturované využití

Záloha na Azure je částka, která se za služby Azure platí předem. Záloha na Azure se spotřebovává používáním služeb. Záloha na Azure se používá k účtování vlastních služeb Azure. Některé poplatky se ale účtují samostatně a služby Azure Marketplace nespotřebovávají zálohu na Azure.

### <a name="charges-billed-separately"></a>Poplatky fakturované samostatně

Některé produkty a služby od třetích stran nespotřebovávají zálohu na Azure. Místo toho se tyto položky fakturují samostatně v rámci standardní faktury za nadlimitní využití ve fakturačním období.

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

## <a name="what-to-expect-after-change-of-channel-partner"></a>Co očekávat po změně distribučního partnera

Pokud ke změně distribučního partnera dojde uprostřed měsíce, zákazník obdrží fakturu na využití pod předchozím přidruženým partnerem a další fakturu za využití pod novým partnerem.

Tyto faktury se vystaví následující měsíc po skončení fakturačního období. V případě měsíční fakturace se faktura za září pro oba partnery vystaví v říjnu. V případě čtvrtletního nebo ročního fakturačního období může zákazník očekávat fakturu za využití v období pod předchozím přidruženým partnerem a zbývající využití se v závislosti na četnosti fakturace bude účtovat pod novým partnerem.

## <a name="next-steps"></a>Další kroky

- Informace, které vysvětlují fakturu a poplatky, najdete v tématu, ve kterém [je vysvětlená faktura ke smlouvě Azure Enterprise](../understand/review-enterprise-agreement-bill.md).
- Pokud chcete začít používat portál Azure Enterprise Portal, přečtěte si, [jak začít používat portál Azure EA](ea-portal-get-started.md).
