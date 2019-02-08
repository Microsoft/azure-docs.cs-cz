---
title: Pochopení dat Azure Cost Management | Dokumentace Microsoftu
description: Tento článek vám pomůže líp pochopit jaká data je zahrnutá ve službě Azure Cost Management a jak často byl zpracován, shromážděná, zobrazené a uzavřené.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/07/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 6a888df453a2626756fa47115ef97d04be701265
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892849"
---
# <a name="understand-cost-management-data"></a>Porozumět datům Cost managementu

Tento článek pomůže lépe pochopit, jaká data budou zahrnuta ve službě Azure Cost Management. A vysvětluje, jak často zpracování dat jsou shromažďovány, zobrazené a uzavřené. Bude se vám účtovat využití Azure každý měsíc. Typ vašeho předplatného Azure určuje po fakturačního měsíce ukončení. Jak často Cost Management přijímá využití dat se liší na základě různých faktorů. Tyto faktory zahrnují jak dlouho trvá zpracování dat a jak často služeb Azure generování informací o využití a fakturace systému.

## <a name="supported-microsoft-offers"></a>Podporované nabídky Microsoftu

Tyto informace zobrazí aktuálně podporované [Microsoft Azure nabízí](https://azure.microsoft.com/support/legal/offer-details/) ve službě Azure Cost Management.  Nabídky Azure je typ, který máte předplatné Azure.

| Kategorie  | **Název nabídky** | **Číslo nabídky** |
| --- | --- | --- |
| **Azure Germany** | [Azure Germany – průběžné platby](https://azure.microsoft.com/offers/ms-azr-de-0003p/) | MS-AZR-DE-0003P |
|**Azure Government**|Azure Government Enterprise|MS-AZR-USGOV-0017P|
| **Smlouva Enterprise (EA)**| Enterprise pro vývoj/testování | MS-AZR-0148P |
| **Smlouva Enterprise (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade/) | MS-AZR-0017P |
| **Microsoft Developer Network (MSDN)** | [Předplatné MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/) | MS-AZR-0062P |
| **Pay-As-You-Go** | [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | MS-AZR-0003P |
| **Pay-As-You-Go** | [Vývoj/testování s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0023p/) | MS-AZR-0023P |
| **Pay-As-You-Go** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/) | MS-AZR-0025P |
| **Pay-As-You-Go** | [Sponzorství Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/) | MS-AZR-0036P |
| **Pay-As-You-Go** | [Bezplatná zkušební verze](https://azure.microsoft.com/offers/ms-azr-0044p/) | MS-AZR-0044P |
| **Pay-As-You-Go** | [Azure v programu Open](https://azure.microsoft.com/offers/ms-azr-0111p/) | MS-AZR-0111P |
| **Pay-As-You-Go** | [Nabídka Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p/) | MS-AZR-0170P |
| **Pay-As-You-Go** | Azure Pass | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p/) | MS-AZR-0029P |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) | MS-AZR-0059P |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) | MS-AZR-0060P |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) | MS-AZR-0063P |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P |

Následující tabulka uvádí nepodporované nabídky.

| Kategorie  | **Název nabídky** | **Číslo nabídky** |
| --- | --- | --- |
| **Cloud Solution Provider (CSP)** | Microsoft Azure | MS-AZR-0145P |
| **Cloud Solution Provider (CSP)** | Azure Government CSP | MS-AZR-USGOV-0145P |
| **Cloud Solution Provider (CSP)** | Azure Germany v CSP pro Microsoft Cloud Germany | MS-AZR-DE-0145P |
| **Pay-As-You-Go** | Azure for Students Starter | MS-AZR-0144P |
| **Plány podpory** | Standard Support | MS-AZR-0041P |
| **Plány podpory** | Plán podpory Professional Direct | MS-AZR-0042P |
| **Plány podpory** | Plán Developer support | MS-AZR-0043P |
| **Plány podpory** | Plán podpory Německo | MS-AZR-DE-0043P |
| **Plány podpory** | Azure Government Standard Support | MS-AZR-USGOV-0041P |
| **Plány podpory** | Azure Government pro-Direct Support | MS-AZR-USGOV-0042P |
| **Plány podpory** | Azure Government Developer Support. | MS-AZR-USGOV-0043P |

