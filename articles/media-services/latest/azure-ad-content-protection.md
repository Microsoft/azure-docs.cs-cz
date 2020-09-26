---
title: Komplexní ochrana obsahu pomocí Azure AD
description: V tomto článku se naučíte, jak chránit obsah pomocí Azure Media Services a Azure Active Directory
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 71bfff1b2ef75a13a3898d2fd9760dd60b83e3a5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318309"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>Kurz: komplexní ochrana obsahu pomocí Azure AD

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V tomto kurzu a v zadané ukázce přehrávače můžete nastavit kompletní podsystém ochrany mediálního obsahu v Azure Media Services (AMS) a Azure Active Directory (AAD) pro streamování mediálního obsahu se všemi podporovanými možnostmi AMS DRM/AES-128, streamování, kodeků a formátů kontejnerů. Vzorek je dostatečně obecný pro zabezpečený přístup k jakýmkoli REST API chráněným protokolem OAuth 2 prostřednictvím autorizačního toku kódu a ověřovacího klíče pro výměnu kódu (PKCE). (Azure Media Services služby doručování licencí je jenom jedna z nich.) Funguje taky pro Microsoft Graph rozhraní API nebo jakýkoli vlastní vyvinutý REST API zabezpečený pomocí toku autorizačního kódu OAuth 2. Toto je doprovodný dokument k [ukázkovému kódu](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).

V tomto kurzu provedete následující:

> [!div class="checklist"]
>
> * Zvažte požadavky na ověřování.
> * Vysvětlení fungování aplikace
> * Registrace aplikace prostředků back-endu
> * Registrace klientské aplikace
> * Nastavení zásad pro klíč obsahu účtu Media Services a zásad streamování
> * Nastavení aplikace Player

