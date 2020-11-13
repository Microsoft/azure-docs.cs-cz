---
title: Registrace aplikace pro použití Azure Active Directory | Microsoft Docs
description: Tento článek poskytuje pokyny pro integraci aplikací Azure se službou Active Directory, které jsou vytvořené pro IT specialisty.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: da1b56e3818d2d9701ecb0252328746dc39bb260
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578310"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Vývoj obchodních aplikací pro Azure Active Directory
Tato příručka poskytuje přehled vývoje obchodních aplikací pro Azure Active Directory (AD). Zamýšlená cílová skupina je globální správce služby Active Directory/Microsoft 365.

## <a name="overview"></a>Přehled
Sestavování aplikací integrovaných do Azure AD poskytuje uživatelům ve vaší organizaci jednotné přihlašování pomocí Microsoft 365. Aplikace v Azure AD vám umožní řídit zásady ověřování pro aplikaci. Další informace o podmíněném přístupu a o tom, jak chránit aplikace pomocí vícefaktorového ověřování (MFA), najdete v tématu [Konfigurace pravidel přístupu](../conditional-access/app-based-mfa.md).

Zaregistrujte svoji aplikaci, aby používala Azure Active Directory. Registrace aplikace znamená, že vývojáři můžou pomocí Azure AD ověřovat uživatele a žádat o přístup k uživatelským prostředkům, jako jsou e-mail, kalendář a dokumenty.

Každý člen adresáře (bez hostů) může zaregistrovat aplikaci, jinak se označuje jako *vytvoření objektu aplikace*. Pokud se vám nedaří zaregistrovat aplikaci, znamená to, že globální správce vašeho adresáře tuto funkci omezil a možná budete muset kontaktovat tyto funkce, [aby bylo možné](https://docs.microsoft.com/azure/active-directory/roles/delegate-app-roles#assign-built-in-application-admin-roles) aplikaci zaregistrovat. Další informace o tom, jak omezit uživatele, najdete [v tématu oprávnění k registraci aplikace delegáta v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/roles/delegate-app-roles#restrict-who-can-create-applications).

Registrace aplikace umožňuje libovolnému uživateli provádět následující akce:

* Získat identitu pro svoji aplikaci, kterou Azure AD rozpozná
* Získání jednoho nebo více tajných kódů a klíčů, které může aplikace použít k ověření služby AD
* Branding aplikace v Azure Portal vlastním názvem, logem atd.
* Použijte funkce ověřování Azure AD na jejich aplikace, včetně:

  * Řízení přístupu na základě role (RBAC)
  * Azure Active Directory jako autorizační Server oAuth (zabezpečení rozhraní API vystavené aplikací)
* Deklarujte požadovaná oprávnění, která jsou nezbytná pro to, aby aplikace fungovala podle očekávání, včetně:

     - Oprávnění aplikace (pouze globální správci) Příklad: členství v roli v jiné aplikaci nebo členství v roli Azure AD vzhledem k prostředku Azure, skupině prostředků nebo předplatnému
     - Delegovaná oprávnění (libovolný uživatel). Příklad: Azure AD, přihlášení a profil čtení

> [!NOTE]
> Ve výchozím nastavení může každý člen zaregistrovat aplikaci. Informace o tom, jak omezit oprávnění k registraci aplikací pro konkrétní členy, najdete v tématu [jak se přidávají aplikace do služby Azure AD](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

V takovém případě je třeba globální správce, aby usnadnil vývojářům, aby svou aplikaci mohli připravit na produkční prostředí:

* Konfigurace pravidel přístupu (zásady přístupu/MFA)
* Konfigurace aplikace tak, aby vyžadovala přiřazení uživatele a přiřazení uživatelů
* Potlačit výchozí prostředí pro vyjádření souhlasu uživatele

## <a name="configure-access-rules"></a>Konfigurace pravidel přístupu
Nakonfigurujte pravidla přístupu pro jednotlivé aplikace do aplikací SaaS. Můžete například vyžadovat MFA nebo jenom udělit přístup uživatelům v důvěryhodných sítích. Podrobnosti o této službě jsou k dispozici v dokumentu [Konfigurace pravidel přístupu](../conditional-access/app-based-mfa.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Konfigurace aplikace tak, aby vyžadovala přiřazení uživatele a přiřazení uživatelů
Ve výchozím nastavení mají uživatelé přístup k aplikacím bez přiřazení. Pokud však aplikace zveřejňuje role nebo pokud chcete, aby se aplikace zobrazila v mých aplikacích uživatele, měli byste vyžadovat přiřazení uživatele.

Pokud jste předplatitelem Azure AD Premium nebo sady Enterprise Mobility Suite (EMS), důrazně doporučujeme používat skupiny. Přiřazení skupin k aplikaci umožňuje delegovat průběžnou správu přístupu na vlastníka skupiny. Tuto skupinu můžete vytvořit nebo požádat, aby ji zúčastněná strana ve vaší organizaci vytvořila skupinu pomocí svého zařízení pro správu skupin.

[Přiřazení uživatelů a skupin k aplikaci](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>Potlačit souhlas uživatele
Ve výchozím nastavení se každý uživatel dostane k přihlášení prostřednictvím prostředí pro vyjádření souhlasu. Prostředí pro vyjádření souhlasu, které žádá uživatele o udělení oprávnění k aplikaci, může být sladěné pro uživatele, kteří neznají taková rozhodnutí.

U aplikací, kterým důvěřujete, můžete zjednodušit uživatelské prostředí tím, že se do aplikace zavazujeme jménem vaší organizace.

Další informace o souhlasu uživatele a prostředí pro vyjádření souhlasu v Azure najdete v tématu [integrování aplikací pomocí Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="related-articles"></a>Související články
* [Povolení zabezpečeného vzdáleného přístupu k místním aplikacím pomocí Azure Proxy aplikací služby AD](application-proxy.md)
* [Správa přístupu k aplikacím pomocí Azure AD](what-is-access-management.md)

