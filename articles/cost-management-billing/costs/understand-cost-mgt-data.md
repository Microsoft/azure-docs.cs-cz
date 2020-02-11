---
title: Vysvětlení dat služby Azure Cost Management | Microsoft Docs
description: Tento článek vám pomůže lépe porozumět datům, která obsahuje služba Azure Cost Management, a tomu, jak často se zpracovávají, shromažďují, zobrazují a uzavírají.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 156684676758d777231d3b159ba7bc4749b8582a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901757"
---
# <a name="understand-cost-management-data"></a>Vysvětlení dat služby Cost Management

Tento článek vám pomůže lépe porozumět datům o využití a nákladech za Azure, která jsou zahrnuta ve službě Azure Cost Management. Vysvětluje, jak často se data zpracovávají, shromažďují, zobrazují a uzavírají. Za využití Azure se vám účtuje měsíčně. Přestože fakturační cykly probíhají měsíčně, počáteční a koncové datum cyklu se liší podle typu předplatného. To, jak často služba Cost Management získává data o využití, se liší v závislosti na různých faktorech. Mezi tyto faktory patří doba, po kterou se zpracovávají data, a to, jak často služby Azure generují využití do fakturačního systému.

Cost Management zahrnuje veškeré využití a nákupy, včetně rezervací a nabídek třetích stran, pro účty pod smlouvou Enterprise (EA). Účty Smlouvy se zákazníkem Microsoftu a jednotlivá předplatná s tarify průběžných plateb zahrnují pouze využití ze služeb Azure a Marketplace. Podpora a další náklady zahrnuty nejsou. Až do vygenerování faktury se náklady pouze odhadují a nezohledňují kredity.

## <a name="supported-microsoft-azure-offers"></a>Podporované nabídky Microsoft Azure

Následující informace zobrazují aktuálně podporované [nabídky Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) ve službě Azure Cost Management. Nabídka Azure je typ předplatného Azure, které máte. Data jsou dostupná ve službě Cost Management od data uvedeného ve sloupci **Dostupnost dat od**. Pokud předplatné změní nabídky, náklady před datem změny nabídky nejsou dostupné.

