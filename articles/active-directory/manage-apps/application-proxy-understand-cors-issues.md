---
title: Pochopení a řešení problémů s Azure AD Application Proxy CORS
description: Poskytuje znalost CORS v Azure AD Application Proxy a jak identifikovat a řešit problémy CORS.
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
ms.openlocfilehash: afc0bb990f69521efb2557a6a086c0de5126f82c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440418"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Pochopení a řešení problémů s Azure Active Directory Application Proxy CORS

[Prostředků mezi zdroji (CORS) pro sdílení obsahu](https://www.w3.org/TR/cors/) může někdy nést výzvy pro aplikace a rozhraní API publikujete pomocí Proxy aplikace Azure Active Directory. Tento článek popisuje Azure AD Application Proxy CORS problémy a jejich řešení.

Zabezpečení prohlížeče obvykle chrání webovou stránku bránit odesílání požadavků AJAX na jinou doménu. Toto omezení je volána *zásada stejného zdroje*a brání škodlivým webům ve čtení citlivých dat z jiné lokality. Ale v některých případech můžete chtít nechat ostatních lokalit volání webového rozhraní API. CORS je standard W3C, která umožňuje server zmírnit zásadu stejného zdroje a povolit některé požadavky cross-origin, zatímco jiné odmítnout.

## <a name="understand-and-identify-cors-issues"></a>Pochopení a identifikovat problémy CORS

Pokud mají stejné schémata, hostitele a porty mít dvě adresy URL stejného původu ([RFC 6454](https://tools.ietf.org/html/rfc6454)), jako například:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Následující adresy URL mají různé zdroje než ta předchozí dvě:

-   http:\//contoso.net - jinou doménu
-   http:\//contoso.com:9000/foo.html - jiný port
-   protokol https:\//contoso.com/foo.html – jiné schéma
-   http:\//www.contoso.com/foo.html – různé subdomény

Zásada stejného zdroje aplikacím bránit přístup k prostředkům z jiných zdrojů, pokud používají ovládací prvek záhlaví správný přístup. Pokud jsou hlavičky CORS chybí nebo není správný, selžou požadavky cross-origin. 

Problémy s CORS můžete identifikovat pomocí ladicí nástroje prohlížeče:

1. Spusťte prohlížeč a přejděte do webové aplikace.
1. Stisknutím klávesy **F12** zobrazíte konzolou pro ladění.
1. Zkuste k reprodukci transakce a projděte si zprávu konzoly. Narušení CORS vytvoří chybu konzoly o původu.

Na následujícím snímku obrazovky vyberete **vyzkoušet** tlačítko způsobila chybovou zprávu CORS tento protokol https:\//corswebclient-contoso.msappproxy.net nebyla nalezena v záhlaví Access-Control-Allow-Origin.

![Problém CORS](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>CORS problémů s Proxy aplikací

Následující příklad ukazuje typické scénáře Azure AD Application Proxy CORS. Interní server hostitele **CORSWebService** kontroleru webového rozhraní API a **CORSWebClient** , která volá **CORSWebService**. Je požadavek AJAX z **CORSWebClient** k **CORSWebService**.

![Místní stejného původu žádosti](./media/application-proxy-understand-cors-issues/image1.png)

CORSWebClient aplikace funguje, když jste hostoval lokálně, ale buď dojde k načtení nebo chyby, když publikované prostřednictvím Proxy aplikací Azure AD. Pokud jste publikovali aplikace CORSWebClient a CORSWebService samostatně jako různé aplikace prostřednictvím Proxy aplikací, jsou dvě aplikace hostované v různých doménách. Je požadavek AJAX z CORSWebClient CORSWebService žádosti nepůvodního zdroje a selže.

![Požadavku sdílení CORS Proxy aplikace](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Řešení problémů, CORS Proxy aplikace

Předchozí problém CORS v některém z několik způsobů, jak lze vyřešit.

### <a name="option-1-set-up-a-custom-domain"></a>Option 1: Nastavení vlastní domény

Pomocí Azure AD Application Proxy [vlastní doménu](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) chcete publikovat ze stejného původu, aniž by bylo nutné provést nějaké změny zdroje aplikace, kód nebo záhlaví. 

### <a name="option-2-publish-the-parent-directory"></a>Option 2: Publikování nadřazený adresář

Publikování nadřazený adresář složky obě aplikace. Toto řešení funguje dobře, zejména pokud máte pouze dvě aplikace na webovém serveru. Místo publikování každou aplikaci zvlášť, můžete publikovat společný nadřazený adresář, což vede k stejného původu.

Následující příklady ukazují na portálu Azure AD Application Proxy stránku CORSWebClient aplikace.  Při **interní adresa URL** je nastavena na *contoso.com/CORSWebClient*, aplikace nemůže provádět úspěšných požadavků na *contoso.com/CORSWebService* adresáře, protože jsou to nepůvodního zdroje. 

![Publikování aplikace jednotlivě](./media/application-proxy-understand-cors-issues/image4.png)

Místo toho nastavte **interní adresa URL** publikovat nadřazený adresář, který obsahuje i *CORSWebClient* a *CORSWebService* adresáře:

![Publikování nadřazeného adresáře](./media/application-proxy-understand-cors-issues/image5.png)

Výsledný adresy URL aplikací efektivně vyřešit problém CORS:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Možnost 3: Aktualizovat hlavičky protokolu HTTP

Přidáte vlastní hlavičku HTTP odpovědi na webovou službu tak, aby odpovídaly požadavku origin. Pro webů spuštěných v Internetové informační služby (IIS) použijte Správce služby IIS změnit záhlaví:

![Přidat vlastní hlavičky odpovědi ve Správci služby IIS](./media/application-proxy-understand-cors-issues/image6.png)

Tato změna nevyžaduje žádné změny kódu. To můžete ověřit v trasování Fiddler:

**Příspěvek přidání záhlaví**\
HTTP/1.1 200 OK\
Cache-Control: Ne cache\
– Direktiva pragma: Ne cache\
Content-Type: text/plain; charset = utf-8\
Vypršení platnosti:-1\
Se liší: Přijměte Encoding\
Server: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin: https://corswebclient-contoso.msappproxy.net** \
X-AspNet-Version: 4.0.30319\
X-využívá podle: ASP.NET\
Content-Length: 17

### <a name="option-4-modify-the-app"></a>Option 4: Upravit aplikaci

Můžete změnit vaší aplikace pro podporu CORS přidáním hlavička Access-Control-Allow-Origin, s příslušnými hodnotami. Způsob, jak přidat hlavičku závisí na jazyk kódu aplikace. Změna kódu nejméně možnost se doporučuje, protože vyžaduje většinu úsilí.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Možnost 5: Prodloužení doby života přístupového tokenu

Některé problémy CORS není možné přeložit, například když vaše aplikace přesměruje *login.microsoftonline.com* k ověření, a zajistí vypršení platnosti přístupového tokenu. Sdílení CORS volání, pak se nezdaří. Alternativní řešení pro tento scénář je k prodloužení doby života přístupového tokenu, aby se zabránilo jejímu vypršení platnosti během uživatelské relace. Další informace o tom, jak to provést, najdete v části [konfigurovatelné životností tokenů ve službě Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Další informace najdete v tématech
- [Kurz: Přidat místní aplikace pro vzdálený přístup prostřednictvím Proxy aplikací v Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Plánování nasazení služby Azure AD Application Proxy](application-proxy-deployment-plan.md) 
- [Vzdálený přístup k místním aplikacím přes Azure Active Directory Application Proxy](application-proxy.md) 
