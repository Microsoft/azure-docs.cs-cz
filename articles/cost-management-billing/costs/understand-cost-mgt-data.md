---
title: Vysvětlení dat služby Azure Cost Management
description: Tento článek vám pomůže lépe porozumět datům, která obsahuje služba Azure Cost Management, a tomu, jak často se zpracovávají, shromažďují, zobrazují a uzavírají.
author: bandersmsft
ms.author: banders
ms.date: 01/17/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: contperf-fy21q2
ms.openlocfilehash: 568f3d811876073dc899204cb8ca4d1753d9cfd0
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499291"
---
# <a name="understand-cost-management-data"></a>Vysvětlení dat služby Cost Management

Tento článek vám pomůže lépe porozumět datům o využití a nákladech za Azure, která jsou zahrnuta ve službě Azure Cost Management. Vysvětluje, jak často se data zpracovávají, shromažďují, zobrazují a uzavírají. Za využití Azure se vám účtuje měsíčně. Přestože fakturační cykly probíhají měsíčně, počáteční a koncové datum cyklu se liší podle typu předplatného. To, jak často služba Cost Management získává data o využití, se liší v závislosti na různých faktorech. Mezi tyto faktory patří doba, po kterou se zpracovávají data, a to, jak často služby Azure generují využití do fakturačního systému.

Cost Management zahrnuje veškeré využití a nákupy, včetně rezervací a nabídek třetích stran, pro účty pod smlouvou Enterprise (EA). Účty Smlouvy se zákazníkem Microsoftu a jednotlivá předplatná s tarify průběžných plateb zahrnují pouze využití ze služeb Azure a Marketplace. Podpora a další náklady zahrnuty nejsou. Až do vygenerování faktury se náklady pouze odhadují a nezohledňují kredity.

Pokud máte nové předplatné, nemůžete rovnou využívat funkce služby Cost Management. Může trvat až 48 hodin, než budete moct využívat všechny funkce služby Cost Management.

## <a name="supported-microsoft-azure-offers"></a>Podporované nabídky Microsoft Azure

Následující informace zobrazují aktuálně podporované [nabídky Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) ve službě Azure Cost Management. Nabídka Azure je typ předplatného Azure, které máte. Data jsou dostupná ve službě Cost Management od data uvedeného ve sloupci **Dostupnost dat od**. Pokud předplatné změní nabídky, náklady před datem změny nabídky nejsou dostupné.

| **Kategorie**  | **Název nabídky** | **ID kvóty** | **Číslo nabídky** | **Dostupnost dat od** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Květen 2014<sup>1</sup> |
| **Azure Government** | Průběžné platby za Azure Government | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P | 2\. října 2018<sup>2</sup> |
| **Smlouva Enterprise (EA)** | Enterprise pro vývoj/testování                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Květen 2014<sup>1</sup> |
| **Smlouva Enterprise (EA)** | Microsoft Azure Enterprise | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Květen 2014<sup>1</sup> |
| **Smlouva se zákazníkem Microsoftu** | Plán Microsoft Azure | EnterpriseAgreement_2014-09-01 | – | Březen 2019<sup>3</sup> |
| **Smlouva se zákazníkem Microsoftu** | Plán Microsoft Azure pro vývoj a testování | MSDNDevTest_2014-09-01 | – | Březen 2019<sup>3</sup> |
| **Smlouva se zákazníkem Microsoftu podporovaná partnery** | Plán Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01 a CSPDEVTEST_2018-05-01<br><br>ID kvóty se opakovaně používá pro Smlouvu se zákazníkem Microsoftu a starší předplatná CSP. Momentálně se podporují pouze předplatná Smlouvy se zákazníkem Microsoftu. | – | Říjen 2019 |
| **Microsoft Developer Network (MSDN)** | MSDN Platforms<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | Průběžné platby                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | Průběžné platby dle aktuálního využití pro vývoj/testování         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | Microsoft Partner Network      | MPN_2014-09-01 | MS-AZR-0025P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | Bezplatná zkušební verze<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | Azure v rámci licenčního programu Open<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P – MS-AZR-0125P, MS-AZR-0128P – MS-AZR-0130P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise – MPN<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Professional<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Test Professional<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise: BizSpark<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2\. října 2018<sup>2</sup> |

