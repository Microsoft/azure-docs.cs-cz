---
title: Cloud Cruiser a fakturaci integrace rozhraní API Microsoft Azure | Dokumentace Microsoftu
description: Poskytuje jedinečnou perspektivou z Microsoft Azure Billing partner Cloud Cruiser na své prostředí integrace API pro fakturaci Azure do svého produktu.  To je užitečné pro zákazníky Azure a Cloud Cruiser, které zajímá použití/zkusit Cloud Cruiser pro Microsoft Azure Pack.
services: ''
documentationcenter: ''
author: tonguyen
manager: tonguyen
editor: ''
tags: billing
ms.assetid: b65128cf-5d4d-4cbd-b81e-d3dceab44271
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/09/2017
ms.author: erikre
ms.openlocfilehash: 95d90e898ddc8766cf96a5a72c315407cd596393
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393855"
---
# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Cloud Cruiser a fakturaci integrace rozhraní API Microsoft Azure
Tento článek popisuje, jak můžete použít informace shromážděné z rozhraní nového Microsoft Azure API pro fakturaci v Cloud Cruiser pro pracovní postup náklady simulaci a analýzu.

## <a name="azure-ratecard-api"></a>Azure RateCard API
RateCard API poskytuje informace o kurzu z Azure. Po ověření se správnými přihlašovacími údaji, můžete dát dotaz na rozhraní API se získat metadata o službách, které jsou k dispozici v Azure, spolu s sazby související s vaší nabídky ID.

Následující ukázkové odpovědi je z rozhraní API s ceny A0 instance (Windows):

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Cloud Cruiser pro rozhraní Azure RateCard API
Cloud Cruiser můžete použít informace RateCard API různými způsoby. Pro účely tohoto článku vám ukážeme, jak to lze provést IaaS úloh, náklady, simulaci a analýzu.

Abychom si předvedli tento případ použití, představte si úlohu několik instancí spuštěných v Microsoft Azure Pack (WAP). Cílem je simulovat tento stejná úloha v Azure a odhad nákladů na provedení migrace. Chcete-li vytvořit tento simulace, existují dvě hlavní úlohy, která se má provést:

1. **Import a proces shromažďují informace o službě z RateCard API.** To se také provádí v sešitech, kde je extrakce z RateCard API transformovat a publikují do nového plánu sazeb. Tento nový plán frekvence se používá na simulace pro odhad ceny Azure.
2. **Normalizujte WAP služby a služby Azure IaaS.** Ve výchozím nastavení, jsou WAP službám v závislosti na jednotlivých prostředků (procesoru, velikosti paměti, velikost disku atd.), kdy Azure services jsou založeny na velikost instance (A0, A1, A2, atd.). Tato první úloha můžete provést pomocí modulu ETL Cloud Cruiser, volá sešity, pokud tyto prostředky je možné seskupit na velikosti instance, obdobná Azure instance služby.

### <a name="import-data-from-the-ratecard-api"></a>Umožňuje importovat data z RateCard API
Cloud Cruiser sešity poskytují automatizovaný způsob, jak ke shromáždění a zpracování informací z RateCard API.  Sešity ETL (extract-transform-load) umožňují konfigurovat shromažďování, transformace a publikování dat do databáze Cloud Cruiser.

Každý sešit může mít jednu nebo více kolekcí, umožňuje korelovat informace z různých zdrojů a doplňují nebo upravte dat o využití. Na následujících dvou snímcích obrazovky ukazují, jak vytvořit novou *kolekce* v existující sešit a import informací do *kolekce* z RateCard API:

![Obrázek 1 – Vytvoření nové kolekce][1]

![Obrázek 2 – Import dat z nové kolekce][2]

Po importu dat do sešitu, je možné vytvořit více kroky a postupy transformace, upravovat a modelovat data. V tomto příkladu od nás zajímá jenom infrastruktura as-a-Service (IaaS), můžeme použít kroky transformace odebrat nepotřebné řádků nebo záznamy, související s jinými službami než IaaS.

Následující snímek obrazovky ukazuje kroky transformace umožňuje zpracovávat data shromážděná z RateCard API:

![Obrázek 3 – kroky transformace zpracovat shromážděná data z RateCard API][3]

