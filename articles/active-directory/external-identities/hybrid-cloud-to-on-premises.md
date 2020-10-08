---
title: Udělení přístupu k místním aplikacím uživatelům B2B – Azure AD
description: Ukazuje, jak poskytnout Cloud B2B uživatelům přístup k místním aplikacím pomocí spolupráce Azure AD B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/10/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 132711b4a3fc584261cd577b4b8f1d4fb13da09d
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819385"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Udělení přístupu k místním aplikacím uživatelům B2B v Azure AD

Jako organizace, která používá funkce spolupráce B2B v Azure Active Directory (Azure AD) k pozvání uživatelů typu host z partnerských organizací do služby Azure AD, teď můžete uživatelům B2B poskytnout přístup k místním aplikacím. Tyto místní aplikace můžou používat ověřování založené na SAML nebo integrované ověřování systému Windows (IWA) s omezeným delegováním protokolu Kerberos (KCD).

## <a name="access-to-saml-apps"></a>Přístup k aplikacím SAML

Pokud vaše místní aplikace používá ověřování založené na SAML, můžete tyto aplikace zpřístupnit uživatelům spolupráce Azure AD B2B prostřednictvím Azure Portal.

Je nutné provést následující akce:

- Integrujte aplikaci pomocí jazyka SAML, jak je popsáno v tématu [Konfigurace jednotného přihlašování založeného na SAML](../manage-apps/configure-saml-single-sign-on.md). Nezapomeňte si uvědomit, co používáte pro hodnotu **adresy URL pro přihlášení** .
-  Použijte Azure Proxy aplikací služby AD k publikování místní aplikace s **Azure Active Directory** nakonfigurovaným jako zdroj ověřování. Pokyny najdete v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](../manage-apps/application-proxy-publish-azure-portal.md). 

   Při konfiguraci nastavení **interní adresy URL** použijte přihlašovací adresu URL, kterou jste zadali v šabloně aplikace mimo galerii. Tímto způsobem můžou uživatelé získat přístup k aplikaci mimo hranice organizace. Proxy aplikace provádí jednotné přihlašování SAML pro místní aplikaci.
 
   ![Zobrazí interní adresu URL a ověřování nastavení místní aplikace.](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Přístup k aplikacím IWA a KCD

Aby uživatelé B2B poskytovali přístup k místním aplikacím zabezpečeným pomocí integrovaného ověřování systému Windows a vynuceným delegováním protokolu Kerberos, budete potřebovat následující komponenty:

- **Ověřování prostřednictvím služby Azure proxy aplikací služby AD**. Uživatelé B2B musí být schopni ověřit místní aplikaci. K tomu je potřeba publikovat místní aplikaci prostřednictvím Proxy aplikací služby AD Azure. Další informace najdete v tématech [Začínáme s proxy aplikací a instalace konektoru](../manage-apps/application-proxy-enable.md) a [publikování aplikací pomocí Azure proxy aplikací služby AD](../manage-apps/application-proxy-publish-azure-portal.md).
- **Autorizace prostřednictvím objektu uživatele B2B v místním adresáři**. Aplikace musí být schopná provádět kontroly přístupu uživatele a udělit přístup ke správným prostředkům. IWA a KCD vyžadují pro dokončení této autorizace objekt uživatele v místní službě Windows Server Active Directory. Jak je popsáno v tématu [jak jednotné přihlašování s KCD funguje](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works), vyžaduje proxy aplikace tento objekt uživatele pro zosobnění uživatele a získání tokenu Kerberos do aplikace. 

   > [!NOTE]
   > Když nakonfigurujete Proxy aplikací služby AD Azure, ujistěte se, že **Identita delegovaného přihlášení** je nastavená na **hlavní název uživatele** (výchozí) pro jednotné přihlašování IWA.

   Ve scénáři uživatele B2B jsou k dispozici dvě metody, které můžete použít k vytvoření objektů hosta, které jsou nutné k autorizaci v místním adresáři:

   - Microsoft Identity Manager (MIM) a Agent pro správu MIM pro Microsoft Graph. 
   - [Powershellový skript](#create-b2b-guest-user-objects-through-a-script-preview). Použití tohoto skriptu je jednodušší řešení, které nepotřebuje MIM. 

Následující obrázek poskytuje podrobný přehled o tom, jak Azure Proxy aplikací služby AD a generace objektu uživatele B2B v místním adresáři pracují společně, aby uživatelům B2B udělil přístup k místním aplikacím IWA a KCD. Očíslované kroky jsou podrobněji popsány pod diagramem.

![Diagram řešení MIM a skriptů B2B](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Uživatel z partnerské organizace (tenant Fabrikam) se vyzve k tenantovi společnosti Contoso.
2.  Objekt uživatele typu Host se vytvoří v tenantovi contoso (například objekt uživatele s hlavním názvem uživatele (UPN) guest_fabrikam. com # EXT # @contoso.onmicrosoft.com ).
3.  Hosta Fabrikam se importuje ze společnosti Contoso prostřednictvím MIM nebo prostřednictvím skriptu prostředí PowerShell pro B2B.
4.  Reprezentace nebo "vystupovat" objektu uživatele hosta Fabrikam (host # EXT #) se vytvoří v místním adresáři Contoso.com, prostřednictvím MIM nebo prostřednictvím skriptu prostředí PowerShell pro B2B.
5.  Uživatel typu Host přistupuje k místní aplikaci, app.contoso.com.
6.  Požadavek na ověření je autorizován prostřednictvím proxy aplikace pomocí omezeného delegování protokolu Kerberos. 
7.  Vzhledem k tomu, že objekt uživatele hosta existuje místně, ověřování je úspěšné.

### <a name="lifecycle-management-policies"></a>Zásady správy životního cyklu

Místní uživatelské objekty B2B můžete spravovat prostřednictvím zásad správy životního cyklu. Příklad:

- Pro uživatele typu Host můžete nastavit zásady MFA (Multi-Factor Authentication), aby se MFA používalo při ověřování proxy aplikací. Další informace najdete v tématu [podmíněný přístup pro uživatele spolupráce B2B](conditional-access.md).
- Jakékoli sponsorships, kontroly přístupu, ověření účtu atd., které se provádějí na cloudu uživatele B2B, se vztahují na místní uživatele. Pokud se například uživatel cloudu odstraní pomocí zásad správy životního cyklu, místní uživatel je také odstraněn pomocí služby MIM Sync nebo pomocí Azure AD Connect synchronizace. Další informace najdete v tématu [Správa přístupu hostů pomocí kontrol přístupu Azure AD](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Vytváření uživatelských objektů hosta B2B prostřednictvím MIM

Informace o tom, jak pomocí MIM 2016 Service Pack 1 a agenta pro správu MIM pro Microsoft Graph vytvořit uživatelské objekty hosta v místním adresáři, najdete v tématu [Spolupráce B2B (Business-to-Business) s Microsoft Identity Manager (MIM) 2016 SP1 s Azure Application proxy](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Vytvoření uživatelských objektů hosta B2B prostřednictvím skriptu (Preview)

K dispozici je ukázkový skript PowerShellu, který můžete použít jako výchozí bod pro vytvoření objektů uživatele typu Host v místní službě Active Directory.

Skript a soubor Readme si můžete stáhnout z [webu Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=51495). Vyberte **skript a soubor Readme pro stažení uživatelů Azure AD B2B on-prem.zip** souboru.

Před použitím tohoto skriptu nezapomeňte zkontrolovat předpoklady a důležité důležité informace v souvisejícím souboru Readme. Také se seznámíte s tím, že skript je zpřístupněn pouze jako ukázka. Váš vývojový tým nebo partner musí před spuštěním přizpůsobit a zkontrolovat skript.

## <a name="license-considerations"></a>Požadavky na licenci

Ujistěte se, že máte správné licence pro klientský přístup (CAL) pro externí uživatele typu Host, kteří přistupují k místním aplikacím. Další informace najdete v části externí konektory v [licencích pro klientský přístup a licencích pro správu](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx). Informace o konkrétních potřebách licencování vám poskytne zástupce Microsoftu nebo místní prodejce.

## <a name="next-steps"></a>Další kroky

- [Azure Active Directory spolupráce B2B pro hybridní organizace](hybrid-organizations.md)

- Přehled Azure AD Connect najdete v tématu [Integrace místních adresářů s Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

