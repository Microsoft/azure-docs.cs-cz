---
title: Omezení aplikace Azure AD na skupinu uživatelů | Azure
titleSuffix: Microsoft identity platform
description: Naučte se, jak omezit přístup k aplikacím registrovaným ve službě Azure AD na vybranou skupinu uživatelů.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f3eff6f5660089472ef431bad9a4f05be1fffe24
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104141"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Postupy: omezení aplikace Azure AD na skupinu uživatelů v tenantovi Azure AD

Aplikace zaregistrované v tenantovi Azure Active Directory (Azure AD) jsou ve výchozím nastavení dostupné všem uživatelům tenanta, kteří se úspěšně ověřují.

Podobně platí, že v případě [víceklientské](howto-convert-app-to-be-multi-tenant.md) aplikace budou mít všichni uživatelé v TENANTOVI Azure AD, kde se tato aplikace zřídí, přístup k této aplikaci až po úspěšném ověření v příslušném tenantovi.

Správci klientů a vývojáři mají často požadavky na to, že aplikace musí být omezená na určitou skupinu uživatelů. Vývojáři můžou totéž dosáhnout pomocí oblíbených autorizačních vzorů, jako je řízení přístupu na základě role Azure (Azure RBAC), ale tento přístup vyžaduje významnou práci, která je součástí vývojáře.

Správci klientů a vývojáři mohou aplikaci omezit na konkrétní skupinu uživatelů nebo skupin zabezpečení v tenantovi pomocí této integrované funkce služby Azure AD.

## <a name="supported-app-configurations"></a>Podporované konfigurace aplikací

Možnost omezit aplikaci na určitou skupinu uživatelů nebo skupin zabezpečení v tenantovi funguje s následujícími typy aplikací:

- Aplikace nakonfigurované pro federované jednotné přihlašování s ověřováním založeném na SAML.
- Aplikace proxy aplikací, které používají předběžné ověřování Azure AD.
- Aplikace vytvořené přímo na aplikační platformě Azure AD, které používají ověřování OAuth 2.0/OpenID, po tom, co uživatel nebo správce souhlasí s touto aplikací.

     > [!NOTE]
     > Tato funkce je dostupná jenom pro webovou aplikaci/webové rozhraní API a podnikové aplikace. Aplikace, které jsou zaregistrované jako [nativní](./quickstart-register-app.md) , se nedají omezit na skupinu uživatelů nebo skupin zabezpečení v tenantovi.

## <a name="update-the-app-to-enable-user-assignment"></a>Aktualizace aplikace, aby se povolilo přiřazení uživatele

Existují dva způsoby, jak vytvořit aplikaci s povoleným přiřazením uživatele. Jedna z nich vyžaduje roli **globálního správce** , druhá ne.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Podnikové aplikace (vyžaduje roli globálního správce)

1. Přihlaste se k <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> jako **globální správce**.
1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu vyberte aplikaci, kterou chcete přiřadit uživateli nebo skupině zabezpečení. 
    Pomocí filtrů v horní části okna vyhledejte konkrétní aplikaci.
1. Na stránce **Přehled** aplikace v části **Spravovat** vyberte **vlastnosti**.
1. Vyhledejte nastavení **přiřazení uživatele požadované?** a nastavte ho na **Ano**. Pokud je tato možnost nastavená na **hodnotu Ano**, uživatelé v tenantovi musí být nejprve přiřazeni k této aplikaci nebo se nebudou moci přihlásit k této aplikaci.
1. Vyberte **Uložit**.

### <a name="app-registration"></a>Registrace aplikace

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**.
1. Vytvořte nebo vyberte aplikaci, kterou chcete spravovat. Musíte být **vlastníkem** této aplikace.
1. Na stránce **Přehled** aplikace vyberte v části **základy** odkaz **spravovaná aplikace v místním adresáři** .
1. V části **Spravovat** vyberte **Vlastnosti**.
1. Vyhledejte nastavení **přiřazení uživatele požadované?** a nastavte ho na **Ano**. Pokud je tato možnost nastavená na **hodnotu Ano**, uživatelé v tenantovi musí být nejprve přiřazeni k této aplikaci nebo se nebudou moci přihlásit k této aplikaci.
1. Vyberte **Uložit**.

## <a name="assign-users-and-groups-to-the-app"></a>Přiřazení uživatelů a skupin k aplikaci

Jakmile nakonfigurujete aplikaci tak, aby umožňovala přiřazení uživatele, můžete pokračovat a přiřadit uživatele a skupiny k aplikaci.

1. V části **Spravovat** vyberte **uživatele a skupiny**  >  **Přidat uživatele nebo skupinu** .
1. Vyberte selektor **uživatelů** . 

     Zobrazí se seznam uživatelů a skupin zabezpečení spolu s textovým polem, kde můžete vyhledat určitého uživatele nebo skupinu. Tato obrazovka umožňuje vybrat více uživatelů a skupin v jednom přechodu.

1. Až budete hotovi s výběrem uživatelů a skupin, vyberte **Vybrat**.
1. Volitelné Pokud jste ve své aplikaci definovali role aplikace, můžete k přiřazení vybraných uživatelů a skupin k jedné z rolí aplikace použít možnost **Vybrat roli** . 
1. Vyberte **přiřadit** a dokončete přiřazení uživatelů a skupin k aplikaci. 
1. Ověřte, že se uživatelé a skupiny, které jste přidali, zobrazují v seznamu aktualizovaných **uživatelů a skupin** .

## <a name="more-information"></a>Další informace

- [Postupy: Přidání rolí aplikace do aplikace](./howto-add-app-roles-in-azure-ad-apps.md)
- [Přidejte autorizaci pomocí rolí aplikace & role deklarace identity do webové aplikace ASP.NET Core.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Používání skupin zabezpečení a rolí aplikací v aplikacích (video)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory nyní s deklaracemi skupin a aplikačními rolemi](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifest aplikace Azure Active Directory](./reference-app-manifest.md)