### <a name="defining-new-services-and-rate-plans"></a>Definice nové služby a rychlost plány
Chcete-li definovat služeb na Cloud Cruiser různými způsoby. Jednu z možností je import z dat o využití služeb. Tato metoda se obvykle používá při práci s veřejnými cloudy, kde jsou již definovány služeb zprostředkovatelem.

Sazba plánu je sada sazby nebo ceny, které lze použít u různých služeb na základě data platnosti, nebo skupinu zákazníků, mezi další možnosti. Plány sazeb lze také na Cloud Cruiser vytvořit simulace nebo scénáře "Co kdyby", abyste pochopili vliv celkové náklady na úlohu změnám ve službách.

V tomto příkladu používáme k definování nových služeb v Cloud Cruiser informace o službě z RateCard API. Stejným způsobem používáme k sazby související ke službám na Cloud Cruiser vytvořit nový plán frekvence.

Na konci procesu transformace je možné vytvořit nový krok a publikovat data z RateCard API jako nové služby a kurzy.

![Obrázek 4 – publikování dat z RateCard API jako nové služby a kurzy][4]

### <a name="verify-azure-services-and-rates"></a>Ověření služby Azure a kurzy
Po publikování služby a sazby, můžete ověřit seznam importovaných služeb v Cloud Cruiser *služby* kartu:

![Obrázek 5 – ověření nových služeb][5]

Na *míra plány* kartu, můžete zkontrolovat nové míry plán nazvaný "AzureSimulation" jako importován z RateCard API.

![Obrázek 6 – ověření nový plán frekvence a související kurzy][6]

### <a name="normalize-wap-and-azure-services"></a>Normalizovat WAP a službami Azure
Ve výchozím nastavení poskytuje WAP využití informace v závislosti na využívání výpočetních, paměťových a síťové prostředky. V Cloud Cruiser, můžete definovat vašich služeb na základě přímo na přidělení nebo účtované podle objemu dat využití těchto prostředků. Můžete například nastavit základní sazba za každou hodinu využití procesoru nebo účtovat GB přidělené paměti na instanci.

Pro tento příklad aby bylo možné porovnávat náklady mezi WAP a Azure, potřebujeme agregace využití prostředků na WAP do sad, které lze mapovat na služby Azure. V sešitech lze snadno implementovat této transformace:

![Obrázek 7: transformace dat WAP normalizace služby][7]

K publikování dat do databáze Cloud Cruiser je posledním krokem v sešitu. Během tohoto kroku je dat o využití teď seskupeny do služeb, (, které se mapují do služeb Azure) a vázané na výchozí sazby vytvoření poplatky.

Po dokončení sešitu, můžete automatizovat zpracování dat, tak přidání úkolu v plánovači a zadáte četnost a čas pro sešit, který chcete spustit.

