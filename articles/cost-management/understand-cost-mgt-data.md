---
title: Pochopení dat Azure Cost Management | Dokumentace Microsoftu
description: Tento článek vám pomůže líp pochopit jaká data je zahrnutá ve službě Azure Cost Management a jak často byl zpracován, shromážděná, zobrazené a uzavřené.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 44b95c92f51ca9782fca492f3dec3142087ecc91
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797025"
---
# <a name="understand-cost-management-data"></a>Vysvětlení dat služby Cost Management

Tento článek vám pomůže líp pochopit Azure nákladů a využití dat, která je zahrnutá ve službě Azure Cost Management. Vysvětluje, jak často zpracování dat jsou shromažďovány, zobrazené a uzavřené. Bude se vám účtovat využití Azure každý měsíc. I když je fakturační cykly jsou měsíčního období, cyklu počáteční a koncové datum se liší podle typu předplatného. Jak často Cost Management přijímá využití dat se liší na základě různých faktorů. Tyto faktory zahrnují jak dlouho trvá zpracování dat a jak často služeb Azure generování informací o využití a fakturace systému.

Služba Cost Management obsahuje veškeré použití a nákup, včetně rezervace a nabídky třetí strany pro účty Enterprise Agreement (EA). Smlouvy zákazníka Microsoftu (MCA) účtů a jednotlivých předplatných s průběžnými sazbami zahrnout pouze pro použití služby Azure a Web Marketplace. Podpora a další náklady nejsou zahrnuty. Náklady se odhadují, dokud se vygeneruje faktury a není zvážit kredity.

## <a name="supported-microsoft-azure-offers"></a>Podporované nabídky Microsoft Azure

