---
title: Azure IoT Suite a Azure Active Directory | Dokumentace Microsoftu
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
ms.openlocfilehash: a56d535ee06a097c7c18bcd507c25708f6a33f91
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296920"
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Oprávnění na webu azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>Co se stane při přihlášení

Přihlaste se na prvním [azureiotsuite.com][lnk-azureiotsuite], webu určuje úrovně oprávnění mít podle aktuálně vybraného tenanta Azure Active Directory (AAD) a předplatné Azure.

1. Nejprve k naplnění seznamu tenantů vidět vedle vašeho uživatelského jména, lokality dozví z Azure které tenantů AAD, musíte patřit do. V současné době webu můžete získat pouze tokeny uživatele pro jednoho tenanta v čase. Proto při přepnutí klientů pomocí rozevíracího seznamu v pravém horním rohu webu přihlásí vás do tohoto tenanta k získání tokenů pro daného tenanta.

2. V dalším kroku lokality dozví z Azure, které předplatné propojené s vybraného tenanta. Můžete zobrazit dostupná předplatná při vytváření nového předkonfigurovaného řešení.

3. Nakonec webu načte všechny prostředky v předplatných a skupin prostředků označené jako předkonfigurovaná řešení a naplní dlaždice na domovskou stránku.

Následující části popisují role, které řídí přístup k předkonfigurovaných řešení.

## <a name="aad-roles"></a>Rolí AAD

Rolí AAD zřídit předkonfigurované řešení možnost řídit a spravovat uživatele v předkonfigurovaném řešení.

Další informace o rolích správce najdete v adresáři AAD v [přiřazení rolí správce ve službě Azure AD][lnk-aad-admin]. Na aktuální článek se zaměřuje na **globálního správce** a **uživatele** role adresáře jako předkonfigurovaná řešení.

### <a name="global-administrator"></a>Globální správce

Může existovat mnoho globálních správců na tenanta AAD:

* Když vytvoříte tenanta služby AAD, které jsou ve výchozím nastavení globálním správcem tohoto tenanta.
* Globální správce může zřídit předkonfigurované řešení a je přiřazena **správce** role pro aplikaci v jejich tenantovi AAD.
* Pokud jiný uživatel ve stejném tenantovi AAD se vytvoří aplikace, je výchozí role udělena globální správci **jen pro čtení**.
* Globální správce můžete přiřadit uživatele k rolím pro aplikace využívající [webu Azure portal][lnk-portal].

### <a name="domain-user"></a>Doména uživatel

Může existovat mnoho uživatelů domény na tenanta služby AAD:

* Uživatel domény můžete zřídit předkonfigurované řešení prostřednictvím [azureiotsuite.com] [ lnk-azureiotsuite] lokality. Ve výchozím se oprávnění uživatele domény **správce** role v aplikaci zřízené.
* Uživatel domény můžete vytvořit aplikaci pomocí skriptu build.cmd [azure-iot-remote-monitoring][lnk-rm-github-repo], [azure-iot-predictive-maintenance] [ lnk-pm-github-repo], nebo [azure-iot-connected-factory] [ lnk-cf-github-repo] úložiště. Výchozí role udělená uživateli domény ale **jen pro čtení**, protože doména uživatel nemá oprávnění k přiřazení rolí.
* Pokud jiný uživatel v tenantovi AAD se vytvoří aplikace, je přiřazen uživatele domény **jen pro čtení** roli ve výchozím nastavení pro danou aplikaci.
* Uživatel domény nelze přiřadit role pro aplikace. proto nelze jako uživatele domény přidat, uživatele nebo role pro uživatele pro aplikaci i v případě, že ho zřídili.

### <a name="guest-user"></a>Uživatel typu Host

Může existovat mnoho uživatelů typu Host na tenanta AAD. Uživatelé typu Host mají omezenou sadu práv v tenantovi AAD. V důsledku toho uživatele typu Host nemůže zřídit předkonfigurované řešení v tenantovi AAD.

Další informace týkající se uživatelů a rolí ve službě AAD najdete v článku na následujících odkazech:

