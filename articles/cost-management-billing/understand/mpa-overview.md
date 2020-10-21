---
title: Začínáme s fakturačním účtem pro smlouvu s partnerem Microsoftu – Azure CSP
description: Seznamte se se svým fakturačním účtem pro smlouvu s partnerem Microsoftu (CSP)
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 4dff56db9f1450ff7eb2a2b9ca6f4ca648e2ac38
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150099"
---
# <a name="get-started-with-your-microsoft-partner-agreement-billing-account"></a>Začínáme s fakturačním účtem pro smlouvu s partnerem Microsoftu

Když se zaregistrujete do Azure, vytvoří se vám fakturační účet. Slouží ke správě faktur a plateb a sledování nákladů. Přístup můžete mít k více fakturačním účtům. Je to například tehdy, když se zaregistrujete v Azure pro práci na svých osobních projektech, ale současně máte k Azure přístup i prostřednictvím smlouvy Enterprise vaší organizace, smlouvy se zákazníkem Microsoftu nebo smlouvy s partnerem Microsoftu. Pro každý z těchto scénářů tak máte samostatný fakturační účet.

Tento článek se týká fakturačních účtů pro smlouvu s partnerem Microsoftu. Tyto účty se vytváří pro poskytovatele CSP (Cloud Solution Provider), aby mohli spravovat fakturaci za své zákazníky v novém komerčním prostředí. Nové prostředí je k dispozici pouze pro partnery s alespoň jedním zákazníkem, který přijal smlouvu se zákazníkem Microsoftu a má plán Azure. [Ověřte si, jestli máte přístup ke smlouvě s partnerem Microsoftu](#check-access-to-a-microsoft-partner-agreement). [Plán Azure](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) v rámci smlouvy se zákazníkem Microsoftu poskytuje zákazníkům přístup ke službám Azure za tarify průběžných plateb.

## <a name="your-billing-account"></a>Váš fakturační účet

Váš fakturační účet pro smlouvu s partnerem Microsoftu obsahuje fakturační profil pro každou měnu, ve které podnikáte. Fakturační profil umožňuje spravovat faktury v příslušné měně. Když navážete vztahy se zákazníky, v závislosti na jejich měnách se budou předplatná Azure a další nákupy fakturovat na příslušné fakturační profily.

Následující diagram ukazuje vztah mezi fakturačním účtem, fakturačními profily, zákazníky a prodejci.

![Diagram znázorňující hierarchii fakturace smlouvy s partnerem Microsoftu](./media/mpa-overview/mpa-hierarchy.svg)

Fakturační účty, fakturační profily a zákazníky můžou spravovat uživatelé s rolí **Globální správce** a **Agent správy** v rámci organizace. Další informace najdete v tématu [Partnerské centrum – Přiřazování uživatelských rolí a oprávnění](/partner-center/permissions-overview).

## <a name="billing-profiles"></a>Fakturační profily

Fakturační profil slouží ke správě faktur v určité měně. Na začátku kalendářního měsíce se pro každý fakturační profil na vašem účtu vygeneruje měsíční faktura. Ta obsahuje poplatky za všechna předplatná Azure a další nákupy z předchozího měsíce v měně fakturačního profilu.

Na webu Azure Portal si můžete fakturu prohlédnout a stáhnout související dokumenty, jako je soubor s informacemi o využití nebo ceník. Další informace najdete v tématu [Stahování faktur pro smlouvu s partnerem Microsoftu](download-azure-invoice.md).

> [!IMPORTANT]
>
> Faktury pro fakturační profily obsahují poplatky pro zákazníky s plány Azure a také nákupy SaaS a rezervací a nákupy na Azure Marketplace pro zákazníky, kteří nepřijali smlouvu se zákazníkem Microsoftu a nemají plány Azure.

## <a name="customers"></a>Zákazníci

Na webu Azure Portal můžete zobrazit a spravovat zákazníky, kteří přijali smlouvu se zákazníkem Microsoftu a mají plán Azure. U těchto zákazníků můžete zobrazit poplatky a transakce a také pro ně vytvářet a spravovat předplatná Azure.

### <a name="enable-policy-to-give-visibility-into-cost"></a>Získání přehledu o nákladech povolením zásad

Pomocí zásad můžete řídit, jestli uživatelé v organizacích zákazníků můžou zobrazit a analyzovat náklady na využití Azure podle tarifů průběžných plateb. Ve výchozím nastavení jsou zásady vypnuté a uživatelé nemůžou zobrazit náklady. Po jejich povolení můžou uživatelé s odpovídající úrovní přístupu [Azure RBAC](../../role-based-access-control/overview.md) k předplatnému zobrazit a analyzovat náklady na předplatné.

Postup zapnutí zásad:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky znázorňující hledání položky Cost Management + Billing na webu Azure Portal](./media/mpa-overview/search-cmb.png)

1. Na levé straně vyberte **Zákazníci** a pak v seznamu vyberte zákazníka.

   ![Snímek obrazovky znázorňující výběr zákazníka](./media/mpa-overview/mpa-customers.png)

1. Na levé straně vyberte **Zásady**.

   ![Snímek obrazovky znázorňující zásady](./media/mpa-overview/mpa-change-policy.png)

1. Vyberte **Ano**.

## <a name="resellers"></a>Prodejci

Nepřímí poskytovatelé v [dvouúrovňovém modelu](/partner-center) CSP můžou při vytváření předplatných pro zákazníky na webu Azure Portal vybrat prodejce. Po vytvoření můžou zobrazit seznam předplatných vyfiltrovaný podle prodejce a v analýze nákladů Azure analyzovat náklady na zákazníka podle prodejců.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Kontrola přístupu ke smlouvě s partnerem Microsoftu
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.

## <a name="next-steps"></a>Další kroky

Informace o fakturačním účtu najdete v následujících článcích:

- [Vytvoření dalšího předplatného Azure pro smlouvu s partnerem Microsoftu](../manage/create-subscription.md)
- Integrace fakturačních údajů s vlastním systémem generování sestav s využitím [rozhraní API pro fakturaci Azure](/rest/api/billing/)
- [Úvodní příručka ke službě Azure Cost Management pro partnery](../costs/get-started-partners.md)