Tyto informace zobrazí aktuálně podporované [Microsoft Azure nabízí](https://azure.microsoft.com/support/legal/offer-details/) ve službě Azure Cost Management. Nabídky Azure je typ, který máte předplatné Azure. Data jsou k dispozici ve službě Cost Management od **nejsou k dispozici Data** datum. Pokud se předplatné změní nabídky, náklady než datum změny nabídky nebude k dispozici. 

| **Kategorie**  | **Název nabídky** | **ID kvóty** | **Číslo nabídky** | **Nejsou k dispozici data** |
| --- | --- | --- | --- | --- |
| **Azure Germany** | [Azure Germany – průběžné platby](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P | 2\. října 2018<sup>2</sup> |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Květen 2014<sup>1</sup> |
| **Smlouva Enterprise (EA)** | Enterprise pro vývoj/testování                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Květen 2014<sup>1</sup> |
| **Smlouva Enterprise (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Květen 2014<sup>1</sup> |
| **Smlouva zákazníků společnosti Microsoft** | [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | Není k dispozici | 2019 března<sup>3</sup> |
| **Smlouva zákazníků společnosti Microsoft** | [Plán Microsoft Azure pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | Není k dispozici | 2019 března<sup>3</sup> |
| **Microsoft Developer Network (MSDN)** | [Předplatné MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2\. října 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2\. října 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Vývoj/testování s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2\. října 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2\. října 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Bezplatná zkušební verze](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2\. října 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Azure v programu Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2\. října 2018<sup>2</sup> |
| **Pay-As-You-Go** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P | 2\. října 2018<sup>2</sup> |
| **Pay-As-You-Go** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2\. října 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2\. října 2018<sup>2</sup> |

_<sup>**1** </sup> data před. května 2014, přejděte [Azure Enterprise portal](https://ea.azure.com)._

_<sup>**2** </sup> data před 2. října 2018, přejděte [centra účtů Azure](https://account.azure.com/subscriptions)._

_<sup>**3** </sup> zákazníka smlouvu se společností Microsoft spuštěny v března 2019 a nemají žádná historická data před tento bod._

_<sup>**4** </sup> historických dat pro předplatná na základě kredit a platba předem nemusí odpovídat vaší faktuře. Zobrazit [historických dat nemusí odpovídat faktury](#historical-data-might-not-match-invoice) níže._

Následující tabulka ukazuje nabídky, které se zatím nepodporují.

| Kategorie  | **Název nabídky** | **ID kvóty** | **Číslo nabídky** |
| --- | --- | --- | --- |
| **Azure Germany** | [Azure Germany – průběžné platby](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Cloud Solution Provider (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Cloud Solution Provider (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Cloud Solution Provider (CSP)** | Azure Germany v CSP pro Microsoft Cloud Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pay-As-You-Go**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pay-As-You-Go**                 | [Sponzorství Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Plány podpory** | Standard Support                    | Default_2014-09-01 | MS-AZR-0041P |
| **Plány podpory** | Plán podpory Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Plány podpory** | Plán Developer support                   | Default_2014-09-01 | MS-AZR-0043P |
| **Plány podpory** | Plán podpory Německo                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Plány podpory** | Azure Government Standard Support   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Plány podpory** | Azure Government pro-Direct Support | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Plány podpory** | Azure Government Developer Support.  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Určení svůj typ nabídky
Pokud se vám nezobrazují žádná data k předplatnému a k určení, pokud vaše předplatné spadá pod podporované nabídky, můžete ověřit, že je vaše předplatné nepodporuje. Chcete-li ověřit, že předplatné Azure se podporuje, přihlaste se do [webu Azure portal](https://portal.azure.com). Potom vyberte **všechny služby** na panelu nabídky vlevo. V seznamu služeb vyberte **předplatná**. V nabídce předplatné seznam klikněte na předplatné, pro kterou chcete ověřit. Vaše předplatné se zobrazí na kartě Přehled a zobrazí se **nabízejí** a **ID nabídky**. Příklad ukazuje následující obrázek.

![Příklad na kartě Přehled předplatného, nabídky a ID nabídky](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Náklady na zahrnuté ve službě Cost Management

Následující tabulky popisují data, která je součástí nebo není ve službě Cost Management. Všechny náklady se odhadují, dokud se vygeneruje faktury. Náklady na uvedené nezahrnují zdarma a předplacené kredity.

**Data o využití a nákladů**

| **Zahrnuté** | **Nezahrnuto** |
| --- | --- |
| Využití služby Azure<sup>5</sup>        | Podpora poplatky – Další informace naleznete v tématu [fakturovat podmínky je vysvětleno](../billing/billing-understand-your-invoice.md). |
| Nabídka využití webu Marketplace<sup>6</sup> | Daně – Další informace najdete v tématu [fakturovat podmínky je vysvětleno](../billing/billing-understand-your-invoice.md). |
| Nákupy na Marketplace<sup>6</sup>      | Kredity – Další informace najdete v tématu [fakturovat podmínky je vysvětleno](../billing/billing-understand-your-invoice.md). |
| Nákup rezervace<sup>7</sup>      |  |

_<sup>**5** </sup> využití služeb azure je založen na rezervaci a Vyjednaný ceny._

_<sup>**6** </sup> využití webu marketplace a nákupy nejsou k dispozici pro průběžné platby MSDN a Visual Studio nabízí v současnosti._

_<sup>**7** </sup> nákup rezervace jsou dostupné jenom pro smlouvy Enterprise (EA) účty v tuto chvíli._

**Metadata**

| **Zahrnuté** | **Nezahrnuto** |
| --- | --- |
| Značky prostředků<sup>8</sup> | značky skupiny prostředků |

_<sup>**8** </sup> značky prostředku se použijí podle využití je vygenerován z každé služby a nejsou k dispozici zpětně historických údajů o využití._

## <a name="rated-usage-data-refresh-schedule"></a>Plán aktualizace dat používání hodnocení

Data o využití a nákladů je k dispozici v Správa nákladů a fakturace na webu Azure Portal a [podpora rozhraní API](index.yml). Při kontrole nákladů, mít na paměti následující body:

- Odhadované náklady pro aktuální fakturační období se aktualizují šestkrát za den.
- Odhadované náklady pro aktuální fakturační období může změnit, protože se vám účtovat využití.
- Každá aktualizace je kumulativní a obsahuje všechny položky řádku a informace z předchozí aktualizace.
- Azure dokončí nebo _zavře_ aktuálního fakturačního období až 72 hodin (tři kalendářních dnů) po fakturačního období skončí.

Následující příklady znázorňují, jak fakturačních obdobích, která by mohla ukončit.

Předplatná se smlouvou Enterprise (EA) – Pokud daný fakturační měsíc končí 31. března odhadované poplatky jsou aktualizovány až 72 hodin. V tomto příkladu ve 4. dubna půlnoc (UTC).

Předplatná s průběžnými platbami – Pokud daný fakturační měsíc končí 15. května, pak odhadovaných poplatků můžou aktualizaci až 72 hodin. V tomto příkladu podle dne 19 půlnoc (UTC).

### <a name="rerated-data"></a>Rerated dat

Ať už používáte [rozhraní API pro správu nákladů](index.yml), Power BI nebo na webu Azure portal načte data, očekávat aktuálního fakturačního období poplatky ohodnoceny znovu a proto změnit, dokud není zavřena faktury.

## <a name="usage-data-update-frequency-varies"></a>Četnost aktualizace dat používání se liší.

Dostupnost dat vzniklých využití ve službě Cost Management, závisí na několika faktory, včetně:

- Služby Azure (jako jsou úložiště, výpočetní prostředky, CDN a SQL) jak často Generovat využití.
- Čas potřebný k prostřednictvím modulu hodnocení zpracování dat o využití a nákladů kanály správy.

Některé služby generování využití častěji než jiné. Ano může se zobrazit data ve službě Cost Management pro některé služby dřív než jiné služby, které generují data méně často. Využití služby obvykle trvá 8-24 hodin se zobrazí ve službě Cost Management. Mějte data pro open měsíc se aktualizuje a jak se vám účtovat žádné další využití, protože jsou kumulativní aktualizace.

## <a name="historical-data-might-not-match-invoice"></a>Historická data se nemusí shodovat faktury

Historická data pro nabídky založené na kreditní a platba předem nemusí odpovídat faktuře. Některé průběžné platby za Azure MSDN a Visual Studio nabízí, můžete kredity Azure a pokročilé platby použily na fakturu. Historická data zobrazená ve službě Cost Management je však založena na vaše odhadované poplatky pouze. Cost Management historická data neobsahuje, platby a kredity. Historická data pro následující nabídky v důsledku toho nemusí odpovídat přesně s vaší faktuře.

- Azure for Students (MS-AZR-0170P)
- Azure v programu Open (MS-AZR - 0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Bezplatná zkušební verze (MS-AZR - 0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Viz také:

- Pokud jste ještě nedokončili první tohoto rychlého startu Cost Management, přečtěte si ho na [začít analýza nákladů](quick-acm-cost-analysis.md).
