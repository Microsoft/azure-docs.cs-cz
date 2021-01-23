---
title: Plánování správy nákladů na App Service
description: Naučte se plánovat a spravovat náklady na Azure App Service pomocí analýzy nákladů v Azure Portal.
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: 9d742606435f62d48446bb8ad56ece7a31b76e47
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736217"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>Plánování a Správa nákladů na Azure App Service

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

Tento článek popisuje, jak plánujete a spravovat náklady na Azure App Service. Nejprve pomocí cenové kalkulačky Azure pomůže naplánovat náklady na App Service před tím, než přidáte jakékoli prostředky pro službu, abyste mohli odhadnout náklady. V dalším kroku můžete při přidávání prostředků Azure zkontrolovat odhadované náklady. Po zahájení práce s prostředky App Service použijte funkce [cost management](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) k nastavení rozpočtů a monitorování nákladů. Můžete si také projít předpokládané náklady a identifikovat trendy útraty, které identifikují oblasti, kde můžete chtít pracovat. Náklady na Azure App Service jsou jenom částí měsíčních nákladů na faktuře Azure. I když tento článek vysvětluje, jak naplánovat a spravovat náklady na App Service, účtují se vám všechny služby a prostředky Azure, které se používají ve vašem předplatném Azure, včetně služeb třetích stran.

## <a name="relevant-costs-for-app-service"></a>Relevantní náklady na App Service

App Service běží na infrastruktuře Azure a účtují náklady. Je důležité pochopit, že další infrastruktura by mohla navýšit náklady. Pokud provedete změny nasazených prostředků, musíte tyto náklady spravovat.

### <a name="costs-that-accrue-with-azure-app-service"></a>Náklady, které jsou časově rozlišené pomocí Azure App Service

V závislosti na tom, jakou funkci používáte v App Service, se můžou vytvořit následující prostředky se snížením nákladů:

- **Plán App Service**  Vyžaduje se pro hostování aplikace App Service.
- **Izolovaná vrstva**  Pro App Service prostředí se vyžaduje [Virtual Network](../virtual-network/index.yml) .
- **Záložní kopie**  K provedení zálohování je nutný [účet úložiště](../storage/index.yml) .
- **Diagnostické protokoly**  Jako možnost protokolování můžete vybrat možnost [účet úložiště](../storage/index.yml) nebo integrovat s [Azure Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md).
- **App Service certifikátů**  Certifikáty, které zakoupíte v Azure, se musí udržovat v [Azure Key Vault](../key-vault/index.yml).

