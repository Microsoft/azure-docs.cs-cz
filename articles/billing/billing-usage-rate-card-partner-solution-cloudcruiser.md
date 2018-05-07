---
title: Cloud Cruiser a fakturace integrace rozhraní API Microsoft Azure | Microsoft Docs
description: Poskytuje jedinečný perspektivy z Microsoft Azure Billing partnera Cruiser cloudu v jejich prostředí integrace rozhraní API Azure fakturace do svých produktech.  To je obzvláště užitečné pro Azure a cloudu Cruiser zákazníky, kteří se chtějí pomocí nebo pokusu o Cruiser cloudu pro Microsoft Azure Pack.
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
ms.author: mobandyo
ms.openlocfilehash: 487636ffb0efc35c282e14d835c6669ed9d47315
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Cloud Cruiser a fakturace integrace rozhraní API Microsoft Azure
Tento článek popisuje, jak můžete používat informace shromážděné z nové rozhraní Microsoft Azure Billing API v cloudu Cruiser pro simulaci náklady pracovního postupu a analýzu.

## <a name="azure-ratecard-api"></a>Rozhraní API Azure RateCard
Rozhraní API RateCard poskytuje míra informace z Azure. Po ověření se správnými přihlašovacími údaji, můžete dát dotaz na rozhraní API ke shromažďování metadat o službách, které jsou k dispozici v Azure, společně s sazby související s vaší nabízejí ID.

Následující ukázková odpověď je z rozhraní API, zobrazuje tyto ceny pro A0 (Windows) instance:

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

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Cloud je Cruiser rozhraní Azure RateCard rozhraní API
Cloud Cruiser můžete použít informace o rozhraní API RateCard různými způsoby. V tomto článku ukážeme, jak je možné použít k IaaS zatížení náklady simulace a analýzy.

K předvedení tento případ použití, představte si zatížení několik instancí spuštěných na Microsoft Azure Pack (WAP). Cílem je pro simulaci tento stejné zatížení v Azure a odhad nákladů na provádění takových migrace. Chcete-li vytvořit tuto simulace, existují dvě hlavní úlohy provést:

1. **Importovat a zpracovat shromážděná z rozhraní API RateCard informace o služby.** Tato úloha provádí také na sešity, kde je extract z rozhraní API RateCard transformovat a publikované na nový plán rychlost. Tento nový plán míry se používá na simulace k zjištění přibližné hodnoty Azure ceny.
2. **Normalizuje WAP služby a služby Azure IaaS.** Ve výchozím nastavení, jsou na základě služby WAP u jednotlivých prostředků (procesoru, velikosti paměti, velikost disku atd.) při Azure jsou služby založené na velikost instance (A0, A1, A2 atd.). Tato první úloha můžete provést pomocí modulu ETL Cruiser cloudu, názvem sešity, kde je možné seskupit tyto prostředky na instanci velikostí, která je obdobou Azure instance služby.

### <a name="import-data-from-the-ratecard-api"></a>Umožňuje importovat data z rozhraní API RateCard
Sešity Cruiser cloudu poskytují automatizovaný způsob, jak ke sběru a zpracování informací z rozhraní API RateCard.  Sešity ETL (extrakce zatížení transformace) umožňují konfigurovat kolekce, transformaci a publikování dat do databáze Cruiser cloudu.

Každý sešit může mít jeden nebo více kolekcí, což umožňuje korelovat informace z různých zdrojů doplňují nebo posílení dat o využití. Následující dva snímky obrazovky ukazují, jak vytvořit nový *kolekce* v existující sešit a import informací do *kolekce* z rozhraní API RateCard:

![Obrázek 1 – Vytvoření nové kolekce][1]

![Obrázek 2 – importovat data z nové kolekce][2]

Po importu dat do sešitu, je možné vytvořit několika kroky a procesy transformace, a upravit data modelu. V tomto příkladu vzhledem k tomu, že jsme zajímá jenom infrastruktura jako služba (IaaS) používáme transformace kroky k odebrání nepotřebných řádků nebo záznamy, týkající se služby než IaaS.

Následující snímek obrazovky ukazuje postup transformace používá ke zpracování data shromážděná z rozhraní API RateCard:

![Obrázek 3 – postup transformace zpracovat shromážděná data z rozhraní API RateCard][3]

