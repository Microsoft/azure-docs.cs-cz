---
title: K místním aplikacím přístup k uživatelům udělit B2B ve službě Azure AD | Dokumentace Microsoftu
description: Ukazuje, jak poskytnout cloudu přístupu uživatelům B2B k aplikacím v místním prostředí se spoluprací Azure AD B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: ef70b9e58a669695f3bdec7ad8ea4bab93e7e4b9
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082040"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Uživatelům udělit B2B v Azure AD přístup k místním aplikacím

Jako organizace, která používá možnosti spolupráce B2B Azure Active Directory (Azure AD) se pozvat uživatele typu Host z partnerských organizací do služby Azure AD můžete nyní zadat těmto uživatelům B2B přístup k místním aplikacím. Tyto místní aplikace můžou používat ověřování založené na SAML nebo integrované ověřování Windows (IWA) s omezené delegování protokolu Kerberos (KCD).

## <a name="access-to-saml-apps"></a>Přístup k aplikacím SAML

Pokud vaše místní aplikace používá ověřování založené na SAML, můžete snadno provádět tyto aplikace k dispozici vaše uživatele spolupráce B2B ve službě Azure AD na webu Azure portal.

Proveďte obě z následujících akcí:

- Integrace aplikace SAML s použitím šablony aplikaci mimo galerii, jak je popsáno v [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](../manage-apps/configure-single-sign-on-non-gallery-applications.md). Ujistěte se, že si uvědomit, co opravdu použijete pro **přihlašovací adresa URL** hodnotu.
-  Pomocí Azure AD Application Proxy publikovat místní aplikaci pomocí **Azure Active Directory** konfigurované jako zdroj ověřování. Pokyny najdete v tématu [publikování aplikací pomocí Proxy aplikací Azure AD](../manage-apps/application-proxy-publish-azure-portal.md). 

   Při konfiguraci **interní adresa Url** nastavení, použijte přihlašovací adresu URL, který jste zadali v šabloně aplikaci mimo galerii. Tímto způsobem uživatelé přístup k aplikaci mimo hranice organizace. Proxy aplikace provádí SAML jednotného přihlašování pro místní aplikace.
 
   ![Ukazuje interní adresa URL aplikace místní aplikace nastavení a ověřování](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Přístup k aplikacím IWA a KCD

K uživatelům B2B přístup k místním aplikacím, které jsou zabezpečené pomocí integrovaného ověřování Windows a omezeného delegování protokolu Kerberos, budete potřebovat následující komponenty:

- **Ověřování prostřednictvím Azure AD Application Proxy**. Uživatele B2B musí být schopni ověřit místní aplikace. K tomu je nutné publikovat místní aplikace prostřednictvím Proxy aplikací služby Azure AD. Další informace najdete v tématu [začít pracovat s Proxy aplikace a nainstalujte konektor](../manage-apps/application-proxy-enable.md) a [publikování aplikací pomocí Proxy aplikací Azure AD](../manage-apps/application-proxy-publish-azure-portal.md).
- **Autorizace přes objekt uživatele B2B v místním adresáři**. Aplikace musí být schopen provádět kontroly přístupu uživatelů a udělení přístupu k prostředkům správné. IWA a KCD vyžaduje objekt uživatele v místní Windows Server Active Directory k dokončení tohoto ověřování. Jak je popsáno v [jak jednotné přihlašování s KCD funguje](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works), musí tento objekt uživatele zosobnit uživatele a získat token protokolu Kerberos k aplikaci Proxy aplikací. 

   Pro uživatelský scénář B2B existují dvě metody, které jsou k dispozici, můžete použít k vytvoření objektů uživatele typu Host, které jsou požadovány pro autorizaci v místním adresáři:

   - Microsoft Identity Manageru (MIM) a [agenta pro správu MIM pro Microsoft Graph](#create-b2b-guest-user-objects-through-mim-preview). 
   - [Skript prostředí PowerShell](#create-b2b-guest-user-objects-through-a-script-preview). Pomocí skriptu je více jednoduché řešení, která nevyžaduje MIM. 

Následující diagram představuje podrobný přehled Azure AD Application Proxy a jeho generaci objekt uživatele B2B v místní pracovní adresář dohromady a udělit uživatelům B2B přístup k vašim místním IWA a KCD aplikacím. Číslované kroky jsou podrobně popsány v následující diagram.

![Diagram skriptu řešení MIM a B2B](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Uživatel od partnerské organizace (Fabrikam tenanta) vyzván k tenantovi Contoso.
2.  Objekt uživatele typu Host se vytvoří v tenantovi Contoso (například objekt uživatele s UPN guest_fabrikam.com#EXT#@contoso.onmicrosoft.com).
3.  Host Fabrikam je importován z Contoso prostřednictvím MIM nebo skript B2B PowerShell.
4.  Reprezentace nebo "nároky" na objektu uživatele typu Host Fabrikam (hosta EXT #) se vytvoří v adresáři v místním Contoso.com, prostřednictvím MIM nebo skript B2B PowerShell.
5.  Aplikace přistupuje k místní uživatele guest, app.contoso.com.
6.  Požadavek na ověření je oprávnění prostřednictvím Proxy aplikací pomocí omezeného delegování protokolu Kerberos. 
7.  Vzhledem k tomu, že objekt uživatele typu Host v místním prostředí existuje, je ověření úspěšné.

### <a name="lifecycle-management-policies"></a>Zásady správy životního cyklu

Můžete spravovat objekty uživatele B2B v místním prostřednictvím zásad správy životního cyklu. Příklad:

- Zásady ověřování službou Multi-Factor Authentication (MFA) pro uživatele typu Host můžete nastavit tak, aby používalo vícefaktorového ověřování během ověřování Proxy aplikací. Další informace najdete v tématu [podmíněného přístupu pro uživatele spolupráce B2B](conditional-access.md).
- Žádné sponsorship, kontroly přístupu, ověřování účtu, atd., které se provádějí v cloudu B2B uživatelů platí pro místní uživatele. Například pokud prostřednictvím zásad správy životního cyklu odstranění uživatele cloud, místní uživatel se odstraní také MIM Sync nebo prostřednictvím synchronizace Azure AD Connect. Další informace najdete v tématu [kontroly přístupu hostů spravovat pomocí služby Azure AD access](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Vytvořit objekty uživatelů typu Host B2B prostřednictvím MIM

Informace o tom, jak vytvořit objekty uživatelů typu Host v místním adresáři pomocí MIM 2016 Service Pack 1 a agenta pro správu MIM pro Microsoft Graph najdete v tématu [business-to-business (B2B) spolupráce Azure AD s Microsoft Identity Manager (MIM) 2016 SP1 s Proxy aplikací Azure](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Vytvořit objekty uživatelů typu Host B2B pomocí skriptu (Preview)

Zde je ukázkový skript prostředí PowerShell k dispozici, můžete použít jako výchozí bod k vytvoření objektů uživatelů typu Host ve vašem místním Active Directory.

Skript a soubor Readme z si můžete stáhnout [Download Center](https://www.microsoft.com/download/details.aspx?id=51495). Zvolte **skript a soubor Readme o přijetí změn B2B ve službě Azure AD uživatelům na prem.zip** souboru.

Než použijete skript, ujistěte se, abyste si požadavky a důležité informace v souvisejícím souboru Readme. Také pochopit, že je k dispozici pouze jako ukázka skriptu. Váš vývojový tým nebo partnera musí přizpůsobit a zkontrolujte skript předtím, než ji spustíte.

## <a name="license-considerations"></a>Důležité informace o licenci

Ujistěte se, že máte správné licence klientského přístupu (CAL) pro externím uživatelům typu Host, kteří přistupují k místním aplikacím. Další informace najdete v části "Externí konektory" [licencí pro klientský přístup a správu licencí](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx). Najdete zástupce Microsoftu nebo místní prodejce ohledně vašich konkrétních potřeb licencování.

## <a name="next-steps"></a>Další postup

- [Spolupráce Azure Active Directory s B2B pro hybridní organizace](hybrid-organizations.md)

- Přehled služby Azure AD Connect, naleznete v tématu [integrace místních adresářů se službou Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