Další nákladové prostředky pro App Service jsou (podrobnosti najdete v tématu [App Service ceny](https://azure.microsoft.com/pricing/details/app-service/) ):

- [App Service domény](manage-custom-dns-buy-domain.md)  Vaše předplatné se účtuje na základě roční registrace domény, pokud povolíte automatické obnovování.
- [App Service certifikátů](configure-ssl-certificate.md#import-an-app-service-certificate)  Jednorázové poplatky v době nákupu. Pokud máte více subdomén, které je třeba zabezpečit, můžete snížit náklady tím, že místo více standardních certifikátů zakoupíte jeden certifikát se zástupnými znaky.
- [Vazby certifikátů na základě IP adresy](configure-ssl-bindings.md#create-binding)  Vazba je nakonfigurovaná na certifikátu na úrovni aplikace. Náklady se účtují pro každou vazbu. U úrovně **Standard** a vyšší se první vazba založená na protokolu IP neúčtuje.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>Náklady, které se můžou po odstranění prostředku časově snížit

Když odstraníte všechny aplikace v plánu App Service, plán bude dál počítat poplatky na základě nakonfigurované cenové úrovně a počtu instancí. Abyste se vyhnuli nechtěným poplatkům, odstraňte plán nebo ho Škálujte dolů na úroveň **Free** .

Po odstranění prostředků Azure App Service můžou dál existovat prostředky ze souvisejících služeb Azure. Nadále se účtují náklady, dokud je neodstraníte. Například:

- Virtual Network, který jste vytvořili pro plán App Service **izolované** vrstvy
- Účty úložiště, které jste vytvořili pro ukládání záloh nebo diagnostických protokolů
- Key Vault jste vytvořili pro ukládání certifikátů App Service.
- Log Analytics Namespaces, které jste vytvořili pro dodávání diagnostických protokolů
- [Rezervace instance nebo razítka](#azure-reservations) pro App Service, jejichž platnost ještě nevypršela

### <a name="using-monetary-credit-with-azure-app-service"></a>Použití peněžního kreditu s Azure App Service

Za poplatky za Azure App Service můžete platit pomocí kreditu služby Azure (dříve nazývaného peněžního závazku). Nemůžete však použít kredit plateb Azure k placení poplatků za produkty a služby třetích stran, včetně těch, které jsou Azure Marketplace.

## <a name="estimate-costs"></a>Odhadněte náklady

Snadný způsob, jak předem odhadnout a optimalizovat náklady na App Service, je použití [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator/).

Pokud chcete použít cenovou kalkulačku, klikněte na **App Service** na kartě **produkty** . Pak se posuňte dolů na práci s kalkulačkou. Následující snímek obrazovky je příkladem a neodráží aktuální ceny.

![Příklad znázorňující odhadované náklady v cenové kalkulačkě Azure](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>Zjištění odhadovaných nákladů na webu Azure Portal

Při vytváření aplikace App Service nebo plánu App Service můžete zobrazit odhadované náklady.

Postup vytvoření aplikace a zobrazení odhadované ceny:

1. Na stránce vytvořit přejděte dolů na **App Service plán** a klikněte na **vytvořit novou**.
1. Zadejte název a klikněte na tlačítko **OK**.
1. Vedle položky **SKU a velikost** klikněte na **změnit velikost**.
1. Přečtěte si očekávanou cenu zobrazenou v souhrnu. Následující snímek obrazovky je příkladem a neodráží aktuální ceny.

    ![Přečtěte si odhadované náklady na jednotlivé cenové úrovně na portálu.](media/overview-manage-costs/pricing-estimates.png)

Pokud má vaše předplatné Azure limit útraty, Azure vám zabrání v útratě za vaši kreditní částku. Při vytváření a používání prostředků Azure se vaše kredity používají. Po dosažení limitu kreditu budou prostředky, které jste nasadili, zakázané pro zbytek tohoto fakturačního období. Limit kreditu nemůžete změnit, ale můžete ho odebrat. Další informace o limitech útraty najdete v tématu [limit útraty Azure](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="optimize-costs"></a>Optimalizace nákladů

Na úrovni Basic se App Service aplikacím účtují App Service plán, který je hostuje. Náklady spojené s vaším nasazením App Service závisí na několika hlavních faktorech:

- **Cenová úroveň**  Jinak se označuje jako SKU plánu App Service. Vyšší úrovně poskytují více jader procesoru, paměti, úložiště nebo funkcí nebo jejich kombinací.
- **Počet instancí**  vyhrazené úrovně (Basic a vyšší) je možné škálovat a každá instance s horizontálním navýšení kapacity počítá náklady.
- **Poplatky za razítko**  V izolované úrovni se za App Service prostředí odchází paušální poplatek bez ohledu na to, kolik aplikací nebo instancí pracovních procesů je hostovaných.

Plán App Service může hostovat víc než jednu aplikaci. V závislosti na vašem nasazení můžete ušetřit náklady hostováním dalších aplikací v jednom App Service plánech (tj. hostování aplikací na méně App Servicech plánů).

Podrobnosti najdete v tématu [Přehled plánu App Service](overview-hosting-plans.md) .

### <a name="non-production-workloads"></a>Úlohy, které nepatří do produkčního prostředí

Pokud chcete testovat App Service nebo řešení a přitom snížit nízké nebo minimální náklady, můžete začít pomocí dvou cenových úrovní vstupní úrovně, **Free** a **Shared**, která je hostovaná ve sdílených instancích. Pokud chcete otestovat aplikaci na vyhrazených instancích s lepším výkonem, můžete upgradovat na úroveň **Basic** , která podporuje aplikace pro Windows i Linux. 

> [!NOTE]
> **Ceny pro vývoj a testování Azure**  K testování předprodukčních úloh, které vyžadují vyšší úrovně (s výjimkou **izolovaných**), můžou předplatitelé sady Visual Studio využívat i [ceny Azure pro vývoj a testování](https://azure.microsoft.com/pricing/dev-test/), které nabízí významné slevy.
>
> Úroveň **Free** i **Shared** i cenové slevy pro vývoj/testování Azure nenesou finančně zajištěnou smlouvu SLA.

### <a name="production-workloads"></a>Produkční úlohy

Produkční úlohy se dodávají s doporučením pro vyhrazenou **standardní** cenovou úroveň nebo vyšší. I když je cena vyšší úrovně, poskytuje vám také více paměti a úložiště a výkonnější hardware, což vám poskytne vyšší hustotu aplikace na výpočetní instanci. To se týká nižšího počtu instancí pro stejný počet aplikací, a proto nižší náklady. Verze **Premium V3** (nejvyšší úroveň mimo **izolovaný** režim) je nejefektivnější způsob, jak vaší aplikaci škálovat. Pro přidání do úspory můžete získat hloubkové slevy na základě [prémiových rezervací V3](#azure-reservations).

> [!NOTE]
> **Premium V3** podporuje kontejnery Windows i kontejnery platformy Linux. 

Po výběru cenové úrovně, kterou chcete, byste měli minimalizovat nečinné instance. V nasazení se škálováním na více instancí můžete vymezit peníze v nevyužitých výpočetních instancích. Měli byste [nakonfigurovat automatické škálování](../azure-monitor/platform/autoscale-get-started.md), které je dostupné na úrovni **Standard** a vyšší. Když vytvoříte plány škálování na více instancí i pravidla škálování na více instancí, platíte jenom za ty, které skutečně v daném okamžiku skutečně potřebujete.

### <a name="azure-reservations"></a>Rezervace Azure

Pokud plánujete využívat známý minimální počet výpočetních instancí po dobu jednoho roku nebo více, měli byste využít výhody úrovně **Premium V3** a významně snížit náklady instance tím, že tyto instance zařadíte na 1 nebo 3 roky. Úspory za měsíční náklady můžou být až 55% na jednu instanci. Možné jsou dva typy rezervací:

- **Windows (nebo platforma nezávislá)**  Může se vztahovat na instance systému Windows nebo Linux v rámci vašeho předplatného.
- **Specifické pro Linux**  Platí jenom pro instance Linux v rámci vašeho předplatného.

Ceny za rezervované instance platí pro příslušné instance v rámci vašeho předplatného, až do počtu rezervovaných instancí. Rezervované instance jsou fakturační a nejsou vázané na konkrétní výpočetní instance. Pokud spustíte méně instancí, než je v jakémkoli okamžiku rezervovaného období rezervováno, bude se vám stále platit rezervované instance. Pokud spustíte více instancí, než kolik rezervujete v jakémkoli okamžiku během období rezervace, platíte normální náklady na další instance.

**Izolovaná** vrstva (App Service prostředí) také podporuje 1 a 3 roky rezervace za sníženou cenu. Další informace najdete v tématu [jak se pro Azure App Service izolované razítka vztahují slevy na rezervace](../cost-management-billing/reservations/reservation-discount-app-service-isolated-stamp.md).

## <a name="monitor-costs"></a>Sledovat náklady

Při používání prostředků Azure s App Service se vám účtují náklady. Náklady na jednotku využívání prostředků Azure se liší podle časových intervalů (sekundy, minuty, hodiny a dny). Jakmile začnete App Service používání, účtují se náklady a náklady se vám budou zobrazovat v [analýze nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Když použijete analýzu nákladů, zobrazí se App Service náklady v grafech a tabulkách pro různé časové intervaly. Některé příklady jsou podle dne, aktuálního a předchozího měsíce a roku. Také se zobrazí náklady na rozpočty a předpokládané náklady. Přepínání na delší zobrazení v průběhu času vám může pomáhat identifikovat trendy útraty. A vidíte, kde mohlo dojít k předanému nadměrnému útratu. Pokud jste vytvořili rozpočty, můžete také snadno zjistit, kde jsou překročeny.
    
Postup zobrazení App Servicech nákladů při analýze nákladů:

1. Přihlaste se k portálu Azure.
2. Otevřete obor v Azure Portal a v nabídce vyberte **Analýza nákladů** . Například přejděte na **odběry**, v seznamu vyberte předplatné a v nabídce vyberte  **Analýza nákladů** . Vyberte **obor** , který se má při analýze nákladů přepnout na jiný obor.
3. Ve výchozím nastavení se náklady na služby zobrazují v prvním prstenovém grafu. Vyberte oblast v grafu označené App Service.

Skutečné měsíční náklady se zobrazí při počátečním otevření analýzy nákladů. Tady je příklad zobrazující všechny měsíční náklady na používání.

![Příklad znázorňující nahromaděné náklady na předplatné](media/overview-manage-costs/all-costs.png)

Chcete-li zúžit náklady na jednu službu, například App Service, vyberte možnost **Přidat filtr** a potom vyberte možnost **název služby**. Pak vyberte **App Service**.

Tady je příklad, který ukazuje náklady jenom na App Service.

![Příklad znázorňující akumulované náklady pro ServiceName](media/overview-manage-costs/service-specific-costs.png)

V předchozím příkladu vidíte aktuální náklady za službu. Zobrazují se také náklady podle oblastí Azure (umístění) a App Servicech nákladů podle skupiny prostředků. Z tohoto místa můžete sami prozkoumat náklady.

## <a name="create-budgets"></a>Tvorba rozpočtů

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

Pro účely řízení nákladů můžete vytvořit [rozpočty](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a nastavit [upozornění](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), která účastníky automaticky upozorňují na anomálie a nebezpečí nadměrných výdajů. Upozornění jsou založena na porovnání útraty s rozpočtem a prahovými hodnotami nákladů. Rozpočty a výstrahy se vytvářejí pro předplatná Azure a skupiny prostředků, takže jsou užitečné v rámci celkové strategie monitorování nákladů. 

Rozpočty je možné vytvořit s filtry pro konkrétní prostředky nebo služby v Azure, pokud chcete mít v monitorování k dispozici větší členitost. Filtry vám pomůžou zajistit, že nechtěně nevytvoříme nové prostředky, které vám budou zajišťovat peníze. Další informace o možnostech filtru dostupných při vytváření rozpočtu najdete v tématu [Možnosti skupiny a filtru](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Export dat nákladů

[Data nákladů](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) můžete také exportovat do účtu úložiště. To je užitečné v případě, že potřebujete nebo jiné pro náklady na analýzu dat. Finanční tým může například analyzovat data pomocí aplikace Excel nebo Power BI. Náklady můžete exportovat na denní, týdenní nebo měsíční plán a nastavit vlastní rozsah kalendářních dat. Export nákladových dat je doporučený způsob, jak načíst datové sady nákladů.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o tom, jak ceny pracují s Azure Storage. Viz [ceny App Service](https://azure.microsoft.com/pricing/details/app-service/).
- Naučte se [optimalizovat investice do cloudu pomocí Azure cost management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si další informace o správě nákladů pomocí [analýzy nákladů](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Přečtěte si, jak se [vyhnout neočekávaným nákladům](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Využijte kurz učení s asistencí [cost management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->