_<sup>**1**</sup> Pokud jde od data před květnem 2014, přejděte na [portál Azure Enterprise](https://ea.azure.com)._

_<sup>**2**</sup> pro data do 2. října 2018 navštivte [centrum účtů Azure](https://account.azure.com/subscriptions) pro globální účty a [centrum účtů Azure gov](https://account.windowsazure.us/subscriptions) pro účty státní správy Azure._

_<sup>**3**</sup> Smlouvy se zákazníkem Microsoft začaly platit od března 2019 a před tímto datem nemají žádná historická data._

_<sup>**4**</sup> Historická data pro předplatná založená na kreditech a platbách předem nemusí odpovídat vaší faktuře. Viz část [Historická data nemusí odpovídat faktuře](#historical-data-might-not-match-invoice) níže._

Následující nabídky se dosud nepodporují:

| Kategorie  | **Název nabídky** | **ID kvóty** | **Číslo nabídky** |
| --- | --- | --- | --- |
| **Azure (Německo)** | Průběžné platby za Azure (Německo) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **CSP (Cloud Solution Provider)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **CSP (Cloud Solution Provider)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **CSP (Cloud Solution Provider)** | Azure (Německo) v CSP pro Microsoft Cloud Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Průběžné platby**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Průběžné platby** | Azure for Students<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Průběžné platby**                 | Microsoft Azure Sponsorship | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Plány podpory** | Podpora Standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Plány podpory** | Podpora Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Plány podpory** | Podpora Developer                   | Default_2014-09-01 | MS-AZR-0043P |
| **Plány podpory** | Plán podpory pro Německo                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Plány podpory** | Azure Government Standard Support   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Plány podpory** | Azure Government Pro-Direct Support | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Plány podpory** | Azure Government Developer Support  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

### <a name="free-trial-to-pay-as-you-go-upgrade"></a>Upgrade bezplatné zkušební verze na průběžné platby

Informace o dostupnosti služeb úrovně Free po upgradu z bezplatné zkušební verze na ceny při průběžných platbách najdete v tématu věnovaném [nejčastějším dotazům k bezplatnému účtu Azure](https://azure.microsoft.com/free/free-account-faq/).

### <a name="determine-your-offer-type"></a>Určení typu nabídky

Pokud nevidíte data pro předplatné a chcete určit, zda vaše předplatné spadá do podporovaných nabídek, můžete si případnou podporu svého předplatného ověřit. Pokud chcete ověřit, zda se předplatné Azure podporuje, přihlaste se k webu Azure Portal. V levém podokně nabídky vyberte **Všechny služby**. V seznamu služeb vyberte **Předplatná**. V nabídce se seznamem vyberte předplatné, které chcete ověřit. Vaše předplatné se zobrazuje na kartě Přehled, kde je uvedena také **nabídka** a **ID nabídky**. Příklad ukazuje následující obrázek.

![Příklad karty s přehledem a vyznačenou nabídkou a ID nabídky](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Náklady zahrnuté ve službě Cost Management

V následujících tabulkách se zobrazují data, která jsou nebo nejsou zahrnuta ve službě Cost Management. Až do vygenerování faktury se náklady pouze odhadují. Zobrazené náklady nezahrnují bezplatné a předplacené kredity.

| **Zahrnuto** | **Nezahrnuto** |
| --- | --- |
| Využití služby Azure<sup>5</sup>        | Poplatky za podporu – další informace najdete v tématu [Vysvětlení podmínek fakturace](../understand/understand-invoice.md). |
| Využití nabídky Marketplace<sup>6</sup> | Daně – další informace najdete v tématu [Vysvětlení podmínek fakturace](../understand/understand-invoice.md). |
| Nákupy na Marketplace<sup>6</sup>      | Kredity – další informace najdete v tématu [Vysvětlení podmínek fakturace](../understand/understand-invoice.md). |
| Nákupy rezervací<sup>7</sup>      |  |
| Amortizace nákupů rezervací<sup>7</sup>      |  |

_<sup>**5**</sup> Využití služby Azure je založeno na rezervacích a sjednaných cenách._

_<sup>**6**</sup> Nákupy na Marketplace nejsou v současné době dostupné pro nabídky MSDN a sady Visual Studio._

_<sup>**7**</sup> Nákupy rezervací jsou v současné době dostupné pouze pro účty se smlouvou Enterprise (EA) a účty se smlouvou se zákazníkem Microsoftu._

## <a name="how-tags-are-used-in-cost-and-usage-data"></a>Způsob použití značek v datech o nákladech a využití

Azure Cost Management přijímá značky jako součást jednotlivých záznamů o využití odesílaných jednotlivými službami. Pro tyto značky platí následující omezení:

- Značky musí být použité přímo na prostředky a implicitně se nedědí z nadřazené skupiny prostředků.
- Značky prostředků se podporují jenom pro prostředky nasazené do skupin prostředků.
- Některé nasazené prostředky nemusí podporovat značky nebo nemusí vkládat značky do dat o využití.
- Značky prostředků jsou zahrnuté jenom v datech o využití, když se značka používá. Značky se neaplikují na historická data.
- Značky prostředků jsou ve službě Cost Management dostupné jenom po aktualizaci dat.
- Značky prostředků jsou ve službě Cost Management dostupné, jenom pokud je prostředek aktivní nebo spuštěný a vytváří záznamy o využití. Například v případě uvolněného virtuálního počítače.
- Správa značek vyžaduje přístup přispěvatele ke každému prostředku.
- Správa zásad značek vyžaduje přístup buď vlastníka, nebo přispěvatele zásad ke skupině pro správu, předplatnému nebo skupině prostředků.
    
Pokud ve službě Cost Management určitou značku nevidíte, zvažte následující okolnosti:

- Použila se značka přímo na prostředek?
- Použila se značka před více než 24 hodinami?
- Podporuje typ prostředku značky? Následující typy prostředků nepodporují značky v datech o využití od 1. prosince 2019. Úplný seznam toho, co se podporuje, najdete v tématu [Podpora značek pro prostředky Azure](../../azure-resource-manager/management/tag-support.md).
    - Adresáře Azure Active Directory B2C
    - Azure Bastion
    - Brány Azure Firewall
    - Azure NetApp Files
    - Data Factory
    - Databricks
    - Nástroje pro vyrovnávání zatížení
    - Výpočetní instance Machine Learning pracovního prostoru
    - Network Watcher
    - Notification Hubs
    - Service Bus
    - Time Series Insights
    
Tady je několik tipů pro práci se značkami:

- Plánujte dopředu a definujte strategii označování, která vám umožní rozdělit náklady podle organizace, aplikace, prostředí atd.
- Použijte Azure Policy ke kopírování značek skupin prostředků do jednotlivých prostředků a vynucení strategie označování.
- Použijte rozhraní API pro značky ve spojení s dotazem nebo podrobnostmi o využití a získejte všechny náklady na základě aktuálních značek.

## <a name="cost-and-usage-data-updates-and-retention"></a>Aktualizace a uchovávání údajů o využití a nákladech

Data o nákladech a využití jsou obvykle dostupná v části Správa nákladů a fakturace na webu Azure Portal a v podpůrných rozhraních API během 8 až 24 hodin. Při kontrole nákladů byste měli brát v úvahu následující pravidla:

- Jednotlivé služby Azure (například Storage, Compute a SQL) vydávají údaje o využití v různých intervalech – data některých služeb se mohou zobrazit dřív než data jiných.
- Odhadované poplatky pro aktuální fakturační období se aktualizují šestkrát denně.
- Odhadované poplatky pro aktuální fakturační období se mohou změnit s tím, jak generujete větší využití.
- Každá aktualizace je kumulativní a zahrnuje všechny řádkové položky a informace z předchozí aktualizace.
- Azure finalizuje, neboli _uzavře_, aktuální fakturační období až 72 hodin (tři kalendářní dny) po skončení fakturačního období.
- V období aktuálního otevřeného měsíce (nefakturovaného) by se údaje správy nákladů měly považovat jenom za odhady. V některých případech může k zaznamenání poplatků do systému dojít nějakou dobu poté, co skutečně došlo k využití.

Následující příklady ilustrují to, jak by mohla fakturační období skončit:

* Předplatná smlouvy Enterprise (EA) – pokud fakturační měsíc končí 31. března, odhadované poplatky se aktualizují až o 72 hodin později. V tomto příkladu k tomu dojde 4. dubna o půlnoci (UTC).
* Předplatná s průběžnými platbami – pokud fakturační měsíc končí 15. května, odhadované poplatky by se mohly aktualizovat až o 72 hodin později. V tomto příkladu by k tomu došlo 19. května o půlnoci (UTC).

Jakmile jsou údaje o nákladech a využití k dispozici v modulu Cost Management + Billing, budou se uchovávat nejméně sedm let.

### <a name="rerated-data"></a>Opakovaně vyhodnocená data

Bez ohledu na to, jestli k načtení dat používáte rozhraní API Cost Management, Power BI nebo Azure Portal, můžete očekávat, že se aktuální poplatky za fakturační období mají přepočítat. Poplatky se mohou změnit až do uzavření faktury.

## <a name="cost-rounding"></a>Zaokrouhlení nákladů

Náklady uvedené ve službě Cost Management jsou zaokrouhlené. Náklady vracené rozhraním API pro dotazy se nezaokrouhlují. Příklad:

- Analýza nákladů na webu Azure Portal: Poplatky se zaokrouhlují s využitím standardních zaokrouhlovacích pravidel: hodnoty větší než 0,5 se zaokrouhlují nahoru, jinak se náklady zaokrouhlují dolů. K zaokrouhlování dochází jenom při zobrazování hodnot. Během zpracování a agregace dat se zaokrouhlování nepoužívá. Při analýze se náklady agregují následujícím způsobem:
  -    Poplatek 1: 0,004 USD
  - Poplatek 2: 0,004 USD
  -    Vyčíslený agregovaný poplatek: 0,004 + 0,004 = 0,008. Poplatek se zobrazí jako 0,01 USD.
- Rozhraní API pro dotazy: Poplatky se zobrazují na osm desetinných míst a nezaokrouhlují se.

## <a name="historical-data-might-not-match-invoice"></a>Historická data nemusí odpovídat faktuře

Historická data pro nabídky založené na kreditech a platbách předem nemusí odpovídat vaší faktuře. Některé nabídky průběžných plateb, MSDN a sady Visual Studio mohou mít na faktuře uvedeny kredity Azure a předplacené částky. Historická data uvedená v Cost Management jsou založená jenom na odhadovaných nákladech za spotřebu. Historická data Cost Management nezahrnují platby a kredity. Historická data zobrazená pro následující nabídky nemusí přesně odpovídat vaší faktuře.

- Azure for Students (MS-AZR-0170P)
- Azure v rámci licenčního programu Open License (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Bezplatná zkušební verze (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="next-steps"></a>Další kroky

- Pokud jste si ještě neprošli úvodní příručku pro Cost Management, najdete ji v tématu [Začínáme s analýzou nákladů](./quick-acm-cost-analysis.md).