### <a name="defining-new-services-and-rate-plans"></a>Definování nových služeb a rychlost plány
Určit služeb v cloudu Cruiser různými způsoby. Jednu z možností je importovat služby z dat o využití. Tato metoda se běžně používá při práci s veřejné cloudy, kde jsou již definováni služby zprostředkovatelem.

Míra plánování je sada sazby nebo ceny, které lze použít pro různé služby, na základě daty platnosti nebo skupinu zákazníků, mezi další možnosti. Míra plány můžete použít taky u cloudu Cruiser k vytvoření simulace nebo "Citlivostních" scénářů, pochopit vliv celkové náklady na zatížení změny ve službě.

V tomto příkladu používáme k definování nových služeb v cloudu Cruiser informace služby z rozhraní API RateCard. Stejným způsobem používáme k sazby související službám vytvořit nový plán míra na Cruiser cloudu.

Na konci procesu transformace je možné vytvořit nový krok a publikovat data z rozhraní API RateCard jako nové služby a sazby.

![Obrázek 4 – publikování dat z rozhraní API RateCard jako nové služby a sazby][4]

### <a name="verify-azure-services-and-rates"></a>Ověření služby Azure a sazby
Po publikování služby a sazby, můžete ověřit seznam importovaných služeb v cloudu Cruiser *služby* karty:

![Obrázek 5 – ověření nových služeb][5]

Na *plány míra* kartě, můžete zkontrolovat nové míra plán nazvaný "AzureSimulation" se kurzy importovat z rozhraní API RateCard.

![Obrázek 6 – ověření nový plán rychlost a přidružené sazby][6]

### <a name="normalize-wap-and-azure-services"></a>Normalizuje WAP a službami Azure
Ve výchozím nastavení WAP poskytuje informace o využití, které jsou založeny na použití výpočetní, paměti a síťovým prostředkům. V cloudu Cruiser, můžete definovat vašich služeb na základě přímo na přidělení nebo Měřené využití těchto prostředků. Můžete například nastavit základní kurz pro každou hodinu využití procesoru nebo účtují GB paměť přidělená pro instanci.

Například pokud chcete porovnat náklady mezi WAP a Azure, je třeba k agregaci využití prostředků na WAP do sad, které lze mapovat ke službám Azure. Tato transformace můžou snadno implementovat v sešitů:

![Obrázek 7: transformace dat WAP k normalizaci služby][7]

Posledním krokem v sešitu je publikovat data do databáze Cruiser cloudu. Během tohoto kroku je dat o využití teď seskupeny do služby (které jsou mapovány na Azure Services) a vázaný na výchozí sazby vytvořit poplatků.

Po dokončení sešit, je možné automatizovat zpracování dat, přidáním úlohu na Plánovač a zadáte četnost a čas pro sešit ke spuštění.

