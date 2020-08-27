---
title: Zobrazení klíčových metrik pomocí řídicích panelů Cloudyn v Azure
description: Tento článek popisuje, jak můžete zobrazit klíčové metriky pomocí řídicích panelů v Cloudynu.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: vitavor
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 03d5e3959b85a2b0bd9ae30744051895ff026b77
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691220"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Zobrazení klíčových metrik nákladů pomocí řídicích panelů

Řídicí panely v Cloudynu zobrazují sestavy z vyšší perspektivy. Řídicí panely umožňují zobrazit klíčové metriky nákladů v jednom zobrazení. Poskytují také důležité body obchodního trendu, které vám pomůžou při důležitých obchodních rozhodnutích.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

Řídicí panely slouží také k vytváření zobrazení pro lidi s různými zodpovědnostmi ve vaší organizaci. Můžou sem patřit:

- Finanční kontrolor
- Vlastník aplikace nebo projektu
- Inženýr DevOps
- Vedení

Řídicí panely se skládají z widgetů, přičemž každý widget je v podstatě miniatura sestavy. Kliknutím na widget otevřete jeho sestavu. Když si sestavy přizpůsobíte, uloží se do oblasti My Reports (Mé sestavy) a přidají se na řídicí panel.

Verze řídicího panelu se pro uživatele Management (MSP), Enterprise a Premium Cloudynu liší. Rozdíly jsou definovány úrovněmi přístupu entit. Další informace o úrovních přístupu najdete v tématu [Úrovně přístupu entit](tutorial-user-access.md#entity-access-levels).

Dostupnost řídicího panelu závisí na typu účtu poskytovatele cloudové služby, který se při prohlížení řídicích panelů používá. Typ informací dostupných a shromažďovaných službou Cloudyn má vliv na sestavy v řídicích panelech. Pokud například nemáte účet AWS, neuvidíte řídicí panel Sledování S3. Pokud nepovolíte Azure Resource Manageru přístup ke Cloudynu, neuvidíte žádné pro Azure specifické informace ve widgetech řídicího panelu optimalizátoru.

Můžete použít kterýkoli z předem připravených řídicích panelů nebo můžete vytvořit vlastní řídicí panel s přizpůsobenými sestavami. Pokud nejste obeznámeni se sestavami Cloudynu, přečtěte si téma [Použití sestav Cloudynu](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Vytvoření vlastního řídicího panelu

Pokud chcete rychle začít pracovat s vlastním řídicím panelem, můžete duplikovat stávající panel a použít jeho vlastnosti. Pak ho můžete upravit tak, aby vyhovoval vašim potřebám. Na řídicím panelu, který chcete zkopírovat, klikněte na **Save As** (Uložit jako). Duplikovat můžete pouze přizpůsobené řídicí panely. Řídicí panely, které jsou součástí Cloudynu, duplikovat nejdou.

Vytvoření vlastního řídicího panelu:

1. Na domovské stránce klikněte na **Add New +** (Přidat nový +). Zobrazí se stránka My Dashboard (Můj řídicí panel).  
    ![Stránka My Dashboard (Můj řídicí panel), kde můžete přidat nové sestavy](./media/dashboards/my-dashboard.png)
2. Klikněte na **Add New Report** (Přidat novou sestavu). Zobrazí se okno Add Report (Přidat sestavu).
3. Vyberte sestavu, kterou chcete přidat do widgetu řídicího panelu. Widget se přidá na řídicí panel.
4. Opakujte předchozí kroky, dokud se řídicí panel nedokončí.
5. Pokud chcete změnit název řídicího panelu, klikněte na název řídicího panelu na jeho domovské stránce a zadejte nový název.

## <a name="modify-a-custom-dashboard"></a>Úprava vlastního řídicího panelu

Nemůžete upravovat řídicí panely, které jsou součástí Cloudynu, je to stejné jako s vytvořením vlastního řídicího panelu. Úprava sestavy vlastního řídicího panelu:

1. V řídicím panelu vyhledejte sestavu, kterou chcete upravit, a klikněte na **Edit** (Upravit). Sestava se zobrazí.
2. Proveďte v sestavě všechny požadované změny a klikněte na **Save** (Uložit). Sestava se aktualizuje a zobrazí vaše změny.

## <a name="share-a-custom-dashboard"></a>Sdílení vlastního řídicího panelu

Vlastní řídicí panel můžete sdílet s ostatními pomocí možností _Public_ (Veřejné) nebo _My Entity_ (Moje entita). Když použijete možnost Public (Veřejné), mohou váš řídicí panel zobrazit všichni uživatelé. Pokud použijete možnost My Entity (Moje entita), mohou řídicí panel zobrazit pouze uživatelé s přístupem k aktuální entitě. Postupy pro sdílení vlastního řídicího panelu s možností Public (Veřejné) a My Entity (Moje entita) jsou podobné.

Sdílení vlastního řídicího panelu s možností Public (Veřejné):

1. V řídicím panelu klikněte na **Dashboard Settings** (Nastavení řídicího panelu). Zobrazí se okno Dashboard Settings (Nastavení řídicího panelu).  
    ![Nastavení řídicího panelu pro vlastní řídicí panel](./media/dashboards/dashboard-options.png)
2. V okně Dashboard Settings (Nastavení řídicího panelu) klikněte na symbol šipky a potom klikněte na **Public** (Veřejné). Zobrazí se dialogové okno pro potvrzení veřejného řídicího panelu.
3. Klikněte na **Yes** (Ano). Řídicí panel je teď dostupný pro ostatní.

## <a name="delete-a-custom-dashboard-report"></a>Odstranění sestavy vlastního řídicího panelu

Komponentu vlastní sestavy můžete z řídicího panelu odstranit. Odstraněním sestavy z řídicího panelu se sestava neodstraní ze seznamu sestav. Odstraněním sestavy dojde pouze k jejímu odebrání z řídicího panelu.

Pokud chcete komponentu řídicího panelu odstranit, klikněte na této komponentě na **Delete** (Odstranit). Kliknutím na **Delete** (Odstranit) dojde k okamžitému odstranění komponenty řídicího panelu.

## <a name="share-a-dashboard-enterprise"></a>Sdílení řídicího panelu (Enterprise)

Vlastní řídicí panely můžete sdílet se všemi uživateli ve vaší organizaci nebo s uživateli aktuální entity. Sdílením řídicího panelu můžete ostatním uživatelům poskytnout rychlý pohled na klíčové ukazatele výkonu (KPI). Při sdílení řídicího panelu se tento řídicí panel automaticky replikuje pro všechny entity nebo zákazníky Cloudynu. Změny sdíleného řídicího panelu se aktualizují automaticky.

Sdílení řídicího panelu se všemi uživateli, včetně dílčích entit:

1. Na domovské stránce řídicího panelu klikněte na **Edit** (Upravit).
2. Klikněte na **Share** (Sdílet) a pak vyberte **Public** (Veřejné).
3. Zobrazí se okno pro potvrzení globálního veřejného řídicího panelu.
4. Kliknutím na **Yes** (Ano) nastavíte řídicí panel jako globální veřejný řídicí panel.

Sdílení řídicího panelu se všemi uživateli aktuální entity:

1. Na domovské stránce řídicího panelu klikněte na **Edit** (Upravit).
2. Klikněte na **Share** (Sdílet) a pak vyberte **My Entity** (Moje entita).
3. Kliknutím na **Yes** (Ano) nastavíte řídicí panel jako veřejný řídicí panel.

## <a name="duplicate-a-custom-dashboard"></a>Duplikování vlastního řídicího panelu

Při vytváření nového řídicího panelu můžete chtít použít podobné vlastnosti z existujícího řídicího panelu. Řídicí panel můžete duplikovat a vytvořit tak nový.

Duplikovat můžete pouze vlastní řídicí panely. Standardní řídicí panely duplikovat nejdou.

Duplikování (klonování) vlastního řídicího panelu:

1. Na řídicím panelu, který chcete duplikovat, klikněte na **Save As** (Uložit jako). Otevře se nový řídicí panel se stejným názvem a číslem.
2. Duplikovaný řídicí panel přejmenujte a upravte ho podle svých potřeb.

- nebo -

1. V části Dashboard Settings (Nastavení řídicího panelu) klikněte na **Save As** (Uložit jako) na řádku řídicího panelu, který chcete duplikovat.
2. Otevře se duplikovaný řídicí panel.
3. Řídicí panel přejmenujte a upravte ho podle svých potřeb.

## <a name="set-a-default-dashboard"></a>Nastavení výchozího řídicího panelu

Jako výchozí můžete nastavit libovolný řídicí panel. Když řídicí panel nastavíte jako výchozí, zobrazí se na seznamu karet řídicích panelů jako karta úplně vlevo. Výchozí řídicí panel se zobrazí při otevření portálu Cloudynu.

- Klikněte na kartu řídicího panelu, který chcete nastavit jako výchozí, a pak na pravé straně klikněte na **Default** (Výchozí).

- nebo -

1. Kliknutím na **Dashboard Settings** (Nastavení řídicího panelu) zobrazíte seznam dostupných řídicích panelů a vyberete řídicí panel, který chcete nastavit jako výchozí.  
    ![Možnosti řídicího panelu pro výchozí řídicí panel](./media/dashboards/dashboard-options.png)
2. V řádku řídicího panelu klikněte na **Default** (Výchozí). Zobrazí se okno pro potvrzení výchozího řídicího panelu.
3. Klikněte na **Ano**. Řídicí panel je nastaven jako výchozí.

## <a name="management-dashboard"></a>Řídicí panel správy
Řídicí panel správy (nebo řídicí panel MSP pro uživatele MSP) zvýrazňuje hlavní typy sestav.  
![Řídicí panel správy zobrazující různé sestavy](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Souhrn entit nákladů (pouze Enterprise)
Tento widget shrnuje spravované entity nákladů, včetně počtu entit a počtu účtů.
- Kliknutím na widget otevřete sestavu podrobností podniku.

### <a name="cost-over-time"></a>Náklady v průběhu času
Tento widget vám pomůže zjistit trendy nákladů. Zvýrazní náklady za poslední den na základě trendu za posledních 30 dní.
- Kliknutím na widget otevřete sestavu skutečných nákladů v průběhu času, kde můžete zobrazit a filtrovat další podrobnosti.

### <a name="asset-controller"></a>Kontroler prostředků
Tento widget zvýrazňuje počet spuštěných instancí z předchozího dne nad trendem využití za posledních 30 dní.
- Kliknutím na widget otevřete řídicí panel kontroleru prostředků.

### <a name="unused-ri-detector"></a>Detektor nevyužitých rezervovaných instancí
Tento widget zvýrazňuje počet nevyužitých rezervací Amazon EC2.
- Kliknutím na widget otevřete sestavu aktuálně nevyužitých rezervací, kde můžete zobrazit nepoužívané rezervace, které můžete upravit.

### <a name="cost-by-service"></a>Náklady podle služby
Tento widget zvýrazňuje amortizované náklady podle služby za posledních 30 dní. Najeďte na výsečový graf myší a zobrazte náklady na službu.
- Kliknutím na widget otevřete sestavu analýzy skutečných nákladů.

### <a name="potential-savings"></a>Potenciální úspory
Tento widget zobrazuje doporučení pro ceny typu instance pro Amazon EC2 a Amazon RDS.
- Kliknutím na widget otevřete sestavu analýzy úspor. Zobrazuje seznam nákladů podle typů instancí s potenciálními úsporami.

### <a name="compute-instances---daily-trend"></a>Výpočetní instance – denní trend
Tento widget zobrazuje aktivní instance podle typu za posledních 30 dní.
- Kliknutím na widget otevřete sestavu instancí v průběhu času, kde můžete zobrazit rozpis všech instancí spuštěných během posledních 30 dní.

### <a name="storage-by-department"></a>Úložiště podle oddělení
Tento widget zobrazuje služby úložiště, které používají oddělení. Najeďte myší na výsečový graf, abyste viděli spotřebu úložiště podle oddělení.
- Kliknutím na widget otevřete řídicí panel Sledování S3.

## <a name="cost-controller-dashboard"></a>Řídicí panel kontroleru nákladů
Řídicí panel kontroleru nákladů zobrazuje přednastavené důležité body přidělení nákladů.  
![Řídicí panel kontroleru nákladů zobrazující různé sestavy](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Náklady v průběhu času
Tento widget vám pomůže zjistit trendy nákladů. Zvýrazní náklady za poslední den na základě trendu za posledních 30 dní.
- Kliknutím na widget otevřete sestavu skutečných nákladů v průběhu času, kde můžete zobrazit a filtrovat další podrobnosti.

### <a name="monthly-cost-trends"></a>Měsíční trendy nákladů
Tento widget zvýrazňuje plánované amortizované útraty a skutečné útraty od začátku měsíce.
- Kliknutím na widget otevřete sestavu plánovaných nákladů pro aktuální měsíc, která poskytuje souhrn nákladů od začátku měsíce.

Tato sestava zobrazuje náklady od začátku měsíce, náklady na předchozí měsíc a plánované náklady pro aktuální měsíc. Plánované náklady pro aktuální měsíc se vypočítají přidáním aktuálních měsíčních nákladů a projekce. Projekce vychází z nákladů monitorovaných za posledních 30 dní.

### <a name="12-month-planner"></a>Plánovač 12 měsíců
Tento widget zvýrazňuje plánované náklady během dalších 12 měsíců a potenciální úspory.
- Kliknutím na widget otevřete sestavu ročních plánovaných nákladů.

### <a name="cost-by-service"></a>Náklady podle služby
Tento widget zvýrazňuje amortizované náklady podle služby za posledních 30 dní.
- Najeďte na výsečový graf myší a zobrazte náklady na službu.
- Kliknutím na widget otevřete sestavu analýzy skutečných nákladů.

### <a name="cost-by-account"></a>Náklady podle účtu
Tento widget zvýrazňuje amortizované náklady podle účtu za posledních 30 dní.
- Najeďte na výsečový graf myší a zobrazte náklady na účet.
- Kliknutím na widget otevřete sestavu analýzy skutečných nákladů.

### <a name="cost-trend-by-day"></a>Trend nákladů podle dne
Tento widget zvýrazňuje útratu za posledních 30 dní.
- Najeďte na pruhový graf myší a zobrazte náklady na den.
- Kliknutím na widget otevřete sestavu skutečných nákladů v průběhu času.

### <a name="cost-trend-by-month---last-6-months"></a>Trend nákladů podle měsíce – posledních 6 měsíců

Tento widget zvýrazňuje útratu za posledních šest měsíců.
- Najeďte na pruhový graf myší a zobrazte náklady na měsíc.
- Kliknutím na widget otevřete sestavu skutečných nákladů v průběhu času.

## <a name="asset-controller-dashboard"></a>Řídicí panel kontroleru prostředků

Tento řídicí panel zobrazuje počet spuštěných instancí, dostupné a používané disky, distribuci typů instancí a informace o úložišti.  
![Řídicí panel kontroleru prostředků zobrazující různé sestavy](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Výpočetní instance
Tento widget zobrazuje počet spuštěných instancí na základě trendu využití v posledních 30 dnech.
- Kliknutím na widget otevřete sestavu instancí v průběhu času.

### <a name="disks"></a>Disky
Tento widget zvýrazňuje celkový počet a objem disků, které se používají a jsou k dispozici.
- Kliknutím na widget otevřete sestavu aktivních disků.

### <a name="instance-type-distribution"></a>Distribuce typu instance
Tento widget zvýrazňuje typy instancí ve výsečovém grafu.
- Kliknutím na widget otevřete sestavu distribuce instancí, která poskytuje rozpis aktivních instancí podle vybrané agregace.

### <a name="compute-instances---daily-trend"></a>Výpočetní instance – denní trend
Tento widget zvýrazňuje výpočetní instance (spot, rezervované a na vyžádání) za den v posledních 30 dnech.
- Najeďte myší na graf a zobrazte počet výpočetních instancí podle typu za den.
- Kliknutím na widget otevřete sestavu instancí v průběhu času.

### <a name="all-buckets-s3"></a>Všechny kbelíky (S3)
Tento widget zvýrazní celkové úložiště S3 a počet uložených objektů.
- Kliknutím na widget otevřete řídicí panel Sledování S3. Řídicí panel vám pomůže najít, analyzovat a zobrazit aktuální využití a trendy úložiště.

### <a name="sql-db-instances-rds"></a>Instance SQL DB (Vzdálená plocha)
Tento widget zvýrazňuje počet spuštěných instancí Amazon RDS na základě trendu posledních 30 dní.
- Kliknutím na widget otevřete sestavu instancí vzdálené plochy v průběhu času.

## <a name="optimizer-dashboard"></a>Řídicí panel optimalizátoru
Tento řídicí panel zobrazuje doporučení pro zmenšení velikosti, nepoužívané prostředky a potenciální úspory.  
![Řídicí panel optimalizátoru zobrazující různé sestavy](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>Kalkulačka rezervovaných instancí
Tento widget zobrazuje počet doporučení pro nákup rezervovaných instancí a zvýrazňuje potenciální roční úspory.
- Kliknutím na widget otevřete kalkulačku rezervovaných instancí, kde můžete určit, kdy se mají použít cenové plány na vyžádání a rezervované cenové plány.

### <a name="sizing"></a>Velikosti
Tento widget zvýrazňuje doporučené velikosti a potenciální úspory, pokud dojde k implementaci.
- Kliknutím na widget otevřete sestavu doporučení efektivní velikosti nákladů EC2.

### <a name="unused-ri-detector"></a>Detektor nevyužitých rezervovaných instancí
Tento widget zvýrazňuje počet nevyužitých rezervací Amazon EC2.
- Kliknutím na widget otevřete sestavu aktuálně nevyužitých rezervací, kde můžete zobrazit nepoužívané rezervace, které můžete upravit.

###  <a name="available-disks"></a>Dostupné disky
Tento widget zvýrazňuje počet nepřipojených disků v nasazení.
- Kliknutím na widget otevřete sestavu nepřipojených disků.

### <a name="rds-ri-calculator"></a>Kalkulačka rezervovaných instancí vzdálené plochy
Tento widget zvýrazňuje počet doporučení rezervací pro instance Amazon RDS a potenciální úspory.
- Kliknutím na widget otevřete sestavu doporučení pro nákup rezervovaných instancí vzdálené plochy, kde uvidíte doporučení Cloudynu k používání rezervovaných instancí namísto instancí na vyžádání.

### <a name="rds-sizing"></a>Nastavení velikosti RDS
Tento widget znázorňuje počet doporučení určení velikosti a potenciální úspory.
- Kliknutím na widget otevřete sestavu doporučení nastavení velikosti RDS, která zobrazuje podrobná doporučení nastavení velikosti pro Amazon RDS.

Doporučení optimalizace vycházejí ze sledovaných údajů o využití a výkonu za posledních 30 dnů.

## <a name="s3-tracker-dashboard"></a>Řídicí panel Sledování S3
Řídicí panel Sledování S3 vám pomůže najít, analyzovat a zobrazit aktuální využití a trendy úložiště.  
![Řídicí panel Sledování S3 zobrazující různé sestavy](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Všechny kbelíky
Tento widget zvýrazňuje celkovou velikost všech vašich kbelíků (v GB) a celkový počet objektů v kbelících.
- Kliknutím na widget otevřete sestavu distribuce velikosti S3. Tato sestava vám pomůže analyzovat velikost S3 podle kbelíku, složky nejvyšší úrovně, třídy úložiště a stavu správy verzí.

### <a name="bucket-properties"></a>Vlastnosti kbelíku
Tento widget zvýrazňuje celkový počet kbelíků úložiště.
- Kliknutím na widget zobrazíte sestavu vlastností kbelíku S3.

### <a name="scan-status"></a>Stav kontroly
Tento widget zvýrazňuje, kdy se provedla poslední kontrola S3 a kdy se spustí další.
- Kliknutím na widget otevřete sestavu stavu kontroly S3.

### <a name="storage-by-bucket"></a>Úložiště podle kbelíku
Tento widget zvýrazňuje procentuální hodnotu, kterou každá třída úložiště kbelíku používá.
- Kliknutím na widget otevřete sestavu distribuce velikosti S3. Tato sestava vám pomůže analyzovat velikost S3 podle kbelíku, složky nejvyšší úrovně, třídy úložiště a stavu správy verzí.

### <a name="number-of-objects-by-bucket"></a>Počet objektů podle kbelíku
Tento widget zvýrazňuje počet objektů na kbelík jako skutečné číslo nebo procentuální hodnotu. Pokud chcete zobrazit celkový počet objektů, najeďte myší na kbelík.
- Kliknutím na widget otevřete sestavu distribuce velikosti S3 (založenou na kontrole).

## <a name="cloud-comparison-dashboard"></a>Řídicí panel porovnání cloudů
Řídicí panel porovnání cloudů vám pomůže porovnat náklady od různých poskytovatelů cloudu na základě cen, typu procesoru a velikosti paměti RAM.  
![Řídicí panel porovnání cloudů zobrazující různé sestavy](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>Náklady EC2 v Azure podle typu instance
Tento widget zvýrazňuje posledních 30 dnů využití v sazbách na vyžádání. Porovnává náklady s aktuálními náklady na Amazon EC2 s potenciálními náklady v Azure.
- Najeďte myší na pruhy a porovnejte náklady na typ instance.
- Kliknutím na widget otevřete sestavu přenosu nasazení – analýzy nákladů.

### <a name="ec2-cost-in-azure"></a>Náklady EC2 v Azure
Tento widget zobrazuje aktuální náklady na Amazon EC2 a porovnává je s Azure. Porovnání vychází z posledních 30 dnů využití v sazbách na vyžádání.
- Kliknutím na widget otevřete sestavu přenosu nasazení – analýzy nákladů.

### <a name="ec2azure-instance-type-mapping"></a>Mapování typu instance EC2/Azure
Tento widget zvýrazňuje nejlepší mapování elastických výpočetních jednotek mezi Amazon EC2 a Azure.
- Kliknutím na widget otevřete sestavu mapování typů instancí.

## <a name="next-steps"></a>Další kroky
- Další informace o sestavách najdete v článku [Použití sestav Cloudynu](use-reports.md).
