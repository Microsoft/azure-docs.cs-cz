---
title: Omezení aplikace zaregistrované v Azure Active Directory pro sadu uživatelů
description: Naučte se, jak omezit přístup k aplikacím registrovaným ve službě Azure AD na vybranou skupinu uživatelů.
services: active-directory
documentationcenter: ''
author: kalyankrishna1
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a51c49633e68fdc5f9afd4bf0205adaa625940ff
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812972"
---
# <a name="how-to-restrict-your-app-to-a-set-of-users"></a>Postup: Omezení aplikace na sadu uživatelů

Aplikace zaregistrované v tenantovi Azure Active Directory (Azure AD) jsou ve výchozím nastavení dostupné všem uživatelům tenanta, kteří se úspěšně ověřují.

Podobně platí, že v případě [víceklientské](howto-convert-app-to-be-multi-tenant.md) aplikace budou mít všichni uživatelé v TENANTOVI Azure AD, kde se tato aplikace zřídí, přístup k této aplikaci až po úspěšném ověření v příslušném tenantovi.

Správci klientů a vývojáři mají často požadavky na to, že aplikace musí být omezená na určitou skupinu uživatelů. Vývojáři můžou totéž dosáhnout pomocí oblíbených autorizačních vzorů, jako je Access Control na základě rolí (RBAC), ale tento přístup vyžaduje významnou práci v rámci vývojářů.

Azure AD umožňuje správcům a vývojářům klientů omezit aplikaci na určitou skupinu uživatelů nebo skupin zabezpečení v tenantovi.

## <a name="supported-app-configurations"></a>Podporované konfigurace aplikací

Možnost omezit aplikaci na určitou skupinu uživatelů nebo skupin zabezpečení v tenantovi funguje s následujícími typy aplikací:

- Aplikace nakonfigurované pro federované jednotné přihlašování s ověřováním založeném na SAML
- Aplikace proxy aplikací, které používají předběžné ověřování Azure AD
- Aplikace vytvořené přímo na aplikační platformě Azure AD, které používají ověřování OAuth 2.0/OpenID, po tom, co uživatel nebo správce souhlasí s touto aplikací.

     > [!NOTE]
     > Tato funkce je dostupná jenom pro webovou aplikaci/webové rozhraní API a podnikové aplikace. Aplikace, které jsou zaregistrované jako [nativní](quickstart-v1-integrate-apps-with-azure-ad.md) , se nedají omezit na skupinu uživatelů nebo skupin zabezpečení v tenantovi.

## <a name="update-the-app-to-enable-user-assignment"></a>Aktualizace aplikace, aby se povolilo přiřazení uživatele

Existují dva způsoby, jak vytvořit aplikaci s povoleným přiřazením uživatele. Jedna z nich vyžaduje roli **globálního správce** , druhá ne.

### <a name="enterprise-applications-requires-the-global-adminstrator-role"></a>Podnikové aplikace (vyžaduje globální roli správce)

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
1. Vyhledejte nastavení **přiřazení uživatele požadované?** a nastavte ho na **Ano**. Pokud je tato možnost nastavená na **hodnotu Ano**, uživatelé se musí nejdřív přiřadit k této aplikaci dřív, než k nim budou mít přístup.
1. Vyberte **Uložit** a uložte tuto změnu konfigurace.

### <a name="app-registration"></a>Registrace aplikace

1. Přejít na [**Azure Portal**](https://portal.azure.com/).
1. V horním panelu vyberte přihlášený účet. 
1. V části **adresář**vyberte TENANTA Azure AD, ve kterém se aplikace zaregistruje.
1. V navigaci vlevo vyberte **Azure Active Directory**.
1. V podokně **Azure Active Directory** v navigační nabídce **Azure Active Directory** levé straně vyberte **Registrace aplikací** .
1. Vytvořte nebo vyberte aplikaci, kterou chcete spravovat. Musíte být **vlastníkem** této registrace aplikace.
1. Na stránce **Přehled** aplikace použijte odkaz **spravovaná aplikace v místním adresáři** v horní části stránky. Tím přejdete na _spravovanou podnikovou aplikaci_ registrace vaší aplikace.
1. V okně navigace vlevo vyberte **vlastnosti**.
1. Vyhledejte nastavení **přiřazení uživatele požadované?** a nastavte ho na **Ano**. Pokud je tato možnost nastavená na **hodnotu Ano**, uživatelé se musí nejdřív přiřadit k této aplikaci dřív, než k nim budou mít přístup.
1. Vyberte **Uložit** a uložte tuto změnu konfigurace.

## <a name="assign-users-and-groups-to-the-app"></a>Přiřazení uživatelů a skupin k aplikaci

Jakmile nakonfigurujete aplikaci tak, aby umožňovala přiřazení uživatele, můžete pokračovat a přiřadit uživatele a skupiny k aplikaci.

1. V navigační nabídce levé části aplikace vyberte podokno **Uživatelé a skupiny** .
1. V horní části seznamu **uživatelů a skupin** vyberte tlačítko **Přidat uživatele** a otevřete podokno **Přidat přiřazení** .
1. V podokně **Přidat přiřazení** vyberte selektor **uživatelů** . 

     Zobrazí se seznam uživatelů a skupin zabezpečení spolu s textovým polem, kde můžete vyhledat určitého uživatele nebo skupinu. Tato obrazovka umožňuje vybrat více uživatelů a skupin v jednom přechodu.

1. Až budete hotovi s výběrem uživatelů a skupin, přejděte kliknutím na tlačítko **Vybrat** v dolní části na další část.
1. Stisknutím tlačítka **přiřadit** v dolní části dokončíte přiřazení uživatelů a skupin do aplikace. 
1. Ověřte, že se uživatelé a skupiny, které jste přidali, zobrazují v seznamu aktualizovaných **uživatelů a skupin** .

