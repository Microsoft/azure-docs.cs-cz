---
title: Přechod na podpisový klíč ve službě Azure AD
description: Tento článek popisuje doporučené postupy pro přechod podpisového klíče pro službu Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: f3585cfa7ea6f0d8afc61e899f9641d415a2e354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161184"
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Přechod podpisového klíče ve službě Azure Active Directory
Tento článek popisuje, co potřebujete vědět o veřejných klíčích, které se používají ve službě Azure Active Directory (Azure AD) k podepisování tokenů zabezpečení. Je důležité si uvědomit, že tyto klíče převrátit v pravidelných intervalech, a v případě nouze, by mohla být převrácena okamžitě. Všechny aplikace, které používají Azure AD by měl být schopen programově zpracovat proces přechodu na klíče nebo vytvořit proces pravidelné ruční přechodu. Pokračujte ve čtení, abyste pochopili, jak klávesy fungují, jak posoudit dopad přechodu na aplikaci a jak aktualizovat aplikaci nebo vytvořit proces pravidelného ručního přechodu pro zpracování efektu přechodu klíčů v případě potřeby.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Přehled podpisových klíčů ve službě Azure AD
Azure AD používá kryptografii veřejného klíče postavenou na oborových standardech k vytvoření důvěry mezi sebou a aplikacemi, které ji používají. V praxi to funguje následujícím způsobem: Azure AD používá podpisový klíč, který se skládá z dvojice veřejného a soukromého klíče. Když se uživatel přihlásí k aplikaci, která používá Azure AD pro ověřování, Azure AD vytvoří token zabezpečení, který obsahuje informace o uživateli. Tento token je podepsán akreditovaný službou Azure AD pomocí jeho privátního klíče před odesláním zpět do aplikace. Chcete-li ověřit, že token je platný a pochází z Azure AD, aplikace musí ověřit podpis tokenu pomocí veřejného klíče vystaveného službou Azure AD, který je obsažen v [dokumentu zjišťování OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html) klienta nebo dokumentu [federačních metadat](../azuread-dev/azure-ad-federation-metadata.md)SAML/WS-Fed .

Z bezpečnostních důvodů azure ad podpisový klíč rolích v pravidelných intervalech a v případě nouze, může být převrácený okamžitě. Každá aplikace, která se integruje s Azure AD by měla být připravena ke zpracování události přechodu na klíče bez ohledu na to, jak často může nastat. Pokud tomu tak není a aplikace se pokusí použít klíč s prošlou platností k ověření podpisu na tokenu, požadavek na přihlášení se nezdaří.

V dokumentu zjišťování OpenID Connect a dokumentu metadat federace je vždy k dispozici více než jeden platný klíč. Vaše aplikace by měla být připravena použít některý z klíčů uvedených v dokumentu, protože jeden klíč může být vrácena brzy, jiný může být jeho nahrazení a tak dále.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Jak posoudit, zda bude vaše žádost ovlivněna a co s tím dělat
Způsob, jakým aplikace zpracovává přechod klíče, závisí na proměnných, jako je typ aplikace nebo jaký protokol identity a knihovna byly použity. Následující části posuzují, zda jsou nejběžnější typy aplikací ovlivněny přechodem na klíče, a poskytují pokyny, jak aktualizovat aplikaci tak, aby podporovala automatický přechod nebo ručně aktualizovalklíč.

