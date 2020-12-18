---
title: Výměna podpisových klíčů na platformě Microsoft identity
description: Tento článek popisuje osvědčené postupy pro výměnu podpisových klíčů pro Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 8/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: a8c9a15761a4b37dfcf5ba7cc4cf046390092145
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672141"
---
# <a name="signing-key-rollover-in-microsoft-identity-platform"></a>Výměna podpisových klíčů na platformě Microsoft identity
Tento článek popisuje, co potřebujete znát o veřejných klíčích, které používá platforma Microsoft Identity Platform k podepisování tokenů zabezpečení. Je důležité si uvědomit, že tyto klíče se převezmou v pravidelných intervalech a v naléhavém případě by mohlo dojít k okamžitému zavedení. Všechny aplikace, které používají platformu Microsoft Identity Platform, by měly být schopné programově zpracovat proces výměny klíčů. Pokračujte ve čtení, abyste pochopili, jak klíče fungují, jak vyhodnotit dopad přechodu na aplikaci a jak aktualizovat aplikaci nebo vytvořit pravidelný ruční proces ručního zpracování, který v případě potřeby zabere v případě potřeby klíčovou výměnu.

## <a name="overview-of-signing-keys-in-microsoft-identity-platform"></a>Přehled podpisových klíčů v platformě Microsoft Identity Platform
Platforma Microsoft Identity Platform používá kryptografii s veřejným klíčem postavená na průmyslových standardech k navázání vztahu důvěryhodnosti mezi sebou samými a aplikacemi, které ho používají V praktických případech funguje takto: platforma Microsoft Identity Platform používá podpisový klíč, který se skládá z páru veřejného a privátního klíče. Když se uživatel přihlásí k aplikaci, která používá Microsoft Identity Platform pro ověřování, Microsoft Identity Platform vytvoří token zabezpečení, který obsahuje informace o uživateli. Tento token je podepsaný platformou Microsoft identity pomocí jeho privátního klíče, než se pošle zpátky do aplikace. Pokud chcete ověřit, jestli je token platný a pochází z platformy Microsoft Identity Platform, musí aplikace ověřit signaturu tokenu pomocí veřejných klíčů vystavených platformou Microsoft identity, která je obsažená v [dokumentu zjišťování OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html) tenanta nebo v [dokumentu federačních METADAT](../azuread-dev/azure-ad-federation-metadata.md)SAML/WS.

Z bezpečnostních důvodů se podpisový klíč platformy Microsoft Identity vystavuje pravidelně a v případě nouze se dá okamžitě navrátit. Mezi těmito klíči se neplatí žádné nastavení ani zaručená doba – jakákoliv aplikace, která se integruje s Microsoft Identity platformou, by měla být připravená na zpracování události změny klíče bez ohledu na to, jak často k ní může dojít. Pokud tomu tak není a vaše aplikace se pokusí použít klíč s vypršenou platností k ověření podpisu na tokenu, žádost o přihlášení selže.  Kontrola každých 24 hodin pro aktualizace je osvědčeným postupem s omezením (jednou za každých pět minut) okamžitými aktualizacemi klíčového dokumentu, pokud byl nalezen token s neznámým identifikátorem klíče. 

V dokumentu zjišťování OpenID Connect a v dokumentu federačních metadat je vždy k dispozici více než jeden platný klíč. Vaše aplikace by se měla připravit na použití všech klíčů, které jsou uvedené v dokumentu, a to i v případě, že jeden klíč může být vyměněn brzo, další může být jeho nahrazení a tak dále.  Počet nalezených klíčů se může v průběhu času měnit na základě interní architektury platformy Microsoft identity, která podporuje nové platformy, nové cloudy nebo nové ověřovací protokoly. Ani pořadí klíčů v odpovědi JSON ani pořadí, ve kterém byly vystavené, by mělo být považováno za meaninful vaší aplikace. 

