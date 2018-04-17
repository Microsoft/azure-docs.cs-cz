---
title: Aktivovat předplatných Azure a účtů | Microsoft Docs
description: Povolte přístup pomocí rozhraní API Správce Azure Resource Manager pro nové a stávající účty a řešení běžných problémů s účtu.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/01/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: dbbbc7ee87d53f65d51b20fd5b8ffcb6c4930f15
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="activate-azure-subscriptions-and-accounts-with-azure-cost-management"></a>Aktivovat předplatných Azure a účtů s Azure náklady na správu

Přidání nebo aktualizace přihlašovacích údajů správce Azure Resource Manager umožňuje Azure náklady na správu a zjistit všechny účty a předplatných v rámci vašeho klienta Azure. Pokud máte také Azure Diagnostics rozšíření povolené na virtuálních počítačích, můžete shromažďovat Azure náklady na správu rozšířené metriky jako procesoru a paměti. Tento článek popisuje, jak povolit přístup pomocí rozhraní API Správce Azure Resource Manager pro nové a stávající účty. Také popisuje, jak řešit běžné problémy, účet.

Azure náklady na správu nemá přístup k většinu data vašeho předplatného Azure, když je předplatné _neaktivovaných_. Je nutné upravit _neaktivovaných_ účty tak, aby k nim měli přístup Azure náklady na správu.

## <a name="required-azure-permissions"></a>Oprávnění vyžadovaná Azure

Konkrétní oprávnění jsou nutné k dokončení postupy v tomto článku. Vy nebo váš správce klienta musí mít obě následující oprávnění:

- Oprávnění k registraci aplikací CloudynCollector klientovi Azure AD.
- Možnost přiřazení aplikace k roli ve vašich předplatných Azure.

