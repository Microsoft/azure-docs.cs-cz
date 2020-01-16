---
title: Zobrazení fakturačních účtů na webu Azure Portal | Microsoft Docs
description: Přečtěte si, jak na webu Azure Portal zobrazit fakturační účty.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 2768d6e146a37e86bb36353f661179ebd7b5033d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994126"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Fakturační účty a obory v Azure Portal

Fakturační účet se vytváří při registraci k Azure. Ten slouží ke správě faktur a plateb a sledování nákladů. Přístup můžete mít k více fakturačním účtům. Může to být třeba v situaci, kdy se zaregistrujete do Azure, abyste mohli pracovat na svých osobních projektech, ale současně máte přístup i prostřednictvím smlouvy Enterprise vaší organizace nebo smlouvy se zákazníkem Microsoftu. Pro každý z těchto scénářů tak máte samostatný fakturační účet.

Azure Portal podporuje následující typ fakturačních účtů:

- **Program Microsoft Online Services**: fakturační účet pro program Microsoft Online Services se vytvoří při registraci k Azure prostřednictvím webu Azure. Může to být třeba tehdy, když si zaregistrujete [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [účet s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo účet pro [předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Smlouva Enterprise**: fakturační účet pro smlouva Enterprise se vytvoří, když vaše organizace podepíše [smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) , aby používala Azure.

- **Smlouva o zákaznících Microsoftu**: fakturační účet pro smlouvu o zákaznících Microsoftu se vytvoří, když vaše organizace spolupracuje s zástupcem Microsoftu k podepsání smlouvy o zákaznících Microsoftu. Někteří zákazníci ve vybraných oblastech, kteří si zaregistrují web Azure pro [účet s tarify](https://azure.microsoft.com/offers/ms-azr-0003p/) průběžných plateb nebo [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) , můžou mít fakturační účet i pro smlouvy o zákaznících Microsoftu. Další informace najdete v tématu [Začínáme s fakturačními účty pro smlouvu se zákazníkem Microsoftu](../understand/mca-overview.md).

- **Smlouva o partnerovi Microsoftu**: fakturační účet pro partnerský vztah Microsoftu se vytvoří pro partnery poskytovatele Cloud Solution Provider (CSP), aby mohli spravovat své zákazníky v novém prostředí pro obchod. Partneři musí mít aspoň jednoho zákazníka s [plánem Azure](https://docs.microsoft.com/partner-center/purchase-azure-plan) ke správě fakturačního účtu v Azure Portal. Další informace najdete v tématu [Začínáme s fakturačním účtem pro dohodu o partnerovi Microsoftu](../understand/mpa-overview.md).

Pokud chcete zjistit, jaký typ fakturačního účtu máte, přejděte do části [Ověření typu fakturačního účtu](#check-the-type-of-your-account).

## <a name="scopes-for-billing-accounts"></a>Rozsahy pro fakturační účty
Obor je uzel v rámci fakturačního účtu, který používáte k zobrazení a správě fakturace. Je tam, kde spravujete fakturační údaje, platby, faktury a provedete obecnou správu účtů.

### <a name="microsoft-online-services-program"></a>Program MOSP (Microsoft Online Services Program)

 ![Snímek obrazovky zobrazující hierarchii MOSP](./media/view-all-accounts/mosp-hierarchy.png)

|Rozsah  |Definice  |
|---------|---------|
|Fakturační účet     | Představuje smlouvu, kterou zákazník přijímá k používání Azure. Obsahuje jeden nebo více předplatných.  |
|Předplatné     |  Představuje seskupení prostředků Azure. V tomto rozsahu se generují faktury. K tomuto oboru jsou přidruženy další fakturační informace, jako jsou způsoby platby a adresa použití.|

### <a name="enterprise-agreement"></a>Smlouva Enterprise

![Snímek obrazovky zobrazující hierarchii EA](./media/view-all-accounts/ea-hierarchy.png)

|Rozsah  |Definice  |
|---------|---------|
|Fakturační účet    | Představuje registraci smlouvy Enterprise. Obsahuje jednu nebo více oddělení a účtů. V tomto rozsahu se generují faktury. |
|Oddělení     |  Volitelné seskupení účtů pro rozdělení nákladů do logických seskupení a nastavení rozpočtu.     |
|Účet     |  Představuje vlastníka jednoho účtu. Vlastníci účtu mají oprávnění k vytváření a správě předplatných Azure, která se účtují k registraci. |

### <a name="microsoft-customer-agreement"></a>Smlouva se zákazníkem Microsoftu

![Snímek obrazovky, který zobrazuje hierarchii MCA](./media/view-all-accounts/mca-hierarchy.png)

|Rozsah  |Úlohy  |
|---------|---------|
|Fakturační účet     |   Představuje smlouvu, kterou zákazník přijímá k používání produktů a služeb společnosti Microsoft. Obsahuje jeden nebo více fakturačních profilů. |
|Fakturační profil     |   Představuje fakturu a související fakturační informace, jako jsou způsoby platby a fakturační adresa. Obsahuje jednu nebo více oddílů faktury. |
|Oddíl faktury     |   Představuje seskupení nákladů na faktuře. K tomuto oboru jsou přidružená předplatná Azure a další nákupy, například Azure Marketplace a zdrojové produkty aplikací.    |

### <a name="microsoft-partner-agreement"></a>Partnerská smlouva Microsoftu

![Snímek obrazovky zobrazující hierarchii aktivace](./media/view-all-accounts/mpa-hierarchy.png)

|Rozsah  |Úlohy  |
|---------|---------|
|Fakturační účet     |   Představuje smlouvu partnera pro správu produktů a služeb společnosti Microsoft pro zákazníky v novém prostředí pro obchod. Obsahuje jeden nebo více fakturačních profilů a zákazníků.   |
|Fakturační profil     |   Představuje fakturu pro měnu.     |
|Zákazník    |   Představuje zákazníka pro partnera poskytovatele Cloud Solution Provider (CSP).  K tomuto oboru jsou přidružená předplatná Azure a další nákupy, například Azure Marketplace a zdrojové produkty aplikací.  |
|Prodejce    |   Prodejce, který poskytuje služby zákazníkům. Je to volitelné pole pro předplatné a platí jenom pro nepřímé zprostředkovatele v modelu dvou vrstev CSP.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Změna rozsahu fakturace na webu Azure Portal

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. Na stránce Přehled vyberte možnost **obor přepínače**.

   ![Snímek obrazovky zobrazující rozsahy fakturace](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > Pokud máte přístup jenom k jednomu oboru, nezobrazí se rozsah přepnutí.

4. Výběrem určitého oboru zobrazíte podrobnosti.

   ![Snímek obrazovky zobrazující rozsahy fakturace](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Ověřte typ účtu.
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- Zjistěte, jak začít [analyzovat náklady](../costs/quick-acm-cost-analysis.md).