Pokud se vám nezobrazují žádná data k předplatnému a k určení, pokud vaše předplatné spadá pod podporované nabídky, můžete ověřit, že je vaše předplatné nepodporuje. Chcete-li ověřit, že předplatné Azure se podporuje, přihlaste se do [webu Azure portal](https://portal.azure.com). Potom vyberte **všechny služby** na panelu nabídky vlevo. V seznamu služeb vyberte **předplatná**. V nabídce předplatné seznam klikněte na předplatné, pro kterou chcete ověřit. Vaše předplatné se zobrazí na kartě Přehled a zobrazí se **nabízejí** a **ID nabídky**. Příklad ukazuje následující obrázek.

![Příklad na kartě Přehled předplatného, nabídky a ID nabídky](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Náklady na zahrnuté ve službě Cost Management

Následující tabulky popisují data, která je součástí nebo není ve službě Cost Management.

**Typy účtů**

| **Zahrnuté** | **Nezahrnuto** |
| --- | --- |
| Smlouva Enterprise (EA) | Cloud Solution Provider (CSP) – Další informace naleznete v tématu [partnerského centra přehled](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview). |
| Průběžných plateb (PAYG) |   |
| Vývoj/testování |   |
| Bezplatné a zkušební verze |   |
| Partner Network |   |
| Azure v rámci licenčního programu Open | &nbsp;  |

**Data o využití a nákladů**

| **Zahrnuté** | **Nezahrnuto** |
| --- | --- |
| Využití služby Azure<sup>1</sup> | Nákup rezervace – Další informace najdete v tématu [rozhraní API pro automatizaci Azure rezervace](../billing/billing-reservation-apis.md). |
| Použití nabídek na webu Marketplace | Nákupy na Marketplace – Další informace najdete v tématu [poplatků za služby třetích stran](../billing/billing-understand-your-azure-marketplace-charges.md). |
|   | Podpora poplatky – Další informace naleznete v tématu [fakturovat podmínky je vysvětleno](../billing/billing-understand-your-invoice.md). |
|   | Daně – Další informace najdete v tématu [fakturovat podmínky je vysvětleno](../billing/billing-understand-your-invoice.md). |
|   | Kredity – Další informace najdete v tématu [fakturovat podmínky je vysvětleno](../billing/billing-understand-your-invoice.md). |

<sup>1</sup> využití služeb azure je založen na rezervaci a Vyjednaný ceny.

**Metadata**

| **Zahrnuté** | **Nezahrnuto** |
| --- | --- |
| Značky prostředků<sup>2</sup> | značky skupiny prostředků |

<sup>2</sup> značky prostředku se použijí podle využití je vygenerován z každé služby a nejsou k dispozici zpětně historických údajů o využití.

## <a name="rated-usage-data-refresh-schedule"></a>Plán aktualizace dat používání hodnocení

Data o využití a nákladů je k dispozici v Správa nákladů a fakturace na webu Azure Portal a [podpora rozhraní API](https://aka.ms/costmgmt/docs). Při kontrole nákladů, mít na paměti následující body:

- Odhadované náklady pro aktuální fakturační období se aktualizují šestkrát za den.
- Odhadované náklady pro aktuální fakturační období může změnit, protože se vám účtovat využití.
- Každá aktualizace je kumulativní a obsahuje všechny položky řádku a informace z předchozí aktualizace.
- Azure dokončí nebo _zavře_ aktuálního fakturačního období až 72 hodin (tři kalendářních dnů) po fakturačního období skončí.

Následující příklady znázorňují, jak fakturačních obdobích, která by mohla ukončit.

Předplatná se smlouvou Enterprise (EA) – Pokud daný fakturační měsíc končí 31. března odhadované poplatky jsou aktualizovány až 72 hodin. V tomto příkladu ve 4. dubna půlnoc (UTC).

Předplatná s průběžnými platbami – Pokud daný fakturační měsíc končí 15. května, pak odhadovaných poplatků můžou aktualizaci až 72 hodin. V tomto příkladu podle dne 19 půlnoc (UTC).

### <a name="rerated-data"></a>Rerated dat

Ať už používáte [rozhraní API pro správu nákladů](https://aka.ms/costmgmt/docs), Power BI nebo na webu Azure portal načte data, očekávat aktuálního fakturačního období poplatky ohodnoceny znovu a proto změnit, dokud není zavřena faktury.

## <a name="usage-data-update-frequency-varies"></a>Četnost aktualizace dat používání se liší.

Dostupnost dat vzniklých využití ve službě Cost Management, závisí na několika faktory, včetně:

- Služby Azure (jako jsou úložiště, výpočetní prostředky, CDN a SQL) jak často Generovat využití.
- Čas potřebný k prostřednictvím modulu hodnocení zpracování dat o využití a nákladů kanály správy.

Některé služby generování využití častěji než jiné. Ano může se zobrazit data ve službě Cost Management pro některé služby dřív než jiné služby, které generují data méně často. Využití služby obvykle trvá 8-24 hodin se zobrazí ve službě Cost Management. Mějte data pro open měsíc se aktualizuje a jak se vám účtovat žádné další využití, protože jsou kumulativní aktualizace.

## <a name="see-also"></a>Další informace najdete v tématech

- Pokud jste ještě nedokončili první tohoto rychlého startu Cost Management, přečtěte si ho na [začít analýza nákladů](quick-acm-cost-analysis.md).