V rámci vašich předplatných Azure, musí mít vaše účty `Microsoft.Authorization/*/Write` přístup k aplikaci CloudynCollector přiřadit. Tato akce je poskytována prostřednictvím [vlastníka](../role-based-access-control/built-in-roles.md#owner) role nebo [správce přístupu uživatelů](../role-based-access-control/built-in-roles.md#user-access-administrator) role.

Pokud je váš účet přiřazenou **Přispěvatel** role, nemáte dostatečná oprávnění k aplikaci přiřadit. Obdržíte chybu při pokusu o přiřazení CloudynCollector aplikace k předplatnému Azure.

### <a name="check-azure-active-directory-permissions"></a>Zkontrolujte oprávnění služby Azure Active Directory

1. Přihlaste se [portál Azure](https://portal.azure.com).
2. Na portálu Azure vyberte **Azure Active Directory**.
3. V Azure Active Directory, vyberte **uživatelská nastavení**.
4. Zkontrolujte **registrace aplikace** možnost.
    - Pokud je nastaven na hodnotu **Ano**, pak uživatelé bez oprávnění správce můžete zaregistrovat aplikace AD. Toto nastavení znamená, že každý uživatel v klientovi Azure AD můžete zaregistrovat aplikaci. Můžete přejít k Azure vyžaduje předplatné oprávnění.  
    ![Registrace aplikace](./media/activate-subs-accounts/app-register.png)
    - Pokud **registrace aplikace** je možnost nastavena na **ne**, pak pouze klienta správní uživatelé mohou registrovat aplikace Azure Active Directory. Správce klienta musí zaregistrovat CloudynCollector aplikace.


## <a name="add-an-account-or-update-a-subscription"></a>Přidat účet nebo aktualizovat předplatné

Když přidáte aktualizace účtu předplatného, povolíte Azure náklady na správu přístup k Azure data.

### <a name="add-a-new-account-subscription"></a>Přidání nového účtu (předplatné)

1. Na portálu Azure náklady na správu, klikněte na symbol ozubené kolečko v pravém horním a vyberte **cloudové účty**.
2. Klikněte na tlačítko **nový účet přidal** a **nový účet přidal** pole se objeví. Zadejte požadované informace.  
    ![Přidat nový účet pole](./media/activate-subs-accounts//add-new-account.png)

### <a name="update-a-subscription"></a>Aktualizace předplatného

1. Pokud chcete aktualizovat _neaktivovaných_ předplatné, které už v Azure náklady na správu v nástroji Správa účtů, kliknutím na symbol tužky upravit napravo od nadřazené _klienta GUID_. Odběry jsou seskupené v rámci nadřazené klienta, tak vyhnout aktivace odběry jednotlivě.
    ![Opětovné zjištění odběrů](./media/activate-subs-accounts/existing-sub.png)
2. V případě potřeby zadejte ID klienta. Pokud si nejste jisti vaše ID klienta, vyhledání pomocí následující kroky:
    1. Přihlaste se [portál Azure](https://portal.azure.com).
    2. Na portálu Azure vyberte **Azure Active Directory**.
    3. K získání ID tenanta vyberte v tenantovi Azure AD možnost **Vlastnosti**.
    4. Zkopírujte identifikátor GUID ID adresáře. Tato hodnota představuje ID tenanta.
    Další informace najdete v tématu [získání ID tenanta](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
3. V případě potřeby vyberte ID míry. Pokud neznáte vaše ID míry, použijte následující kroky k vyhledání.
    1. V pravém horním portálu Azure, klikněte na tlačítko informace o uživateli a potom klikněte na **Zobrazit Moje faktury**.
    2. V části **fakturace účtu**, klikněte na tlačítko **odběry**.
    3. V části **mé odběry**, vyberte předplatné.
    4. Vaše míra ID se zobrazí v části **nabízejí ID**. Zkopírujte ID nabízejí pro předplatné.
4. Přidat nový účet (nebo upravit předplatného) klikněte na **Uložit** (nebo **Další**). Budete přesměrováni na portálu Azure.
5. Přihlaste se k portálu. Klikněte na tlačítko **přijmout** autorizace Azure náklady na správu kolekce přístup k účtu Azure.

    Budete přesměrováni na stránku Správa účtů pro správu Azure náklady a vaše předplatné se aktualizuje s **active** stav účtu. Mělo by se zobrazit symbol zelená značka zaškrtnutí ve sloupci Resource Manager.

    Pokud nevidíte symbol zeleného zaškrtnutí pro jeden nebo více odběrů, znamená to, že nemáte oprávnění k vytvoření čtečky aplikace (CloudynCollector) pro předplatné. Uživatel s vyšší oprávnění pro odběr je potřeba tento postup opakujte.

Sledování [připojení do Azure Resource Manageru službou Azure náklady Management](https://youtu.be/oCIwvfBB6kk) video, které provede procesem.

>[!VIDEO https://www.youtube.com/embed/oCIwvfBB6kk?ecver=1]

## <a name="resolve-common-indirect-enterprise-set-up-problems"></a>Řešení běžných problémů nastavení nepřímých enterprise

Při prvním použití portálu Azure náklady na správu, může zobrazit následující zprávy, pokud se uživatel s microsoftem smlouvu Enterprise nebo Cloud Solution Provider (CSP):

- *Zadaný klíč rozhraní API není klíč nejvyšší úrovně zápisu* zobrazí v **nastavit až Azure náklady na správu** průvodce.
- *Přímá registrace – ne* zobrazí na portálu smlouvy Enterprise.
- *Nebyla nalezena žádná data využití za posledních 30 dní. Obraťte se na vaše distributora tak, aby zajistěte, aby byl povolen poznámky k účtu Azure* zobrazí na portálu Azure náklady na správu.

Předchozí zprávy naznačují, že jste zakoupili smlouvu Enterprise Agreement Azure přes prodejce nebo poskytovatel CSP. Svého prodejce nebo zprostředkovatele kryptografických služeb je potřeba povolit _značek_ pro vaši Azure účet tak, aby vaše data můžete zobrazit v Azure náklady na správu.

Chcete-li opravit problémy:

1. Váš prodejce je potřeba povolit _značek_ pro váš účet. Pokyny najdete v tématu [nepřímých Průvodce registrací ve službě zákazníka](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).
2. Vygenerování klíče smlouvou Azure Enterprise pro použití s Azure náklady na správu. Pokyny najdete v tématu [registrovat smlouvy Enterprise Azure a zobrazení náklady data](https://docs.microsoft.com/en-us/azure/cost-management/quick-register-ea).

Pouze správce služby Azure můžete povolit náklady na správu. Oprávnění správce společné nestačí.

Než můžete vygenerovat klíč rozhraní API smlouvy Enterprise Azure nastavit náklady na správu Azure, musíte povolit fakturace rozhraní API služby Azure podle těchto pokynů:

- [Přehled rozhraní API pro vytváření sestav pro podnikové zákazníky](../billing/billing-enterprise-api.md)
- [Portál Microsoft Azure enterprise rozhraní API pro vytváření sestav](https://ea.azure.com/helpdocs/reportingAPI) pod **povolení přístupu k rozhraní API datům**

Také možná budete muset poskytnout správci oddělení, účet vlastníků a enterprise administrators oprávnění k _zobrazit poplatky_ s rozhraním API fakturace.

## <a name="next-steps"></a>Další postup

- Pokud jste ještě nedokončili první kurz pro náklady na správu, přečtěte si ho na [zkontrolujte využití a náklady na](tutorial-review-usage.md).
