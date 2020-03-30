---
title: Principy a řešení problémů s proxy serverem Azure AD Pro server
description: Poskytuje pochopení CORS v proxy aplikací Azure AD a jak identifikovat a řešit problémy CORS.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: c49535ad11139ac5145d4f283374bf9cc6d71f52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025789"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Principy a řešení problémů s cors proxy aplikace služby Azure Active Directory

[Sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/) může někdy představovat výzvy pro aplikace a rozhraní API, které publikujete prostřednictvím proxy aplikace služby Azure Active Directory. Tento článek popisuje problémy a řešení proxy aplikace Azure AD.

Zabezpečení prohlížeče obvykle brání webové stránce v tom, aby ajax požadavky do jiné domény. Toto omezení se nazývá *zásady stejného původu*a zabraňuje škodlivému webu ve čtení citlivých dat z jiného webu. Někdy však můžete chtít, aby jiné weby volaly vaše webové rozhraní API. CORS je standard W3C, který umožňuje serveru uvolnit zásady stejného původu a povolit některé požadavky napříč původy při odmítnutí jiných.

## <a name="understand-and-identify-cors-issues"></a>Pochopení a identifikace problémů CORS

Dvě adresy URL mají stejný původ, pokud mají stejná schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)), například:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Následující adresy URL mají jiný původ než předchozí dvě:

-   http:\//contoso.net - Jiná doména
-   http:\//contoso.com:9000/foo.html - Jiný port
-   https:\//contoso.com/foo.html - Různé schéma
-   http:\//www.contoso.com/foo.html - Různé subdomény

Zásady stejného původu brání aplikacím v přístupu k prostředkům z jiných původů, pokud nepoužívají správná záhlaví řízení přístupu. Pokud hlavičky CORS chybí nebo nesprávné, požadavky mezi původem nezdaří. 

Problémy cors můžete identifikovat pomocí ladicích nástrojů prohlížeče:

1. Spusťte prohlížeč a přejděte do webové aplikace.
1. Stisknutím **klávesy F12** zvete ladicí konzolu.
1. Pokuste se transakci reprodukovat a zkontrolujte zprávu konzoly. Porušení CORS vytvoří chybu konzoly o původu.

Na následujícím snímku obrazovky výběr tlačítka **Try It** způsobil chybovou\/zprávu CORS, která https: /corswebclient-contoso.msappproxy.net nebyl nalezen v záhlaví Access-Control-Allow-Origin.

![Problém CORS](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>CORS výzvy s proxy aplikací

Následující příklad ukazuje typický scénář Azure AD Aplikace proxy CORS. Interní server je hostitelem řadiče webového rozhraní API **CORSWebService** a klienta **CORSWebClient,** který volá **službu CORSWebService**. Existuje požadavek AJAX z **CORSWebClient** na **CORSWebService**.

![Místní žádost o stejný původ](./media/application-proxy-understand-cors-issues/image1.png)

Aplikace CORSWebClient funguje, když ji hostujete místně, ale při publikování prostřednictvím proxy aplikace Azure AD se nepodaří načíst nebo se chyby nepodaří načíst nebo se chyby. Pokud jste publikovali aplikace CORSWebClient a CORSWebService samostatně jako různé aplikace prostřednictvím proxy aplikace, jsou tyto dvě aplikace hostovány v různých doménách. Požadavek AJAX z CORSWebClient na CORSWebService je požadavek napříč původem a nezdaří se.

![Požadavek CORS proxy aplikace](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Řešení pro problémy s proxy aplikací CORS

Předchozí problém cors můžete vyřešit libovolným z několika způsobů.

### <a name="option-1-set-up-a-custom-domain"></a>Možnost 1: Nastavení vlastní domény

Pomocí [vlastní domény](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) proxy aplikace Azure AD publikujte ze stejného původu, aniž byste museli provádět změny v původech aplikací, kódu nebo záhlavích. 

### <a name="option-2-publish-the-parent-directory"></a>Možnost 2: Publikování nadřazeného adresáře

Publikujte nadřazený adresář obou aplikací. Toto řešení funguje obzvláště dobře, pokud máte na webovém serveru pouze dvě aplikace. Místo publikování jednotlivých aplikací samostatně můžete publikovat společný nadřazený adresář, což má za následek stejný původ.

Následující příklady ukazují stránku proxy aplikace Azure AD pro aplikaci CORSWebClient.  Pokud je **interní adresa URL** nastavena na *contoso.com/CORSWebClient*, aplikace nemůže úspěšně zažádat do *contoso.com/CORSWebService* adresáře, protože jsou napříč výchozími informacemi. 

![Publikování aplikace jednotlivě](./media/application-proxy-understand-cors-issues/image4.png)

Místo toho nastavte **interní adresu URL** pro publikování nadřazeného adresáře, který zahrnuje adresáře *CORSWebClient* a *CORSWebService:*

![Publikovat nadřazený adresář](./media/application-proxy-understand-cors-issues/image5.png)

Výsledné adresy URL aplikací efektivně řeší problém cors:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Možnost 3: Aktualizace záhlaví PROTOKOLU HTTP

Přidejte vlastní hlavičku odpovědi HTTP ve webové službě tak, aby odpovídala požadavku původu. U webů spuštěných v Internetové informační službě (IIS) můžete pomocí Správce služby IIS upravit záhlaví:

![Přidání vlastní hlavičky odpovědi ve Správci služby IIS](./media/application-proxy-understand-cors-issues/image6.png)

Tato změna nevyžaduje žádné změny kódu. Můžete ověřit v Šumař stopy:

**Zaúčtovat přidání záhlaví**\
HTTP/1.1 200 OK\
Řízení mezipaměti: bez mezipaměti\
Pragma: bez cache\
Typ obsahu: text/prostý; charset=UTF-8\
Platnost vyprší: -1\
Vary: Kódování přijmout\
Server: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin:\:https //corswebclient-contoso.msappproxy.net**\
X-AspNet-Verze: 4.0.30319\
X-Powered-By: ASP.NET\
Délka obsahu: 17

### <a name="option-4-modify-the-app"></a>Možnost 4: Úprava aplikace

Aplikaci můžete změnit tak, aby podporovala CORS přidáním hlavičky Access-Control-Allow-Origin s příslušnými hodnotami. Způsob přidání záhlaví závisí na jazyku kódu aplikace. Změna kódu je nejméně doporučenou možností, protože vyžaduje největší úsilí.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Možnost 5: Prodloužení životnosti přístupového tokenu

Některé problémy cors nelze vyřešit, například když vaše aplikace přesměruje na *login.microsoftonline.com* k ověření a vyprší platnost přístupového tokenu. Volání CORS se pak nezdaří. Řešení pro tento scénář je prodloužit životnost přístupového tokenu, aby se zabránilo vypršení jeho platnosti během relace uživatele. Další informace o tom, jak to provést, naleznete [v tématu konfigurovatelné životnosti tokenů ve službě Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Viz také
- [Kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace ve službě Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Plánování nasazení proxy aplikace Azure AD](application-proxy-deployment-plan.md) 
- [Vzdálený přístup k místním aplikacím prostřednictvím proxy aplikací služby Azure Active Directory](application-proxy.md) 
