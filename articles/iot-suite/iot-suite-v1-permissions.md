---
title: Azure IoT Suite a Azure Active Directory | Microsoft Docs
description: Popisuje, jak Azure IoT Suite využívá Azure Active Directory ke správě oprávnění.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 4d0c7aff54ee20cf9ae4e6cdf2fc0718cd0b2f2a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303370"
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Oprávnění na webu azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>Co se stane, když se přihlásíte

Přihlaste se na prvním [azureiotsuite.com][lnk-azureiotsuite], lokality Určuje úrovně oprávnění, můžete mít na základě aktuálně vybraného klienta Azure Active Directory (AAD) a předplatné Azure.

1. Nejprve k naplnění seznamu klientů vidět vedle vaše uživatelské jméno, lokality zjistí z Azure které AAD klienty patří do. V současné době lokality může obsahovat pouze tokeny uživatele pro jednoho klienta v čase. Proto že při přepnutí klientů pomocí rozevíracího seznamu v pravém horním rohu webu v protokoly na tohoto tenanta získat tokeny pro tohoto klienta.

2. V dalším kroku webu zjistí z Azure, které odběry byla přidružena vybraného klienta. Při vytváření nové předkonfigurované řešení zobrazit dostupných předplatných.

3. Nakonec webu načte všechny prostředky v skupiny prostředků a předplatná označené jako předkonfigurovaná řešení a naplní dlaždice na domovské stránce.

Následující části popisují role, která řídí přístup k předkonfigurovaných řešení.

## <a name="aad-roles"></a>Role AAD

Role AAD zřídit předkonfigurované řešení možnost řídit a spravovat uživatele v předkonfigurované řešení.

Další informace o rolích správce můžete najít v AAD v [přiřazení rolí správce ve službě Azure AD][lnk-aad-admin]. Aktuální článek se týká **globálního správce** a **uživatele** role directory používaného předkonfigurovaných řešení.

### <a name="global-administrator"></a>Globální správce

Může být mnoho globální správci za klienta AAD:

* Při vytváření klienta služby AAD se ve výchozím nastavení globální správce tohoto klienta.
* Globální správce, můžete zřídit předkonfigurované řešení a je mu přiřazená **správce** role pro danou aplikaci uvnitř jejich klienta AAD.
* Pokud byl vytvořen jiným uživatelem ve stejném tenantovi AAD aplikace, je výchozí role Globální správce udělit **jen pro čtení**.
* Globální správce můžete přiřadit uživatele k rolím pro aplikace pomocí [portál Azure][lnk-portal].

### <a name="domain-user"></a>Uživatel domény

Může být velký počet uživatelů domény na klienta AAD:

* Uživatel domény můžete zřídit předkonfigurované řešení prostřednictvím [azureiotsuite.com] [ lnk-azureiotsuite] lokality. Standardně je povolen uživatele domény **správce** role v aplikaci zřízené.
* Uživatel domény můžete vytvořit aplikaci pomocí skriptu build.cmd [azure-iot-remote-monitoring][lnk-rm-github-repo], [azure-iot-predictive-maintenance][lnk-pm-github-repo], nebo [azure-iot připojené factory] [ lnk-cf-github-repo] úložiště. Je však výchozí role uživatele domény udělit **jen pro čtení**, protože uživatel domény nemá oprávnění k přiřazení rolí.
* Pokud byl vytvořen jiným uživatelem v tenantovi AAD aplikace, uživatele domény, je přiřazen **jen pro čtení** roli ve výchozím nastavení pro tuto aplikaci.
* Uživatel domény nelze přiřadit role pro aplikace; proto nelze uživatele domény přidat, uživatelé nebo role pro uživatele pro aplikaci i v případě, že se jeho zřízení.

### <a name="guest-user"></a>Uživatele Guest

Může být velký počet uživatelů typu Host za klienta AAD. Uživatele typu Host mají omezenou sadu oprávnění v tenantovi AAD. V důsledku toho uživatele typu Host nejde zřídit předkonfigurované řešení v tenantovi AAD.

Další informace týkající se uživatelů a rolí v AAD najdete v následujících zdrojích informací:

* [Vytvořte uživatele ve službě Azure AD][lnk-create-edit-users]
* [Přiřazení uživatelů k aplikacím][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Role správce předplatného Azure

Role Azure správce řízení možnost mapovat předplatné Azure klient služby AD.

Další informace o Azure správce rolí v článku [postup přidání nebo změna Azure Spolusprávcem, Správce služeb a správce účtu][lnk-admin-roles].

## <a name="application-roles"></a>Aplikační role

Aplikační role řízení přístupu k zařízení v předkonfigurovaném řešení.

Existují dvě definované role a jedna implicitní role, které jsou definované v zřízené aplikaci:

* **Správce:** má plnou kontrolu pro přidání, správu, odeberte zařízení a upravit nastavení.
* **Jen pro čtení:** můžete zobrazit zařízení, pravidla, akce, úlohy a telemetrie.

Oprávnění přiřazená uživateli každou roli v můžete najít [RolePermissions.cs] [ lnk-resource-cs] zdrojový soubor.

### <a name="changing-application-roles-for-a-user"></a>Změna aplikační role pro uživatele

Následující postup vám pomůže nastavit uživatele ve službě Active Directory správcem předkonfigurované řešení.

Musí být globální správce AAD změnit role uživatele:

1. Přejděte na web [Azure Portal][lnk-portal].
2. Vyberte **Azure Active Directory**.
3. Ujistěte se, že používáte adresáře, které jste zvolili na azureiotsuite.com při zřizování řešení. Pokud máte několik adresářů, které jsou spojené s vaším předplatným, můžete přepnout mezi nimi Pokud kliknete na název účtu v pravé horní části portálu.
4. Klikněte na tlačítko **podnikové aplikace, které**, pak **všechny aplikace**.
4. Zobrazit **všechny aplikace** s **žádné** stavu. Poté vyhledejte aplikaci s názvem předkonfigurované řešení.
5. Klikněte na název aplikace, která odpovídá názvu vašeho předkonfigurované řešení.
6. Klikněte na **Uživatelé a skupiny**.
7. Vyberte uživatele, kterého chcete přepnout role.
8. Klikněte na tlačítko **přiřadit** a vyberte roli (například **správce**) chcete uživateli přiřadit ručně, klikněte na zatržítko.

## <a name="faq"></a>Nejčastější dotazy

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Správce služby a v chcete změnit adresář mapování mezi Moje předplatné a konkrétní klienta AAD. Jak tento úkol provést?

V tématu [přidat odběr do adresáře Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Jsem uživatele nebo členem domény na klienta AAD a vytvořili jste předkonfigurované řešení. Jak I přiřazovány roli své aplikaci?

Požádejte o globální správce a zkontrolujte globální správce v tenantovi AAD pak chcete přiřadit role uživatele sami. Případně požádejte globální správce, aby vám přiřadil roli přímo. Pokud chcete změnit klienta AAD předkonfigurované řešení byla nasazena do, najdete v části Další otázka.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Jak lze přepnout, které Moje předkonfigurovaného řešení vzdáleného monitorování a aplikace jsou přiřazeny k klienta AAD?

Můžete spustit nasazení cloudu z <https://github.com/Azure/azure-iot-remote-monitoring> a znovu nasaďte s nově vytvořený klienta AAD. Protože jsou ve výchozím globálním správcem při vytváření klienta služby AAD máte oprávnění k přidání uživatelů a přiřazování rolí pro tyto uživatele.

1. Vytvořte adresář služby AAD v [portál Azure][lnk-portal].
2. Přejděte do části <https://github.com/Azure/azure-iot-remote-monitoring> (Soubor > Nový > Jiné).
3. Spustit `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (například `build.cmd cloud debug myRMSolution`)
4. Po zobrazení výzvy, nastavte **tenantid** být klientovi nově vytvořený místo předchozí klienta.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Změna služby správce nebo Spolusprávce při přihlášení k účtu organizační

Najdete v článku na podporu [změna správce služeb a Spolusprávce při přihlášení k účtu organizační][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Proč se zobrazuje tato chyba? "Váš účet nemá dostatečná oprávnění k vytvoření řešení. "Svého účtu správce nebo zkuste to znovu s jiným účtem."

Podívejte se na následující diagram pokyny:

![][img-flowchart]

> [!NOTE]
> Pokud nadále zobrazovat chybu po ověření, které jsou jako globální správce v tenantovi AAD a spolusprávce u předplatného, musí správce účtu odebrat uživatele a přiřadit potřebná oprávnění v tomto pořadí. Nejdřív přidejte uživatele jako globální správce a poté přidejte uživatele jako spolusprávce na předplatné Azure. Pokud problémy potrvají, obraťte se na [centru pro nápovědu a podporu][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Proč se zobrazuje tato chyba, pokud předplatné Azure? "K vytvoření předem nakonfigurovaná řešení je potřeba předplatné Azure. Bezplatný zkušební účet můžete vytvořit během několika minut."

Pokud jste si jisti, že máte předplatné Azure, ověření klienta mapování pro vaše předplatné a ujistěte se, že je vybrána správná klienta v rozevírací nabídce. Pokud jste ověřit správnost požadované klienta, postupujte podle na předchozím obrázku a ověřit mapování vaše předplatné a tohoto klienta AAD.

## <a name="next-steps"></a>Další postup
Pokračujte ve čtení o IoT Suite, najdete v tématu jak můžete [přizpůsobení předkonfigurovaného řešení][lnk-customize].

[img-flowchart]: media/iot-suite-v1-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