* [Vytvoření uživatelů ve službě Azure AD][lnk-create-edit-users]
* [Přiřazení uživatelů k aplikacím][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Role správce předplatného Azure

Role správce Azure řídí schopnost mapování předplatného Azure na klienta služby AD.

Další informace o rolích správce Azure v článku [přidat nebo změnit správce předplatného Azure][lnk-admin-roles].

## <a name="application-roles"></a>Aplikační role

Aplikační role řízení přístupu k zařízení v předkonfigurovaném řešení.

Existují dvě definované role a jedna implicitní role definované v zřízené aplikaci:

* **Správce:** má plnou kontrolu nad přidat, odebrat zařízení a spravovat nastavení upravit.
* **Jen pro čtení:** můžete zobrazit zařízení, pravidla, akce, úlohy a telemetrie.

Oprávnění přiřazená ke každé roli v můžete najít [RolePermissions.cs] [ lnk-resource-cs] zdrojový soubor.

### <a name="changing-application-roles-for-a-user"></a>Změna aplikační role pro uživatele

Následující postup slouží k nastavit uživatele ve službě Active Directory správce předkonfigurovaného řešení.

Musíte být globální správce AAD, chcete-li změnit role uživatele:

1. Přejděte na web [Azure Portal][lnk-portal].
2. Vyberte **Azure Active Directory**.
3. Ujistěte se, že používáte adresáře, který jste zvolili na webu azureiotsuite.com při zřizování řešení. Pokud máte několik adresářů, které jsou spojené s předplatným, můžete přepnout mezi nimi kliknutím na název svého účtu v pravém horním rohu portálu.
4. Klikněte na tlačítko **podnikové aplikace**, pak **všechny aplikace**.
4. Zobrazit **všechny aplikace** s **jakékoli** stav. Vyhledejte aplikaci s názvem předkonfigurovaného řešení.
5. Klikněte na název aplikace, která odpovídá názvu vašeho předkonfigurovaného řešení.
6. Klikněte na **Uživatelé a skupiny**.
7. Vyberte uživatele, který chcete přepnout role.
8. Klikněte na tlačítko **přiřadit** a vyberte roli (například **správce**) chcete uživateli přiřadit ručně, klikněte na zatržítko.

## <a name="faq"></a>Nejčastější dotazy

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Jsem Správce služeb a chci změnit adresář mapování mezi svoje předplatné a konkrétního tenanta AAD. Jak tento úkol provést?

Zobrazit [k přidání existujícího předplatného do adresáře služby Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Jsem uživatel/člena domény v tenantovi AAD a jsem vytvořil předkonfigurovaného řešení. Jak můžu získat přiřazenou roli pro svoji aplikaci?

Požádejte globálního správce a ujistěte se, jste globálním správcem v tenantovi AAD a pak přiřazení rolí uživatelům sami. Případně požádejte přiřazení je přímo role globálního správce. Pokud byste chtěli změna klienta AAD vaše předkonfigurované řešení byla nasazena do, viz další dotaz.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Jak se přepíná tenanta AAD, které Moje předkonfigurovaného řešení vzdáleného monitorování a aplikace jsou přiřazeny k?

Můžete spustit z cloudového nasazení <https://github.com/Azure/azure-iot-remote-monitoring> a znovu nasadit pomocí nově vytvořeného tenanta AAD. Vzhledem k tomu, že jsou ve výchozím nastavení, globálního správce, když vytvoříte tenanta služby AAD, nemáte oprávnění k přidání uživatelů a přiřazení rolí uživatelům.

1. Vytvoření adresáře v AAD [webu Azure portal][lnk-portal].
2. Přejděte do části <https://github.com/Azure/azure-iot-remote-monitoring> (Soubor > Nový > Jiné).
3. Spustit `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (například `build.cmd cloud debug myRMSolution`)
4. Po zobrazení výzvy nastavte **tenantid** bude nově vytvořeného tenanta namísto předchozí tenanta.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Chci změnit správce služeb nebo spolupracujícího správce po přihlášení účet organizace

Přečtěte si článek podpory [změna správce služeb nebo spolupracujícího správce po přihlášení účet organizace][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Proč se zobrazuje tato chyba? "Váš účet nemá potřebná oprávnění k vytvoření řešení. "Obraťte se na svého správce účtu, nebo zkuste s jiným účtem."

Podívejte se na následující diagram pokyny:

![][img-flowchart]

> [!NOTE]
> Pokud bude pořád zobrazovat chyby po ověření je globálním správcem v tenantovi AAD spolupracující správce daného předplatného a mít účet správce odeberete uživateli a znovu přiřadíte potřebná oprávnění v tomto pořadí. Nejprve přidat uživatele jako globální správce a pak přidat uživatele jako spolusprávce předplatného Azure. Pokud problémy přetrvávají, obraťte se na [Nápověda a podpora][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Proč vidím tuto chybu, když mám předplatné Azure? "Předplatné Azure je potřeba vytvořit předem nakonfigurovaná řešení. Bezplatný zkušební účet můžete vytvořit během několika minut."

Pokud si nejste jisti, že máte předplatné Azure, ověřit mapování pro vaše předplatné tenanta a ujistěte se, že je vybrán správný tenanta v rozevírací nabídce. Pokud jste ověřili požadovaný tenant je správný, postupujte podle předchozí diagram a ověřit mapování vašeho předplatného a tohoto tenanta AAD.

## <a name="next-steps"></a>Další postup
Pokračujte ve čtení o sadě IoT Suite, najdete v tématu Jak [přizpůsobení předkonfigurovaného řešení][lnk-customize].

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
