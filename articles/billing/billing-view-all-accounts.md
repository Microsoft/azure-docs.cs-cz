---
title: Zobrazení fakturačních účtů na webu Azure Portal | Microsoft Docs
description: Přečtěte si, jak na webu Azure Portal zobrazit fakturační účty.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: 36430e9b0a4554761d53b537d3c32fa57068eabb
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490218"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Zobrazení fakturačních účtů na webu Azure Portal  

Když se zaregistrujete do Azure, vytvoří se vám fakturační účet. Ten slouží ke správě faktur a plateb a sledování nákladů. Přístup můžete mít k více fakturačním účtům. Může to být třeba v situaci, kdy se zaregistrujete do Azure, abyste mohli pracovat na svých osobních projektech, ale současně máte přístup i prostřednictvím smlouvy Enterprise vaší organizace nebo smlouvy se zákazníkem Microsoftu. Pro každý z těchto scénářů tak máte samostatný fakturační účet.

Web Azure Portal aktuálně podporuje následující typ fakturačních účtů:

- **Program MOSP (Microsoft Online Services Program):** Fakturační účet pro program MOSP (Microsoft Online Services Program) se vytvoří při registraci do Azure prostřednictvím webu Azure. Může to být třeba tehdy, když si zaregistrujete [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [účet s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo účet pro [předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Smlouva Enterprise:** Fakturační účet pro smlouvu Enterprise se vytvoří, když vaše organizace uzavře [smlouvu Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) na používání Azure.

- **Smlouva se zákazníkem Microsoftu:** Fakturační účet pro smlouvu se zákazníkem Microsoftu se vytvoří tehdy, když vaše organizace uzavře smlouvu se zákazníkem Microsoftu prostřednictvím zástupce Microsoftu. Fakturační účet pro smlouvu se zákazníkem Microsoftu můžou mít i někteří zákazníci ve vybraných oblastech, kteří si prostřednictvím webu Azure zaregistrují [účet s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo upgradují svůj [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/). Další informace najdete v tématu [Začínáme s fakturačními účty pro smlouvu se zákazníkem Microsoftu](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>Rozsahy pro fakturační účty
Rozsah je uzel v rámci fakturačního účtu, pomocí kterého uživatelé zobrazují a spravují informace o fakturaci. Právě tady uživatelé spravují fakturační údaje, platby a faktury a provádějí všeobecnou správu účtu. 

### <a name="microsoft-online-services-program"></a>Program MOSP (Microsoft Online Services Program)

|Rozsah  |Definice  |
|---------|---------|
|Fakturační účet     | Představuje jednoho vlastníka (správce účtu) pro jedno nebo více předplatných Azure. Správce účtu má oprávnění provádět různé úkony spojené s fakturací, jako je vytváření předplatných, zobrazení faktur nebo změna fakturace pro předplatná.  |
|Předplatné     |  Představuje seskupení prostředků Azure. V tomto rozsahu se generují faktury. Má vlastní způsoby platby, pomocí kterých se hradí příslušné faktury.|


### <a name="enterprise-agreement"></a>Smlouva Enterprise

|Rozsah  |Definice  |
|---------|---------|
|Fakturační účet    | Představuje registraci smlouvy Enterprise. V tomto rozsahu se generují faktury. Je strukturovaný na základě oddělení a registračních účtů.  |
|Oddělení     |  Volitelné seskupení registračních účtů.      |
|Registrační účet     |  Představuje vlastníka jednoho účtu. V tomto rozsahu se vytvářejí předplatná Azure.  |


### <a name="microsoft-customer-agreement"></a>Smlouva se zákazníkem Microsoftu

|Rozsah  |Úlohy  |
|---------|---------|
|Fakturační účet     |   Představuje zákaznickou smlouvu na více produktů a služeb Microsoftu. Je strukturovaný pomocí fakturačních profilů a oddílů faktur.   |
|Fakturační profil     |  Představuje fakturu a související způsoby platby. V tomto rozsahu se generují faktury. Může obsahovat více oddílů faktury.      |
|Oddíl faktury     |   Představuje skupinu nákladů na faktuře. K tomuto rozsahu jsou přidružená předplatná a další nákupy.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Změna rozsahu fakturace na webu Azure Portal


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Na levé straně vyberte **Všechny rozsahy fakturace**.

   ![Snímek obrazovky se všemi obory fakturace](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** Pokud máte přístup jenom k jednomu rozsahu, možnost **Všechny rozsahy fakturace** se vám nezobrazí.

4. Výběrem určitého oboru zobrazíte podrobnosti.



## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- Zjistěte, jak začít [analyzovat náklady](../cost-management/quick-acm-cost-analysis.md).