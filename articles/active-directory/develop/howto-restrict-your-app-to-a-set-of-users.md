---
title: Jak omezit aplikace Azure Active Directory zaregistrované pro skupinu uživatelů
description: Zjistěte, jak omezit přístup na vaše aplikace registrované ve službě Azure AD pro vybranou skupinu uživatelů.
services: active-directory
documentationcenter: ''
author: kalyankrishna1
manager: mtillman
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
ms.openlocfilehash: 17b76a25b3c5c2c3ce4dc4217389706a4b24d837
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210284"
---
# <a name="how-to-restrict-your-app-to-a-set-of-users"></a>Postup: Omezení aplikace na sadu uživatelů

Zaregistrované v tenantovi Azure Active Directory (Azure AD) aplikace jsou ve výchozím nastavení dostupné pro všechny uživatele tenantovi, kteří úspěšně ověřit.

Podobně v případě klíčových [víceklientské](howto-convert-app-to-be-multi-tenant.md) aplikace, všichni uživatelé v tenantovi Azure AD, kde je zřízený tato aplikace bude možné přistupovat k této aplikaci, jakmile se úspěšně ověřit v jejich příslušných tenanta.

Správci tenanta a vývojáři často mají požadavky na kde aplikace musí být omezena na sadu uživatelů. Vývojáři stejné lze provádět pomocí oblíbených autorizace vzory jako na základě řízení přístupu Role (RBAC), ale tento přístup vyžaduje značné množství práce na část vývojáře.

Azure AD umožňuje tenanta správci a vývojáři aplikace omezit na konkrétní sadu uživatelů nebo skupin zabezpečení v tenantovi.

## <a name="supported-app-configurations"></a>Konfigurace podporovaných aplikací.

Možnost omezit na konkrétní sadu uživatelů nebo skupin zabezpečení v tenantovi aplikace funguje s následujícími typy aplikací:

- Aplikace, konfigurované pro federované jednotné přihlašování pomocí ověřování založené na SAML
- Proxy aplikace, které používají předběžné ověřování Azure AD
- Aplikace vytvořené přímo na platformě Azure AD aplikace, které používají OAuth 2.0 a OpenID Connect ověřování po uživatel nebo správce vyjádřil souhlas tuto aplikaci.

     > [!NOTE]
     > Tato funkce je dostupná pro webovou aplikaci nebo webové rozhraní API a podnikovými aplikacemi pouze. Aplikace, které jsou registrovány jako [nativní](quickstart-v1-integrate-apps-with-azure-ad.md) nemůže být omezeny na sadu uživatelů nebo skupin zabezpečení v tenantovi.

## <a name="update-the-app-to-enable-user-assignment"></a>Aktualizace aplikace, abyste umožnili přiřazení uživatelů

1. Přejděte [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**
1. Na horním panelu vyberte účet přihlášený. 
1. V části **Directory**, vyberte tenanta Azure AD, kde bude aplikace zaregistrované.
1. V navigačním panelu na levé straně vyberte **Azure Active Directory**. Pokud Azure Active Directory není k dispozici v navigačním podokně, postupujte podle těchto kroků:

    1. Vyberte **všechny služby** v horní části hlavní navigační nabídce vlevo.
    1. Zadejte **Azure Active Directory** do vyhledávacího pole filtrovat a pak vyberte **Azure Active Directory** položky z výsledku.

1. V **Azure Active Directory** vyberte **podnikové aplikace** z **Azure Active Directory** navigační nabídce vlevo.
1. Vyberte **všechny aplikace** zobrazíte seznam všech aplikací.

     Pokud nevidíte aplikaci, kterou má, zobrazí se zde, pomocí různých filtrů v horní části **všechny aplikace** seznamu k omezení seznamu nebo přejděte dolů v seznamu vyhledejte aplikace.

1. Vyberte aplikaci, kterou chcete přiřadit uživatele nebo skupinu zabezpečení a ze seznamu.
1. V aplikaci prvku **přehled** stránce **vlastnosti** levé navigační nabídce aplikace.
1. Vyhledejte nastavení **přiřazení uživatelů povinné?** a nastavte ho na **Ano**. Když je tato možnost nastavená na **Ano**, pak uživatelé musí být přiřazen k této aplikaci, aby mohli k němu přistupovat.
1. Vyberte **Uložit** uložte tuto změnu konfigurace.

## <a name="assign-users-and-groups-to-the-app"></a>Přiřazení uživatelů a skupin k aplikaci

Po konfiguraci vaší aplikace, abyste umožnili přiřazení uživatelů, můžete pokračovat a přiřazení uživatelů a skupin k aplikaci.

1. Vyberte **uživatelů a skupin** podokno v levé navigační nabídce aplikace.
1. V horní části **uživatelů a skupin** seznamu, vyberte **přidat uživatele** tlačítko Otevřít **přidat přiřazení** podokně.
1. Vyberte **uživatelé** pro výběr **přidat přiřazení** podokně. 

     Seznam uživatelů a skupin zabezpečení se zobrazí spolu s textového pole hledání a vyhledejte uživatele nebo skupinu. Tato obrazovka umožňuje vybrat najednou více uživatelů a skupin.

1. Po dokončení výběru uživatelů a skupin, stiskněte **vyberte** tlačítko na konci článku přejít k další části.
1. Stisknutím klávesy **přiřadit** tlačítko v dolní části na dokončení přiřazení uživatelů a skupin k aplikaci. 
1. Potvrďte, že uživatelé a skupiny, které jste přidali se zobrazují v aktualizovaném **uživatelů a skupin** seznamu.