![Obrázek 8 - sešitu plánování][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Vytváření sestav pro úlohy analýzy nákladů simulace
Po použití jsou shromažďovány, včetně poplatky se načtou do databáze Cloud Cruiser, jsme modul Cloud Cruiser Insights můžete použít k vytvoření úlohy nákladů simulace, která chceme.

Aby bylo možné předvést tento scénář, jsme vytvořili na následující sestavu:

![Porovnání nákladů][9]

Začátek graf ukazuje porovnání nákladů službami porovnání ceny spuštěná úloha pro každou konkrétní službu WAP (tmavě modrá) od Azure (světle modrá).

Dolní části grafu zobrazí stejná data, ale rozděleno podle oddělení. Náklady pro každé oddělení pro svoje úlohy WAP i Azure, spolu s rozdíl mezi nimi se zobrazí na panelu úspory (zelená).

## <a name="azure-usage-api"></a>Rozhraní API využití Azure
### <a name="introduction"></a>Úvod
Společnost Microsoft nedávno zavedla rozhraní API pro využití Azure umožňuje odběratelům prostřednictvím kódu programu o přijetí změn v datech využití a získejte přehled o jejich využití. Cloud Cruiser zákazníci můžou využít výhod bohatší datové sady k dispozici prostřednictvím tohoto rozhraní API.

Cloud Cruiser pomocí integrace rozhraní API pro využití několika způsoby. Členitost (každou hodinu informace o použití) a resource metadata informací dostupných prostřednictvím rozhraní API poskytuje nezbytné datovou sadu pro podporu flexibilní modely sestav metod Showback a Chargeback. 

V tomto kurzu jsme k dispozici jeden příklad, jak Cloud Cruiser využívat informace o využití rozhraní API. Přesněji řečeno jsme se vytvořit skupinu prostředků v Azure, přiřadit značky pro strukturu účtu a popisují proces stahování a zpracování informací značky do Cloud Cruiser.

Poslední cílem je umožnit vytváření sestav, jako je následující a mít k analýze nákladů a využití na základě struktury účtu vyplněn značky.

![Obrázek 10 - sestavy s použitím rozdělení pomocí značek][10]

### <a name="microsoft-azure-tags"></a>Značky Microsoft Azure
K dispozici prostřednictvím Azure Usage API data obsahují pouze informace o spotřebě, ale také resource metadata, včetně všechny značky, které s ním spojená. Značky poskytují jednoduchý způsob k uspořádání prostředků, ale aby bylo možné platit, je třeba Ujistěte se, že:

* Značky jsou správně použity k prostředkům v době poskytování
* Značky jsou správně použít v procesu sestav metod Showback a Chargeback a jejich zapojení využití a struktura účtu organizace.

Oba tyto požadavky může být náročné, zejména v případě, že je ruční proces na poskytování nebo účtování straně. Chybným, nesprávná nebo dokonce i chybějící značky jsou častých stížností od zákazníků, když pomocí značek a k těmto chybám může ztěžovat života na straně poplatků.

S novou Azure Usage API můžete Cloud Cruiser aktivního získávání informací označování prostředků a prostřednictvím sofistikované ETL nástroj zvaný sešity, opravte tyto chyby běžných označení. Díky transformaci pomocí regulárních výrazů a korelace dat můžete Cloud Cruiser identifikovat nesprávně označené prostředky a použít správné značky, zajistit správné přidružení prostředky k příjemci.

Na straně zpoplatnění Cloud Cruiser automatizuje proces sestav metod Showback a Chargeback a můžete použít informace o značkách a jejich zapojení využití odpovídající příjemci (oddělení, divize, Project, atd.). Tato automatizace poskytuje obrovskou zlepšování a může zajistit konzistentní a auditovatelných zpoplatnění proces.

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Vytváří se skupina prostředků pomocí značek v Microsoft Azure
Prvním krokem v tomto kurzu je vytvořte skupinu prostředků na webu Azure Portal, pak vytvořte nové značky pro přidružení k prostředkům. V tomto příkladu budeme vytvářet následující značky: oddělení, prostředí, vlastník projektu.

Následující snímek obrazovky ukazuje příklad skupinu prostředků s přidružených značek.

![Obrázek 11 – skupina prostředků se přidružených značek na portálu Azure portal][11]

Dalším krokem je která informace přenese do Cloud Cruiser z rozhraní API využití. Rozhraní API využití aktuálně poskytuje odpovědi ve formátu JSON. Tady je ukázka načtených dat:

    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Import dat z rozhraní API využití do Cloud Cruiser
Cloud Cruiser sešity poskytují automatizovaný způsob, jak ke shromáždění a zpracování informací z rozhraní API využití. Sešit ETL (extract-transform-load) umožňuje nakonfigurovat kolekci, transformace a publikování dat do databáze Cloud Cruiser.

Každý sešit může mít jednu nebo více kolekcí. To umožňuje korelovat informace z různých zdrojů a doplňují nebo upravte dat o využití. V tomto příkladu vytvoříme nový list do sešitu šablony Azure (*UsageAPI)* a nastavit nové *kolekce* pro import informací z rozhraní API využití.

![Obrázek 3 – importovat do listu UsageAPI data o využití rozhraní API][12]

Všimněte si, že tento sešit už obsahuje další seznamy vlastností služby Import z Azure (*ImportServices*) a informace o spotřebě z rozhraní API pro fakturaci zpracování (*PublishData*).

V dalším kroku použijeme využití rozhraní API k naplnění *UsageAPI* list a korelovat informací data o spotřebě z rozhraní API pro fakturaci na *PublishData* list.

### <a name="processing-the-tag-information-from-the-usage-api"></a>Zpracování informací značky z rozhraní API využití
Po importu dat do sešitu, vytvoříme kroky transformace v *UsageAPI* list, aby bylo možné zpracovat informace z rozhraní API. Prvním krokem je použití procesor "JSON rozdělit" značky extrahovat z jednoho pole a pak vytvořte pole pro každou z nich (oddělení, projektů, vlastníka a prostředí).

![Obrázek 4 – vytvořit nové pole značky informace][13]

Všimněte si, že služby "Síť" chybí informace o značkách (žlutým rámečkem), ale abychom mohli ověřit, že je součástí stejné skupiny prostředků podle *ResourceGroupName* pole. Vzhledem k tomu, že máme značky pro ostatní prostředky v této skupině prostředků, můžete tyto informace používáme k použít chybějící značky k tomuto prostředku později v tomto postupu.

Dalším krokem je vytvoření vyhledávací tabulky přidružení informace ze značek, aby se *ResourceGroupName*. Vyhledávací tabulka slouží v dalším kroku k obohacení data o spotřebě s informacemi o značku.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Přidání značky informací k datům o spotřebě
Teď můžeme přejít na *PublishData* listu, která zpracovává informace o spotřebě z rozhraní API pro fakturaci a přidávání polí extrahovaná ze značek. Tento proces se provádí ve vyhledávací tabulce vytvořili v předchozím kroku, pomocí *ResourceGroupName* jako klíč pro vyhledávání.

![Obrázek 5 – sestavování struktura účtu pomocí informací z vyhledávání][14]

Všimněte si, že byly použity pole struktury příslušný účet pro službu "Síť", problém vyřešíte pomocí chybějící značek. Také naplnili jsme pole struktury účtu pro prostředky než naše cílové skupiny prostředků s "Jiné", aby bylo možné odlišit v sestavách.

Teď potřebujeme přidat krok pro publikování dat o využití. Během tohoto kroku příslušnými sazbami za u každé služby definované v našeho plánu míry použít informace o využití, výsledný poplatků načtena do databáze.

Nejlepší je, že máte jenom jednou projít tento proces. Po dokončení sešitu, stačí ho přidat do plánovače a spouští každou hodinu nebo každý den v naplánovaném čase. Pak je jenom na vás vytváření nových sestav nebo přizpůsobení existující aplikace, aby bylo možné analyzovat data a získat smysluplné přehledy z využití cloudu.

### <a name="next-steps"></a>Další kroky
* Podrobné pokyny k vytvoření Cloud Cruiser sešitů a sestav, najdete v tématu Cloud Cruiser online [dokumentaci](http://docs.cloudcruiser.com/) (vyžaduje se platné přihlášení).  Obraťte se na další informace o Cloud Cruiser [ info@cloudcruiser.com ](mailto:info@cloudcruiser.com).
* Zobrazit [získání přehledů o spotřebě prostředků Microsoft Azure](billing-usage-rate-card-overview.md) přehledné informace o využití prostředků Azure a RateCard API.
* Podívejte se [Azure Billing Reference k REST API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) Další informace o obou rozhraní API, které jsou součástí sady rozhraní API poskytovaných službou Azure Resource Manageru.
* Pokud chcete rovnou do ukázkového kódu, prohlédněte si naše Microsoft Azure Billing ukázky kódu rozhraní API na [vzorových kódů Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Další informace
* Další informace o Azure Resource Manageru, najdete v článku [přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) článku.

<!--Image references-->

[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Obrázek 1 – Vytvoření nové kolekce"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Obrázek 2 – Import dat z nové kolekce"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Obrázek 3 – kroky transformace zpracovat shromážděná data z RateCard API"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Obrázek 4 – publikování dat z RateCard API jako nové služby a kurzy"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Obrázek 5 – ověření nových služeb"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Obrázek 6 – ověření nový plán frekvence a související kurzy"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Obrázek 7: transformace dat WAP normalizace služby"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Obrázek 8 - sešitu plánování"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Obrázek 9 - ukázkové sestavě pro scénář porovnání nákladů pracovního vytížení"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Obrázek 10 - sestavy s použitím rozdělení pomocí značek"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Obrázek 11 – skupina prostředků se přidružených značek na portálu Azure portal"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Obrázek 12 - naimportovány do listu UsageAPI data o využití rozhraní API"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Obrázek 13 - vytvořit nové pole značky informace"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Obrázek 14 - naplnění struktura účtu pomocí informací z vyhledávání"
