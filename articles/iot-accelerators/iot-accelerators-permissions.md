---
title: Akcelerátory řešení Azure IoT a Azure Active Directory | Dokumentace Microsoftu
description: Popisuje, jak akcelerátory řešení Azure IoT používá Azure Active Directory pro správu oprávnění.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: e45954389c8dd1b484a7009460c541bf35266973
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713846"
---
# <a name="permissions-on-the-azureiotsolutionscom-site"></a>Oprávnění na webu azureiotsolutions.com

## <a name="what-happens-when-you-sign-in"></a>Co se stane při přihlášení

Přihlaste se na prvním [azureiotsuite.com][lnk-azureiotsolutions], webu určuje úrovně oprávnění mít podle aktuálně vybraného tenanta Azure Active Directory (AAD) a předplatné Azure.

1. Nejprve k naplnění seznamu tenantů vidět vedle vašeho uživatelského jména, lokality dozví z Azure které tenantů AAD, musíte patřit do. V současné době webu můžete získat pouze tokeny uživatele pro jednoho tenanta v čase. Proto při přepnutí klientů pomocí rozevíracího seznamu v pravém horním rohu webu přihlásí vás do tohoto tenanta k získání tokenů pro daného tenanta.

2. V dalším kroku lokality dozví z Azure, které předplatné propojené s vybraného tenanta. Když vytvoříte nový akcelerátor řešení zobrazí dostupná předplatná.

3. Nakonec webu načte všechny prostředky v předplatných a skupin prostředků označené jako akcelerátory řešení a naplní dlaždice na domovskou stránku.

Následující části popisují role, které řídí přístup k akcelerátorům řešení.

## <a name="aad-roles"></a>Rolí AAD

Role AAD řídí schopnost zřízení akcelerátory řešení pro správu uživatelů a některé služby Azure v akcelerátoru řešení.

Další informace o rolích správce najdete v adresáři AAD v [přiřazení rolí správce ve službě Azure AD][lnk-aad-admin]. Na aktuální článek se zaměřuje na **globálního správce** a **uživatele** role adresáře jako akcelerátory řešení.

### <a name="global-administrator"></a>Globální správce

Může existovat mnoho globálních správců na tenanta AAD:

* Když vytvoříte tenanta služby AAD, které jsou ve výchozím nastavení globálním správcem tohoto tenanta.
* Globální správce můžete zřídit akcelerátory řešení basic a standard (základní nasazení používá jeden virtuální počítač Azure).

### <a name="domain-user"></a>Doména uživatel

Může existovat mnoho uživatelů domény na tenanta služby AAD:

* Uživatel domény můžete zřídit akcelerátoru základní řešení prostřednictvím [azureiotsolutions.com] [ lnk-azureiotsolutions] lokality.
* Uživatel domény může vytvářet akcelerátoru základní řešení pomocí rozhraní příkazového řádku.

### <a name="guest-user"></a>Uživatel typu Host

Může existovat mnoho uživatelů typu Host na tenanta AAD. Uživatelé typu Host mají omezenou sadu práv v tenantovi AAD. V důsledku toho uživatele typu Host nemůže zřídit akcelerátoru řešení v tenantovi AAD.

Další informace týkající se uživatelů a rolí ve službě AAD najdete v článku na následujících odkazech:

* [Vytvoření uživatelů ve službě Azure AD][lnk-create-edit-users]
* [Přiřazení uživatelů k aplikacím][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Role správce předplatného Azure

Role správce Azure řídí schopnost mapování předplatné Azure s tenantem AAD.

Další informace o rolích správce Azure v článku [přidat nebo změnit správce předplatného Azure][lnk-admin-roles].

## <a name="faq"></a>Nejčastější dotazy

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Jsem Správce služeb a chci změnit adresář mapování mezi svoje předplatné a konkrétního tenanta AAD. Jak tento úkol provést?

Zobrazit [k přidání existujícího předplatného do adresáře služby Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Chci změnit správce služeb nebo spolupracujícího správce po přihlášení pomocí účtu organizace

Přečtěte si článek podpory [změna správce služeb a spolupracujícího správce po přihlášení pomocí účtu organizace][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Proč se zobrazuje tato chyba? "Váš účet nemá potřebná oprávnění k vytvoření řešení. "Obraťte se na svého správce účtu, nebo zkuste s jiným účtem."

Podívejte se na následující diagram pokyny:

![][img-flowchart]

> [!NOTE]
> Pokud bude pořád zobrazovat chyby po ověření je globálním správcem tenanta AAD spolusprávcem předplatného a mít účet správce odeberete uživateli a znovu přiřadíte potřebná oprávnění v tomto pořadí. Nejprve přidat uživatele jako globální správce a pak přidat uživatele jako spolusprávce předplatného Azure. Pokud problémy přetrvávají, obraťte se na [Nápověda a podpora][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Proč vidím tuto chybu, když mám předplatné Azure? "Předplatné Azure je potřeba vytvořit předem nakonfigurovaná řešení. Bezplatný zkušební účet můžete vytvořit během několika minut."

Pokud si nejste jisti, že máte předplatné Azure, ověřit mapování pro vaše předplatné tenanta a ujistěte se, že je vybrán správný tenanta v rozevírací nabídce. Pokud jste ověřili požadovaný tenant je správný, postupujte podle předchozí diagram a ověřit mapování vašeho předplatného a tohoto tenanta AAD.

## <a name="next-steps"></a>Další postup
Chcete-li pokračovat v seznamování s využitím akcelerátorů řešení IoT, naleznete v tématu Jak [přizpůsobení akcelerátoru řešení][lnk-customize].

[img-flowchart]: media/iot-accelerators-permissions/flowchart.png

[lnk-azureiotsolutions]: https://www.azureiotsolutions.com
[lnk-rm-github-repo]: https://github.com/Azure/remote-monitoring-services-dotnet
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]:../active-directory/users-groups-roles/directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com
[lnk-create-edit-users]:../active-directory/fundamentals/active-directory-users-profile-azure-portal.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-accelerators-remote-monitoring-customize.md
