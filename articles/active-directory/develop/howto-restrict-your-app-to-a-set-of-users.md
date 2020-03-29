---
title: Omezení aplikace Azure AD na sadu uživatelů | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak omezit přístup k aplikacím registrovaným ve službě Azure AD na vybranou sadu uživatelů.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cccd2df334828c0b8103e4da2ffcd8549673b69c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696992"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users"></a>Postup: Omezte aplikaci Azure AD na sadu uživatelů

Aplikace registrované v tenantovi Služby Azure Active Directory (Azure AD) jsou ve výchozím nastavení k dispozici všem uživatelům klienta, kteří se úspěšně ověřují.

Podobně v případě [víceklientské](howto-convert-app-to-be-multi-tenant.md) aplikace budou mít všichni uživatelé v tenantovi Azure AD, kde je tato aplikace zřízená, přístup k této aplikaci, jakmile se úspěšně ověří v příslušném tenantovi.

Správci a vývojáři klienta mají často požadavky, kde musí být aplikace omezena na určitou sadu uživatelů. Vývojáři mohou provést stejné pomocí populární autorizační vzory, jako je řízení přístupu na základě rolí (RBAC), ale tento přístup vyžaduje značné množství práce na části vývojáře.

Azure AD umožňuje správcům klienta a vývojářům omezit aplikaci na určitou sadu uživatelů nebo skupin zabezpečení v tenantovi.

## <a name="supported-app-configurations"></a>Podporované konfigurace aplikací

Možnost omezit aplikaci na určitou sadu uživatelů nebo skupin zabezpečení v tenantovi funguje s následujícími typy aplikací:

- Aplikace nakonfigurované pro federované jednotné přihlašování pomocí ověřování založeného na saml
- Aplikace proxy aplikací, které používají azure ad předběžné ověřování
- Aplikace postavené přímo na platformě aplikace Azure AD, které používají ověřování OAuth 2.0/OpenID Connect poté, co uživatel nebo správce s tímto uživatelem souhlasil.

     > [!NOTE]
     > Tato funkce je k dispozici pouze pro webové aplikace/webové rozhraní API a podnikové aplikace. Aplikace, které jsou registrované jako [nativní,](quickstart-v1-integrate-apps-with-azure-ad.md) nelze omezit na sadu uživatelů nebo skupin zabezpečení v tenantovi.

## <a name="update-the-app-to-enable-user-assignment"></a>Aktualizace aplikace pro povolení přiřazení uživatele

Existují dva způsoby, jak vytvořit aplikaci s povoleným přiřazením uživatele. Jeden vyžaduje roli **globálního správce,** druhý ne.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Podnikové aplikace (vyžaduje roli globálního správce)

1. Přejděte na [**portál Azure**](https://portal.azure.com/) a přihlaste se jako **globální správce**.
1. Na horním panelu vyberte přihlášený účet. 
1. V **části Adresář**vyberte klienta Azure AD, kde bude aplikace zaregistrována.
1. V navigaci vlevo vyberte **Azure Active Directory**. Pokud služba Azure Active Directory není v navigačním podokně dostupná, postupujte takto:

    1. V horní části hlavní nabídky navigace vlevo vyberte **Všechny služby.**
    1. Do vyhledávacího pole filtru zadejte **službu Azure Active Directory** a z výsledku vyberte položku **Služby Azure Active Directory.**

1. V podokně **Azure Active Directory** vyberte podnikové **aplikace** z levé navigační nabídky Azure **Active Directory.**
1. Výběrem **možnosti Všechny aplikace** zobrazíte seznam všech aplikací.

     Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte různé filtry v horní části seznamu **Všechny aplikace** k omezení seznamu nebo posuňte seznam dolů a vyhledejte aplikaci.

1. Ze seznamu vyberte aplikaci, ke které chcete přiřadit uživatele nebo skupinu zabezpečení.
1. Na stránce **Přehled** aplikace vyberte **Vlastnosti** z nabídky navigace vlevo.
1. Vyhledejte **požadované** nastavení Přiřazení uživatele a nastavte jej na **Ano**. Pokud je tato možnost nastavena na **ano**, musí být uživatelé nejprve přiřazeni k této aplikaci, aby k ní měli přístup.
1. Chcete-li uložit tuto změnu konfigurace, vyberte **uložit.**

### <a name="app-registration"></a>Registrace aplikací

1. Přejděte na [**portál Azure**](https://portal.azure.com/).
1. Na horním panelu vyberte přihlášený účet. 
1. V **části Adresář**vyberte klienta Azure AD, kde bude aplikace zaregistrována.
1. V navigaci vlevo vyberte **Azure Active Directory**.
1. V podokně **Azure Active Directory** vyberte registrace aplikací z levé navigační nabídky **Služby** **Azure Active Directory.**
1. Vytvořte nebo vyberte aplikaci, kterou chcete spravovat. Musíte být **vlastníkem** registrace této aplikace.
1. Na stránce **Přehled** aplikace postupujte podle **spravované aplikace v odkazu místního adresáře** pod základními informacemi v horní části stránky. Tím přejdete do _spravované podnikové aplikace_ registrace aplikace.
1. V navigačním noži vlevo vyberte **Vlastnosti**.
1. Vyhledejte **požadované** nastavení Přiřazení uživatele a nastavte jej na **Ano**. Pokud je tato možnost nastavena na **ano**, musí být uživatelé nejprve přiřazeni k této aplikaci, aby k ní měli přístup.
1. Chcete-li uložit tuto změnu konfigurace, vyberte **uložit.**

## <a name="assign-users-and-groups-to-the-app"></a>Přiřazení uživatelů a skupin k aplikaci

Jakmile aplikaci nakonfigurujete tak, aby povolila přiřazení uživatelů, můžete k aplikaci přiřadit uživatele a skupiny.

1. V levém navigačním menu aplikace vyberte podokno **Uživatelé a skupiny.**
1. V horní části seznamu **Uživatelé a skupiny** vyberte tlačítko **Přidat uživatele** a otevřete podokno **Přidat přiřazení.**
1. V podokně Přidat **přiřazení** vyberte volič **i uživatelé.** 

     Zobrazí se seznam uživatelů a skupin zabezpečení spolu s textovým polem pro vyhledávání a vyhledání určitého uživatele nebo skupiny. Tato obrazovka umožňuje vybrat více uživatelů a skupin najednou.

1. Po dokončení výběru uživatelů a skupin se stisknutím tlačítka **Vybrat** dole přesuňte na další část.
1. Stisknutím tlačítka **Přiřadit** dole dokončete přiřazení uživatelů a skupin do aplikace. 
1. Zkontrolujte, zda se uživatelé a skupiny, které jste přidali, zobrazují v aktualizovaném seznamu **Uživatelů a skupin.**

