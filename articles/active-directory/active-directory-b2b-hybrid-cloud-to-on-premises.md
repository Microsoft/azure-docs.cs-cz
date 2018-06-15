---
title: Uživatelům udělit B2B ve službě Azure AD přístup k místním aplikacím | Microsoft Docs
description: Ukazuje, jak poskytnout cloudu B2B uživatelům přístup k místní aplikace s spolupráce Azure AD B2B.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/20/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 028bbb28c7091db3c3ebea321ca2e167b999949d
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068792"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Uživatelům udělit B2B ve službě Azure AD přístup k místním aplikacím

Organizace pomocí možnosti spolupráce B2B Azure Active Directory (Azure AD), která pozvat uživatele typu Host z partnerských organizací do služby Azure AD že teď můžete zadat tito uživatelé B2B přístup k místním aplikacím. Tyto místní aplikace můžete použít ověřování založené na SAML nebo integrované ověřování systému Windows (IWA) s omezeným delegováním protokolu Kerberos (použitím KCD).

## <a name="access-to-saml-apps"></a>Přístup k aplikacím SAML

Pokud vaše místní aplikace používá ověřování založené na SAML, můžete snadno provedete tyto aplikace dostupné uživatelům spolupráce Azure AD B2B pomocí portálu Azure.

Proveďte obě z následujících akcí:

- Integrace aplikace SAML pomocí šablony aplikace bez galerie, jak je popsáno v [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](active-directory-saas-custom-apps.md). Nezapomeňte si všimněte si, co můžete použít pro **přihlašovací adresa URL** hodnotu.
-  Pomocí Azure AD Application Proxy publikovat místní aplikace s **Azure Active Directory** konfigurované jako zdroj ověřování. Pokyny najdete v tématu [publikování aplikací pomocí proxy aplikace služby Azure AD](manage-apps/application-proxy-publish-azure-portal.md). 

   Když nakonfigurujete **interní adresa Url** nastavení, použijte adresu URL přihlašování, kterou jste zadali v šabloně bez Galerie aplikace. Tímto způsobem můžete uživatelům přístup k aplikaci z mimo hranice organizace. Proxy aplikací se provádí SAML jednotné přihlašování pro místní aplikace.
 
   ![Zobrazuje interní URL nastavení místní aplikace a ověřování](media/active-directory-b2b-hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Přístup k aplikacím, integrované ověřování systému Windows a použitím KCD

Chcete-li B2B uživatelům poskytnout přístup k místním aplikacím, které jsou zabezpečené integrované ověřování systému Windows a vynuceného delegování protokolu Kerberos, je třeba následující součásti:

- **Ověřování prostřednictvím proxy aplikace služby Azure AD**. K ověření místní aplikace musí mít uživatelé B2B. To pokud chcete udělat, musíte publikovat místní aplikace prostřednictvím Azure AD Application Proxy. Další informace najdete v tématu [začít pracovat s Proxy aplikace a nainstalujte konektor](manage-apps/application-proxy-enable.md) a [publikování aplikací pomocí proxy aplikace služby Azure AD](manage-apps/application-proxy-publish-azure-portal.md).
- **Autorizace prostřednictvím objektu B2B uživatele v adresáři místní**. Aplikace musí být schopný provádět kontroly přístupu uživatele a udělit přístup k prostředkům správné. Integrované ověřování systému Windows a použitím KCD vyžadují objekt uživatele v místní Windows Server Active Directory k dokončení této autorizace. Jak je popsáno v [jak jednotné přihlašování s použitím KCD funguje](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works), musí tento objekt uživatele k zosobnění uživatele a získat token protokolu Kerberos na aplikaci Proxy aplikace. 

   Pro uživatelský scénář B2B existují dvě metody, které jsou k dispozici, můžete použít k vytvoření hosta uživatelské objekty, které jsou požadovány pro autorizaci v místním adresáři:

   - Microsoft Identity Manager (MIM) a [agenta pro správu MIM pro Microsoft Graph](#create-b2b-guest-user-objects-through-mim-preview). 
   - [Skript prostředí PowerShell](#create-b2b-guest-user-objects-through-a-script-preview). Pomocí skriptu je více lightweight řešení, které nevyžaduje MIM. 

Následující obrázek poskytuje souhrnné informace o tom, jak Azure AD Application Proxy a generování uživatelského objektu B2B v místní pracovní adresář dohromady a B2B uživatelům uděluje přístup k místní integrované ověřování systému Windows a použitím KCD aplikace. Číslované kroky jsou podrobně popsány v níže na obrázku.

![Diagram řešení MIM a B2B skriptu](media/active-directory-b2b-hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Uživatel od partnerské organizace (klientské Fabrikam) vyzván k klientovi Contoso.
2.  Objekt uživatele guest je vytvořen v klientovi Contoso (například objekt uživatele s UPN guest_fabrikam.com#EXT#@contoso.onmicrosoft.com).
3.  Host Fabrikam je naimportována ze společnosti Contoso prostřednictvím MIM nebo skript B2B PowerShell.
4.  Reprezentace nebo "otisk" objekt uživatele Fabrikam guest (Host #EXT #) je vytvořen v adresáři místní Contoso.com, prostřednictvím MIM nebo skript B2B PowerShell.
5.  Aplikace přistupuje na místní uživatele guest, app.contoso.com.
6.  Žádost o ověření se oprávnění prostřednictvím služby Proxy aplikací pomocí vynuceného delegování protokolu Kerberos. 
7.  Vzhledem k tomu, že objekt uživatele guest existuje místně, je ověřování úspěšné.

### <a name="lifecycle-management-policies"></a>Zásady správy životního cyklu

Můžete spravovat místní B2B uživatelské objekty prostřednictvím zásad správy životního cyklu. Příklad:

- Zásady vícefaktorového ověřování (MFA) pro uživatele Guest můžete nastavit tak, aby vícefaktorového ověřování se používá při ověřování Proxy aplikace. Další informace najdete v tématu [podmíněného přístupu pro uživatele spolupráce B2B](active-directory-b2b-mfa-instructions.md).
- Všechny sponsorships recenze přístup, ověření účtu, atd., které jsou prováděny v cloudu B2B pro uživatele platí pro místní uživatele. Například pokud odstranění uživatelem cloudu prostřednictvím zásad správy životního cyklu místní uživatel je taky odstranit MIM Sync nebo prostřednictvím synchronizace Azure AD Connect. Další informace najdete v tématu [Správa přístupu k hostované v Azure AD přístupu zkontroluje](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim-preview"></a>Vytváření objektů uživatele guest B2B prostřednictvím MIM (Preview)

Informace o tom, jak použít k vytvoření objektů uživatele guest v adresáři místní MIM 2016 Service Pack 1 a agenta pro správu MIM pro Microsoft Graph najdete v tématu [business-to-business (B2B) spolupráce Azure AD s Microsoft Identity Manager (MIM) 2016 SP1 s Proxy aplikace Azure (verze Public Preview)](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Vytvořit objekty uživatelů B2B hosta pomocí skriptu (Preview)

Je ukázkový skript prostředí PowerShell k dispozici, že k vytváření objektů uživatele guest v místním Active Directory můžete jako výchozí bod.

Můžete si stáhnout skript a soubor Readme z [Download Center](https://www.microsoft.com/download/details.aspx?id=51495). Vyberte **skript a soubor Readme vyžádání Azure AD B2B uživatelé na prem.zip** souboru.

Než použijete skript, ujistěte se, abyste si prošli požadavky a důležité informace v souvisejícím souboru Readme. Navíc pochopit, že je k dispozici pouze jako ukázka skriptu. Váš vývojový tým nebo partnera musíte přizpůsobit a zkontrolujte skript před jeho spuštěním.

## <a name="license-considerations"></a>Důležité informace o licenci

Ujistěte se, zda máte správné licence klientského přístupu (CAL) pro externí hosta uživatele, kteří přístup k místním aplikacím. Další informace najdete v části "Externí konektory" [licencí pro klientský přístup a správu licencí](https://www.microsoft.com/en-us/licensing/product-licensing/client-access-license.aspx). Poraďte se zástupcem společnosti Microsoft nebo místní prodejce ohledně licencování potřeb.

## <a name="next-steps"></a>Další postup

- [Spolupráce Azure Active Directory s B2B pro hybridní organizace](active-directory-b2b-hybrid-organizations.md)

- Přehled služby Azure AD Connect, najdete v tématu [integraci místních adresářů se službou Azure Active Directory](connect/active-directory-aadconnect.md).

