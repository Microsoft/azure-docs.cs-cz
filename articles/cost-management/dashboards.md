---
title: Zobrazení klíčových metrik v řídicích panelech Azure Cost Management | Dokumentace Microsoftu
description: Tento článek popisuje, jak zobrazit klíčových metrik pomocí řídicích panelů ve službě Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/12/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 4f51598dd2b026f1c3a4b0d763b1547a44eedc7b
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642750"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Klíč zobrazení nákladů metrik pomocí řídicích panelů

Řídicí panely ve službě Cloudyn poskytují souhrnný přehled sestav. Řídicí panely umožňují zobrazit náklady na klíčové metriky na jednom místě. Obsahují taky, že obchodní trend zvýrazní můžete dělat důležitá obchodní rozhodnutí.

Řídicí panely se také používají k vytvoření zobrazení pro osoby s rozdílné povinnosti ve vaší organizaci, která by mohla obsahovat:

- Finanční kontrolor
- Vlastník aplikace nebo projekt
- Inženýr DevOps
- Členové vedení

Řídicí panely jsou tvořené widgetů a u každé pomůcky máte je v podstatě miniaturu sestavy. Kliknutím na widget a otevřete jeho sestavu. Při přizpůsobování zprávy uložit na stránku Mé sestavy a přidávají na řídicí panel.

