---
title: Pochopení a řešení potíží s Proxy aplikací služby AD CORS pro Azure
description: Poskytuje informace o CORS v Azure Proxy aplikací služby AD a o tom, jak identifikovat a řešit problémy CORS.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 8836295e9f54260c4e9ff6c1da333ef2a86d58fb
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651851"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Pochopení a řešení potíží s Proxy aplikací služby Azure Active Directory CORS

[Sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/) může někdy představovat výzvy pro aplikace a rozhraní API, které publikujete prostřednictvím proxy aplikací služby Azure Active Directory. Tento článek popisuje problémy a řešení pro Azure Proxy aplikací služby AD CORS.

Zabezpečení prohlížeče obvykle brání webové stránce v provádění požadavků AJAX na jinou doménu. Toto omezení se nazývá *zásady stejného původu* a brání škodlivému webu v čtení citlivých dat z jiné lokality. V některých případech však můžete chtít povolit jiným webům volat vaše webové rozhraní API. CORS je standard W3C, který umožňuje serveru zmírnit zásady stejného původce a při zamítnutí dalších požadavků umožnit některé žádosti mezi zdroji.

## <a name="understand-and-identify-cors-issues"></a>Pochopení a identifikace problémů s CORS

Dvě adresy URL mají stejný původ, pokud mají identická schémata, hostitele a porty ([RFC 6454](https://tools.ietf.org/html/rfc6454)), například:

-   http: \/ /contoso.com/foo.html
-   http: \/ /contoso.com/bar.html

Následující adresy URL mají jiný původ než předchozí dvě:

-   http: \/ /contoso.NET – odlišná doména
-   http: \/ /contoso.com:9000/foo.html – jiný port
-   https: \/ /contoso.com/foo.html – odlišné schéma
-   http: \/ /www.contoso.com/foo.html – odlišná subdoména

Zásady stejného původu zabraňují aplikacím v přístupu k prostředkům z jiných zdrojů, pokud nepoužívají správné hlavičky řízení přístupu. Pokud jsou hlavičky CORS chybějící nebo nesprávné, požadavky na více zdrojů selžou. 

Problémy CORS můžete identifikovat pomocí ladicích nástrojů prohlížeče:

1. Spusťte prohlížeč a přejděte k webové aplikaci.
1. Stisknutím klávesy **F12** otevřete konzolu ladění.
1. Zkuste reprodukování transakce a zkontrolujte zprávu konzoly. Porušení CORS vyvolá chybu konzoly týkající se původu.

Na následujícím snímku obrazovky se při výběru tlačítka **vyzkoušet** vyvolala chybová zpráva CORS, že protokol https: \/ /corswebclient-contoso.msappproxy.NET nebyl nalezen v hlavičce Access-Control-Allow-Origin.

![Problém CORS](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>Problémy CORS s proxy aplikací

Následující příklad ukazuje typický scénář Azure Proxy aplikací služby AD CORS. Interní Server hostuje kontroler webového rozhraní API **CORSWebService** a **CORSWebClient** , který volá **CORSWebService**. Je k dispozici požadavek AJAX od **CORSWebClient** do **CORSWebService**.

![Místní žádost o stejný původ](./media/application-proxy-understand-cors-issues/image1.png)

Aplikace CORSWebClient funguje i v případě, že je místně hostovaná, ale při publikování prostřednictvím Azure Proxy aplikací služby AD se buď nepovede načíst, nebo vyřadit chyby. Pokud jste aplikace CORSWebClient a CORSWebService publikovali samostatně jako jiné aplikace prostřednictvím proxy aplikací, jsou tyto dvě aplikace hostovány v různých doménách. Požadavek AJAX od CORSWebClient do CORSWebService je požadavek na více zdrojů, který se nezdařil.

![Žádost o proxy CORS aplikace](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Řešení problémů s proxy aplikací CORS

Předchozí problém CORS můžete vyřešit některým z několika způsobů.

### <a name="option-1-set-up-a-custom-domain"></a>Možnost 1: nastavení vlastní domény

Použijte [vlastní doménu](./application-proxy-configure-custom-domain.md) Azure proxy aplikací služby AD k publikování ze stejného původu, aniž byste museli dělat změny v původních aplikacích, kódu nebo hlavičkách. 

### <a name="option-2-publish-the-parent-directory"></a>Možnost 2: publikování nadřazeného adresáře

Publikujte nadřazený adresář obou aplikací. Toto řešení funguje zejména v případě, že na webovém serveru máte jenom dvě aplikace. Místo toho, aby se jednotlivé aplikace publikovaly samostatně, můžete publikovat společný nadřazený adresář, který má za následek stejný původ.

Následující příklady ukazují stránku Proxy aplikací služby AD portálu Azure pro aplikaci CORSWebClient.  Když je **interní adresa URL** nastavená na *contoso.com/CORSWebClient*, aplikace nemůže provést úspěšné požadavky do adresáře *contoso.com/CORSWebService* , protože se jedná o různé zdroje. 

![Publikovat aplikaci jednotlivě](./media/application-proxy-understand-cors-issues/image4.png)

Místo toho nastavte **interní adresu URL** pro publikování nadřazeného adresáře, který zahrnuje adresáře *CORSWebClient* i *CORSWebService* :

![Publikovat nadřazený adresář](./media/application-proxy-understand-cors-issues/image5.png)

Výsledné adresy URL aplikací efektivně vyřeší problém CORS:

- https: \/ /corswebclient-contoso.msappproxy.NET/CORSWebService
- https: \/ /corswebclient-contoso.msappproxy.NET/CORSWebClient

### <a name="option-3-update-http-headers"></a>Možnost 3: Aktualizace hlaviček protokolu HTTP

Přidejte vlastní hlavičku HTTP odpovědi na webovou službu, aby odpovídala požadavku na původ. Pro weby běžící ve službě Internetová informační služba (IIS) použijte Správce služby IIS a upravte hlavičku:

![Přidat vlastní hlavičku odpovědi ve Správci služby IIS](./media/application-proxy-understand-cors-issues/image6.png)

Tato úprava nevyžaduje žádné změny kódu. Můžete ho ověřit v trasování Fiddler:

**Publikovat záhlaví**\
HTTP/1.1 200 OK \
Cache-Control: no-cache \
Pragma: no-cache \
Content-Type: text/prostý; charset = UTF-8 \
Konec platnosti:-1 \
Lišící se: přijmout – kódování \
Server: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0 \
**Přístup-řízení-povolení-původu: https \: //corswebclient-contoso.msappproxy.NET**\
X-AspNet-verze: 4.0.30319 \
X-napájen-by: ASP.NET \
Content-Length: 17

### <a name="option-4-modify-the-app"></a>Možnost 4: Úprava aplikace

Aplikaci můžete změnit tak, aby podporovala CORS přidáním hlavičky Access-Control-Allow-Origin s příslušnými hodnotami. Způsob, jak přidat hlavičku, závisí na jazyku kódu aplikace. Změna kódu je minimální doporučená možnost, protože vyžaduje největší úsilí.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Možnost 5: prodloužení životnosti přístupového tokenu

Některé problémy CORS nelze vyřešit, například když vaše aplikace přesměruje na *Login.microsoftonline.com* k ověření a platnost přístupového tokenu vyprší. Volání CORS pak neproběhne úspěšně. Alternativním řešením pro tento scénář je prodloužení životnosti přístupového tokenu, aby nedocházelo k vypršení jeho platnosti během uživatelské relace. Další informace o tom, jak to udělat, najdete [v tématu konfigurovatelné životnosti tokenů ve službě Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Viz také
- [Kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Naplánování nasazení proxy aplikací služby Azure AD](application-proxy-deployment-plan.md) 
- [Vzdálený přístup k místním aplikacím prostřednictvím Proxy aplikací služby Azure Active Directory](application-proxy.md)