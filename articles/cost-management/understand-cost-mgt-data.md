---
title: Pochopení Azure Cost Management dat | Microsoft Docs
description: Tento článek vám pomůže lépe porozumět datům, která jsou součástí Azure Cost Management a jak často je zpracovává, shromažďuje, zobrazuje a uzavřel.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 8aaaa6465b501cee83f4c2d8cb60729282651967
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384847"
---
# <a name="understand-cost-management-data"></a>Vysvětlení dat služby Cost Management

Tento článek vám pomůže lépe porozumět nákladům a datům o využití Azure, které jsou součástí Azure Cost Management. Vysvětluje, jak často se data zpracovávají, shromažďují, zobrazují a uzavřela. Účtují se za využití Azure za měsíc. I když fakturační cykly jsou měsíční období, počáteční a koncové datum cyklu se liší podle typu předplatného. Jak často Cost Management přijímat data o využití se liší v závislosti na různých faktorech. Tyto faktory zahrnují, jak dlouho trvá zpracování dat a jak často služby Azure emitují využití do fakturačního systému.

Cost Management zahrnuje veškeré využití a nákupy, včetně rezervací a nabídek třetích stran pro účty smlouva Enterprise (EA). Účty Microsoft Custom Agreement (MCA) a jednotlivé předplatné s tarify průběžných plateb zahrnují jenom využití ze služeb Azure a Marketplace. Není zahrnutá podpora a další náklady. Náklady jsou odhadované, dokud se nevygeneruje faktura a nebere v kreditech žádný faktor.

## <a name="supported-microsoft-azure-offers"></a>Podporované nabídky Microsoft Azure