* [Nativní klientské aplikace přistupující k prostředkům](#nativeclient)
* [Webové aplikace / API přistupující k prostředkům](#webclient)
* [Webové aplikace / API chrání prostředky a jsou vytvořená pomocí služby Azure App Services](#appservices)
* [Webové aplikace / rozhraní API chránící prostředky pomocí middlewaru .NET OWIN OpenID Connect, WS-Fed nebo WindowsAzureActiveDirectoryBearerAuthentication](#owin)
* [Webové aplikace / rozhraní API chránící prostředky pomocí middlewaru .NET Core OpenID Connect nebo JwtBearerAuthentication](#owincore)
* [Webové aplikace / rozhraní API chránící prostředky pomocí modulu Passport-azure-ad Node.js](#passport)
* [Webové aplikace / API chránící prostředky a vytvořené pomocí Sady Visual Studio 2015 nebo novějších](#vs2015)
* [Webové aplikace chránící prostředky a vytvořené pomocí Sady Visual Studio 2013](#vs2013)
* Webová api chrání cítění a vytvořená pomocí Visual Studia 2013
* [Webové aplikace chránící prostředky a vytvořené pomocí Sady Visual Studio 2012](#vs2012)
* [Webové aplikace chránící prostředky a vytvořené pomocí sady Visual Studio 2010, 2008 o pomocí služby Windows Identity Foundation](#vs2010)
* [Webové aplikace / API chráníprostředky pomocí jiných knihoven nebo ručně implementují některý z podporovaných protokolů](#other)

Tyto pokyny se **nevztahují** na:

* Aplikace přidané z Galerie aplikací Azure AD (včetně custom) mají samostatné pokyny s ohledem na podpisové klíče. [Více informací.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Místní aplikace publikované prostřednictvím proxy aplikace se nemusí starat o podepisování klíčů.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>Nativní klientské aplikace přistupující k prostředkům
Aplikace, které přistupují pouze k prostředkům (tj. Microsoft Graph, KeyVault, Outlook API a další rozhraní API Microsoft) obecně pouze získat token a předat jej spolu vlastníka prostředku. Vzhledem k tomu, že nechrání žádné prostředky, nekontrolují token, a proto nemusí zajistit, aby byl řádně podepsán.

Nativní klientské aplikace, ať už stolní nebo mobilní, spadají do této kategorie, a proto nejsou přechodem ovlivněny.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>Webové aplikace / API přistupující k prostředkům
Aplikace, které přistupují pouze k prostředkům (tj. Microsoft Graph, KeyVault, Outlook API a další rozhraní API Microsoft) obecně pouze získat token a předat jej spolu vlastníka prostředku. Vzhledem k tomu, že nechrání žádné prostředky, nekontrolují token, a proto nemusí zajistit, aby byl řádně podepsán.

Webové aplikace a webová úložiště API, která používají tok pouze pro aplikace (pověření klienta / klientský certifikát), spadají do této kategorie a nejsou tedy přechodem ovlivněny.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>Webové aplikace / API chrání prostředky a jsou vytvořená pomocí služby Azure App Services
Azure App Services 'ověřování / autorizace (EasyAuth) funkce již má potřebnou logiku pro automatické zpracování přechodu klíče.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>Webové aplikace / rozhraní API chránící prostředky pomocí middlewaru .NET OWIN OpenID Connect, WS-Fed nebo WindowsAzureActiveDirectoryBearerAuthentication
Pokud vaše aplikace používá middleware .NET OWIN OpenID Connect, WS-Fed nebo WindowsAzureActiveDirectoryBearerAuthentication, má již potřebnou logiku pro automatické zpracování přechodu klíče.

Můžete potvrdit, že vaše aplikace používá některý z těchto hledá některou z následujících úryvků v Startup.cs nebo Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
     });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>Webové aplikace / rozhraní API chránící prostředky pomocí middlewaru .NET Core OpenID Connect nebo JwtBearerAuthentication
Pokud vaše aplikace používá middleware .NET Core OWIN OpenID Connect nebo JwtBearerAuthentication, má již potřebnou logiku pro automatické zpracování efektu převrácení klíče.

Můžete potvrdit, že vaše aplikace používá některý z těchto hledá některou z následujících úryvků v Startup.cs nebo Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Webové aplikace / rozhraní API chránící prostředky pomocí modulu Passport-azure-ad Node.js
Pokud vaše aplikace používá modul Passport-ad Node.js, má již potřebnou logiku pro automatické zpracování efektu přechodu pomocí klíče.

Můžete potvrdit, že vaše žádost pas-ad vyhledáním následující úryvek v aplikaci app.js vaší žádosti

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>Webové aplikace / API chránící prostředky a vytvořené pomocí Sady Visual Studio 2015 nebo novějších
Pokud byla vaše aplikace vytvořena pomocí šablony webové aplikace v sadě Visual Studio 2015 nebo novější a v nabídce **Změnit ověřování** jste vybrali **pracovní nebo školní účty,** má už potřebnou logiku pro automatické zpracování efektu přechodu pomocí klíče. Tato logika, vložená do middlewaru OWIN OpenID Connect, načte a uloží klíče z dokumentu zjišťování OpenID Connect a pravidelně je aktualizuje.

Pokud jste do řešení přidali ověření ručně, aplikace nemusí mít potřebnou logiku přechodu klíčů. Budete muset napsat sami, nebo postupujte podle kroků ve [webových aplikacích / API pomocí jiných knihoven nebo ručně implementovat některý z podporovaných protokolů](#other).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Webové aplikace chránící prostředky a vytvořené pomocí Sady Visual Studio 2013
Pokud byla vaše aplikace vytvořena pomocí šablony webové aplikace v sadě Visual Studio 2013 a vybrali jste **organizační účty** z nabídky **Změnit ověřování,** má už potřebnou logiku pro automatické zpracování efektu přechodu pomocí klíče. Tato logika ukládá jedinečný identifikátor vaší organizace a informace o podpisovém klíči ve dvou databázových tabulkách přidružených k projektu. Připojovací řetězec pro databázi naleznete v souboru Web.config projektu.

Pokud jste do řešení přidali ověření ručně, aplikace nemusí mít potřebnou logiku přechodu klíčů. Budete muset napsat sami, nebo postupujte podle kroků ve [webových aplikacích / API pomocí jiných knihoven nebo ručně implementovat některý z podporovaných protokolů.](#other).

Následující kroky vám pomohou ověřit, že logika funguje správně ve vaší aplikaci.

1. V Visual Studiu 2013 otevřete řešení a klikněte na kartu **Průzkumník serveru** v pravém okně.
2. Rozbalte **položky Datová připojení**, **Výchozí připojení**a potom **tabulky**. Vyhledejte tabulku **IssuingAuthorityKeys,** klikněte na ni pravým tlačítkem myši a potom klikněte na **zobrazit data tabulky**.
3. V **tabulce IssuingAuthorityKeys** bude existovat alespoň jeden řádek, což odpovídá hodnotě kryptografického otisku klíče. Odstraňte všechny řádky v tabulce.
4. Klikněte pravým tlačítkem myši na tabulku **Klienti** a potom klikněte na **Zobrazit data tabulky**.
5. V tabulce **Tenants** bude existovat alespoň jeden řádek, což odpovídá jedinečnému identifikátoru klienta adresáře. Odstraňte všechny řádky v tabulce. Pokud neodstraníte řádky v tabulce **Tenants** a **IssuingAuthorityKeys** tabulka, zobrazí se chyba za běhu.
6. Sestavte a spusťte aplikaci. Po přihlášení ke svému účtu můžete aplikaci zastavit.
7. Vraťte se do **Průzkumníka serveru** a podívejte se na hodnoty v tabulce **IssuingAuthorityKeys** and **Tenants.** Všimněte si, že byly automaticky znovu naplněny příslušnými informacemi z dokumentu metadat federace.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Webová api chrání cítění a vytvořená pomocí Visual Studia 2013
Pokud jste ve Visual Studiu 2013 vytvořili aplikaci webového rozhraní API pomocí šablony webového rozhraní API a pak jste vybrali **organizační účty** z nabídky **Změnit ověřování,** máte už ve své aplikaci potřebnou logiku.

Pokud jste ověřování nakonfigurovali ručně, postupujte podle následujících pokynů a zjistěte, jak nakonfigurovat webové rozhraní API tak, aby automaticky aktualizovalo jeho klíčové informace.

Následující fragment kódu ukazuje, jak získat nejnovější klíče z dokumentu metadat federace a potom pomocí [obslužné rutiny tokenu JWT](https://msdn.microsoft.com/library/dn205065.aspx) ověřte token. Fragment kódu předpokládá, že budete používat vlastní mechanismus ukládání do mezipaměti pro uchování klíče k ověření budoucí tokeny z Azure AD, ať už je v databázi, konfigurační soubor nebo jinde.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>Webové aplikace chránící prostředky a vytvořené pomocí Sady Visual Studio 2012
Pokud vaše aplikace byla postavena v Sadě Visual Studio 2012, pravděpodobně jste ke konfiguraci aplikace použili nástroj Identity and Access Tool. Je také pravděpodobné, že používáte [ověřování registru názvů vydavatelů (VINR).](https://msdn.microsoft.com/library/dn205067.aspx) VINR je zodpovědný za udržování informací o důvěryhodných zprostředkovatelů identit (Azure AD) a klíče používané k ověření tokeny vydané jimi. VINR také usnadňuje automatickou aktualizaci klíčových informací uložených v souboru Web.config stažením nejnovějšího dokumentu federačních metadat přidruženého k vašemu adresáři, kontrolou, zda je konfigurace zastaralá s nejnovějším dokumentem, a aktualizaci aplikace tak, aby podle potřeby používala nový klíč.

Pokud jste vytvořili aplikaci pomocí některého z ukázek kódu nebo návoddokumentace poskytované společností Microsoft, logika přechodu klíčů je již zahrnuta v projektu. Všimněte si, že níže uvedený kód již v projektu existuje. Pokud vaše aplikace ještě nemá tuto logiku, postupujte podle následujících kroků a přidejte ji a ověřte, zda funguje správně.

1. V **Průzkumníku řešení**přidejte odkaz na sestavení **System.IdentityModel** pro příslušný projekt.
2. Otevřete soubor **Global.asax.cs** a přidejte následující příkazy pomocí direktiv:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Do **Global.asax.cs** souboru přidejte následující metodu:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Vyvolat **Metodu RefreshValidationSettings()** v metodě **Application_Start()** v **Global.asax.cs** jak je znázorněno:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Po provedení těchto kroků bude soubor Web.config aplikace aktualizován nejnovějšími informacemi z dokumentu metadat federace, včetně nejnovějších klíčů. K této aktualizaci dojde při každém recyklaci fondu aplikací ve službě IIS. Ve výchozím nastavení je iis nastavena na recyklaci aplikací každých 29 hodin.

Postupujte podle následujících kroků a ověřte, zda logika přechodu klíče funguje.

1. Po ověření, že vaše aplikace používá výše uvedený kód, otevřete soubor **Web.config** a přejděte do bloku ** \<issuerNameRegistry>,** konkrétně vyberte následující několik řádků:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. V ** \<nastavení add thumbprint="">** změňte hodnotu kryptografického otisku nahrazením libovolného znaku jiným znakem. Uložte soubor **Web.config**.
3. Vytvořte aplikaci a spusťte ji. Pokud můžete dokončit proces přihlášení, aplikace úspěšně aktualizuje klíč stažením požadovaných informací z dokumentu metadat federace vašeho adresáře. Pokud máte problémy s přihlášením, ujistěte se, že změny ve vaší aplikaci jsou správné, přečtete si přidání přihlášení do webové aplikace pomocí článku [Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) nebo stáhnete a zkontrolujete následující ukázku kódu: [Víceklientská cloudová aplikace pro Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>Webové aplikace chránící prostředky a vytvořené pomocí sady Visual Studio 2008 nebo 2010 a Windows Identity Foundation (WIF) v1.0 pro rozhraní .NET 3.5
Pokud jste vytvořili aplikaci na WIF v1.0, neexistuje žádný mechanismus pro automatické aktualizace konfigurace aplikace pro použití nového klíče.

* *Nejjednodušší způsob, jak* Použijte nástroje FedUtil obsažené v sadě WIF SDK, které mohou načíst nejnovější dokument metadat a aktualizovat konfiguraci.
* Aktualizujte aplikaci na rozhraní .NET 4.5, která obsahuje nejnovější verzi rozhraní WIF umístěnou v oboru názvů Systém. Potom můžete použít [validující registr názvů vystavitele (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) k provádění automatických aktualizací konfigurace aplikace.
* Proveďte ruční přechod podle pokynů na konci tohoto pokynu.

Pokyny k aktualizaci konfigurace pomocí Nástroje FedUtil:

1. Ověřte, zda máte ve vývojovém počítači sady Visual Studio 2008 nebo 2010 nainstalovanou sadu WIF v1.0 SDK. Zde si jej můžete [stáhnout,](https://www.microsoft.com/en-us/download/details.aspx?id=4451) pokud jste jej ještě nenainstalovali.
2. V sadě Visual Studio otevřete řešení a potom klikněte pravým tlačítkem myši na příslušný projekt a vyberte **aktualizovat metadata federace**. Pokud tato možnost není k dispozici, nebyla nainstalována sada FedUtil a/nebo WIF v1.0 SDK.
3. V řádku vyberte **Aktualizovat** a začněte aktualizovat metadata federace. Pokud máte přístup k prostředí serveru, kde je aplikace hostována, můžete volitelně použít [plánovač automatických aktualizací metadat](https://msdn.microsoft.com/library/ee517272.aspx)FedUtil .
4. Chcete-li dokončit proces aktualizace, klepněte na tlačítko **Dokončit.**

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>Webové aplikace / API chráníprostředky pomocí jiných knihoven nebo ručně implementují některý z podporovaných protokolů
Pokud používáte jinou knihovnu nebo ručně implementovali některý z podporovaných protokolů, budete muset zkontrolovat knihovnu nebo implementaci, abyste se ujistili, že klíč je načítán z dokumentu zjišťování OpenID Connect nebo z metadat federace Dokumentu. Jedním ze způsobů, jak to zkontrolovat, je provést vyhledávání v kódu nebo v kódu knihovny pro všechny hovory do dokumentu zjišťování OpenID nebo dokumentu metadat federace.

Pokud je klíč uložen někde nebo pevně zakódován ve vaší aplikaci, můžete ručně načíst klíč a odpovídajícím způsobem jej aktualizovat provedením ručního přechodu podle pokynů na konci tohoto pokynů. **Důrazně doporučujeme, abyste vylepšili aplikaci pro podporu automatického přechodu** pomocí některého z přístupů osnovy v tomto článku, aby se zabránilo budoucí narušení a režie, pokud Azure AD zvyšuje jeho kadence přechodu nebo má nouzové out-of-band rollover.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Jak otestovat aplikaci k určení, zda bude ovlivněna
Můžete ověřit, zda vaše aplikace podporuje automatické převrácení klíčů stažením skriptů a podle pokynů v [tomto úložišti GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Jak provést ruční přechod, pokud vaše aplikace nepodporuje automatický přechod
Pokud vaše aplikace **nepodporuje** automatické převrácení, budete muset vytvořit proces, který pravidelně monitoruje podpisové klíče Azure AD a provede ruční přechod odpovídajícím způsobem. [Toto úložiště GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) obsahuje skripty a pokyny, jak to udělat.

