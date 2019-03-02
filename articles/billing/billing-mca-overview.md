---
title: Začínáme s vaším fakturačním účtem Microsoft zákaznické smlouvy – Azure | Dokumentace Microsoftu
description: Vysvětlení fakturační účet pro zákaznické smlouvy Microsoft
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: banders
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: c263669d7f74d8abebcd84e818ae2ccf2dcac0f0
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57248905"
---
# <a name="get-started-with-your-billing-account-for-a-microsoft-customer-agreement"></a>Začínáme s vaším fakturačním účtem Microsoft zákaznické smlouvy

Fakturační účet se vytvoří pro každou smlouvu, že se že přihlásíte s Microsoftem používání Azure. Fakturační účet vám správy fakturace a sledování nákladů. Můžete mít přístup k několika účtům fakturace. Například může registraci pro Azure pro vaše osobní projekty. Také může mít přístup k Azure prostřednictvím smlouvy Enterprise nebo smlouvy zákazníka Microsoftu vaší organizace. Pro každý z těchto scénářů by mít samostatné fakturační účet.

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="understand-billing-account"></a>Vysvětlení fakturační účet

Fakturačnímu účtu smlouvy zákazníka Microsoftu obsahuje jeden nebo více profilů fakturace. Každý fakturační profil má své vlastní metody faktury a platba. Fakturační profil obsahuje jednoho nebo více oddílů faktury, které umožňují uspořádat náklady na faktuře fakturační profil.

Následující diagram znázorňuje vztah mezi fakturační účet, fakturace profily a části faktury.

![Diagram zobrazující průběh fakturační hierarchie pro smlouvy Microsoft zákazníka](./media/billing-mca-overview/mca-billing-hierarchy.png)

Role na fakturační účet mít nejvyšší úroveň oprávnění. Pracovníci v těchto rolích přiřadit uživatelům, kteří potřebují k zobrazení faktur a sledovat náklady pro celou organizaci, jako jsou finanční nebo správci IT. Další informace najdete v tématu [fakturační účet role a úlohy](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="understand-billing-profiles"></a>Principy fakturace profily

Fakturační profil použijte ke správě faktury a platební metody. Měsíční faktuře se vygeneruje pro předplatná Azure a další produkty nákupu využít fakturační profil. Pomocí metody platby zaplatit fakturu.

Fakturační profil se automaticky vytvoří pro váš fakturační účet. Můžete vytvořit nové profily fakturační nastavit další faktury. Například můžete různé faktury pro každé oddělení nebo projekt ve vaší organizaci.

Můžete také vytvořit části faktury pro uspořádání náklady na faktuře fakturační profil. Poplatky za předplatná Azure a produkty zakoupené pro oddíl faktury se zobrazí v části. Fakturační profil faktury zahrnuje poplatky za všechny části faktury.

Role na fakturační profily mají oprávnění k zobrazení a správa faktury a platby. Pracovníci v těchto rolích přiřadíte uživatelům, kteří platit faktury jako členy týmu účtárny ve vaší organizaci. Další informace najdete v tématu [fakturace profilu role a úlohy](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="monthly-invoice-generated-for-each-billing-profile"></a>Měsíční faktuře vygenerována pro každý fakturační profil

Měsíční faktuře se vygeneruje na datum faktury pro každý fakturační profil. Faktura obsahuje všechny poplatky za předchozí měsíc.

Můžete zobrazit fakturu, stáhněte si dokumenty a změnit nastavení, chcete-li získat budoucí faktury e-mailem, na webu Azure Portal. Další informace najdete v tématu [získat faktury e-mailem](billing-download-azure-invoice-daily-usage-date.md#get-your-invoice-in-email-pdf).

### <a name="invoices-paid-through-payment-methods"></a>Faktury placené prostřednictvím způsobů platby

Každý fakturační profil má svůj vlastní způsoby platby, které se používají k platbám jeho faktury. Podporují se následující platby:

| Type             | Definice  |
|------------------|-------------|
|Kredity Azure    |  Kredity se automaticky použijí pro Celková fakturovaná částka na vaší faktuře k výpočtu velikosti, budete muset platit. Další informace najdete v tématu [zůstatek kreditu Azure sledování pro váš fakturační profil](billing-mca-check-azure-credits-balance.md). |
|Šek nebo bezhotovostní převod | Můžete platit dlužná částka fakturu buď prostřednictvím vrácení, nebo při přenosu přenášet. Jsou uvedeny pokyny pro platbu na fakturu |

### <a name="control-azure-marketplace-and-reservation-purchases-by-applying-policies"></a>Nákupy na Azure Marketplace ovládacího prvku a rezervace pomocí zásad

Použijte zásady, které určují nákupy provedené pomocí fakturační profil. Můžete nastavit zásady zakážete nákup rezervace Azure a produkty webu Marketplace. Když zásady se použijí, předplatná vytvořená pro části faktury v fakturační profil nelze použít k nákupu rezervace Azure a produkty pro Marketplace.

### <a name="allow-users-to-create-azure-subscriptions-by-enabling-azure-plans"></a>Povolit uživatelům vytvářet předplatná Azure tím, že Azure plány

Plány Azure jsou povolené automaticky při vytváření fakturačního profilu. Všechny části faktury fakturační profil získat přístup do těchto plánů. Uživatelé s přístupem k části faktury pomocí plánů vytvářet předplatná Azure. Pokud je pro fakturační profil povoleno plánu služby Azure, nelze vytvářet předplatná Azure. Následující plány Azure jsou podporovány v účtování účty smlouvy zákazníků společnosti Microsoft:

| Plánování             | Definice  |
|------------------|-------------|
|Microsoft Azure Plan   | Povolení uživatelům vytvářet předplatná, která můžete spouštět žádné úlohy. Další informace najdete v tématu [plánu Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Plán Microsoft Azure pro vývoj/testování | Povolit předplatitele Visual Studia vytvořit předplatné, které mohly probíhat jenom pro vývoj nebo testování zatížení. Tato předplatná, získáte výhody, jako je například nižší sazby a přístup k imagím virtuálních počítačů vylučuje na webu Azure Portal. Další informace najdete v tématu [Microsoft Azure plánování DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)| <!--- TODO - Add the link to plan details page -->

## <a name="understand-invoice-sections"></a>Vysvětlení faktury oddíly

Vytvoření oddílů faktury náklady na faktuře fakturační profil. Například může potřebovat jedné faktuře pro vaši organizaci, ale chcete uspořádat náklady podle oddělení, tým nebo projektu. V tomto scénáři máte jeden fakturační profil kde vytvořte oddíl faktury pro každé oddělení, týmu nebo projektu.

Když se části faktury, můžete jiné udělit oprávnění vytvářet předplatná Azure pro oddíl. Všechny poplatky za využívání a nákup předplatných se projeví v příslušné části faktury.

Role v části faktury mají oprávnění řídit, kdo vytváří předplatných Azure. Pracovníci v těchto rolích přiřadíte uživatelům, kteří nastavení prostředí Azure pro týmy v naší organizaci, jako je engineering potenciálních zákazníků a technické architekty. Další informace najdete v tématu [fakturovat část role a úlohy](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

## <a name="next-steps"></a>Další postup

Naleznete v následujících článcích se dozvíte o vašem fakturačním účtu:

- [Vysvětlení smlouvy zákazníků společnosti Microsoft pro správu role v Azure](billing-understand-mca-roles.md)
- [Vytvořte předplatné Azure pro váš fakturační účet pro smlouvy Microsoft zákazníka](billing-mca-create-subscription.md)
- [Uspořádání nákladů s částmi faktury](billing-mca-section-invoice.md)