Následující informace zobrazují aktuálně podporované [Microsoft Azure nabídky](https://azure.microsoft.com/support/legal/offer-details/) v Azure cost management. Nabídka Azure je typ předplatného Azure, které máte. Data jsou k dispozici v Cost Management od **data k dispozici** . Pokud se změní předplatné, nebudou k dispozici náklady ještě před datem změny nabídky. 

| **Kategorie**  | **Název nabídky** | **ID kvóty** | **Číslo nabídky** | **Data dostupná z** |
| --- | --- | --- | --- | --- |
| **Azure Německo** | [Azure Německo – průběžné platby](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P | 2\. října 2018<sup>2</sup> |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Květen 2014<sup>1</sup> |
| **Smlouva Enterprise (EA)** | Enterprise pro vývoj/testování                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Květen 2014<sup>1</sup> |
| **Smlouva Enterprise (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Květen 2014<sup>1</sup> |
| **Smlouva o zákaznících Microsoftu** | [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | Není k dispozici | Březen 2019<sup>3</sup> |
| **Smlouva o zákaznících Microsoftu** | [Microsoft Azure plán pro vývoj a testování](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | Není k dispozici | Březen 2019<sup>3</sup> |
| **Microsoft Developer Network (MSDN)** | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p) <sup>4</sup> . | MSDN_2014-09-01 | MS-AZR-0062P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Průběžné platby dle aktuálního využití pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Bezplatná zkušební verze](https://azure.microsoft.com/offers/ms-azr-0044p) <sup>4</sup> .         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Systém Azure v rámci licenčního programu Open](https://azure.microsoft.com/offers/ms-azr-0111p) <sup>4</sup> .      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | [Azure pro studenty](https://azure.microsoft.com/offers/ms-azr-0170p) <sup>4</sup> . | AzureForStudents_2018-01-01 | MS-AZR-0170P | 2\. října 2018<sup>2</sup> |
| **Průběžné platby** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p) <sup>4</sup> .     | MPN_2014-09-01 | MS-AZR-0029P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2\. října 2018<sup>2</sup> |

_<sup>**1**</sup> pro data před 1. května 2014 navštivte [portál Azure Enterprise](https://ea.azure.com)._

_<sup>**2**</sup> pro data do 2. října 2018 navštivte [centrum účtů Azure](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> smlouvy o zákaznících Microsoftu zahájené v březen 2019 a neobsahují historická data před tímto bodem._

_<sup>**4**</sup> historická data pro předplatná založená na kreditech a platbách předem nemusí odpovídat vaší faktuře. Zobrazit [historická data nemusí odpovídat](#historical-data-might-not-match-invoice) níže uvedené faktuře._

Následující nabídky ještě nejsou podporované:

| Kategorie  | **Název nabídky** | **ID kvóty** | **Číslo nabídky** |
| --- | --- | --- | --- |
| **Azure Německo** | [Azure Německo – průběžné platby](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Poskytovatel Cloud Solution Provider (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Poskytovatel Cloud Solution Provider (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Poskytovatel Cloud Solution Provider (CSP)** | Azure Germany v CSP pro Microsoft Cloud Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Průběžné platby**                 | Azure for Students Starter | DreamSpark_2015 – 02 – 01 | MS-AZR-0144P |
| **Průběžné platby**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Plány podpory** | Standard Support                    | Default_2014-09-01 | MS-AZR-0041P |
| **Plány podpory** | Podpora Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Plány podpory** | Podpora pro vývojáře                   | Default_2014-09-01 | MS-AZR-0043P |
| **Plány podpory** | Plán podpory pro Německo                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Plány podpory** | Azure Government Standard Support   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Plány podpory** | Azure Government pro – Direct support | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Plány podpory** | Azure Government Developer Support  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Určení typu nabídky
Pokud nevidíte data pro předplatné a chcete zjistit, jestli vaše předplatné spadá do podporovaných nabídek, můžete ověřit, jestli je vaše předplatné podporované. Pokud chcete ověřit, jestli je předplatné Azure podporované, přihlaste se k [Azure Portal](https://portal.azure.com). Pak v levém podokně nabídky vyberte **všechny služby** . V seznamu služeb vyberte předplatná. V nabídce seznam předplatných klikněte na odběr, který chcete ověřit. Vaše předplatné se zobrazí na kartě Přehled, kde můžete zobrazit **nabídku** a **ID nabídky**. Příklad ukazuje následující obrázek.

![Příklad karty Přehled předplatného zobrazující nabídku a ID nabídky](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Náklady zahrnuté v Cost Management

V následujících tabulkách jsou uvedena data, která jsou součástí Cost Management. Všechny náklady jsou odhadované až do vygenerování faktury. Uvedené náklady nezahrnují bezplatné a předem placené kredity.

**Náklady a data o využití**

| **Obsaženy** | **Nezahrnuto** |
| --- | --- |
| Využití služby Azure<sup>5</sup>        | Poplatky za podporu – Další informace najdete v tématu [vysvětlení podmínek faktury](../billing/billing-understand-your-invoice.md). |
| Marketplace – využití nabídky<sup>6</sup> | Daně – Další informace najdete v tématu [vysvětlení podmínek faktury](../billing/billing-understand-your-invoice.md). |
| Nákupy na Marketplace<sup>6</sup>      | Kredity – Další informace najdete v tématu [vysvětlení podmínek faktury](../billing/billing-understand-your-invoice.md). |
| Nákupy na rezervované<sup>7</sup>      |  |
| Amortizace nákupních rezervací<sup>7</sup>      |  |

_<sup>**5**</sup> využití služeb Azure je založené na rezervacích a sjednaných cenách._

_<sup>**6**</sup> nákupů na tržišti není v tuto chvíli k dispozici pro nabídky s průběžnými platbami, MSDN a Visual Studio._

_<sup>**7**</sup> nákupy rezervací jsou v tuto chvíli dostupné jenom pro účty smlouva Enterprise (EA)._

**Metadata**

| **Obsaženy** | **Nezahrnuto** |
| --- | --- |
| Značky prostředků<sup>8</sup> | Značky skupiny prostředků |

_<sup>**8**</sup> značek prostředků se používá, protože použití je emitováno z každé služby a není dostupné zpětně k historickému využití._

## <a name="rated-usage-data-refresh-schedule"></a>Plán aktualizace dat o hodnocení využití

Data o nákladech a využití jsou dostupná ve službě Cost Management + fakturace v Azure Portal a v [podpůrných rozhraních API](index.yml). Při kontrole nákladů mějte na paměti následující body:

- Odhadované poplatky za aktuální fakturační období se aktualizují po dobu šesti dní za den.
- Odhadované poplatky za aktuální fakturační období se můžou změnit, protože se vám bude účtovat víc využívání.
- Každá aktualizace je kumulativní a obsahuje všechny položky řádku a informace z předchozí aktualizace.
- Azure dokončí nebo _uzavře_ aktuální fakturační období až do 72 hodin (tři kalendářní dny) po skončení fakturačního období.

Následující příklady znázorňují způsob, jakým mohou být fakturační období ukončeny.

Předplatná smlouva Enterprise (EA) – Pokud se fakturační měsíc končí 31. března, odhadované poplatky se aktualizují až 72 hodin později. V tomto příkladu podle půlnoci (UTC) 4. dubna.

Předplatná s průběžnými platbami – Pokud se fakturační měsíc ukončí 15. května, odhadované poplatky se můžou aktualizovat až 72 hodin později. V tomto příkladu může půlnoc (UTC) 19.

### <a name="rerated-data"></a>Přehodnocená data

Bez ohledu na to, jestli k načtení dat používáte [rozhraní api Cost Management](index.yml)Power BI nebo Azure Portal, můžete očekávat, že se aktuální poplatky za fakturační období mají přepočítat a následně se změnit, dokud se faktura nezavře.

## <a name="usage-data-update-frequency-varies"></a>Frekvence aktualizace dat využití se liší.

Dostupnost dat o využití v Cost Management závisí na několika faktorech, včetně:

- Jak často služby Azure (například úložiště, výpočetní prostředky, CDN a SQL) generují využití.
- Čas potřebný ke zpracování dat o využití prostřednictvím modulu hodnocení a kanálů pro správu nákladů.

Některé služby emitují využití častěji než jiné. Můžete tak zobrazit data v Cost Management pro některé služby dřív než jiné služby, které generují data méně často. K zobrazení v Cost Management obvykle trvá použití služeb 8-24 hodin. Mějte na paměti, že data pro otevřený měsíc se budou aktualizovat, protože budete mít větší využití, protože aktualizace jsou kumulativní.

## <a name="historical-data-might-not-match-invoice"></a>Historická data se nemusí shodovat s fakturou.

Historická data pro nabídky na základě kreditu a platby se nemusí shodovat s vaší fakturou. Některé nabídky Azure s průběžnými platbami podle aktuálního využití, MSDN a Visual Studio můžou mít kredity Azure a na fakturu se vztahují rozšířené platby. Historická data uvedená v Cost Management však vycházejí jenom z odhadovaných poplatků za využití. Cost Management historická data nezahrnují platby a kredity. V důsledku toho historická data zobrazená pro následující nabídky nemusí přesně odpovídat vaší faktuře.

- Azure for Students (MS-AZR-0170P)
- Systém Azure v rámci licenčního programu Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Bezplatná zkušební verze (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Viz také:

- Pokud jste ještě nedokončili první tohoto rychlého startu Cost Management, přečtěte si ho na [začít analýza nákladů](quick-acm-cost-analysis.md).