Pokud nemáte předplatné Azure Media Services, vytvořte [bezplatný zkušební účet](https://azure.microsoft.com/free/) Azure a pak vytvořte účet Media Services.

### <a name="duration"></a>Doba trvání
Tento kurz by měl trvat přibližně dvě hodiny, než se dokončí, když máte připravenou technologii, kterou si můžete projít.

## <a name="prerequisites"></a>Požadavky

Použijí se následující nejnovější technologické verze a koncepty. Před zahájením tohoto kurzu se jim doporučujeme seznámit s nimi.

### <a name="prerequisite-knowledge"></a>Předpokládané znalosti

Je volitelné, ale doporučujeme, abyste před zahájením tohoto kurzu seznámili s následujícími koncepty:

* Digitální Rights Management (DRM)
* [Azure Media Services (AMS) V3](./media-services-overview.md)
* [Zásady klíčů obsahu](content-key-policy-concept.md) AMS pomocí nástroje AMS API v3, Azure Portal nebo [aplikace Azure Media Services Explorer (AMSE)](https://github.com/Azure/Azure-Media-Services-Explorer)
* Koncové body Azure AD v2 na [platformě Microsoft Identity](../../active-directory/develop/index.yml)
* Moderní cloudové ověřování, jako je [OAuth 2,0 a OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md)
  * [Tok autorizačního kódu v OAuth 2,0](../../active-directory/develop/v2-oauth2-auth-code-flow.md) a proč je potřeba PKCE
  * [Delegované oprávnění vs – oprávnění aplikace](../../active-directory/develop/developer-glossary.md#permissions)
* [Token JWT](../../active-directory/develop/access-tokens.md), jeho deklarace a výměna podpisového klíče (zahrnuto v ukázce)

### <a name="prerequisite-code-and-installations"></a>Požadovaný kód a instalace

* Vzorový kód. Stáhněte si [vzorový kód](https://github.com/Azure-Samples/media-services-content-protection-azure-ad).
* Instalace Visual Studio Code. Stáhněte si Visual Studio Code sem [https://code.visualstudio.com/download](https://code.visualstudio.com/download) .
* Instalace Node.js. Stáhněte si Node.js sem [https://nodejs.org](https://nodejs.org) . NPM je součástí instalace.
* [Předplatné Azure](https://azure.microsoft.com/free/)
* Účet Azure Media Services (AMS).
* @azure/msal-browser v 2.0 jeden z členů sady SDK [Microsoft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) pro různé klientské platformy
* Nejnovější verze [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples)(obsažená v ukázce)
* Přihlašovací údaje pro FPS od společnosti Apple, pokud chcete zahrnout FairPlay DRM a certifikát aplikace hostovaný s CORS, který je přístupný prostřednictvím JavaScriptu na straně klienta.

> [!IMPORTANT]
> V tomto kurzu se pomocí .NET vytvoří omezení zásad pro klíč obsahu.  Pokud nejste vývojářem .NET, ale chcete se pokusit o Node.js připojit k Azure Media Services, přečtěte si téma [připojení k Media Services rozhraní API v3 Node.js](configure-connect-nodejs-howto.md). K dispozici je také modul Node.js k dispozici pro automatické zpracování výměny klíčů v tématu Node.js [Passport-AD Module](https://github.com/AzureAD/passport-azure-ad).

## <a name="consider-the-authentication-and-authorization-requirements"></a>Zvažte požadavky na ověřování a autorizaci.

V návrhu subsystému se zobrazí několik výzev. Má několik přesouvaných částí, existuje omezení klientské aplikace a výměna klíčů Azure AD, ke které dochází každých šest týdnů.

Jednostránkové aplikace (SPA) použitá v tomto kurzu vezme v úvahu problémy s požadavky na ověření a níže uvedené omezení. Používá:

* Koncové body Azure AD v2 jako Azure AD Developer Platform (v1 koncové body) se mění na platformu Microsoft Identity Platform (v2 – koncové body).
* Tok autorizačního kódu, protože tok implicitního udělení OAuth 2 je zastaralý.
* Aplikace, která podléhá následujícím omezením:
    * Veřejný klient nemůže skrýt tajný klíč klienta.  Samotný tok autorizačního kódu vyžaduje skrytí tajného klíče klienta, takže se použije tok autorizačního kódu s PKCE.
    * Aplikace založená na prohlížeči, která podléhá izolovanému prostoru zabezpečení prohlížeče (omezení CORS/Kontrola před výstupem).
    * Aplikace založená na prohlížeči pomocí moderního JavaScriptu, která podléhá omezením zabezpečení JavaScriptu (přístupnost vlastní hlavičky, ID korelace).

## <a name="understand-the-subsystem-design"></a>Pochopení návrhu subsystému

Návrh subsystému je zobrazen v následujícím diagramu.  Má tři vrstvy:

* Back-Office (černá) pro konfiguraci zásad klíčů obsahu a publikování obsahu pro streamování
* Veřejné koncové body (modře), které jsou koncové body přehrávače nebo zákazníka poskytující ověřování, autorizaci, licenci DRM a šifrovaný obsah
* Aplikace přehrávače (světle modrá), která integruje všechny součásti a
    * zpracovává ověřování uživatelů prostřednictvím služby Azure AD.
    * zpracovává access_token akvizice z Azure AD.
    * přijímá manifest a šifrovaný obsah z AMS/CDN.
    * Získá licenci DRM od Azure Media Services.
    * zpracovává dešifrování, dekódování a zobrazení obsahu.

![obrazovka pro analýzu tokenů JWT](media/aad-ams-content-protection/subsystem.svg)

Další podrobnosti o subsystému najdete v tématu [Návrh systému ochrany obsahu s více DRM pomocí řízení přístupu](./design-multi-drm-system-with-access-control.md) .

## <a name="understand-the-single-page-app"></a>Pochopení jednostránkové aplikace

Aplikace přehrávače je jednostránkové aplikace (SPA), vyvinutá v Visual Studio Code pomocí:

* Node.js s využitím jazyka JavaScript pro ES 6
* @azure/msal-browser 2,0 beta
* Sada Azure Media Player SDK
* Tok OAuth 2 s koncovými body služby Azure AD v2 (Microsoft Identity Platform)

Aplikace Player pro SPA se dokončí následujícími akcemi:

* Ověřování uživatelů pro uživatele nativně pro tenanta a uživatele typu host z jiných klientů AAD nebo účtů MSA. Uživatelé se můžou rozhodnout, že se přihlásí pomocí místní nabídky prohlížeče nebo přesměrování (pro prohlížeče nepovolující automaticky otevíraná okna, jako je Safari).
* Získání `access_token` prostřednictvím toku autorizačního kódu s PKCE.
* Obnovení `access_token` (tokeny vydané AAD jsou platné po dobu 1 hodiny), pro které `refresh_token` se taky získá.
* Analyzují se tokeny JWT ( `access_token` a `id_token` ) pro test/kontrolu.
* Získání licencí DRM pro všechny tři klíče obsahu několikanásobnou nebo AES-128.
* Streamování obsahu v rámci různých kombinací protokolu DRM a formátu kontejneru vs. Pro každou kombinaci se vygeneruje správný řetězec formátu.
* Dešifrování, dekódování a zobrazení.
* Microsoft Graph volání rozhraní API pro účely řešení potíží. <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

Obrazovka pro přihlášení, získání tokenu, obnovení tokenu a zobrazení tokenu:

 ![Obrazovka pro přihlášení, získání tokenu, obnovení tokenu a zobrazení tokenu](media/aad-ams-content-protection/token-acquisition.png)

Obrazovka pro analýzu tokenů JWT (access_token nebo id_token):

![obrazovka pro analýzu tokenů JWT](media/aad-ams-content-protection/parsing-jwt-tokens.png)

Obrazovka pro testování chráněného obsahu s různými kombinacemi protokolů DRM/AES vs streaming a formátu kontejneru:

![obrazovka pro analýzu tokenů JWT](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>Výběr tenanta Azure AD

> [!NOTE]
> Od tohoto okamžiku se předpokládá, že jste se přihlásili ke Azure Portal a máte aspoň jeden tenant služby Azure AD.

Vyberte tenanta Azure AD, který se použije pro ucelenou ukázku. Máte dvě možnosti:

* Existující tenant služby Azure AD. Jakékoli předplatné Azure musí mít jednoho tenanta Azure AD, ale tenanta Azure AD může používat víc předplatných Azure.
* Nový tenant Azure AD, který není *používán žádným* předplatným Azure. Pokud zvolíte možnost Nový tenant, účet mediální služby a ukázková aplikace přehrávače musí být v předplatném Azure, které používá samostatného tenanta Azure AD. To přináší značnou flexibilitu. Můžete například použít vlastního tenanta Azure AD, ale také účet mediální služby zákazníka v předplatném Azure zákazníka.

## <a name="register-the-backend-resource-app"></a>Registrace aplikace prostředku back-endu

1. Přejděte do tenanta služby Azure AD, který jste zvolili nebo vytvořili.
1. V nabídce vyberte **Azure Active Directory** .
1. V nabídce vyberte **Registrace aplikací** .
1. Klikněte na **+ Nová registrace**.
1. Pojmenujte aplikaci *LicenseDeliveryResource2* (kde 2 označuje koncové body AAD v2).
1. Vyberte **účty jenom v tomto organizačním adresáři ([*název vašeho tenanta*] jenom jeden tenant)**. Pokud chcete povolit přístup k více klientům, vyberte jednu z dalších možností víceklientské architektury.
1. **Identifikátor URI přesměrování** je nepovinný a později ho můžete změnit.
1. Klikněte na **Zaregistrovat**. Zobrazí se zobrazení Registrace aplikací.
1. V nabídce vyberte **manifest** . Zobrazí se zobrazení manifestu.
1. Změňte hodnotu na `accessTokenAcceptedVersion` *2* (žádné uvozovky).
1. Změňte hodnotu na `groupMembershipClaims` *"securitycollection"* (s uvozovkami).
1. Klikněte na **Uložit**.
1. V nabídce vyberte **zveřejnit rozhraní API** . Zobrazí se zobrazení přidat obor. (Azure poskytuje identifikátor URI ID aplikace, ale pokud ho chcete změnit, můžete upravit v poli identifikátor URI ID aplikace.)
1. Klikněte na **Uložit a pokračujte**. Zobrazení se změní. Pro každé nastavení ve sloupci nastavení v následující tabulce zadejte hodnotu ve sloupci Hodnota a pak klikněte na **Přidat obor**.

| Nastavení | Hodnota | Popis |
| ------- | ----- | ----------- |
| Název oboru | *Digitálních. Licence. doručení* | Způsob zobrazení oboru při žádosti o přístup k tomuto rozhraní API a v přístupových tokenech v případě udělení oboru klientské aplikaci. Toto musí být v rámci této aplikace jedinečné. Osvědčeným postupem je použití "prostředku. Operation. Constraint" jako vzoru pro vygenerování názvu. |
| Kdo může souhlasit? | *Správci a uživatelé* | Určuje, jestli uživatelé můžou v adresářích, kde je povolený souhlas uživatele, souhlasit s tímto oborem. |
| Zobrazovaný název souhlasu správce | *Doručování licencí DRM* | Jaký rozsah bude volán na obrazovce pro vyjádření souhlasu, když správci souhlasí s tímto oborem. |
| Popis souhlasu správce * * | *Obor prostředku back-endu doručování licencí DRM* | Podrobný popis oboru, který se zobrazí, když správci tenanta rozbalí obor na obrazovce pro vyjádření souhlasu. |
| Zobrazovaný název souhlasu uživatele | *Digitálních. Licence. doručení* | Jaký rozsah bude vyvolán na obrazovce pro vyjádření souhlasu uživatele, pokud uživatel souhlasí s tímto oborem. |
| Popis souhlasu uživatele | *Obor prostředku back-endu doručování licencí DRM* | Toto je podrobný popis oboru, který se zobrazí, když uživatel rozbalí obor na obrazovce pro vyjádření souhlasu. |
| Stav | *Povoleno* | Určuje, jestli je tento obor dostupný pro klienty k vyžádání. Nastavte ji na Disabled (zakázáno) pro obory, které nechcete zobrazovat klientům. Odstranit lze pouze zakázané obory a doporučujeme počkat aspoň týden po zakázání rozsahu před jeho odstraněním, aby se zajistilo, že ho žádní klienti stále nepoužívají. |

## <a name="register-the-client-app"></a>Registrace klientské aplikace

1. Přejděte do tenanta služby Azure AD, který jste zvolili nebo vytvořili.
1. V nabídce vyberte **Azure Active Directory** .
1. V nabídce vyberte **Registrace aplikací** .
1. Klikněte na **+ Nová registrace**.
1. Poskytněte klientské aplikaci název, třeba *AAD Content Protection v AMS*.
1. Vyberte **účty jenom v tomto organizačním adresáři ([*název vašeho tenanta*] jenom jeden tenant)**. Pokud chcete povolit přístup k více klientům, vyberte jednu z dalších možností víceklientské architektury.
1. **Identifikátor URI přesměrování** je nepovinný a později ho můžete změnit.
1. Klikněte na **Zaregistrovat**.
1. V nabídce vyberte **oprávnění rozhraní API** .
1. Klikněte na **+ Přidat oprávnění**. Otevře se zobrazení oprávnění rozhraní API pro vyžádání.
1. Klikněte na kartu **Moje rozhraní API** a vyberte aplikaci *LicenseDeliveryResource2* , kterou jste vytvořili v poslední části.
1. Klikněte na šipku DRM a ověřte *DRM. Oprávnění License. Delivery* .
1. Klikněte na tlačítko **Přidat oprávnění**. Bude zavřeno zobrazení přidat oprávnění.
1. V nabídce vyberte **manifest** . Zobrazí se zobrazení manifestu.
1. Vyhledejte a přidejte následující páry hodnot do `replyUrlsWithType` atributu:

   ```json
   "replyUrlsWithType": [
        {
            "url": "https://npmwebapp.azurewebsites.net/",
            "type": "SPA"
        },
        {
            "url": "http://localhost:3000/",
            "type": "SPA"
        }
    ],
   ```

    > [!NOTE]
    > V tomto okamžiku nemáte ještě adresu URL pro aplikaci Player.  Pokud aplikaci spouštíte z webserveru localhost, můžete použít jenom dvojici hodnota localhost. Po nasazení aplikace Player můžete sem přidat položku s nasazenou adresou URL.  Pokud se k tomu zapomenete, zobrazí se v přihlášení Azure AD chybová zpráva.

1. Klikněte na **Uložit**.
1. Nakonec ověřte, zda je konfigurace správná, vyberte **ověřování**.  Zobrazí se zobrazení ověřování. Klientská aplikace bude uvedena jako jediná stránková aplikace (SPA), bude uveden identifikátor URI pro přesměrování a typ udělení bude tok kódu autorizace pomocí PKCE.

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>Nastavení zásad pro klíč obsahu Media Services účtu a zásady streamování

**V této části se předpokládá, že jste vývojářem .NET a máte zkušenosti s používáním rozhraní API AMS v3.**

> [!NOTE]
> V době psaní tohoto textu nemůžete použít Azure Portal pro nastavení zásad účtu Media Services, protože nepodporuje použití podpisového klíče asymetrického tokenu s OpenID-config.  Nastavení musí podporovat výměnu klíčů Azure AD, protože vydaný token služby Azure AD je podepsaný asymetrickým klíčem a klíč se přenese každých šest týdnů. Proto tento kurz používá rozhraní .NET a rozhraní API AMS v3.

Konfigurace [zásad klíče obsahu](content-key-policy-concept.md) a zásad [streamování](streaming-policy-concept.md) pro DRM a AES-128 platí pro.  Změňte `ContentKeyPolicyRestriction` v zásadách klíče obsahu.

Níže je uvedený kód .NET pro vytvoření omezení zásad klíče obsahu.

```dotnetcli
ContentKeyPolicyRestriction objContentKeyPolicyRestriction;

//use Azure Active Directory OpenId discovery document, supporting key rollover
objContentKeyPolicyRestriction = new ContentKeyPolicyTokenRestriction()
    {
        OpenIdConnectDiscoveryDocument = ConfigAccessor.AppSettings["ida_AADOpenIdDiscoveryDocument"]
    };

string audience = ConfigAccessor.AppSettings["ida_audience"];
string issuer   = ConfigAccessor.AppSettings["ida_issuer"];

ContentKeyPolicyTokenRestriction objContentKeyPolicyTokenRestriction = (ContentKeyPolicyTokenRestriction)objContentKeyPolicyRestriction;
objContentKeyPolicyTokenRestriction.Audience             = audience;
objContentKeyPolicyTokenRestriction.Issuer               = issuer;
objContentKeyPolicyTokenRestriction.RestrictionTokenType = ContentKeyPolicyRestrictionTokenType.Jwt;

objContentKeyPolicyRestriction = objContentKeyPolicyTokenRestriction;

return objContentKeyPolicyRestriction;
```

Změňte `ida_AADOpenIdDiscoveryDocument` hodnoty, `ida_audience` a `ida_issuer` ve výše uvedeném kódu. Chcete-li najít hodnoty pro tyto položky v Azure Portal:

1. Vyberte tenanta AAD, kterého jste použili dříve, klikněte v nabídce na **Registrace aplikací** a pak klikněte na odkaz **koncové body** .
1. Vyberte a zkopírujte hodnotu pole **dokumentu metadat OpenIdConnect** a vložte ho do kódu jako `ida_AADOpenIdDiscoveryDocument` hodnotu.
1. `ida_audience`Hodnota je ID aplikace (klienta) registrované aplikace *LicenseDeliveryResource2*.
1. `ida_issuer`Hodnota je adresa URL `https://login.microsoftonline.com/[tenant_id]/v2.0` . Nahraďte [*tenant_id*] svým ID tenanta.

## <a name="set-up-the-sample-player-app"></a>Nastavení ukázkové aplikace přehrávače

Pokud jste to ještě neudělali, naklonujte nebo Stáhněte aplikaci ze zdrojového úložiště: [https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad) .

Máte dvě možnosti, jak nastavit aplikaci přehrávače:

* Minimální přizpůsobení (nahrazuje pouze některé hodnoty konstantních řetězců, například client_id, tenant_id a adresa URL streamování), ale je nutné použít Visual Studio Code a Node.js.
* Chcete-li použít jiné integrované vývojové prostředí (IDE) a webovou platformu, jako je například ASP.NET Core, můžete do projektu umístit soubory webových stránek, soubory JavaScriptu a soubor CSS, protože aplikace přehrávače sama nepoužívá žádný kód na straně serveru.

### <a name="option-1"></a>Možnost 1

1. Spusťte Visual Studio Code.
1. Chcete-li projekt otevřít, klikněte na položku soubor-> otevřít složku – > vyhledejte a vyberte nadřazenou složku *package.jsv* souboru.
1. Otevřete JavaScriptový soubor *Public/JavaScript/constants.js*.
1. Nahraďte `OAUTH2_CONST.CLIENT_ID` `client_id` vaší registrovanou klientskou aplikací v tenantovi AAD.  Najdete ho v `client_id` části Přehled registrované aplikace v Azure Portal. Poznámka: Toto je ID klienta, nikoli ID objektu.
1. Nahraďte `OAUTH2_CONST.TENANT_ID` `tenant_id` členem vašeho TENANTA Azure AD. Můžete `tenant_id` to najít tak, že kliknete na nabídku Azure Active Directory. V části Přehled se zobrazí tenant_id.
1. Nahraďte `OAUTH2_CONST.SCOPE` rozsahem, který jste přidali do registrované klientské aplikace. Rozsah najdete tak, že přejdete do registrované klientské aplikace z nabídky **Registrace aplikací** a pak vyberete klientskou aplikaci:
    1. Vyberte klientskou aplikaci, klikněte na nabídku **oprávnění rozhraní API** a pak vyberte rozsah *DRM. License. Delivery* v rámci oprávnění API *LicenseDeliveryResource2*. Oprávnění by mělo být ve formátu, jako je *API://df4ed433-dbf0-4da6-B328-e1fe05786db5/DRM. License. Delivery*. **Důležité**: zachovejte místo na začátku `offline_access` v `OAUTH2_CONST.SCOPE` .
1. Nahraďte dva konstantní řetězce pro `AMS_CONST` , jak je znázorněno níže. Jedna je adresa URL chráněného streamování vašeho testovacího prostředku a druhá je adresa URL certifikátu aplikace FPS, pokud chcete zahrnout FairPlay testovací případ. V opačném případě ji můžete ponechat tak, jak je pro `AMS_CONST.APP_CERT_URL` . Pak klikněte na **Uložit**.

```javascript
//defaults in ams.js
class AMS_CONST {
    static get DEFAULT_URL() {
        return "https://eventgridmediaservice-usw22.streaming.media.azure.net/9591e337-ae90-420e-be30-1da36c06665b/MicrosoftElite01.ism/manifest(format=mpd-time-csf,encryption=cenc)";
    }
    //FairPlay application cert URL
    static get APP_CERT_URL() {
     return `${window.location.href}cert/FPSAC.cer`;
    }
}
```

K otestování lokálně:

1. V Visual Studio Code (VSC) vyberte v hlavní nabídce příkaz **Zobrazit** a potom na **terminálu**.
1. Pokud jste ještě nenainstalovali NPM, zadejte do příkazového řádku `npm install` .
1. `npm start`Do příkazového řádku zadejte. (Pokud se npm nespustí, zkuste změnit adresář tak, `npmweb` že zadáte do `cd npmweb` příkazového řádku.)
1. K procházení použijte prohlížeč `http://localhost:3000` .

V závislosti na prohlížeči, který používáte, vyberte správnou kombinaci formátu standardu DRM/AES vs streamování, který se má testovat po přihlášení ( `access_token` pořízení). Pokud testujete v Safari na macOS, podívejte se na možnost přesměrování rozhraní API, protože Safari nepovoluje automaticky otevíraná okna. Většina ostatních prohlížečů povoluje jak místní nabídky, tak možnosti přesměrování.

### <a name="option-2"></a>Možnost 2

Pokud máte v úmyslu použít jinou platformu IDE/web nebo webový server, jako je například služba IIS spuštěná na vývojovém počítači, zkopírujte následující soubory do nového adresáře na místním webovém serveru. Níže uvedené cesty najdete místo, kde se nacházíte v kódu, který jste stáhli.

* *views/index. EJS* (změna přípony na. html)
* *views/JWT. EJS* (změna přípony na. html)
* *views/info. EJS* (změna přípony na HTML)
* *Public/** (soubory JavaScript, CSS, obrázky, jak je znázorněno níže)

1. Zkopírujte soubory nacházející se ve složce *zobrazení* do kořenového adresáře nového adresáře.
1. Zkopírujte *složky* nalezené ve *veřejné* složce do kořenového adresáře nového adresáře.
1. Změňte přípony `.ejs` souborů na `.html` . (Není použita žádná proměnná na straně serveru, abyste ji mohli bezpečně změnit.)
1. Otevřete *index.html* v VSC (nebo jiném editoru kódu) a změňte `<script>` cesty a `<link>` , aby odrážely, kde se soubory nacházejí.  Pokud jste postupovali podle předchozích kroků, stačí pouze odstranit `\` v cestě.  Například `<script type="text/javascript" src="/javascript/constants.js"></script>` se bude jednat o `<script type="text/javascript" src="javascript/constants.js"></script>` .
1. Upravte konstanty v souboru *JavaScriptu/constants.js* jako v možnosti 1.

## <a name="common-customer-scenarios"></a>Běžné scénáře zákazníků

Teď, když jste dokončili kurz a máte funkční subsystém, můžete ho zkusit upravit v následujících scénářích zákazníků:

### <a name="role-based-access-control-rbac-for-license-delivery-via-azure-ad-group-membership"></a>Access Control na základě rolí (RBAC) pro doručování licencí prostřednictvím členství ve skupině Azure AD

V tomto případě systém umožňuje každému uživateli, který se může přihlásit, získat platnou licenci a přehrát chráněný obsah.

Je to běžný požadavek zákazníka, že podmnožina ověřených uživatelů může sledovat obsah, i když jiné nejsou, například zákazník, který nabízí předplatné Basic a Premium pro svůj obsah videa. Jejich zákazníci, kteří zaplatili za základní předplatné, by neměli umět sledovat obsah, který vyžaduje předplatné Premium. Níže jsou uvedené další kroky potřebné k splnění tohoto požadavku:

#### <a name="set-up-the-azure-ad-tenant"></a>Nastavení tenanta Azure AD

1. Nastavte dva účty ve vašem tenantovi. Můžou mít název *premium_user* a *basic_user*;
1. Vytvořte skupinu uživatelů a zavolejte ji na skupinu *Premium*.
1. Přidejte *premium_user* do skupiny *Premium* jako člena, ale nepřidávejte *basic_user* do skupiny.
1. Poznamenejte si **ID objektu** úrovně *Premium*.

#### <a name="set-up-the-media-services-account"></a>Nastavení účtu Media Services

Upravte `ContentKeyPolicyRestriction` (jak je znázorněno v části nastavení v účtu Media Service), přidáním deklarace s názvem *skupiny*, kde `ida_EntitledGroupObjectId` má jako hodnotu ID objektu *Premium* :

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

Deklarace *skupin* je členem [omezené sady deklarací identity](../../active-directory/develop/active-directory-claims-mapping.md#claim-sets) ve službě Azure AD.

#### <a name="test"></a>Test

1. Přihlaste se pomocí účtu *premium_user* . Měli byste být schopni přehrát chráněný obsah.
1. Přihlaste se pomocí účtu *basic_user* . Měla by se zobrazit chyba oznamující, že video je šifrované, ale není k dispozici žádný klíč k dešifrování. Pokud zobrazíte události, chyby a soubory ke stažení s rozevíracím seznamem v dolní části překrytí diagnostiky přehrávače, chybová zpráva by měla indikovat selhání získání licence z důvodu chybějící hodnoty deklarace identity skupin v tokenu JWT vydaného koncovým bodem tokenu služby Azure AD.

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>Podpora více účtů mediálních služeb (u několika předplatných)

Zákazník může mít několik účtů mediálních služeb v jednom nebo několika předplatných Azure. Zákazník může například mít jeden účet Media Service jako primární produkční primární, druhý jako sekundární/záložní a druhý pro vývoj a testování.

Stačí, abyste měli jistotu, že používáte stejnou sadu parametrů, jako jste použili v části (nastavení v účtu Media Service) v tématu vytvoření `ContentKeyPolicyRestriction` ve všech účtech mediální služby.

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>Podpora zákazníků, jejich dodavatelů a/nebo poboček v rámci několika tenantů AAD

Jako uživatelé řešení můžou dceřiné společnosti a dodavatelé a partneři zákazníka být v různých klientech AAD, jako jsou `mycustomer.com` , `mysubsidiary.com` a `myparther.com` . I když je toto řešení postavené na jednom konkrétním tenantovi AAD, jako je `mycustomer.com` , můžete ho zpřístupnit pro uživatele z jiných tenantů.

Pomocí `mycustomer.com` tohoto řešení přidejte uživatele z `mypartner.com` jako uživatel typu Host do `mycustomer.com` tenanta. Ujistěte se, že `mypartner.com` Uživatel účet hosta aktivuje. Účet hosta může být buď z jiného tenanta služby AAD, nebo z `outlook.com` účtu.

Všimněte si, že uživatelé typu Host od `mypartner.com` , po aktivaci v `mycustomer.com` , jsou stále ověřeni pomocí svého vlastního nebo původního tenanta AAD, `mypartner.com` ale `access_token` vystavil `mycustomer.com` .

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>Podpora tenanta nebo předplatného zákazníka s nastavením ve vašem předplatném/tenantovi

Pomocí instalačního programu můžete testovat chráněný obsah v účtu nebo předplatném služby Media Services zákazníka. Nastavili jste ho jako klienta Azure AD a účet mediální služby ve stejném předplatném. Účet Media Services zákazníka by byl v předplatném Azure s vlastním tenant Azure AD.

1. Přidejte účet zákazníka do svého tenanta jako účet Guest.
1. Spolupracujte se svým zákazníkem a připravte chráněný obsah v účtu mediální služby na vašem zákazníkovi zadáním tří parametrů uvedených v části nastavení účtu služby Media Services.

Zákazník pak může přejít k vašemu nastavení, přihlásit se pomocí účtu hosta a otestovat vlastní chráněný obsah. Můžete se také přihlásit pomocí vlastního účtu a otestovat obsah svého zákazníka.

Ukázkové řešení je možné nastavit v tenantovi Microsoftu s předplatným Microsoft nebo vlastním klientem s předplatným Microsoft. Instance Azure Media Service může být z jiného předplatného s jeho klientem.

## <a name="clean-up-resources"></a>Vyčištění prostředků

> [!WARNING]
> Pokud nebudete tuto aplikaci nadále používat, odstraňte prostředky, které jste vytvořili, a to za tímto kurzem. V opačném případě se vám budou účtovat poplatky za ně.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rychlý Start: šifrování obsahu](encrypt-content-quickstart.md)
