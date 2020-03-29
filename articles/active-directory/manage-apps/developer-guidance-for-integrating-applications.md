---
title: Registrace aplikace pro používání služby Azure Active Directory | Dokumenty společnosti Microsoft
description: Tento článek, který je napsán pro IT Pro, obsahuje pokyny pro integraci aplikací Azure se službou Active Directory.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 10/30/2018
ms.author: mimart
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba54f8042c20a00f8d559ddce28e007a93afaace
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108287"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Vývoj obchodních aplikací pro Azure Active Directory
Tato příručka poskytuje přehled vývoje obchodních aplikací (LoB) pro Azure Active Directory (AD). Zamýšlenou cílovou skupinou jsou globální správci služby Active Directory/Office 365.

## <a name="overview"></a>Přehled
Vytváření aplikací integrovaných s Azure AD poskytuje uživatelům ve vaší organizaci jednotné přihlašování s Office 365. S aplikací ve službě Azure AD vám dává kontrolu nad zásady ověřování pro aplikaci. Další informace o podmíněném přístupu a o tom, jak chránit aplikace pomocí vícefaktorového ověřování (MFA), najdete [v tématu Konfigurace pravidel přístupu](../conditional-access/app-based-mfa.md).

Zaregistrujte svou aplikaci a použijte službu Azure Active Directory. Registrace aplikace znamená, že vaši vývojáři můžou používat Azure AD k ověřování uživatelů a vyžádání přístupu k uživatelským prostředkům, jako je e-mail, kalendář a dokumenty.

Každý člen vašeho adresáře (nikoli hosté) může zaregistrovat aplikaci, jinak známou jako *vytvoření aplikačního objektu*.

Registrace aplikace umožňuje každému uživateli provést následující akce:

* Získání identity pro jejich aplikaci, kterou Azure AD rozpozná
* Získejte jeden nebo více tajných kódů/klíčů, které aplikace může použít k ověření sebe sama do služby AD
* Aplikaci označte na webu Azure Portal vlastním názvem, logem atd.
* Použijte funkce autorizace Azure AD pro jejich aplikaci, včetně:

  * Řízení přístupu na základě role (RBAC)
  * Azure Active Directory jako autorizační server OAuth (zabezpečení rozhraní API vystaveného aplikací)
* Deklarujte požadovaná oprávnění nezbytná pro to, aby aplikace fungovala podle očekávání, včetně:

     - Oprávnění aplikací (pouze globální správci). Příklad: Členství v rolích v jiné aplikaci Azure AD nebo členství v roli vzhledem k prostředku, skupině prostředků nebo předplatnému Azure
     - Delegovaná oprávnění (libovolný uživatel). Příklad: Azure AD, Přihlášení a profil čtení

> [!NOTE]
> Ve výchozím nastavení může každý člen zaregistrovat aplikaci. Informace o tom, jak omezit oprávnění pro registraci aplikací na konkrétní členy, najdete v tématu [Jak se aplikace přidávají do Služby Azure AD](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Zde je to, co vy, globální správce, musíte udělat, abyste pomohli vývojářům připravit jejich aplikaci pro produkční prostředí:

* Konfigurace pravidel přístupu (zásady přístupu/vícefaktorové řízení)
* Konfigurace aplikace tak, aby vyžadovala přiřazení uživatele a přiřazovala uživatele
* Potlačit výchozí prostředí pro souhlas uživatele

## <a name="configure-access-rules"></a>Konfigurace pravidel přístupu
Nakonfigurujte pravidla přístupu pro všechny aplikace SaaS. Můžete například vyžadovat vícefaktorové povolení nebo povolit přístup pouze uživatelům v důvěryhodných sítích. Podrobnosti jsou k dispozici v dokumentu [Konfigurace pravidel přístupu](../conditional-access/app-based-mfa.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Konfigurace aplikace tak, aby vyžadovala přiřazení uživatele a přiřazovala uživatele
Ve výchozím nastavení mohou uživatelé přistupovat k aplikacím, aniž by jim byly přiřazeny. Pokud však aplikace zpřístupňuje role nebo pokud chcete, aby se aplikace zobrazila na přístupovém panelu uživatele, měli byste vyžadovat přiřazení uživatele.

Pokud jste předplatitelem Sady Azure AD Premium nebo Enterprise Mobility Suite (EMS), důrazně doporučujeme používat skupiny. Přiřazení skupin k aplikaci umožňuje delegovat průběžnou správu přístupu na vlastníka skupiny. Můžete vytvořit skupinu nebo požádat odpovědnou stranu ve vaší organizaci, aby skupinu vytvořila pomocí nástroje pro správu skupiny.

[Přiřazení uživatelů a skupin k aplikaci](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>Potlačit souhlas uživatele
Ve výchozím nastavení každý uživatel prochází prostředím pro přihlášení pomocí souhlasu. Prostředí souhlasu, které žádá uživatele o udělení oprávnění k aplikaci, může být znepokojující pro uživatele, kteří nejsou obeznámeni s takovými rozhodnutími.

U aplikací, kterým důvěřujete, můžete zjednodušit uživatelské prostředí souhlasem s aplikací jménem vaší organizace.

Další informace o souhlasu uživatelů a prostředí pro souhlas v Azure najdete v [tématu Integrace aplikací se službou Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="related-articles"></a>Související články
* [Povolení zabezpečeného vzdáleného přístupu k místním aplikacím pomocí proxy aplikací Azure AD](application-proxy.md)
* [Správa přístupu k aplikacím pomocí Azure AD](what-is-access-management.md)

