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
ms.openlocfilehash: a9b0090fa13a6d2f7a837a4f5ffee37d70893318
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88116899"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Postupy: omezení aplikace Azure AD na skupinu uživatelů v tenantovi Azure AD

Aplikace zaregistrované v tenantovi Azure Active Directory (Azure AD) jsou ve výchozím nastavení dostupné všem uživatelům tenanta, kteří se úspěšně ověřují.

Podobně platí, že v případě [víceklientské](howto-convert-app-to-be-multi-tenant.md) aplikace budou mít všichni uživatelé v TENANTOVI Azure AD, kde se tato aplikace zřídí, přístup k této aplikaci až po úspěšném ověření v příslušném tenantovi.

Správci klientů a vývojáři mají často požadavky na to, že aplikace musí být omezená na určitou skupinu uživatelů. Vývojáři můžou totéž dosáhnout pomocí oblíbených autorizačních vzorů, jako je řízení přístupu na základě role Azure (Azure RBAC), ale tento přístup vyžaduje významnou práci, která je součástí vývojáře.

Správci klientů a vývojáři mohou aplikaci omezit na konkrétní skupinu uživatelů nebo skupin zabezpečení v tenantovi pomocí této integrované funkce služby Azure AD.

## <a name="supported-app-configurations"></a>Podporované konfigurace aplikací

Možnost omezit aplikaci na určitou skupinu uživatelů nebo skupin zabezpečení v tenantovi funguje s následujícími typy aplikací:

- Aplikace nakonfigurované pro federované jednotné přihlašování s ověřováním založeném na SAML
- Aplikace proxy aplikací, které používají předběžné ověřování Azure AD
- Aplikace vytvořené přímo na aplikační platformě Azure AD, které používají ověřování OAuth 2.0/OpenID, po tom, co uživatel nebo správce souhlasí s touto aplikací.

     > [!NOTE]
     > Tato funkce je dostupná jenom pro webovou aplikaci/webové rozhraní API a podnikové aplikace. Aplikace, které jsou zaregistrované jako [nativní](./quickstart-register-app.md) , se nedají omezit na skupinu uživatelů nebo skupin zabezpečení v tenantovi.

## <a name="update-the-app-to-enable-user-assignment"></a>Aktualizace aplikace, aby se povolilo přiřazení uživatele

Existují dva způsoby, jak vytvořit aplikaci s povoleným přiřazením uživatele. Jedna z nich vyžaduje roli **globálního správce** , druhá ne.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Podnikové aplikace (vyžaduje roli globálního správce)

1. Přejít na [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce**.
1. V horním panelu vyberte přihlášený účet. 
1. V části **adresář**vyberte TENANTA Azure AD, ve kterém se aplikace zaregistruje.
1. V navigaci vlevo vyberte **Azure Active Directory**. Pokud v navigačním podokně není Azure Active Directory k dispozici, postupujte následovně:

    1. V horní části hlavní navigační nabídky vlevo vyberte **všechny služby** .
    1. Do vyhledávacího pole filtru zadejte **Azure Active Directory** a pak vyberte položku **Azure Active Directory** z výsledku.

1. V podokně **Azure Active Directory** v navigační nabídce **Azure Active Directory** vlevo vyberte **podnikové aplikace** .
1. Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

     Pokud nevidíte tu aplikaci, kterou chcete zobrazit, pomocí různých filtrů v horní části seznamu **všechny aplikace seznam všech aplikací** omezte nebo posuňte seznam a vyhledejte svoji aplikaci.

1. Ze seznamu vyberte aplikaci, kterou chcete přiřadit uživatele nebo skupinu zabezpečení.
1. Na stránce **Přehled** aplikace vyberte možnost **vlastnosti** z navigační nabídky aplikace na levé straně.
1. Vyhledejte nastavení **přiřazení uživatele požadované?** a nastavte ho na **Ano**. Pokud je tato možnost nastavená na **hodnotu Ano**, uživatelé v tenantovi musí být nejprve přiřazeni k této aplikaci nebo se nebudou moci přihlásit k této aplikaci.
1. Vyberte **Uložit** a uložte tuto změnu konfigurace.

### <a name="app-registration"></a>Registrace aplikací

1. Přejít na [**Azure Portal**](https://portal.azure.com/).
1. V horním panelu vyberte přihlášený účet. 
1. V části **adresář**vyberte TENANTA Azure AD, ve kterém se aplikace zaregistruje.
1. V navigaci vlevo vyberte **Azure Active Directory**.
1. V podokně **Azure Active Directory** v navigační nabídce **Azure Active Directory** levé straně vyberte **Registrace aplikací** .
1. Vytvořte nebo vyberte aplikaci, kterou chcete spravovat. Musíte být **vlastníkem** této registrace aplikace.
1. Na stránce **Přehled** aplikace použijte odkaz **spravovaná aplikace v místním adresáři** v horní části stránky. Tím přejdete na _spravovanou podnikovou aplikaci_ registrace vaší aplikace.
1. V okně navigace vlevo vyberte **vlastnosti**.
1. Vyhledejte nastavení **přiřazení uživatele požadované?** a nastavte ho na **Ano**. Pokud je tato možnost nastavená na **hodnotu Ano**, uživatelé v tenantovi musí být nejprve přiřazeni k této aplikaci nebo se nebudou moci přihlásit k této aplikaci.
1. Vyberte **Uložit** a uložte tuto změnu konfigurace.

## <a name="assign-users-and-groups-to-the-app"></a>Přiřazení uživatelů a skupin k aplikaci

Jakmile nakonfigurujete aplikaci tak, aby umožňovala přiřazení uživatele, můžete pokračovat a přiřadit uživatele a skupiny k aplikaci.

1. V navigační nabídce levé části aplikace vyberte podokno **Uživatelé a skupiny** .
1. V horní části seznamu **uživatelů a skupin** vyberte tlačítko **Přidat uživatele** a otevřete podokno **Přidat přiřazení** .
1. V podokně **Přidat přiřazení** vyberte selektor **uživatelů** . 

     Zobrazí se seznam uživatelů a skupin zabezpečení spolu s textovým polem, kde můžete vyhledat určitého uživatele nebo skupinu. Tato obrazovka umožňuje vybrat více uživatelů a skupin v jednom přechodu.

1. Až budete hotovi s výběrem uživatelů a skupin, přejděte kliknutím na tlačítko **Vybrat** v dolní části na další část.
1. Volitelné Pokud jste ve své aplikaci definovali role aplikace, můžete k přiřazení vybraných uživatelů a skupin k jedné z rolí aplikace použít možnost **Vybrat roli** . 
1. Stisknutím tlačítka **přiřadit** v dolní části dokončíte přiřazení uživatelů a skupin do aplikace. 
1. Ověřte, že se uživatelé a skupiny, které jste přidali, zobrazují v seznamu aktualizovaných **uživatelů a skupin** .

## <a name="more-information"></a>Další informace

- [Postupy: Přidání rolí aplikace do aplikace](./howto-add-app-roles-in-azure-ad-apps.md)
- [Přidejte autorizaci pomocí rolí aplikace & role deklarace identity do webové aplikace ASP.NET Core.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Používání skupin zabezpečení a rolí aplikací v aplikacích (video)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [Azure Active Directory nyní s deklaracemi skupin a aplikačními rolemi](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Manifest aplikace Azure Active Directory](./reference-app-manifest.md)