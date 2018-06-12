---
title: Zobrazit klíčové metriky v Azure náklady na správu řídicí panely | Microsoft Docs
description: Tento článek popisuje, jak zobrazit klíčové metriky pomocí řídicích panelů v Azure náklady na správu.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: b1dc2e2eca900ca0ae72329c3c373b2d24f1b2e0
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303359"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Klíč zobrazení náklady metriky pomocí řídicích panelů

Řídicí panely v Cloudyn poskytují souhrnné zobrazení sestavy. Řídicí panely umožňují zobrazit náklady na klíčové metriky v rámci jednoho zobrazení. Obsahují taky, že obchodní trend označuje můžete důležité podnikatelská rozhodnutí.

Řídicí panely se také používají k vytvoření zobrazení pro osoby s jinou odpovědnosti ve vaší organizaci, která by mohla obsahovat:

- Finanční řadiče
- Vlastníka aplikace nebo produktu project
- Pracovník DevOps
- Členové vedení

Řídicí panely jsou tvořeny pomůcky a každá pomůcka je v podstatě miniaturu sestavy. Klikněte na tlačítko widget otevřete její sestavy. Při přizpůsobování sestavy můžete uložit do Moje sestavy a jste přidali do řídicího panelu.

Řídicí panel verze se liší pro správu (MSP), Enterprise a Premium Cloudyn uživatele. Rozdíly jsou určeny úrovně přístupu entity. Další informace o úrovních přístupu najdete v tématu [úrovně přístupu Entity](tutorial-user-access.md#entity-access-levels).

Řídicí panel dostupnosti, závisí na typu účtu služby poskytovatele cloudu, který se používá při zobrazení řídicích panelů. Typ informací, dostupnost a shromažďují Cloudyn ovlivňuje sestavy v řídicí panely. Například pokud nemáte účet AWS pak neuvidíte sledovací modul S3 řídicího panelu. Podobně pokud nepovolíte Azure Resource Manager přístup k Cloudyn pak neuvidíte žádné informace specifické pro Azure v Optimalizátor pomůcky řídicího panelu.

Můžete použít libovolnou předem vytvořeného řídicí panely, nebo můžete vytvořit vlastní řídicí panel s přizpůsobených sestav. Pokud jste obeznámeni s Cloudyn sestavy, najdete v části [náklady na správu pomocí sestavy](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Vytvořit vlastní řídicí panel

Rychle začít pracovat s vlastní řídicí panel, můžete duplikovat stávající používat jeho vlastnosti. Potom můžete ho upravit podle svých potřeb. Na řídicím panelu, kterou chcete zkopírovat, klikněte na tlačítko **uložit jako**. Pouze můžete duplikovat přizpůsobené řídicí panely – nelze duplicitní řídicí panely, které jsou součástí Cloudyn.

Chcete-li vytvořit vlastní řídicí panel:

1. Na domovské stránce, klikněte na tlačítko **přidat nové +**. Zobrazí se stránka vlastní řídicí panel.  
    ![Můj řídicí panel](./media/dashboards/my-dashboard.png)
2. Klikněte na tlačítko **přidat novou sestavu**. Zobrazí se do sestavy přidat pole.
3. Vyberte sestavu, kterou chcete přidat do pomůcky řídicího panelu. Widgetu se přidá na řídicí panel.
4. Opakujte předchozí kroky, dokud se nedokončí řídicího panelu.
5. Chcete-li změnit název řídicího panelu, klikněte na název řídicího panelu na domovské stránce řídicího panelu a zadejte nový název.

## <a name="modify-a-custom-dashboard"></a>Upravit vlastní řídicí panel

Jako je vytváření vlastní řídicí panel, nelze změnit součástí Cloudyn řídicí panely. Chcete-li upravit sestavu vlastní řídicí panel:

1. Na řídicím panelu najít sestavu, kterou chcete upravit a klikněte na tlačítko **upravit**. Sestava se zobrazí.
2. Žádné změny, které chcete sestavu a klikněte na **Uložit**. Spuštění sestavy se aktualizuje a zobrazí vaše změny.

## <a name="share-a-custom-dashboard"></a>Sdílet vlastní řídicí panel

Vlastní řídicí panel můžete sdílet s ostatními k _veřejné_ nebo _Moje Entity_. Při sdílení na hodnotu veřejná můžete zobrazit všechny uživatele řídicího panelu. Pouze uživatelé s přístupem k aktuální entity můžete zobrazit řídicí panel, když sdílíte Moje entitě. Kroky pro sdílení vlastní řídicí panel s veřejné a My Entity jsou podobné.

Chcete-li sdílet vlastní řídicí panel pro veřejné:

1. Na řídicím panelu klikněte na tlačítko **nastavení řídicího panelu**. Zobrazí se pole nastavení řídicího panelu.  
    ![řídicí panel Možnosti](./media/dashboards/dashboard-options.png)
2. V dialogovém okně Nastavení řídicího panelu klikněte na šipku symbol a pak klikněte na **veřejné**. Zobrazí se dialogové okno potvrzení veřejné řídicího panelu.
3. Klikněte na tlačítko **Ano**. Řídicí panel je nyní k dispozici jiným uživatelům.

## <a name="delete-a-custom-dashboard-report"></a>Odstranit sestavu vlastní řídicí panel

Součást vlastní sestavu můžete odstranit z řídicího panelu. Odstranění sestavy z řídicího panelu neodstraní sestavu ze seznamu sestav. Místo toho odstraněním sestavu odeberete pouze na řídicím panelu.

Chcete-li odstranit komponentu řídicího panelu na komponentu řídicí panel, klikněte na tlačítko **odstranit**. Kliknutím na tlačítko **odstranit** okamžitě odstraní komponentu řídicího panelu.

## <a name="share-a-dashboard-enterprise"></a>Sdílet řídicí panel (Enterprise)

Můžete sdílet vlastní řídicí panely pro všechny uživatele ve vaší organizaci nebo s uživateli aktuální entity. Sdílení řídicího panelu můžete poskytnout ostatním rychlý podrobný přehled o vašich klíčových ukazatelů výkonu. Když sdílíte řídicí panel, automaticky se replikuje řídicího panelu všechny Cloudyn entity nebo zákazníkům. Změny sdíleného řídicího panelu jsou automaticky aktualizovány.

Řídicí panel sdílet se všemi uživateli, včetně podentit:

1. Na domovské stránce řídicího panelu klikněte na tlačítko **upravit**.
2. Klikněte na tlačítko **sdílené složky** a pak vyberte **veřejné**.
3. Zobrazí se pole potvrzení globální veřejné řídicího panelu.
4. Klikněte na tlačítko **Ano** nastavit řídicí panel jako globální veřejné řídicí panel.

Řídicí panel sdílet se všemi uživateli aktuální entity:

1. Z domovské stránky řídicího panelu, klikněte na tlačítko **upravit**.
2. Klikněte na tlačítko **sdílené složky** a pak vyberte **Moje Entity**.
3. Klikněte na tlačítko **Ano** nastavit řídicí panel jako veřejné řídicí panel.

## <a name="duplicate-a-custom-dashboard"></a>Duplicitní vlastní řídicí panel

Když vytvoříte nový řídicí panel, můžete chtít použít podobné vlastnosti z existující řídicí panel. Můžete duplikovat k řídicímu vytvořte novou.

Pouze můžete duplikovat vlastní řídicí panely. Nelze duplicitní standardní řídicí panely.

Duplicitní (klonování) vlastní řídicí panel:

1. Na řídicím panelu, kterou chcete duplikovat, klikněte na tlačítko **uložit jako**. Otevře se nové řídicí panel se stejným názvem a číslem.
2. Přejmenujte duplicitní řídicí panel a upravit ho podle potřeby.

- nebo -

1. V nastavení řídicího panelu, klikněte na **uložit jako** na řádku řídicího panelu, který chcete duplikovat.
2. Otevře se duplicitní řídicí panel.
3. Přejmenujte řídicího panelu a upravit ho podle potřeby.

## <a name="set-a-default-dashboard"></a>Nastavit výchozí řídicí panely

Kterýkoli řídicí panel můžete nastavit jako výchozí. Nastavení na výchozí umožňuje zobrazit jako kartě nejvíce vlevo v seznamu karta řídicího panelu. Výchozí řídicí panel zobrazuje při otevření portálu Cloudyn.

- Klikněte na kartu řídicí panel, kterou chcete nastavit jako výchozí, a pak klikněte na **výchozí** na pravé straně.

- nebo -

1. Klikněte na tlačítko **nastavení řídicího panelu** najdete v seznamu dostupných řídicích panelů a vyberte řídicí panel, který chcete nastavit jako výchozí.  
    ![řídicí panel Možnosti](./media/dashboards/dashboard-options.png)
2. Klikněte na tlačítko **výchozí** v řádku řídicího panelu. Zobrazí se pole potvrzení výchozí řídicí panel.
3. Klikněte na **Ano**. Řídicí panel je nastaven výchozí.

## <a name="management-dashboard"></a>Řídicí panel správy
Pro správu (nebo MSP řídicí panel pro uživatele MSP) řídicí panel obsahuje označuje typy hlavní části sestavy.  
![Řídicí panel správy](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Souhrn náklady Entity (pouze v Enterprise)
Tato pomůcka shrnuje náklady na spravované entity, včetně počet entit a počet účtů.
- Klikněte na tlačítko widgetu otevřete sestavu Podrobnosti Enterprise.

### <a name="cost-over-time"></a>Náklady v čase
Tato pomůcka můžete Sledujte trendy nákladů. Zvýrazní náklady pro poslední den, podle trendu za posledních 30 dnů.
- Klikněte na tlačítko widgetu otevřete sestavu skutečné náklady v čase, chcete-li zobrazit a vyfiltrovat další podrobnosti.

### <a name="asset-controller"></a>Asset řadiče
Tato pomůcka označuje počet spuštěných instancí z předchozí den výše trend využití za posledních 30 dní.
- Klikněte na tlačítko widgetu Otevřete řídící panel Asset řadiče.

### <a name="unused-ri-detector"></a>Detektor nepoužívané RI
Tato pomůcka označuje počet Amazon EC2 nepoužívané rezervace.
- Klikněte na tlačítko widgetu otevřete sestavu aktuálně nepoužívané rezervace, kde můžete zobrazit nepoužívané rezervace můžete upravit.

### <a name="cost-by-service"></a>Náklady služby
Tato pomůcka označuje amortizovaný náklady službou za posledních 30 dní. Najeďte na výsečový graf zobrazíte náklady na služby.
- Klikněte na tlačítko widgetu otevřete sestavu analýzy skutečné náklady.

### <a name="potential-savings"></a>Potenciální úspora
Tato pomůcka ukazuje typ instance ceny doporučení pro Amazon EC2 a Amazon vzdálené plochy
- Klikněte na Otevřít pomůcky sestava analýzy úspory. Zobrazí se seznam náklady na typy instance s potenciální úspora.

### <a name="compute-instances---daily-trend"></a>Výpočetní instance - denní trendu
Tato pomůcka zobrazí aktivních instancích podle typu, za posledních 30 dní.
- Klikněte na tlačítko widgetu otevřete sestavu instancí v čase, kde můžete zobrazit rozpis všech instancí během posledních 30 dnů.

### <a name="storage-by-department"></a>Úložiště podle oddělení
Tato pomůcka zobrazí služby úložiště, které oddělení používá. Najeďte na výsečový graf zobrazíte spotřeby vašeho úložiště podle oddělení.
- Klikněte na tlačítko widgetu Otevřete řídící panel sledovací modul S3.

## <a name="cost-controller-dashboard"></a>Náklady řadiče řídicí panel
Řídicí panel náklady řadiče zobrazuje označuje přidělení předem nastavené náklady.  
![Náklady řadiče řídicí panel](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Náklady v čase
Tato pomůcka pomáhá rozpoznat trendy nákladů. Zvýrazní náklady pro poslední den, podle trendu za posledních 30 dnů.
- Klikněte na tlačítko widgetu otevřete sestavu skutečné náklady v čase, chcete-li zobrazit a vyfiltrovat další podrobnosti.

### <a name="monthly-cost-trends"></a>Měsíční náklady trendů
Tato pomůcka označuje předpokládané amortizovaný výdaje a skutečným tráví od začátku měsíce.
- Klikněte na tlačítko widgetu otevřete sestavu projektovat náklady na aktuální měsíc, které poskytuje s souhrnné náklady měsíc k datu.

Tato sestava obsahuje náklady na od začátku měsíce, náklady na předchozího měsíce a náklady na aktuální měsíc k projekci. V aktuálním měsíci předpokládané náklady se počítá přidáním aktuální měsíční náklady a projekce. Projekce je založena na náklady na monitorované za posledních 30 dní.

### <a name="12-month-planner"></a>Plánovač 12 měsíců
Tato pomůcka označuje přes dalších 12 měsíců a potenciální úspora nákladů na předpokládané.
- Klikněte na tlačítko widgetu otevřete sestavu roční projektovat náklady.

### <a name="cost-by-service"></a>Náklady služby
Tato pomůcka označuje amortizovaný náklady službou za posledních 30 dní.
- Najeďte na výsečový graf zobrazíte náklady na služby.
- Klikněte na tlačítko widgetu otevřete sestavu analýzy skutečné náklady.

### <a name="cost-by-account"></a>Náklady účtu
Tato pomůcka označuje amortizovaný náklady účet za posledních 30 dní.
- Najeďte na výsečový graf zobrazíte náklady na každý účet.
- Klikněte na tlačítko widgetu otevřete sestavu analýzy skutečné náklady.

### <a name="cost-trend-by-day"></a>Cenově trendu za den
Tato pomůcka označuje tráví za posledních 30 dní.
- Najeďte myší na pruhový graf zobrazíte náklady za den.
- Klikněte na tlačítko widgetu otevřete sestavu skutečné náklady v čase.

### <a name="cost-trend-by-month---last-6-months"></a>Náklady Trend měsíc - posledních 6 měsíců

Tato pomůcka označuje tráví za posledních šest měsíců.
- Najeďte myší na pruhový graf zobrazíte náklady za měsíc.
- Klikněte na tlačítko widgetu otevřete sestavu skutečné náklady v čase.

## <a name="asset-controller-dashboard"></a>Řídicí panel Asset řadiče

Tento řídicí panel zobrazuje počet spuštěných instancí, k dispozici a používání disků, distribuční typů instancí a informace o úložiště.  
![Řídicí panel Asset řadiče](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Výpočetní instance
Tato pomůcka zobrazí počet spuštěných instancí podle trend využití za posledních 30 dní.
- Klikněte na tlačítko widgetu otevřete sestavu instancí v čase.

### <a name="disks"></a>Disky
Tato pomůcka klade důraz na celkový počet a objem disky, které jsou v provozu a k dispozici.
- Klikněte na tlačítko widgetu otevřete sestavu Active disky.

### <a name="instance-type-distribution"></a>Instance typu distribuce
Tato pomůcka označuje typy instance výsečového grafu.
- Klikněte na widgetu otevřete sestavu distribuční Instance, který rozděluje aktivních instancích podle vybrané agregace.

### <a name="compute-instances---daily-trend"></a>Výpočetní instance - denní trendu
Tato pomůcka označuje výpočetní instance (přímé vyhrazené a na vyžádání) za den za posledních 30 dní.
- Najeďte myší na graf můžete zobrazit počet instancí výpočetní podle typu za den.
- Klikněte na tlačítko widgetu otevřete sestavu instancí v čase.

### <a name="all-buckets-s3"></a>Všechny Kbelíků (S3)
Tato pomůcka klade důraz na celkové úložiště S3 a počet objektů, které jsou uložené.
- Klikněte na tlačítko widgetu Otevřete řídící panel sledovací modul S3. Řídicí panel vám pomůže najít, analyzovat a zobrazit aktuální využití úložiště a trendy.

### <a name="sql-db-instances-rds"></a>Instance databáze SQL (RDS)
Tato pomůcka označuje počet spuštěných instancí Amazon RDS podle trendu za posledních 30 dnů.
- Klikněte na tlačítko widgetu otevřete sestavu RDS Instance v čase.

## <a name="optimizer-dashboard"></a>Řídicí panel pro optimalizaci
Tento řídicí panel zobrazuje downsizing doporučení, nepoužité zdroje a potenciální úspora.  
![Řídicí panel pro optimalizaci](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>RI kalkulačky
Tato pomůcka zobrazí počet RI nákupní doporučení a označuje potenciální roční úspory.
- Klikněte na widgetu otevřete vyhrazenou instanci kalkulačky, kde můžete určit, kdy použít na vyžádání oproti vyhrazené cenových plánů.

### <a name="sizing"></a>Velikost
Tato pomůcka klade důraz na doporučené nastavení velikosti a potenciální úspora, pokud je implementována.
- Klikněte na tlačítko widgetu otevřete sestavu EC2 náklady platné velikost doporučení.

### <a name="unused-ri-detector"></a>Detektor nepoužívané RI
Tato pomůcka označuje počet Amazon EC2 nepoužívané rezervace.
- Klikněte na tlačítko widgetu otevřete sestavu aktuálně nepoužívané rezervace, kde můžete zobrazit nepoužívané rezervace, které lze upravit.

###  <a name="available-disks"></a>Dostupné disky
Tato pomůcka označuje počet odpojit disků ve vašem nasazení.
- Klikněte na tlačítko widgetu otevřete sestavu disky odpojit.

### <a name="rds-ri-calculator"></a>Kalkulačky RI vzdálené plochy
Tato pomůcka označuje počet rezervace doporučení pro vaše instance Amazon vzdálené plochy a potenciální úspora.
- Klikněte na tlačítko widgetu otevřete sestavu RDS RI kdybyste kupovali doporučení, kde se můžete podívat Cloudyn doporučení pro použití vyhrazenou instancí místo instance na vyžádání.

### <a name="rds-sizing"></a>Nastavení velikosti vzdálené plochy
Tato pomůcka zobrazuje počet doporučení pro změnu velikosti a potenciální úspora.
- Klikněte na tlačítko widgetu otevřete sestavu doporučení pro změnu velikosti vzdálené plochy, které zobrazí podrobné Amazon RDS dimenzování doporučení.

Optimalizace doporučení jsou založené na využití a výkonu data sledovat v poslední měsíc.

## <a name="s3-tracker-dashboard"></a>Řídicí panel sledovací modul S3
Řídicí panel sledovací modul S3 umožňuje hledat, analyzovat a zobrazit aktuální využití úložiště a trendy.  
![Řídicí panel sledovací modul S3](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Všechny intervalů
Tato pomůcka označuje celková velikost všech intervalů, v GB a celkový počet objektů v vaší intervalů.
- Klikněte na tlačítko widgetu otevřete sestavu distribuční S3 velikost. Sestava umožňuje analýzu velikost vašeho S3 sady, složku nejvyšší úrovně, třídy úložiště a stav správy verzí.

### <a name="bucket-properties"></a>Sady vlastností
Tato pomůcka označuje celkový počet kbelíků úložiště.
- Klikněte na tlačítko widgetu sestavu vlastnosti sady S3.

### <a name="scan-status"></a>Kontrola stavu
Tato pomůcka označuje, kdy bylo provedeno poslední kontroly S3 a kdy se spustí další jeden.
- Klikněte na tlačítko widgetu otevřete sestavu S3 kontrolovat stav.

### <a name="storage-by-bucket"></a>Úložiště pomocí sady
Tato pomůcka označuje procento, které používá každá třída sady úložiště.
- Klikněte na tlačítko widgetu otevřete sestavu distribuční S3 velikost. Sestava umožňuje analýzu velikost vašeho S3 sady, složku nejvyšší úrovně, třídy úložiště a stav správy verzí.

### <a name="number-of-objects-by-bucket"></a>Počet objektů pomocí sady
Tato pomůcka označuje počet objektů v rámci jedné sady v skutečný počet a procento. Najeďte myší kbelík zobrazíte celkový počet objektů.
- Klikněte na tlačítko widgetu otevřete sestavu distribuční S3 velikost (na základě kontroly).

## <a name="cloud-comparison-dashboard"></a>Řídicí panel cloud porovnání
Řídicí panel Cloud porovnání vám pomůže porovnat náklady od poskytovatelů jiný cloud na základě ceny, typu procesoru a velikost paměti RAM.  
![Řídicí panel cloud porovnání](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>EC2 Náklady v Azure pomocí Instance typu
Tato pomůcka označuje za posledních 30 dní využití v sazby na vyžádání. Ho comares náklady na s aktuální EC2 Amazon náklady vs potenciální náklady v Azure.
- Podržte ukazatel nad řádky k porovnání náklady na typ instance.
- Klikněte na tlačítko widgetu otevřete portování vaše nasazení – sestava analýzy náklady.

### <a name="ec2-cost-in-azure"></a>EC2 Náklady v Azure
Tato pomůcka zobrazuje náklady na aktuální Amazon EC2 a porovná je do Azure. Porovnání podle posledních 30 dnech využití v sazby na vyžádání.
- Klikněte na tlačítko widgetu otevřete portování vaše nasazení – sestava analýzy náklady.

### <a name="ec2azure-instance-type-mapping"></a>Mapování typu Instance EC2 nebo Azure
Tato pomůcka označuje nejlepší mapování jednotky elastické výpočetní mezi Amazon EC2 a Azure.
- Klikněte na tlačítko widgetu otevřete sestavu instance typu mapování.
