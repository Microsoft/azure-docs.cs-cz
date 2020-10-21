---
title: Zobrazení fakturačních účtů na webu Azure Portal
description: Zjistěte, jak na webu Azure Portal zobrazit fakturační účty. Projděte si informace o rozsahu pro smlouvu Enterprise, Smlouvu se zákazníkem Microsoftu a Smlouvu s partnerem Microsoftu.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 0049efda603532099cc9bf24dc9d9411b5aaa389
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132121"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Rozsahy a fakturační účty na webu Azure Portal

Když se zaregistrujete do Azure, vytvoří se vám fakturační účet. Ten slouží ke správě faktur a plateb a sledování nákladů. Přístup můžete mít k více fakturačním účtům. Může to být třeba v situaci, kdy se zaregistrujete do Azure, abyste mohli pracovat na svých osobních projektech, ale současně máte přístup i prostřednictvím smlouvy Enterprise vaší organizace nebo smlouvy se zákazníkem Microsoftu. Pro každý z těchto scénářů tak máte samostatný fakturační účet.

Azure Portal podporuje následující typ fakturačních účtů:

- **Program MOSP (Microsoft Online Services Program):** Fakturační účet pro program MOSP (Microsoft Online Services Program) se vytvoří při registraci do Azure prostřednictvím webu Azure. Může to být třeba tehdy, když si zaregistrujete [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/), [účet s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo účet pro [předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Smlouva Enterprise:** Fakturační účet pro smlouvu Enterprise se vytvoří, když vaše organizace uzavře [smlouvu Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) na používání Azure. V rámci smlouvy Enterprise můžete mít maximálně 2000 předplatných.

- **Smlouva se zákazníkem Microsoftu:** Fakturační účet pro smlouvu se zákazníkem Microsoftu se vytvoří tehdy, když vaše organizace uzavře smlouvu se zákazníkem Microsoftu prostřednictvím zástupce Microsoftu. Fakturační účet pro smlouvu se zákazníkem Microsoftu mohou mít i někteří zákazníci ve vybraných oblastech, kteří si prostřednictvím webu Azure zaregistrují [účet s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0003p/) nebo [bezplatný účet Azure](https://azure.microsoft.com/offers/ms-azr-0044p/). V rámci Smlouvy se zákazníkem Microsoftu můžete mít maximálně 20 předplatných. Další informace najdete v tématu [Začínáme s fakturačními účty pro smlouvu se zákazníkem Microsoftu](../understand/mca-overview.md).

- **Smlouva s partnerem Microsoftu:** Fakturační účty pro smlouvu s partnerem Microsoftu se vytváří pro partnery CSP (Cloud Solution Provider), aby mohli spravovat své zákazníky v novém komerčním prostředí. Aby mohli partneři spravovat svůj fakturační účet na webu Azure Portal, musí mít nejméně jednoho zákazníka s [plánem Azure](/partner-center/purchase-azure-plan). Další informace najdete v tématu [Začínáme s fakturačními účty pro smlouvu s partnerem Microsoftu](../understand/mpa-overview.md).

Pokud chcete zjistit, jaký typ fakturačního účtu máte, přejděte do části [Ověření typu fakturačního účtu](#check-the-type-of-your-account).

## <a name="scopes-for-billing-accounts"></a>Rozsahy pro fakturační účty
Rozsah je uzel v rámci fakturačního účtu, pomocí kterého zobrazujete a spravujete fakturaci. Právě tady spravujete fakturační údaje, platby a faktury a provádíte všeobecnou správu účtu.

### <a name="microsoft-online-services-program"></a>Program MOSP (Microsoft Online Services Program)

![Snímek obrazovky znázorňující hierarchii MOSP](./media/view-all-accounts/mosp-hierarchy.png)

|Rozsah  |Definice  |
|---------|---------|
|Fakturační účet     | Představuje smlouvu, kterou zákazník přijímá, aby mohl využívat Azure. Obsahuje jedno nebo několik předplatných.  |
|Předplatné     |  Představuje seskupení prostředků Azure. V tomto rozsahu se generují faktury. K tomuto rozsahu jsou přidružené další fakturační informace, jako jsou způsoby platby a adresa využití.|

### <a name="enterprise-agreement"></a>Smlouva Enterprise

![Snímek obrazovky znázorňující hierarchii EA](./media/view-all-accounts/ea-hierarchy.png)

|Rozsah  |Definice  |
|---------|---------|
|Fakturační účet    | Představuje registraci smlouvy Enterprise. Obsahuje jeden nebo více oddělení a účtů. V tomto rozsahu se generují faktury. |
|Oddělení     |  Volitelné seskupení účtů pro rozdělení nákladů do logických skupin a nastavení rozpočtů.     |
|Účet     |  Představuje vlastníka jednoho účtu. Vlastníci účtu mají oprávnění k vytváření a správě předplatných Azure, která se účtují na příslušnou registraci. |

### <a name="microsoft-customer-agreement"></a>Smlouva se zákazníkem Microsoftu

![Snímek obrazovky znázorňující hierarchii MCA](./media/view-all-accounts/mca-hierarchy.png)

|Rozsah  |Úlohy  |
|---------|---------|
|Fakturační účet     |   Představuje smlouvu, kterou zákazník přijímá, aby mohl využívat služby a produkty Microsoftu. Obsahuje jeden nebo několik fakturačních profilů. |
|Fakturační profil     |   Představuje fakturu a související fakturační informace, jako jsou způsoby platby a fakturační adresa. Obsahuje jeden nebo několik oddílů faktur. |
|Oddíl faktury     |   Představuje seskupení nákladů na faktuře. K tomuto rozsahu jsou přidružená předplatná Azure a další nákupy, například na Azure Marketplace a App Source.    |

### <a name="microsoft-partner-agreement"></a>Smlouva s partnerem Microsoftu

![Snímek obrazovky znázorňující hierarchii MPA](./media/view-all-accounts/mpa-hierarchy.png)

|Rozsah  |Úlohy  |
|---------|---------|
|Fakturační účet     |   Představuje partnerskou smlouvu pro správu produktů a služeb Microsoftu pro zákazníky v novém komerčním prostředí. Obsahuje jeden nebo několik fakturačních profilů a zákazníků.   |
|Fakturační profil     |   Představuje fakturu pro konkrétní měnu.     |
|Zákazník    |   Představuje zákazníka pro partnera CSP (Cloud Solution Provider).  K tomuto rozsahu jsou přidružená předplatná Azure a další nákupy, například na Azure Marketplace a App Source.  |
|Prodejce    |   Prodejce, který poskytuje služby zákazníkům. Toto pole pro předplatné je volitelné a vztahuje se jenom na nepřímé prodejce ve dvouúrovňovém modelu CSP.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Změna rozsahu fakturace na webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky znázorňující hledání položky Cost Management + Billing](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. Na stránce s přehledem vyberte **Přepnout rozsah**.

   ![Snímek obrazovky s rozsahy fakturace](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > Pokud máte přístup jenom k jednomu rozsahu, možnost Přepnout rozsah se vám nezobrazí.

4. Výběrem určitého oboru zobrazíte podrobnosti.

   ![Snímek obrazovky s rozsahy fakturace, které můžete vybrat a zobrazit podrobnosti](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Ověření typu účtu
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- Zjistěte, jak začít [analyzovat náklady](../costs/quick-acm-cost-analysis.md).