![Obrázek 8 - sešitu plánování][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Vytváření sestav pro analýzu náklady simulace pracovního vytížení
Až se shromáždí využití a poplatky jsou načtena do cloudu Cruiser databáze, jsme můžete použít k vytvoření úlohy náklady simulace, který jsme požadavky modulu Statistika Cruiser cloudu.

K prokázání tohoto scénáře, jsme vytvořili následující sestavy:

![Cenově porovnání][9]

Horní graf zobrazuje porovnání náklady službami porovnání ceny spuštěných úloh pro každou konkrétní službu mezi WAP (tmavý modrá) a Azure (světla modrá).

Dolní graf zobrazuje stejná data, ale rozděleno podle oddělení. Náklady pro každé oddělení ke spuštění jejich pracovního vytížení na WAP a Azure, společně s rozdíl mezi nimi se zobrazí na panelu úspory (zelený).

## <a name="azure-usage-api"></a>Rozhraní API Azure využití
### <a name="introduction"></a>Úvod
Microsoft nedávno zavedl využití rozhraní API služby Azure, umožňuje odběratelům prostřednictvím kódu programu stáhnout data o využití a získáte přehled o jejich používání. Cloud Cruiser zákazníci mohou využít výhod bohatší datová sada k dispozici prostřednictvím tohoto rozhraní API.

Cruiser cloudu pomocí integrace s rozhraním API pro využití několika způsoby. Členitost (každou hodinu informace o využití) a informace metadat prostředků, které jsou k dispozici prostřednictvím rozhraní API poskytuje nezbytné datovou sadu pro podporu flexibilní modely kompletní přehled nákladů nebo vrácení peněz. 

V tomto kurzu jsme k dispozici jeden příklad, jak cloudové Cruiser využívat informace o využití rozhraní API. Přesněji řečeno jsme se vytvoření skupiny prostředků v Azure, přiřadit značky pro strukturu účtu a pak popisují proces stahování a zpracování informací značka do cloudu Cruiser.

Konečné cílem je možnost vytváření sestav, jako je ta následující, a bude schopen analyzovat náklady a spotřeba podle struktura účtu naplněn značek.

![Obrázek 10 - sestava s členění pomocí značek][10]

### <a name="microsoft-azure-tags"></a>Značky Microsoft Azure
K dispozici prostřednictvím rozhraní API pro využití služby Azure data zahrnují pouze informace o spotřebě, ale také metadata prostředků, včetně všechny značky, které s ním spojená. Značky poskytují snadný způsob k uspořádání prostředků, ale aby efektivní, je třeba zajistit, aby:

* Značky správně použijí k prostředkům během zřizování
* Značky jsou správně použít v procesu kompletní přehled nákladů/vracení peněz ke svázání využití pro strukturu účtu organizace.

Oba tyto požadavky může být náročné, zejména v případě, že je ruční proces na poskytování nebo poplatků straně. Chybným, nesprávná nebo chybějící i značek jsou častých stížností od zákazníků, když pomocí značek a tyto chyby mohou ztížit životnosti na plnících straně.

S novou využití rozhraní API služby Azure můžete cloudové Cruiser označování informace o prostředcích pro vyžádání obsahu a prostřednictvím sofistikované ETL nástroj nazvaný sešity, opravte tyto chyby běžné označování. Prostřednictvím transformaci pomocí regulárních výrazů a korelace data můžete cloudové Cruiser identifikovat nesprávně s příznakem prostředky a použít správné značky, zajistíte správné přidružení prostředky k příjemce.

Na straně plnících cloudu Cruiser automatizuje proces kompletní přehled nákladů/vracení peněz a značky informace můžete použít ke svázání využití odpovídající příjemci (oddělení, dělení, projektu atd.). Tato automatizace poskytuje obrovské zlepšování a zajistit konzistentní a kontrolovatelný plnících procesu.

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Vytvoření skupiny prostředků pomocí značek v Microsoft Azure
Prvním krokem v tomto kurzu je vytvořte skupinu prostředků na portálu Azure, pak vytvořte nové značky pro přidružení k prostředkům. V tomto příkladu jsme vytvářet těmito značkami: oddělení, prostředí, vlastník projektu.

Následující snímek obrazovky ukazuje ukázku skupinu prostředků s přidružených značek.

![Obrázek 11 – skupina prostředků se přidružených značek na portálu Azure][11]

Dalším krokem je načítat informace z rozhraní API využití do cloudu Cruiser. Využití rozhraní API aktuálně poskytuje odpovědi ve formátu JSON. Zde je ukázka data načtená:

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


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importovat data z rozhraní API využití do cloudu Cruiser
Sešity Cruiser cloudu poskytují automatizovaný způsob, jak ke sběru a zpracování informací z rozhraní API využití. Sešit aplikace ETL (extrakce zatížení transformace) umožňuje nakonfigurovat kolekce, transformaci a publikování dat do databáze Cruiser cloudu.

Každý sešit může mít jednu nebo více kolekcí. Tato funkce umožňuje korelovat informace z různých zdrojů doplňují nebo posílení dat o využití. V tomto příkladu vytvoříme nový list v sešitu šablony Azure (*UsageAPI)* a nastavte novou *kolekce* pro import informací z rozhraní API využití.

![Obrázek 3 - list UsageAPI importovat data o využití rozhraní API][12]

Všimněte si, že tento sešit již má jiné listy import služeb z Azure (*ImportServices*) a zpracování informací o spotřebě z rozhraní API fakturace (*PublishData*).

V dalším kroku používáme využití rozhraní API k naplnění *UsageAPI* list a korelovat informace s datům o spotřebě z rozhraní API fakturace na *PublishData* listu.

### <a name="processing-the-tag-information-from-the-usage-api"></a>Zpracování značky informací z rozhraní API využití
Po importu dat do sešitu, vytvoříme transformace kroky *UsageAPI* listu, aby bylo možné zpracovat informace z rozhraní API. Prvním krokem je použití procesor "JSON rozdělení" extrahovat značek z jediné pole a pak vytvořit pole pro každou z nich (oddělení, projektů, vlastníka a prostředí).

![Obrázek 4 – vytvoření nové pole značky informace][13]

Všimněte si "Síť" služby chybí značka informace (žlutý pole), ale nemůžeme můžete ověřit, že je součástí stejné skupiny prostředků prohlížením *ResourceGroupName* pole. Vzhledem k tomu, že máme značky pro další prostředky z této skupiny prostředků, můžete tyto informace používáme k nastavení platnosti chybějící značky pro tento prostředek později v tomto procesu.

Dalším krokem je vytvoření vyhledávací tabulky přidružení informace z značek k *ResourceGroupName*. Tento vyhledávací tabulky se používá na další krok k rozšíření dat spotřeby s informacemi o značky.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Přidání značka informací k datům o spotřebě
Nyní jsme přejít na *PublishData* seznamu, která zpracovává informací o spotřebě z rozhraní API fakturace a přidat pole extrahovat z uvedených značek. Tento proces se provádí prohlížením vyhledávací tabulky vytvořili v předchozím kroku, pomocí *ResourceGroupName* jako klíč k vyhledávání na.

![Obrázek 5 – naplnění strukturu účet s informacemi z hledání][14]

Všimněte si, že byly použity struktura pole odpovídající účet pro službu "Síť", opravit problém s chybějící značky. Také jsme naplněno pole struktura účtu pro prostředky než naše cíle skupinu prostředků s "Ostatní", aby je bylo možné odlišit na sestavy.

Nyní potřebujeme přidejte krok k publikování dat o využití. V tomto kroku odpovídající sazby u každé služby definované v našich míra plánování použijí na údaje o využití, s výsledné zdarma do databáze.

Nejlepší je, že máte jenom jednou projít tento proces. Po dokončení sešit, stačí přidat do Plánovač a běží hodinové nebo denní v naplánovaném čase. Potom ji stačí vytvoření nové sestavy, nebo přizpůsobení existujících, aby bylo možné analyzovat data a získat smysluplné přehledy z použití cloudové.

### <a name="next-steps"></a>Další kroky
* Podrobné pokyny pro vytvoření cloudu Cruiser sešitů a sestav, naleznete v cloudu Cruiser online [dokumentace](http://docs.cloudcruiser.com/) (vyžaduje se platné přihlášení).  Obraťte se na další informace o cloudu Cruiser [ info@cloudcruiser.com ](mailto:info@cloudcruiser.com).
* V tématu [proniknout do vaší spotřeby prostředků Microsoft Azure](billing-usage-rate-card-overview.md) přehled informací o rozhraní API RateCard a využití prostředků Azure.
* Podívejte se [referenční dokumentace rozhraní API Azure fakturace REST](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) Další informace o obou rozhraní API, které jsou součástí sadu rozhraní API zadaná pomocí Správce prostředků Azure.
* Pokud chcete pusťte do ukázkový kód, podívejte se na naše Microsoft Azure Billing API ukázky kódu na [ukázky kódu Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Další informace
* Další informace o Azure Resource Manager, najdete v článku [přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) článku.

<!--Image references-->

[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Obrázek 1 – Vytvoření nové kolekce"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Obrázek 2 – importovat data z nové kolekce"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Obrázek 3 – postup transformace zpracovat shromážděná data z rozhraní API RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Obrázek 4 – publikování dat z rozhraní API RateCard jako nové služby a sazby"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Obrázek 5 – ověření nových služeb"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Obrázek 6 – ověření nový plán rychlost a přidružené sazby"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Obrázek 7: transformace dat WAP k normalizaci služby"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Obrázek 8 - sešitu plánování"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Obrázek 9 – ukázková sestava pro scénář porovnání náklady pracovního vytížení"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Obrázek 10 - sestava s členění pomocí značek"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Obrázek 11 – skupina prostředků se přidružených značek na portálu Azure"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Obrázek 12 - list UsageAPI importovat data o využití rozhraní API"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Obrázek 13 – vytvořit nové pole informace značky"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Obrázek 14 - naplnění strukturu účet s informacemi z hledání"
