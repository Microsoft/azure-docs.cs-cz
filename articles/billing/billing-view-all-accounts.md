---
title: Zobrazit vaše fakturační účty na webu Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit vaše fakturační účty na webu Azure portal.
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
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490218"
---
# <a name="view-billing-accounts-in-azure-portal"></a>Zobrazit fakturační účty na webu Azure portal  

Fakturační účet se vytvoří při registraci k Azure můžete používat. Fakturační účet použít ke správě vašich fakturách se projeví, platby a sledování nákladů. Můžete mít přístup k několika účtům fakturace. Například může registraci pro Azure pro vaše osobní projekty. Můžete mít také přístup prostřednictvím smlouvy Enterprise nebo smlouvy zákazníka Microsoftu vaší organizace. Pro každý z těchto scénářů by mít samostatné fakturační účet.

Portál Azure aktuálně podporuje následující typ fakturační účty:

- **Microsoft Online Services Program**: Fakturační účet pro Microsoft Online Services Program se vytvoří při registraci k Azure prostřednictvím webu Azure. Příklad, kdy můžete zaregistrovat [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [účet s průběžnými sazbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo jako [předplatitel sady Visual studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Smlouva Enterprise Agreement**: Fakturační účet pro smlouvu Enterprise Agreement se vytvoří při vaší organizaci zaregistruje [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) používání systému Azure.

- **Zákazníkem Microsoft**: Fakturační účet Microsoft zákaznické smlouvy se vytvoří při vaše organizace pracuje s zástupce společnosti Microsoft k podepsání smlouvy zákazníka Microsoftu. Někteří zákazníci ve vybraných oblastech, kteří si prostřednictvím webu Azure pro [účet s průběžnými sazbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo upgradovat jejich [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) může mít fakturační účet pro Microsoft Customer Také smlouvy. Další informace najdete v tématu [Začínáme s fakturační účet pro smlouvy Microsoft zákazníka](billing-mca-overview.md).

<!--Todo Add section to identify the type of accounts -->

## <a name="scopes-for-billing-accounts"></a>Obory pro fakturační účty
Obor je uzel v rámci fakturační účet, který uživatelé použít k zobrazení a správě fakturace. Je, kde uživatelé spravovat fakturačních dat, plateb faktur a provádět správu obecné účtu. 

### <a name="microsoft-online-services-program"></a>Program MOSP (Microsoft Online Services Program)

|Scope  |Definice  |
|---------|---------|
|Fakturační účet     | Představuje jednoho vlastníka (správce účtu) pro jeden nebo více předplatných Azure. Účet správce mohou provádět různé úlohy fakturace jako vytvářet předplatná, Zobrazit faktury nebo změnit fakturace za předplatné.  |
|Předplatné     |  Představuje skupinu prostředků Azure. V tomto oboru je vygenerována faktura. Má svůj vlastní platební metody, které se používají k platbám jeho faktury.|


### <a name="enterprise-agreement"></a>Smlouva Enterprise

|Scope  |Definice  |
|---------|---------|
|Fakturační účet    | Představuje smlouvu Enterprise Agreement enrollment. V tomto oboru je vygenerována faktura. Strukturovaná, pomocí oddělení a registračních účtů.  |
|Oddělení     |  Volitelné seskupení registračních účtů.      |
|Registrační účet     |  Představuje jeden účet vlastníka. Předplatná Azure se vytvoří v tomto oboru.  |


### <a name="microsoft-customer-agreement"></a>Smlouva se zákazníkem Microsoftu

|Scope  |Úlohy  |
|---------|---------|
|Fakturační účet     |   Představuje zákaznické smlouvy pro více produktů a služeb Microsoftu. Strukturovaná, pomocí fakturační profily a části faktury.   |
|Fakturační profil     |  Představuje faktury a jeho způsoby platby. V tomto oboru je vygenerována faktura. Může mít více oddílů faktury.      |
|Část faktury     |   Představuje skupinu náklady, faktury. Předplatná a další nákupy jsou přidružené k tomuto oboru.    |


## <a name="switch-billing-scope-in-the-azure-portal"></a>Přepnout fakturační obor na webu Azure Portal


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-view-all-accounts/billing-search-cost-management-billing.png)

3. Vyberte **všechny obory fakturační** na levé straně.

   ![Snímek obrazovky zobrazující všechny obory fakturace](./media/billing-view-all-accounts/billing-list-of-accounts.png)

   ** Neuvidíte **všechny obory fakturační** Pokud máte přístup jenom na jeden obor.

4. Vyberte rozsah zobrazíte podrobnosti.



## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup
- Zjistěte, jak spustit [analýze nákladů](../cost-management/quick-acm-cost-analysis.md).