Řídicí panel verze se liší pro správu (MSP), Enterprise a Premium Cloudyn uživatele. Rozdíly jsou určeny entita úrovně přístupu. Další informace o úrovně přístupu najdete v tématu [úrovně přístupu Entity](tutorial-user-access.md#entity-access-levels).

Řídicí panel dostupnost závisí na typu cloudové služby poskytovatele účtu, který se používá při zobrazení řídicích panelů. Typ informací k dispozici a shromážděných od Cloudyn má vliv na sestavy v řídicích panelech. Například pokud nemáte účet AWS pak nezobrazí řídicí panel sledování S3. Podobně pokud nepovolíte, Azure Resource Manageru přístup do Cloudyn pak neuvidíte žádné informace týkající se Azure v optimalizátoru pomůcky řídicího panelu.

Můžete použít některý z předem připravené řídicí panely nebo můžete vytvořit vlastní řídicí panel s přizpůsobené sestavy. Pokud nejste obeznámeni s sestavách Cloudyn, přečtěte si téma [sestavy pomocí Cost Management](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Vytvoření vlastního řídicího panelu

Pokud chcete rychle začít s vlastní řídicí panel, můžete duplikovat některý z existujících použít jeho vlastnosti. Potom můžete upravit tak, aby odpovídala vašim potřebám. Na řídicím panelu, které chcete kopírovat, klikněte na tlačítko **uložit jako**. Můžete pouze duplicitní přizpůsobené řídicí panely, nemůžete duplikovat řídicí panely, které jsou součástí Cloudyn.

Pokud chcete vytvořit vlastní řídicí panel:

1. Na domovské stránce, klikněte na tlačítko **přidat nový +**. Zobrazí se stránka Můj řídicí panel.  
    ![Můj řídicí panel](./media/dashboards/my-dashboard.png)
2. Klikněte na tlačítko **přidat novou sestavu**. Zobrazí se okno Přidat sestavu.
3. Vyberte sestavu, kterou chcete přidat pomůcky řídicího panelu. Ve widgetu se přidá na řídicí panel.
4. Zopakujte předchozí kroky, dokud se nedokončí řídicího panelu.
5. Chcete-li změnit název řídicího panelu, klikněte na název řídicího panelu na domovské stránce řídicího panelu a zadejte nový název.

## <a name="modify-a-custom-dashboard"></a>Upravit vlastní řídicí panel

Jako je vytváření vlastních řídicích panelů, nelze upravit řídicí panely zahrnuté v Cloudyn. Chcete-li upravit sestavu vlastní řídicí panel:

1. Na řídicím panelu vyhledejte sestavu, kterou chcete upravit, a klikněte na tlačítko **upravit**. Zobrazí se sestava.
2. Žádné změny, které chcete sestavu a klikněte na tlačítko **Uložit**. Sestava se aktualizuje a zobrazí změny.

## <a name="share-a-custom-dashboard"></a>Sdílení vlastního řídicího panelu

Vlastní řídicí panel můžete sdílet s ostatními _veřejné_ nebo _Moje Entity_. Když sdílíte veřejnosti, všichni uživatelé mohou zobrazit řídicí panel. Pouze uživatelé s přístupem k aktuální entitě můžete zobrazit řídicí panel, když sdílíte Moje entity. Postup pro sdílení vlastního řídicího panelu se veřejné a My Entity jsou podobné.

Chcete-li sdílet vlastní řídicí panel na veřejnou:

1. Na řídicím panelu, klikněte na tlačítko **nastavení řídicího panelu**. Zobrazí se okno Nastavení řídicího panelu.  
    ![řídicí panel možností](./media/dashboards/dashboard-options.png)
2. V dialogovém okně Nastavení řídicího panelu klikněte na symbol šipku a potom klikněte na tlačítko **veřejné**. Zobrazí se dialogové okno potvrzení veřejné řídicího panelu.
3. Klikněte na tlačítko **Ano**. Řídicí panel je teď dostupná ostatním uživatelům.

## <a name="delete-a-custom-dashboard-report"></a>Odstranit sestavu vlastní řídicí panel

Z řídicího panelu můžete odstranit komponenty vlastní sestavy. Odstranění sestavy z řídicího panelu neodstraní sestavu ze seznamu sestav. Místo toho odstranění sestavy odebere pouze na řídicím panelu.

Chcete-li odstranit komponenty řídicí panel, na řídicí panel komponenty, klikněte na tlačítko **odstranit**. Kliknutím na **odstranit** okamžitě odstraní součást řídicího panelu.

## <a name="share-a-dashboard-enterprise"></a>Sdílení řídicího panelu (Enterprise)

Můžete sdílet vlastní řídicí panely pro všechny uživatele ve vaší organizaci nebo s uživateli aktuální entitu. Sdílení řídicího panelu můžete poskytnout ostatním rychlé široký přehled o klíčových ukazatelů výkonu. Když sdílíte řídicí panel, automaticky replikuje řídicího panelu všechny Cloudyn entity/zákazníkům. Změny na sdílený řídicí panel se automaticky aktualizují.

Sdílet řídicí panel se všemi uživateli, včetně subentities:

1. Na domovské stránce řídicího panelu, klikněte na tlačítko **upravit**.
2. Klikněte na tlačítko **sdílenou složku** a pak vyberte **veřejné**.
3. Zobrazí se okno potvrzení globální veřejné řídicího panelu.
4. Klikněte na tlačítko **Ano** nastavit řídicí panel jako globální veřejné řídicí panel.

Sdílet řídicí panel se všemi uživateli aktuální entita:

1. Na domovské stránce řídicího panelu, klikněte na tlačítko **upravit**.
2. Klikněte na tlačítko **sdílenou složku** a pak vyberte **Moje Entity**.
3. Klikněte na tlačítko **Ano** nastavit řídicí panel jako veřejné řídicí panel.

## <a name="duplicate-a-custom-dashboard"></a>Duplicitní vlastní řídicí panel

Když vytvoříte nový řídicí panel, můžete chtít použít podobná vlastnosti z existující řídicí panel. Můžete duplikovat řídicí panel vytvořit nový certifikát.

Můžete pouze duplicitní vlastní řídicí panely. Standardní řídicí panely nejde duplikovat.

Duplikovat řídicí panel (klonovat) vlastní:

1. Na řídicím panelu, který chcete duplikovat, klikněte na tlačítko **uložit jako**. Nový řídicí panel se otevře s názvem a číslem.
2. Přejmenujte duplicitní řídicí panel a upravit, jak potřebujete.

- nebo -

1. V nastavení řídicího panelu, klikněte na tlačítko **uložit jako** na řádek, který chcete duplikovat řídicí panel.
2. Otevře se duplicitní řídicí panel.
3. Přejmenovat řídicí panel a můžete je změnit.

## <a name="set-a-default-dashboard"></a>Nastavit výchozí řídicí panel

Kterýkoli řídicí panel můžete nastavit jako výchozí. Nastavení pro výchozí umožňuje zobrazit jako seznam karet řídicího panelu na kartě nejvíce vlevo. Výchozí řídicí panel zobrazí, když otevřete portál Cloudyn.

- Klikněte na kartu řídicí panel chcete nastavit jako výchozí a potom klikněte na **výchozí** na pravé straně.

- nebo -

1. Klikněte na tlačítko **nastavení řídicího panelu** zobrazte seznam dostupných řídicích panelů a vyberte řídicí panel, který chcete nastavit jako výchozí.  
    ![řídicí panel možností](./media/dashboards/dashboard-options.png)
2. Klikněte na tlačítko **výchozí** v řádku řídicího panelu. Zobrazí se okno potvrzení výchozí řídicí panel.
3. Klikněte na **Ano**. Řídicí panel je nastavena na výchozí.

## <a name="management-dashboard"></a>Řídicí panel správy
Rozhraní pro správu (nebo řídicí panel MSP pro uživatele MSP) řídicí panel obsahuje stručný přehled typů hlavní sestav.  
![Řídicí panel správy](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Souhrn entit nákladů (pouze v Enterprise)
Tato pomůcka shrnuje náklady na spravované entity, včetně počet entit a počtu účtů.
- Kliknutím na widget a otevřete tak sestavu podrobnosti organizace.

### <a name="cost-over-time"></a>Náklady za časové období
Tohoto widgetu můžete sledovat trendy nákladů. Zvýrazní náklady za poslední den, založené na trend v posledních 30 dnech.
- Kliknutím na widget a otevřete tak sestavu Actual Cost Over Time, chcete-li zobrazit a vyfiltrovat další podrobnosti.

### <a name="asset-controller"></a>Kontroler prostředků
Tato pomůcka označuje počet spuštěných instancí z předchozího dne, nad trend využití za posledních 30 dní.
- Kliknutím na widget otevřete řídicí panel kontroler prostředků.

### <a name="unused-ri-detector"></a>Detektor nevyužitých RI
Tato pomůcka zvýrazní počet Amazon EC2 nevyužitých rezervací.
- Kliknutím na widget a otevřete tak sestavu aktuálně nevyužité rezervace, kde můžete zobrazit nevyužitých rezervací můžete upravit.

### <a name="cost-by-service"></a>Náklady podle služeb
Tato pomůcka zvýrazní amortizované náklady službou za posledních 30 dní. Najeďte myší výseč kruhového grafu můžete zobrazit náklady na službu.
- Kliknutím na widget a otevřete tak sestavu Analýza skutečných nákladů.

### <a name="potential-savings"></a>Potenciální úspory
Tato pomůcka ukazuje typ instance ceny doporučení pro Amazon EC2 a Amazon vzdálené plochy
- Kliknutím na widget otevřete sestavu analýzy úspory. Vypíše instance typy potenciální úspory nákladů.

### <a name="compute-instances---daily-trend"></a>Výpočetní instance – denní Trend
Tato pomůcka zobrazí aktivní instance podle typu, za posledních 30 dní.
- Kliknutím na widget a otevřete tak sestavu instance v čase, kde můžete zobrazit rozpis všech instancí během posledních 30 dní.

### <a name="storage-by-department"></a>Úložiště podle oddělení
Tato pomůcka zobrazí služby úložiště používá po odděleních. Najeďte myší výsečový graf zobrazíte spotřebu úložiště podle oddělení.
- Kliknutím na widget otevřete řídicí panel sledování S3.

## <a name="cost-controller-dashboard"></a>Řídicí panel kontroler nákladů
Na řídicím panelu kontroler nákladů zobrazuje zvýraznění přidělení přednastaveným náklady.  
![Řídicí panel kontroler nákladů](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Náklady za časové období
Tato pomůcka pomáhá odhalit trendy nákladů. Zvýrazní náklady za poslední den, založené na trend v posledních 30 dnech.
- Kliknutím na widget a otevřete tak sestavu Actual Cost Over Time, chcete-li zobrazit a vyfiltrovat další podrobnosti.

### <a name="monthly-cost-trends"></a>Měsíční náklady trendů
Tato pomůcka zvýrazní předpokládané útraty amortizované a vaší skutečné náklady od začátku měsíce.
- Kliknutím na widget a otevřete tak sestavu plánovaných nákladů pro aktuální měsíc, které nabízí souhrnné náklady za měsíc k datu.

Tato sestava ukazuje náklady od začátku měsíce, náklady na předchozí měsíc a aktuální měsíc plánovaných nákladů. Aktuální měsíc předpokládané náklady se počítají tak, že přidáte aktuální měsíční náklady a projekce. Projekce je založen na náklady na monitorované za posledních 30 dní.

### <a name="12-month-planner"></a>Plánovač 12 měsíců
Tato pomůcka zvýrazní předpokládaných nákladů za příštích 12 měsíců a potenciální úspory.
- Kliknutím widget, který se otevře sestava ročních plánovaných nákladů.

### <a name="cost-by-service"></a>Náklady podle služeb
Tato pomůcka zvýrazní amortizované náklady službou za posledních 30 dní.
- Najeďte myší výseč kruhového grafu můžete zobrazit náklady na službu.
- Kliknutím na widget a otevřete tak sestavu Analýza skutečných nákladů.

### <a name="cost-by-account"></a>Náklady podle účtu
Tato pomůcka zvýrazní amortizované náklady na účet za posledních 30 dní.
- Najeďte myší výseč kruhového grafu můžete zobrazit náklady na účet.
- Kliknutím na widget a otevřete tak sestavu Analýza skutečných nákladů.

### <a name="cost-trend-by-day"></a>Trend nákladů za den
Tato pomůcka vybraná vystoupení výdajů za posledních 30 dní.
- Najeďte myší na pruhový graf, který chcete zobrazit náklady za den.
- Kliknutím na widget a otevřete tak sestavu Actual Cost Over Time.

### <a name="cost-trend-by-month---last-6-months"></a>Trend nákladů podle kategorie Month - posledních 6 měsíců

Tato pomůcka vybraná vystoupení výdajů za posledních šest měsíců.
- Najeďte myší na pruhový graf, který chcete zobrazit náklady za měsíc.
- Kliknutím na widget a otevřete tak sestavu Actual Cost Over Time.

## <a name="asset-controller-dashboard"></a>Řídicí panel kontroler prostředků

Tento řídicí panel zobrazuje počet spuštěných instancí, k dispozici a používané disky, distribuce typů instancí a informace o úložiště.  
![Řídicí panel kontroler prostředků](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Výpočetní instance
Tato pomůcka zobrazuje počet spuštěných instancí podle trend využití za posledních 30 dní.
- Kliknutím na widget a otevřete tak sestavu instance v čase.

### <a name="disks"></a>Disky
Tato pomůcka klade důraz na celkový počet a objemu disky, které jsou používané a k dispozici.
- Kliknutím na widget a otevřete tak sestavu aktivní disky.

### <a name="instance-type-distribution"></a>Distribuce typu instance
Tato pomůcka zvýrazňuje typy instancí ve výsečovém grafu.
- Kliknutím na widget a otevřete tak sestavu distribuce Instance obsahuje rozpis systémů aktivní instance podle vybrané agregace.

### <a name="compute-instances---daily-trend"></a>Výpočetní instance – denní Trend
Tato pomůcka zvýrazní výpočetních instancí (přímé vyhrazené a na vyžádání) za den za posledních 30 dní.
- Najeďte myší grafu zobrazí počet výpočetních instancí podle typu za den.
- Kliknutím na widget a otevřete tak sestavu instance v čase.

### <a name="all-buckets-s3"></a>Všechny Kbelíky (S3)
Tato pomůcka klade důraz na celkový úložiště S3 a počet objektů, které jsou uložené.
- Kliknutím na widget otevřete řídicí panel sledování S3. Řídicí panel pomáhá najít, analyzovat a zobrazit aktuální využití úložiště a trendy.

### <a name="sql-db-instances-rds"></a>Instance SQL DB (RDS)
Tato pomůcka označuje počet spuštěných instancí Amazon RDS podle trend v posledních 30 dnech.
- Kliknutím na widget a otevřete tak sestavu RDS Instance v čase.

## <a name="optimizer-dashboard"></a>Optimalizace řídicího panelu
Tento řídicí panel zobrazuje downsizing doporučení, nevyužité prostředky a potenciální úspory.  
![Optimalizace řídicího panelu](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>Rezervované instance kalkulačky
Zobrazí počet doporučení k nákupu rezervovaných Instancí a tohoto widgetu nejlepší potenciální roční úspory.
- Kliknutím na widget otevřete rezervované Instance kalkulačky, kde můžete určit, kdy se má použít na vyžádání vs. vyhrazené cenové plány.

### <a name="sizing"></a>Velikost
Tato pomůcka klade důraz na doporučené velikosti a potenciální úspory, pokud implementovaná.
- Kliknutím na widget a otevřete tak sestavu EC2 Cost Effective Sizing Recommendations.

### <a name="unused-ri-detector"></a>Detektor nevyužitých RI
Tato pomůcka zvýrazní počet Amazon EC2 nevyužitých rezervací.
- Kliknutím na widget a otevřete tak sestavu aktuálně nevyužité rezervace, kde můžete zobrazit nevyužitých rezervací, které můžete upravit.

###  <a name="available-disks"></a>Dostupné disky
Tato pomůcka označuje počet nepřipojené disky ve vašem nasazení.
- Kliknutím na widget a otevřete tak sestavu nepřipojené disky.

### <a name="rds-ri-calculator"></a>Rezervované instance kalkulačky vzdálené plochy
Tato pomůcka označuje počet rezervace doporučení pro vaše instance Amazon vzdálené plochy a potenciální úspory.
- Kliknutím na widget a otevřete tak sestavu doporučení k nákupu rezervovaných Instancí vzdálené plochy, ve kterém uvidíte Cloudyn doporučení použít rezervované instance místo instance na vyžádání.

### <a name="rds-sizing"></a>Nastavení velikosti vzdálené plochy
Tato pomůcka zobrazuje počet doporučení velikosti a potenciální úspory.
- Kliknutím na widget a otevřete tak sestavu doporučení velikosti vzdálené plochy, které zobrazí podrobné Amazon vzdálené plochy pro změnu velikosti doporučení.

Optimalizace doporučení jsou založená na data o využití a výkonu monitorované během posledního měsíce.

## <a name="s3-tracker-dashboard"></a>Řídicí panel sledování S3
Řídicí panel sledování S3 pomáhá najít, analyzovat a zobrazit aktuální využití úložiště a trendy.  
![Řídicí panel sledování S3](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Všechny kontejnery
Tato pomůcka zvýrazní celková velikost všech kbelíků, v GB a celkový počet objektů v vaše kontejnery.
- Kliknutím na widget a otevřete tak sestavu distribuce S3 velikost. Sestava pomáhá analyzovat vaše S3 velikost kbelíku, složku nejvyšší úrovně, třída úložiště a stav správy verzí.

### <a name="bucket-properties"></a>Vlastnosti kontejneru
Tato pomůcka ukazuje celkový počet kbelíků úložiště.
- Kliknutím na widget zobrazíte sestavu vlastnosti sady S3.

### <a name="scan-status"></a>Stav kontroly
Tato pomůcka zvýrazní, když bylo provedeno poslední kontrola S3 a když se spustí další příkaz.
- Kliknutím na widget a otevřete tak sestavu Stav prohledávání S3.

### <a name="storage-by-bucket"></a>Úložiště podle kontejneru
Tato pomůcka zvýrazní procentuální hodnotu, která používá každá třída kontejneru úložiště.
- Kliknutím na widget a otevřete tak sestavu distribuce S3 velikost. Sestava pomáhá analyzovat vaše S3 velikost kbelíku, složku nejvyšší úrovně, třída úložiště a stav správy verzí.

### <a name="number-of-objects-by-bucket"></a>Počet objektů v kontejneru
Tato pomůcka označuje počet objektů na kbelík v skutečný počet a procento. Najeďte myší do kbelíku zobrazit celkový počet objektů.
- Kliknutím na widget a otevřete tak sestavu velikost S3 distribuce (na základě kontroly).

## <a name="cloud-comparison-dashboard"></a>Řídicí panel cloud porovnání
Porovnání Cloud řídicí panel vám pomůže porovnat náklady ze různí poskytovatelé cloudových na základě ceny, typu procesoru a velikost paměti RAM.  
![Řídicí panel cloud porovnání](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>EC2 Náklady v Azure, typu Instance
Tato pomůcka zvýrazní za posledních 30 dní používání kurzy na vyžádání. Porovná nákladů s aktuální vs Amazon EC2 náklady potenciální finanční v Azure.
- Najeďte myší pruhy pro porovnání nákladů na typ instance.
- Kliknutím na widget otevřete přenos vašeho nasazení – sestava analýzy nákladů.

### <a name="ec2-cost-in-azure"></a>EC2 Náklady v Azure
Tato pomůcka ukazuje náklady na aktuální Amazon EC2 a porovnává je s Azure. Porovnání je založená na za posledních 30 dní používání kurzy na vyžádání.
- Kliknutím na widget otevřete přenos vašeho nasazení – sestava analýzy nákladů.

### <a name="ec2azure-instance-type-mapping"></a>Mapování typu Instance EC2 a Azure
Tato pomůcka zvýrazní nejlepší mapování jednotky elastický výpočetní výkon mezi Amazon EC2 a Azure.
- Kliknutím na widget a otevřete tak sestavu mapování typu instance.

## <a name="next-steps"></a>Další postup
- Čtení [sestavy pomocí Cost Management](use-reports.md) článku se dozvíte informace o sestavách.
