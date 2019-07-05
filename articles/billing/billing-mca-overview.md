---
title: Začínáme s fakturačnímu účtu smlouvy zákazníků společnosti Microsoft – Azure
description: Vysvětlení smlouvy zákazníka Microsoftu fakturační účet
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 87483c967641489e9548f38c99eebbf121d0d252
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490737"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Začínáme s vaší fakturační účet smlouvy zákazníka se společností Microsoft

Fakturační účet se vytvoří při registraci k Azure můžete používat. Fakturační účet použít ke správě faktury, platby a sledování nákladů. Můžete mít přístup k několika účtům fakturace. Například může registraci pro Azure pro vaše osobní projekty. Také může mít přístup k Azure prostřednictvím smlouvy Enterprise nebo smlouvy zákazníka Microsoftu vaší organizace. Pro každý z těchto scénářů by mít samostatné fakturační účet.

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>Fakturační účet

Fakturačnímu účtu zákazníka smlouva Microsoft obsahuje jeden nebo více fakturační profily, které umožňují spravovat způsoby platby a faktury. Každý fakturační profil obsahuje jednoho nebo více oddílů faktury, které umožňují uspořádat náklady na faktuře fakturační profil.

Následující diagram znázorňuje vztah mezi fakturační účet, fakturace profily a části faktury.

![Diagram znázorňující smlouvy zákazníka Microsoftu fakturace hierarchie](./media/billing-mca-overview/mca-billing-hierarchy.png)

Role na fakturační účet mít nejvyšší úroveň oprávnění. Ve výchozím nastavení jenom uživatele, kterému zaregistrovali službu Azure získá přístup k fakturačnímu účtu. Pracovníci v těchto rolích měla být přiřazena uživatelům, kteří potřebují zobrazení faktur a sledovat náklady pro celou organizaci, jako jsou finanční nebo správce IT. Další informace najdete v tématu [fakturační účet role a úlohy](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="billing-profiles"></a>Profily fakturace

Fakturační profil použijte ke správě faktury a platební metody. Měsíční faktuře se vygeneruje na začátku měsíce pro každý fakturační profil ve vašem účtu. Faktura obsahuje příslušné poplatky pro všechna předplatná Azure a další nákupy z předchozího měsíce.

Fakturační profil se automaticky vytvoří pro váš fakturační účet. Ve výchozím nastavení obsahuje jeden oddíl faktury. Můžete vytvořit další sekce a snadno sledovat a organizovat náklady podle svých potřeb, jestli je na projektu, oddělení nebo vývojovém prostředí. Tyto části uvidíte na faktuře fakturační profil odráží využití každé předplatné a nákup, které jste přiřadili k němu.

Role na fakturační profily mají oprávnění k zobrazení a správa faktury a platby. Pracovníci v těchto rolích přiřadíte uživatelům, kteří platit faktury jako členy týmu účtárny ve vaší organizaci. Další informace najdete v tématu [fakturace profilu role a úlohy](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>Každý fakturační profil získá měsíční faktuře

Měsíční faktuře se vygeneruje na začátku měsíce pro každý fakturační profil. Faktura obsahuje všechny poplatky za předchozí měsíc.

Můžete zobrazit fakturu, stáhněte si dokumenty a změnit nastavení, chcete-li získat budoucí faktury e-mailem, na webu Azure Portal. Další informace najdete v tématu [stahovat faktury zákaznické smlouvy Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoice-payment-methods"></a>Způsoby platby faktura

Každý fakturační profil má svůj vlastní způsoby platby, které se používají k platbám jeho faktury. Podporují se následující platby:

| Type             | Definice  |
|------------------|-------------|
|Kredity Azure    |  Kredity se automaticky použijí pro oprávněné poplatky na faktuře, čímž snižuje velikost, která budete muset platit. Další informace najdete v tématu [zůstatek kreditu Azure pro váš fakturační profil sledovat](billing-mca-check-azure-credits-balance.md). |
|Kontrola přenos při přenosu | Pokud váš účet je schválená pro platbu prostřednictvím přenosu kontrola/při přenosu. Můžete platit dobu vypršení platnosti fakturu přenos kontrola/při přenosu. Jsou uvedeny pokyny pro platbu na fakturu |
|Platební karty | Zákazníci, kteří si pro Azure prostřednictvím webu Azure můžete zaplatit prostřednictvím platební karty. |

### <a name="apply-policies-to-control-purchases"></a>Použít zásady, které určují nákupů

Použití zásad ke správě Azure Marketplace a nákup rezervace pomocí fakturační profil. Můžete nastavit zásady zakážete nákup rezervace Azure a produkty webu Marketplace. Když zásady se použijí, nelze tyto nákupy předplatná, která se fakturují na fakturační profil.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Plány Azure určit cen a service smlouvu o úrovni pro předplatná

Plány Azure určete ceny a smlouvám o úrovni za odběr služeb Azure. Při vytváření fakturačního profilu jsou automaticky povoleny. Všechny oddíly faktury, které jsou spojeny s fakturační profil můžete použít tyto plány. Uživatelé s přístupem k části faktury pomocí plánů vytvářet předplatná Azure. Následující plány Azure jsou podporovány v účtování účty smlouvy zákazníků společnosti Microsoft:

| Plánování             | Definice  |
|------------------|-------------|
|Microsoft Azure Plan   | Povolení uživatelům vytvářet předplatná, která můžete spouštět žádné úlohy. Další informace najdete v tématu [plánu Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Plán Microsoft Azure pro vývoj/testování | Povolit předplatitele Visual Studia vytvořit předplatné, které mohly probíhat jenom pro vývoj nebo testování zatížení. Tato předplatná, získáte výhody, jako je například nižší sazby a přístup k imagím virtuálních počítačů vylučuje na webu Azure Portal. Další informace najdete v tématu [Microsoft Azure plánování DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="invoice-sections"></a>Části faktury

Vytvoření oddílů faktury náklady na vaší faktuře. Například může potřebovat jedné faktuře pro vaši organizaci, ale chcete uspořádat náklady podle oddělení, tým nebo projektu. V tomto scénáři máte jeden fakturační profil kde vytvořte oddíl faktury pro každé oddělení, týmu nebo projektu.

Když se části faktury, můžete ostatním uživatelům oprávnění k vytvoření předplatného Azure, které se účtují do části. Do části pak účtovat žádné poplatky za využívání a nákup předplatných.

Role v části faktury mají oprávnění řídit, kdo vytváří předplatných Azure. Pracovníci v těchto rolích přiřadíte uživatelům, kteří nastavení prostředí Azure pro týmy v naší organizaci, jako je engineering potenciálních zákazníků a technické architekty. Další informace najdete v tématu [fakturovat část role a úlohy](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktujte podporu.

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

## <a name="next-steps"></a>Další postup

Naleznete v následujících článcích se dozvíte o vašem fakturačním účtu:

- [Vysvětlení smlouvy zákazníků společnosti Microsoft pro správu role v Azure](billing-understand-mca-roles.md)
- [Vytvořte další předplatné Azure pro smlouvy Microsoft zákazníka](billing-mca-create-subscription.md)
- [Vytvořit oddíly na vaší faktuře uspořádat svoje náklady](billing-mca-section-invoice.md)
