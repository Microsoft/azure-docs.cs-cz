---
title: Udělte uživatelům B2B přístup k místním aplikacím – Azure AD
description: Ukazuje, jak poskytnout uživatelům cloudu B2B přístup k místním aplikacím pomocí spolupráce Azure AD B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 098f464b6af5f10866403e1cd1549d571d883ac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272800"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Udělení přístupu uživatelům B2B ve službě Azure AD k místním aplikacím

Jako organizace, která používá možnosti spolupráce B2B služby Azure Active Directory (Azure AD) k pozvání uživatelů typu Host z partnerských organizací do služby Azure AD, můžete teď těmto uživatelům B2B poskytnout přístup k místním aplikacím. Tyto místní aplikace můžou používat ověřování založené na SAML nebo integrované ověřování windows (IWA) s delegováním s omezením protokolu Kerberos (KCD).

## <a name="access-to-saml-apps"></a>Přístup k aplikacím SAML

Pokud vaše místní aplikace používá ověřování založené na SAML, můžete tyto aplikace snadno zpřístupnit uživatelům spolupráce Azure AD B2B prostřednictvím portálu Azure.

Musíte provést obě následující akce:

- Integrujte aplikaci SAML pomocí šablony aplikace bez galerie, jak je popsáno v [části Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](../manage-apps/configure-single-sign-on-non-gallery-applications.md). Nezapomeňte si poznamenat, co používáte pro hodnotu **přihlašovací adresy URL.**
-  Pomocí proxy aplikace Azure AD publikujte místní aplikaci s **Azure Active Directory** nakonfigurovanou jako zdrojem ověřování. Pokyny najdete v tématu [publikování aplikací pomocí proxy aplikace Azure AD](../manage-apps/application-proxy-publish-azure-portal.md). 

   Při konfiguraci nastavení **Interní adresa URL** použijte přihlašovací adresu URL, kterou jste zadali v šabloně aplikace, která není v galerii. Tímto způsobem mohou uživatelé přistupovat k aplikaci mimo hranice organizace. Proxy aplikace provádí jednotné přihlašování SAML pro místní aplikaci.
 
   ![Zobrazuje interní adresu URL a ověřování nastavení místní aplikace.](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Přístup k aplikacím IWA a KCD

Chcete-li uživatelům B2B poskytnout přístup k místním aplikacím, které jsou zabezpečené pomocí integrovaného ověřování systému Windows a delegování s omezenou hodnotou Kerberos, potřebujete následující součásti:

- **Ověřování prostřednictvím proxy aplikace Azure AD**. Uživatelé B2B musí být schopni ověřit místní aplikaci. Chcete-li to provést, musíte publikovat místní aplikaci prostřednictvím proxy aplikace Azure AD. Další informace najdete [v tématu Začínáme s proxy aplikací a nainstalujte konektor](../manage-apps/application-proxy-enable.md) a [publikovat aplikace pomocí proxy aplikací Azure AD](../manage-apps/application-proxy-publish-azure-portal.md).
- **Autorizace prostřednictvím objektu uživatele B2B v místním adresáři**. Aplikace musí být schopna provádět kontroly přístupu uživatelů a udělit přístup ke správným prostředkům. IWA a KCD vyžadují k dokončení této autorizace objekt uživatele v místní službě Windows Server Active Directory. Jak je popsáno v [řešení jednotného přihlašování pomocí rozhraní KCD](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works), proxy aplikace potřebuje tento objekt uživatele, aby mohl uživatele zosobnit a získat do aplikace token protokolu Kerberos. 

   Pro scénář uživatele B2B jsou k dispozici dvě metody, které můžete použít k vytvoření objektů hosta uživatele, které jsou požadovány pro autorizaci v místním adresáři:

   - Správce identit společnosti Microsoft (MIM) a agent správy MIM pro microsoft graph. 
   - [Skript prostředí PowerShell](#create-b2b-guest-user-objects-through-a-script-preview). Použití skriptu je odlehčenější řešení, které nevyžaduje MIM. 

Následující diagram poskytuje podrobný přehled o tom, jak proxy aplikace Azure AD a generování objektu uživatele B2B v místním adresáři spolupracují na udělení přístupu uživatelům B2B k místním aplikacím IWA a KCD. Očíslované kroky jsou podrobně popsány pod diagramem.

![Diagram řešení skriptů MIM a B2B](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  Uživatel z partnerské organizace (tenant a klient Fabrikam) je pozván do klienta Contoso.
2.  Objekt uživatele typu Host je vytvořen v tenantovi Contoso (například objekt uživatele s@contoso.onmicrosoft.comhlavní název uživatele guest_fabrikam.com#EXT# ).
3.  Host Fabrikam je importován z Contoso přes MIM nebo prostřednictvím skriptu B2B PowerShell.
4.  Reprezentace nebo "stopa" objektu hosta Fabrikam (Guest#EXT#) je vytvořena v místním adresáři, Contoso.com, prostřednictvím MIM nebo prostřednictvím skriptu B2B PowerShell.
5.  Uživatel typu Host přistupuje k místní aplikaci, app.contoso.com.
6.  Požadavek na ověření je autorizován prostřednictvím serveru Proxy aplikace pomocí delegování s omezeným protokolem Kerberos. 
7.  Vzhledem k tomu, že objekt uživatele typu Host existuje místně, je ověření úspěšné.

### <a name="lifecycle-management-policies"></a>Zásady správy životního cyklu

Místní uživatelské objekty B2B můžete spravovat prostřednictvím zásad správy životního cyklu. Například:

- Můžete nastavit vícefaktorové ověřování (MFA) zásady pro uživatele typu Host tak, aby mfa se používá při ověřování proxy aplikací. Další informace naleznete [v tématu Podmíněný přístup pro uživatele spolupráce B2B](conditional-access.md).
- Jakékoli sponzorství, kontroly přístupu, ověření účtu atd., které se provádějí na uživateli B2B cloudu, se vztahují na místní uživatele. Například pokud se uživatel cloudu odstraní prostřednictvím zásad správy životního cyklu, místní uživatel se také odstraní pomocí MIM Sync nebo prostřednictvím synchronizace Azure AD Connect. Další informace najdete [v tématu Správa přístupu hosta pomocí kontrol přístupu azure ad](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Vytváření objektů hosta B2B prostřednictvím MIM

Informace o tom, jak pomocí aktualizace MIM 2016 Service Pack 1 a agenta správy MIM pro Microsoft Graph vytvořit objekty hosta uživatele v místním adresáři, najdete v [tématu spolupráce mezi virtuálními zařízeními Azure AD mezi podniky (B2B) se službou Microsoft Identity Manager (MIM) 2016 SP1 s proxy aplikací Azure](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Vytváření objektů hosta B2B pomocí skriptu (náhled)

K dispozici je ukázkový skript prostředí PowerShell, který můžete použít jako výchozí bod k vytvoření objektů hosta uživatele v místním adresáři Active Directory.

Skript a soubor Readme si můžete stáhnout ze [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=51495). Zvolte **skript a Readme pro vyprošťování souborů uživatelů Azure AD B2B on-prem.zip.**

Před použitím skriptu zkontrolujte požadavky a důležité aspekty v přidruženém souboru Readme. Také pochopit, že skript je k dispozici pouze jako ukázka. Váš vývojový tým nebo partner musí skript před spuštěním přizpůsobit a zkontrolovat.

## <a name="license-considerations"></a>Aspekty licence

Ujistěte se, že máte správné licence klientského přístupu (CAL) pro externí uživatele typu Host, kteří přistupují k místním aplikacím. Další informace naleznete v části Externí konektory v části [Licence klientského přístupu a licence pro správu](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx). Informace o konkrétních licenčních potřebách se obraťte na zástupce nebo místního prodejce společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

- [Spolupráce Azure Active Directory B2B pro hybridní organizace](hybrid-organizations.md)

- Přehled služby Azure AD Connect najdete v [tématu Integrace místních adresářů pomocí Služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