Aplikace, které podporují jenom jeden podpisový klíč, nebo ty, které vyžadují ruční aktualizace podpisových klíčů, jsou z vlastního podstaty méně zabezpečené a spolehlivé.  Měli byste je aktualizovat tak, aby používaly [standardní knihovny](reference-v2-libraries.md) , abyste měli jistotu, že vždycky používají aktuální podpisové klíče, a to mimo jiné osvědčené postupy. 

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Jak posoudit, jestli bude vaše aplikace ovlivněná a co s nimi dělat
Způsob, jakým vaše aplikace zpracovává výměna klíčů, závisí na proměnných, jako je typ aplikace nebo jaký protokol identity a knihovna se použily. Níže uvedené části vyhodnocují, jestli jsou na nejběžnějších typech aplikací ovlivněná výměna klíčů, a poskytuje pokyny k aktualizaci aplikace na podporu automatického přechodu nebo ruční aktualizace klíče.

* [Nativní klientské aplikace přistupující k prostředkům](#nativeclient)
* [Webové aplikace/rozhraní API přistupující k prostředkům](#webclient)
* [Webové aplikace/rozhraní API chrání prostředky a sestavené pomocí Azure App Services](#appservices)
* [Webové aplikace/rozhraní API chrání prostředky pomocí .NET OWIN OpenID Connect, WS-Fed nebo WindowsAzureActiveDirectoryBearerAuthentication middlewaru](#owin)
* [Webové aplikace/rozhraní API chrání prostředky pomocí middlewaru .NET Core OpenID Connect nebo JwtBearerAuthentication middleware](#owincore)
* [Webové aplikace/rozhraní API Ochrana prostředků pomocí Node.js Passport – modul Azure-AD](#passport)
* [Webové aplikace/rozhraní API chrání prostředky a vytvořené pomocí sady Visual Studio 2015 nebo novější](#vs2015)
* [Webové aplikace, které chrání prostředky a vytvářejí je pomocí Visual Studio 2013](#vs2013)
* Webová rozhraní API chránící prostředky a vytvořené pomocí Visual Studio 2013
* [Webové aplikace chránící prostředky a vytvořené pomocí sady Visual Studio 2012](#vs2012)
* [Webové aplikace chránící prostředky a vytvořené pomocí sady Visual Studio 2010, 2008 o pomocí technologie Windows Identity Foundation](#vs2010)
* [Webové aplikace/rozhraní API chrání prostředky pomocí jiných knihoven nebo ručně implementují některé podporované protokoly.](#other)

Tyto doprovodné materiály **neplatí pro** :

* Aplikace přidané z Galerie aplikací Azure AD (včetně vlastní) mají samostatné pokyny týkající se podpisových klíčů. [Další informace.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Místní aplikace publikované prostřednictvím proxy aplikací si nemusíte dělat starosti s podpisovým klíčem.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>Nativní klientské aplikace přistupující k prostředkům
Aplikace, které získávají přístup pouze k prostředkům (tj. Microsoft Graph, Trezor klíčů, rozhraní API pro Outlook a další rozhraní Microsoft API) obecně jenom získají token a předají ho vlastníkovi prostředku. Vzhledem k tomu, že nechrání žádné prostředky, nekontrolují token, a proto není nutné se ujistit, že je správně podepsán.

Nativní klientské aplikace, ať už se jedná o Desktop nebo mobilní zařízení, spadají do této kategorie, a proto se na ně neovlivní výměna.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>Webové aplikace/rozhraní API přistupující k prostředkům
Aplikace, které získávají přístup pouze k prostředkům (tj. Microsoft Graph, Trezor klíčů, rozhraní API pro Outlook a další rozhraní Microsoft API) obecně jenom získají token a předají ho vlastníkovi prostředku. Vzhledem k tomu, že nechrání žádné prostředky, nekontrolují token, a proto není nutné se ujistit, že je správně podepsán.

Webové aplikace a webová rozhraní API, které používají tok pouze pro aplikace (pověření klienta/klientský certifikát) k vyžádání tokenů do této kategorie a nejsou tak ovlivněny přejezdem.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>Webové aplikace/rozhraní API chrání prostředky a sestavené pomocí Azure App Services
Funkce ověřování/autorizace v Azure App Services (EasyAuth) již má potřebnou logiku pro automatické zpracování výměny klíčů.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>Webové aplikace/rozhraní API chrání prostředky pomocí .NET OWIN OpenID Connect, WS-Fed nebo WindowsAzureActiveDirectoryBearerAuthentication middlewaru
Pokud vaše aplikace používá middleware rozhraní .NET OWIN OpenID Connect, WS-Fed nebo WindowsAzureActiveDirectoryBearerAuthentication, již má potřebnou logiku pro automatické zpracování výměny klíčů.

Můžete potvrdit, že aplikace používá některé z následujících fragmentů kódu v souborech Startup.cs nebo Startup.Auth.cs vaší aplikace.

```csharp
app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWindowsAzureActiveDirectoryBearerAuthentication(
    new WindowsAzureActiveDirectoryBearerAuthenticationOptions
    {
        // ...
    });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>Webové aplikace/rozhraní API chrání prostředky pomocí middlewaru .NET Core OpenID Connect nebo JwtBearerAuthentication middleware
Pokud vaše aplikace používá middleware OWIN OpenID Connect nebo JwtBearerAuthentication pro .NET Core, má již potřebnou logiku pro automatické zpracování výměny klíčů.

Můžete potvrdit, že aplikace používá některé z následujících fragmentů kódu v Startup.cs nebo Startup.Auth.cs vaší aplikace.

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

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Webové aplikace/rozhraní API Ochrana prostředků pomocí Node.js Passport – modul Azure-AD
Pokud vaše aplikace používá modul Node.js Passport-AD, již má potřebnou logiku pro automatické zpracování výměny klíčů.

Pomocí následujícího fragmentu kódu ve app.js vaší aplikace můžete potvrdit, že vaše aplikace Passport – AD.

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>Webové aplikace/rozhraní API chrání prostředky a vytvořené pomocí sady Visual Studio 2015 nebo novější
Pokud byla vaše aplikace sestavena pomocí šablony webové aplikace v aplikaci Visual Studio 2015 nebo novější a Vy jste vybrali **pracovní nebo školní účty** v nabídce **změnit ověřování** , již má potřebná logika pro automatické zpracování výměny klíčů. Tato logika, vložená v middlewaru OWIN OpenID Connect, načítá a ukládá klíče do mezipaměti z dokumentu zjišťování OpenID Connect a pravidelně je aktualizuje.

Pokud jste k řešení přidali ověřování ručně, nemusí mít aplikace potřebnou logiku pro výměnu klíčů. Budete je muset napsat sami nebo můžete postupovat podle kroků ve [webových aplikacích/rozhraních API pomocí jiných knihoven nebo ručně implementovat jakýkoli z podporovaných protokolů](#other).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Webové aplikace, které chrání prostředky a vytvářejí je pomocí Visual Studio 2013
Pokud byla vaše aplikace sestavena pomocí šablony webové aplikace v Visual Studio 2013 a Vy jste vybrali **účty organizace** z nabídky **změnit ověřování** , již má potřebná logika pro automatické zpracování výměny klíčů. Tato logika ukládá jedinečný identifikátor vaší organizace a informace o podpisovém klíči ve dvou databázových tabulkách přidružených k tomuto projektu. Připojovací řetězec pro databázi můžete najít v souboru Web.config projektu.

Pokud jste k řešení přidali ověřování ručně, nemusí mít aplikace potřebnou logiku pro výměnu klíčů. Budete je muset napsat sami nebo můžete postupovat podle kroků ve [webových aplikacích/rozhraních API pomocí jiných knihoven nebo ručně implementovat jakýkoli z podporovaných protokolů.](#other)

Následující kroky vám pomůžou ověřit, že logika správně funguje ve vaší aplikaci.

1. V Visual Studio 2013 otevřete řešení a pak klikněte na kartu **Průzkumník serveru** v pravém okně.
2. Rozbalte položku **datová připojení**, **DefaultConnection** a pak **tabulky**. Vyhledejte tabulku **IssuingAuthorityKeys** , klikněte na ni pravým tlačítkem myši a pak klikněte na **Zobrazit data tabulky**.
3. V tabulce **IssuingAuthorityKeys** bude k dispozici alespoň jeden řádek, který odpovídá hodnotě kryptografického otisku klíče. Odstraňte všechny řádky v tabulce.
4. Klikněte pravým tlačítkem na tabulku **klienti** a pak klikněte na možnost **Zobrazit data tabulky**.
5. V tabulce **tenantůs** bude k dispozici alespoň jeden řádek, který odpovídá jedinečnému identifikátoru tenanta adresáře. Odstraňte všechny řádky v tabulce. Pokud neodstraníte řádky v tabulce **tenantů** i v tabulce **IssuingAuthorityKeys** , zobrazí se při spuštění chyba.
6. Sestavte a spusťte aplikaci. Po přihlášení ke svému účtu můžete aplikaci zastavit.
7. Vraťte se do **Průzkumník serveru** a podívejte se na hodnoty v tabulce **IssuingAuthorityKeys** a **tenantů** . Všimnete si, že automaticky znovu naplnily příslušné informace z dokumentu federačních metadat.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Webová rozhraní API chránící prostředky a vytvořené pomocí Visual Studio 2013
Pokud jste vytvořili aplikaci webového rozhraní API v Visual Studio 2013 pomocí šablony webového rozhraní API a pak jste v nabídce **změnit ověřování** vybrali možnost **účty organizace** , v aplikaci už máte potřebnou logiku.

Pokud jste ručně nakonfigurovali ověřování, postupujte podle pokynů níže, abyste se dozvěděli, jak nakonfigurovat webové rozhraní API tak, aby automaticky aktualizovalo své klíčové informace.

Následující fragment kódu ukazuje, jak získat nejnovější klíče z dokumentu federačních metadat a pak pomocí [obslužné rutiny tokenu JWT](/previous-versions/dotnet/framework/security/json-web-token-handler) ověřit token. Fragment kódu předpokládá, že budete používat vlastní mechanismus ukládání do mezipaměti pro uchování klíče k ověřování budoucích tokenů z platformy Microsoft Identity Platform, ať už se jedná o databázi, konfigurační soubor nebo jiné místo.

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

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>Webové aplikace chránící prostředky a vytvořené pomocí sady Visual Studio 2012
Pokud byla vaše aplikace sestavena v aplikaci Visual Studio 2012, pravděpodobně jste pro konfiguraci aplikace použili nástroj identita a přístup. Je také možné, že používáte [ověřovací registr názvů vystavitele (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry). VINR zodpovídá za údržbu informací o důvěryhodných zprostředkovatelích identity (Microsoft Identity Platform) a klíčů používaných k ověřování tokenů vydaných jimi. VINR také usnadňuje automatické aktualizace informací o klíčích uložených v souboru Web.config stažením nejnovějšího dokumentu federačních metadat přidružených k vašemu adresáři, zkontrolováním, zda je konfigurace neaktuální s nejnovějším dokumentem, a aktualizujte aplikaci tak, aby používala nový klíč podle potřeby.

Pokud jste aplikaci vytvořili pomocí některého z ukázek kódu nebo pokynů v dokumentaci od Microsoftu, je logika přecházení mezi klíči již obsažena v projektu. Všimněte si, že níže uvedený kód již v projektu existuje. Pokud vaše aplikace ještě tuto logiku nemá, postupujte podle následujících kroků a ověřte, zda správně funguje.

1. V **Průzkumník řešení** přidejte odkaz na sestavení **System. IdentityModel** pro příslušný projekt.
2. Otevřete soubor **Global.asax.cs** a přidejte následující direktivy using:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Do souboru **Global.asax.cs** přidejte následující metodu:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Vyvolejte metodu **RefreshValidationSettings ()** v metodě **Application_Start ()** v **Global.asax.cs** , jak je znázorněno níže:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Po provedení těchto kroků se Web.config vaší aplikace aktualizuje o nejnovější informace z dokumentu federačních metadat, včetně nejnovějších klíčů. K této aktualizaci dojde pokaždé, když se fond aplikací recykluje ve službě IIS. ve výchozím nastavení je služba IIS nastavená na recyklaci aplikací každých 29 hodin.

Použijte následující postup, chcete-li ověřit, zda je logika výměny klíčů funkční.

1. Po ověření, že aplikace používá výše uvedený kód, otevřete soubor **Web.config** a přejděte do **\<issuerNameRegistry>** bloku, konkrétně se podívejte na následující pár řádků:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. V **\<add thumbprint="">** nastavení změňte hodnotu kryptografického otisku tak, že nahradíte libovolný znak jiným. Uložte soubor **Web.config**.
3. Sestavte aplikaci a potom ji spusťte. Pokud můžete dokončit proces přihlášení, aplikace úspěšně aktualizuje klíč stažením požadovaných informací z dokumentu federačních metadat vašeho adresáře. Pokud máte problémy s přihlášením, zajistěte, aby změny v aplikaci byly správné, a přečtěte si téma [přidání Sign-On do webové aplikace pomocí platformy Microsoft Identity Platform](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) nebo stažení a kontrola následujícího příkladu kódu: [víceklientské cloudová aplikace pro Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>Webové aplikace chránící prostředky a vytvořené pomocí sady Visual Studio 2008 nebo 2010 a technologie Windows Identity Foundation (WIF) v 1.0 pro .NET 3,5
Pokud jste vytvořili aplikaci v WIF v 1.0, není k dispozici žádný mechanismus pro automatickou aktualizaci konfigurace vaší aplikace, aby používal nový klíč.

* *Nejjednodušší způsob* Použijte nástroje soubor FedUtil obsažené v sadě WIF SDK, které mohou načíst nejnovější dokument metadat a aktualizovat konfiguraci.
* Aktualizujte svou aplikaci na .NET 4,5, která zahrnuje nejnovější verzi WIF, která se nachází v oboru názvů System. Pak můžete pomocí [ověřování názvu vystavitele (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry) provádět automatické aktualizace konfigurace aplikace.
* Proveďte ruční výměnu podle pokynů na konci tohoto dokumentu s pokyny.

Pokyny k aktualizaci konfigurace pomocí nástroje soubor FedUtil:

1. Ověřte, že je na vašem vývojovém počítači nainstalována sada WIF v 1.0 pro sadu Visual Studio 2008 nebo 2010. Pokud jste ho ještě nenainstalovali, můžete [si ho stáhnout tady](https://www.microsoft.com/en-us/download/details.aspx?id=4451) .
2. V aplikaci Visual Studio otevřete řešení a klikněte pravým tlačítkem myši na příslušný projekt a vyberte možnost **aktualizovat federační metadata**. Pokud tato možnost není k dispozici, soubor FedUtil a/nebo sada SDK WIF v 1.0 nebyla nainstalována.
3. V příkazovém řádku vyberte **aktualizovat** a začněte aktualizovat federační metadata. Máte-li přístup k prostředí serveru, kde je aplikace hostována, můžete volitelně použít [Plánovač aktualizací automatických metadat](/previous-versions/windows-identity-foundation/ee517272(v=msdn.10))soubor FedUtil.
4. Kliknutím na **Dokončit** dokončete proces aktualizace.

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>Webové aplikace/rozhraní API chrání prostředky pomocí jiných knihoven nebo ručně implementují některé podporované protokoly.
Pokud používáte jinou knihovnu nebo jste ručně implementovali některé z podporovaných protokolů, budete muset zkontrolovat knihovnu nebo implementaci, abyste se ujistili, že se klíč načítá buď z dokumentu zjišťování OpenID Connect, nebo z dokumentu federačních metadat. Jedním ze způsobů, jak tuto kontrolu provést, je hledání v kódu nebo kódu knihovny pro jakékoli volání buď do dokumentu OpenID Discovery, nebo do dokumentu federačních metadat.

Pokud se klíč ukládá do vaší aplikace někam nebo pevně zakódované, můžete ho ručně načíst a podle pokynů provést ruční přechod podle pokynů na konci tohoto dokumentu s pokyny. **Důrazně doporučujeme, abyste aplikaci vylepšili tak, aby podporovala automatické přecházení** pomocí některé z osnovy přístupů v tomto článku, aby nedocházelo k budoucímu výpadku a režii v případě, že se v rámci platformy Microsoft Identity zvyšuje tempo nebo má nouzové vzdálené přepnutí.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Jak otestovat aplikaci, abyste zjistili, jestli bude ovlivněná
Můžete ověřit, jestli vaše aplikace podporuje automatickou výměnu klíčů, a to stažením skriptů a podle pokynů v [tomto úložišti GitHubu.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Jak provést ruční přecházení, pokud vaše aplikace nepodporuje automatickou výměnu
Pokud vaše **aplikace nepodporuje automatickou** výměnu, budete muset vytvořit proces, který pravidelně monitoruje podpisové klíče platformy Microsoft Identity Platform a odpovídajícím způsobem provede ruční přesměrování. [Toto úložiště GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) obsahuje skripty a pokyny k tomu, jak to provést.