| **Kategorie**  | **Název nabídky** | **ID kvóty** | **Číslo nabídky** | **Dostupnost dat od** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Květen 2014<sup>1</sup> |
| **Smlouva Enterprise (EA)** | Enterprise pro vývoj/testování                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Květen 2014<sup>1</sup> |
| **Smlouva Enterprise (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Květen 2014<sup>1</sup> |
| **Smlouva se zákazníkem Microsoftu** | [Plán Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | – | Březen 2019<sup>3</sup> |
| **Smlouva se zákazníkem Microsoftu** | [Plán Microsoft Azure pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | – | Březen 2019<sup>3</sup> |
| **Smlouva se zákazníkem Microsoftu podporovaná partnery** | Plán Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01 a CSPDEVTEST_2018-05-01<br><br>ID kvóty se opakovaně používá pro Smlouvu se zákazníkem Microsoftu a starší předplatná CSP. Momentálně se podporují pouze předplatná Smlouvy se zákazníkem Microsoftu. | – | Říjen 2019 |
| **Microsoft Developer Network (MSDN)** | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Průběžné platby dle aktuálního využití pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Bezplatná zkušební verze](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Azure v rámci licenčního programu Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P – MS-AZR-0125P, MS-AZR-0128P – MS-AZR-0130P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2\. října 2018<sup>2</sup> |

_<sup>**1**</sup> Pokud jde od data před květnem 2014, přejděte na [portál Azure Enterprise](https://ea.azure.com)._

_<sup>**2**</sup> Pokud jde o data před 2. říjnem 2018, navštivte [Centrum účtů Azure](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> Smlouvy se zákazníkem Microsoft začaly platit od března 2019 a před tímto datem nemají žádná historická data._

_<sup>**4**</sup> Historická data pro předplatná založená na kreditech a platbách předem nemusí odpovídat vaší faktuře. Viz část [Historická data nemusí odpovídat faktuře](#historical-data-might-not-match-invoice) níže._

Následující nabídky se dosud nepodporují:

| Kategorie  | **Název nabídky** | **ID kvóty** | **Číslo nabídky** |
| --- | --- | --- | --- |
| **Azure (Německo)** | [Průběžné platby za Azure (Německo)](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Průběžné platby za Azure Government | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **CSP (Cloud Solution Provider)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **CSP (Cloud Solution Provider)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **CSP (Cloud Solution Provider)** | Azure (Německo) v CSP pro Microsoft Cloud Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Průběžné platby**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Průběžné platby** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Průběžné platby**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Plány podpory** | Podpora Standard                    | Default_2014-09-01 | MS-AZR-0041P |
| **Plány podpory** | Podpora Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Plány podpory** | Podpora Developer                   | Default_2014-09-01 | MS-AZR-0043P |
| **Plány podpory** | Plán podpory pro Německo                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Plány podpory** | Azure Government Standard Support   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Plány podpory** | Azure Government Pro-Direct Support | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Plány podpory** | Azure Government Developer Support  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Určení typu nabídky
Pokud nevidíte data pro předplatné a chcete určit, zda vaše předplatné spadá do podporovaných nabídek, můžete si případnou podporu svého předplatného ověřit. Pokud chcete ověřit, zda se předplatné Azure podporuje, přihlaste se k webu [Azure Portal](https://portal.azure.com). V levém podokně nabídky vyberte **Všechny služby**. V seznamu služeb vyberte **Předplatná**. V nabídce se seznamem vyberte předplatné, které chcete ověřit. Vaše předplatné se zobrazuje na kartě Přehled, kde je uvedena také **nabídka** a **ID nabídky**. Příklad ukazuje následující obrázek.

![Příklad karty s přehledem a vyznačenou nabídkou a ID nabídky](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Náklady zahrnuté ve službě Cost Management

V následujících tabulkách se zobrazují data, která jsou nebo nejsou zahrnuta ve službě Cost Management. Až do vygenerování faktury se náklady pouze odhadují. Zobrazené náklady nezahrnují bezplatné a předplacené kredity.

**Data o nákladech a využití**

| **Zahrnuto** | **Nezahrnuto** |
| --- | --- |
| Využití služby Azure<sup>5</sup>        | Poplatky za podporu – další informace najdete v tématu [Vysvětlení podmínek fakturace](../understand/understand-invoice.md). |
| Využití nabídky Marketplace<sup>6</sup> | Daně – další informace najdete v tématu [Vysvětlení podmínek fakturace](../understand/understand-invoice.md). |
| Nákupy na Marketplace<sup>6</sup>      | Kredity – další informace najdete v tématu [Vysvětlení podmínek fakturace](../understand/understand-invoice.md). |
| Nákupy rezervací<sup>7</sup>      |  |
| Amortizace nákupů rezervací<sup>7</sup>      |  |

_<sup>**5**</sup> Využití služby Azure je založeno na rezervacích a sjednaných cenách._

_<sup>**6**</sup> Nákupy na Marketplace nejsou momentálně dostupné pro nabídky průběžných plateb, MSDN a Visual Studio._

_<sup>**7**</sup> Nákupy rezervací jsou momentálně dostupné pouze pro účty smlouvy Enterprise (EA)._

**Metadata**

| **Zahrnuto** | **Nezahrnuto** |
| --- | --- |
| Značky prostředků<sup>8</sup> | Značky skupiny prostředků |

_<sup>**8**</sup> Značky prostředků použité při využití se generují z každé služby a nejsou dostupné zpětně u historického využití._

**Upgrade bezplatné zkušební verze na nabídku průběžných plateb**

Zákazníci s nabídkou bezplatné zkušební verze (044P), kteří přecházejí na nabídku PAYG (003P), uvidí svoje využití během bezplatného zkušebního období. Po převodu ale o možnost zobrazení využití bezplatné zkušební nabídky přijdou. Po převodu se ve službě Cost Management budou zobrazovat pouze využití a náklady PAYG.

## <a name="rated-usage-data-refresh-schedule"></a>Plán aktualizace vyhodnocených dat o využití

Data o nákladech a využití jsou dostupná v části Správa nákladů a fakturace na webu Azure Portal a v [podpůrných rozhraních API](../index.yml). Při kontrole nákladů byste měli brát v úvahu následující pravidla:

- Odhadované poplatky pro aktuální fakturační období se aktualizují šestkrát denně.
- Odhadované poplatky pro aktuální fakturační období se mohou změnit s tím, jak generujete větší využití.
- Každá aktualizace je kumulativní a zahrnuje všechny řádkové položky a informace z předchozí aktualizace.
- Azure finalizuje, neboli _uzavře_, aktuální fakturační období až 72 hodin (tři kalendářní dny) po skončení fakturačního období.

Následující příklady ilustrují to, jak by mohla fakturační období skončit.

Předplatná smlouvy Enterprise (EA) – pokud fakturační měsíc končí 31. března, odhadované poplatky se aktualizují až o 72 hodin později. V tomto příkladu k tomu dojde 4. dubna o půlnoci (UTC).

Předplatná s průběžnými platbami – pokud fakturační měsíc končí 15. května, odhadované poplatky by se mohly aktualizovat až o 72 hodin později. V tomto příkladu by k tomu došlo 19. května o půlnoci (UTC).

### <a name="rerated-data"></a>Opakovaně vyhodnocená data

Ať už data načítáte pomocí [rozhraní API služby Cost Management](../index.yml), Power BI nebo webu Azure Portal, počítejte s tím, že poplatky pro aktuální fakturační období se přepočítávají a neustále mění až do uzavření faktury.

## <a name="usage-data-update-frequency-varies"></a>Rozdíly ve frekvenci aktualizace dat o využití

Dostupnost vygenerovaných dat o využití ve službě Cost Management závisí na několika faktorech, včetně těchto:

- Jak často služby Azure (například Storage, Compute, CDN a SQL) generují využití.
- Délka doby zpracování dat využití prostřednictvím modulu hodnocení a kanálu správy nákladů.

Některé služby generují využití častěji než jiné. U některých služeb tak můžete ve službě Cost Management vidět data dříve než u jiných služeb, které generují data méně často. Zobrazení dat o využití služeb ve službě Cost Management obvykle trvá 8–24 hodin. Mějte na paměti, že data pro otevřený měsíc se aktualizují s tím, jak generujete další využití, protože aktualizace jsou kumulativní.

## <a name="historical-data-might-not-match-invoice"></a>Historická data nemusí odpovídat faktuře

Historická data pro nabídky založené na kreditech a platbách předem nemusí odpovídat vaší faktuře. Některé nabídky průběžných plateb, MSDN a sady Visual Studio mohou mít na faktuře uvedeny kredity Azure a předplacené částky. Historická data zobrazená ve službě Cost Management jsou ale založena pouze na odhadovaných poplatcích za využití. Historická data Cost Management nezahrnují platby a kredity. Historická data zobrazená u následujících nabídek nemusí přesně odpovídat faktuře.

- Azure for Students (MS-AZR-0170P)
- Azure v rámci licenčního programu Open License (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Bezplatná zkušební verze (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Viz také

- Pokud jste si ještě neprošli úvodní příručku pro Cost Management, najdete ji v tématu [Začínáme s analýzou nákladů](../../cost-management/quick-acm-cost-analysis.md).
