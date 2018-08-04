---
title: Registrace aplikace pomocí Azure Active Directory | Dokumentace Microsoftu
description: Napsané pro IT specialisty, tento článek obsahuje pokyny pro integraci aplikací Azure Active Directory.
services: active-directory
documentationcenter: ''
author: kgremban
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2018
ms.author: kgremban
ms.custom: seohack1
ms.openlocfilehash: 41829add17f69c5f051618195247ed2a5512ca64
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505632"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Vývoj-obchodních aplikací pro Azure Active Directory
Tato příručka obsahuje základní informace o vývoji – obchodní aplikace (LoB) pro Azure Active Directory (AD). Jeho zamýšlenou cílovou skupinou je globální správce Active Directory nebo Office 365.

## <a name="overview"></a>Přehled
Vytváření aplikací, které jsou integrované s Azure AD poskytuje uživatelům ve vaší organizaci jednotné přihlašování s Office 365. Aplikace s Azure AD získáte tak kontrolu nad zásady ověřování pro aplikaci. Další informace o podmíněný přístup a ochrana aplikací pomocí služby Multi-Factor authentication (MFA) najdete v tématu [konfigurace pravidel přístupu](active-directory-conditional-access-azure-portal-get-started.md).

Registrace aplikace pomocí Azure Active Directory. Registruje se aplikace znamená, že vývojáři slouží k ověřování uživatelů a žádat o přístup k prostředkům uživatele, jako jsou e-maily, kalendáře a dokumenty Azure AD.

Každý člen vašeho adresáře (ne Hosté) můžou registrovat aplikace, jinak známé jako *vytvoření objektu aplikace*.

Registrace aplikace umožňuje všem uživatelům provádět následující akce:

* Získat identitu pro své aplikace, která rozpozná Azure AD
* Získat jednu nebo více tajné klíče nebo klíče, které aplikace můžete použít ke svému ověření ke službě AD
* Vytvoření aplikace na webu Azure Portal s vlastním názvem, loga, atd.
* Použití funkce ověřování Azure AD do své aplikace, včetně:

  * Řízení přístupu na základě role (RBAC)
  * Azure Active Directory jako server autorizace oAuth (zabezpečení rozhraní API vystavené aplikace)
* Deklarujte požadovaná oprávnění nezbytná pro aplikaci na funkci podle očekávání, včetně:

      - Oprávnění aplikací (jenom globální správci). Příklad: členství v roli v jiné službě Azure AD aplikace nebo členství v rolích vzhledem k prostředku Azure, skupinu prostředků nebo předplatného
      - Delegovaná oprávnění (všechny uživatele). Příklad: Azure AD, přihlášení a čtení profilu

> [!NOTE]
> Ve výchozím nastavení může každý člen zaregistrovat aplikaci. Zjistěte, jak omezit oprávnění pro registraci aplikace do konkrétních členů, najdete v článku [jak se aplikace přidávají do služby Azure AD](develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Zde je, co jste globální správce, potřeba udělat, což vývojářům umožňuje vytvořit jejich aplikace připravené pro produkční prostředí:

* Konfigurace pravidel přístupu (zásad přístupu vícefaktorové ověřování)
* Nakonfiguruje aplikaci, aby vyžadují přiřazení uživatelů a přiřazení uživatelů
* Potlačit výchozí možnosti souhlasu uživatele

## <a name="configure-access-rules"></a>Konfigurace pravidel přístupu
Konfigurace pravidel jednotlivým aplikacím přístup k aplikacím SaaS. Můžete třeba vyžadovat vícefaktorové ověřování nebo povolení přístupu uživatelům jenom v důvěryhodných sítích. Podrobnosti k tomu jsou k dispozici v dokumentu [konfigurace pravidel přístupu](active-directory-conditional-access-azure-portal-get-started.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Nakonfiguruje aplikaci, aby vyžadují přiřazení uživatelů a přiřazení uživatelů
Ve výchozím nastavení uživatelé mohou využívat aplikace bez přiřazení. Pokud aplikace jednotlivě vystavuje pro role, nebo pokud chcete, aby aplikace bude zobrazovat na přístupovém panelu uživatele, ale by měl vyžadují přiřazení uživatele.

[Vyžádání přiřazení uživatele](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Pokud jste předplatitelem Azure AD Premium nebo Enterprise Mobility Suite (EMS), důrazně doporučujeme používat skupiny. Přiřazování skupin k aplikaci umožňuje delegovat správu probíhající přístup vlastníkovi skupiny. Můžete vytvořit skupinu, nebo požádejte stran zodpovědná ve vaší organizaci vytvářet skupiny pomocí vaší skupině správy zařízení.

[Přiřazování uživatelů k aplikaci](active-directory-applications-guiding-developers-assigning-users.md)  
[Přiřazování skupin k aplikaci](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>Potlačit souhlasu uživatele
Ve výchozím nastavení každý uživatel prochází prostředí pro vyjádření souhlasu pro přihlášení. Prostředí pro vyjádření souhlasu s dotazem, uživatelům udělit oprávnění k aplikaci, může být zneklidňovat pro uživatele, kteří nejsou obeznámeni s rozhodování.

Pro aplikace, kterým důvěřujete můžete zjednodušit uživatelské prostředí tím vyjadřuje souhlas jménem svojí organizace.

Další informace o souhlas uživatele a souhlasu prostředí v Azure, najdete v části [integrace aplikací s Azure Active Directory](develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="related-articles"></a>Související články
* [Povolit zabezpečený vzdálený přístup k místním aplikacím přes Proxy aplikací Azure AD](manage-apps/application-proxy.md)
* [Podmíněný přístup služby Azure ve verzi Preview pro aplikace SaaS](active-directory-conditional-access-azure-portal-get-started.md)
* [Správa přístupu k aplikacím v Azure AD](manage-apps/what-is-access-management.md)
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](active-directory-apps-index.md)
