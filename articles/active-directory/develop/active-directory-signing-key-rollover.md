---
title: Podepisování výměna klíče ve službě Azure AD
description: Tento článek popisuje podpisového klíče výměny osvědčené postupy pro Azure Active Directory
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: a84cca4b0944db5cde038fb72917ebac23d0be8c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317455"
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Podepisování výměna klíče v Azure Active Directory
Tento článek popisuje, co potřebujete vědět o veřejné klíče, které se používají v Azure Active Directory (Azure AD) k podepisování tokenů zabezpečení. Je důležité si uvědomit, že tyto výměny klíčů v pravidelných intervalech a v případě nouze, může být převracet okamžitě. Všechny aplikace, které používají Azure AD by mohli prostřednictvím kódu programu zpracování procesu výměna klíče nebo vytvořit proces periodické ruční výměny. Materiály, abyste pochopili, jak funguje klíče, jak posoudit dopad výměny do vaší aplikace a jak aktualizovat aplikaci nebo vytvořit proces periodické ruční výměna pro zpracování výměny klíčů, v případě potřeby.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Přehled podpisových klíčů ve službě Azure AD
Azure AD používá šifrování veřejného klíče založený na oborových standardů k navázání vztahu důvěryhodnosti mezi samostatně a aplikace, které ho používají. V praxi, tento postup funguje následujícím způsobem: Azure AD používá podpisový klíč, který se skládá z pár veřejného a privátního klíče. Když se uživatel přihlásí aplikaci, která používá Azure AD pro ověřování, Azure AD vytvoří token zabezpečení, který obsahuje informace o uživateli. Tento token je podepsána pomocí jeho privátní klíč, než budou odeslána zpět do aplikace Azure AD. Pokud chcete ověřit, zda je token platný a původu z Azure AD, musíte aplikace ověřit podpis tokenu pomocí veřejný klíč vystavený službou Azure AD, která je součástí klienta [OpenID Connect dokumentu zjišťování](http://openid.net/specs/openid-connect-discovery-1_0.html) nebo SAML / WS-Fed [dokument federačních metadat](active-directory-federation-metadata.md).

Z bezpečnostních důvodů Azure AD podpisového klíče zobrazí souhrn v pravidelných intervalech a v případě nouze, může být převracet okamžitě. Všechny aplikace, který se integruje s Azure AD měli připravit zpracování události výměna klíče bez ohledu na to, jak často může dojít. Pokud tomu tak není, a aplikace se pokusí použít vypršela platnost klíč k ověření podpisu na token, požádat o přihlášení selže.

Vždy není k dispozici v dokumentu OpenID Connect zjišťování a dokument federačních metadat více než jeden platný klíč. Aplikace by měly být připravené k používání některé z klíče určené v dokumentu, vzhledem k tomu, že jeden klíč může být vrácena brzy, jiné může být jeho nahrazení a tak dále.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Postup vyhodnocení, pokud vaše aplikace bude mít vliv na a co dělat, o něm
Jak vaše aplikace zpracovává výměny klíčů, závisí na proměnné, jako je typ aplikace nebo jaké protokol identity a knihovna byl použit. V níže uvedených částech vyhodnocení, zda nejběžnějších typů aplikací je postiženo výměny klíčů a poskytují pokyny o tom, jak aktualizovat aplikaci pro podporu automatického výměny nebo ručně aktualizovat klíč.

* [Nativní klientské aplikace přístup k prostředkům](#nativeclient)
* [Webové aplikace / rozhraní API pro přístup k prostředkům](#webclient)
* [Webové aplikace / rozhraní API Ochrana prostředků a vyvíjené v Azure App Services](#appservices)
* [Webové aplikace / Ochrana prostředků pomocí rozhraní .NET OWIN OpenID Connect, WS-Fed nebo WindowsAzureActiveDirectoryBearerAuthentication middleware rozhraní API](#owin)
* [Webové aplikace / Ochrana prostředků pomocí rozhraní .NET Core OpenID Connect nebo JwtBearerAuthentication middleware rozhraní API](#owincore)
* [Webové aplikace / rozhraní API Ochrana prostředků pomocí modulu passport-azure-ad Node.js](#passport)
* [Webové aplikace / rozhraní API Ochrana prostředků a vytvořené pomocí sady Visual Studio 2015 nebo Visual Studio 2017](#vs2015)
* [Webové aplikace Ochrana prostředků a vytvořené pomocí sady Visual Studio 2013](#vs2013)
* [Webová rozhraní API Ochrana prostředků a vytvořené pomocí sady Visual Studio 2013](#vs2013_webapi)
* [Webové aplikace Ochrana prostředků a vytvořené pomocí sady Visual Studio 2012](#vs2012)
* [Webové aplikace Ochrana prostředků a vytvořit s Visual Studio 2010, o 2008 pomocí technologie Windows Identity Foundation](#vs2010)
* [Webové aplikace / Ochrana prostředků pomocí jiné knihovny nebo některé z podporovaných protokolů ručně implementace rozhraní API](#other)

V tomto návodu je **není** platí pro:

* Aplikace přidána z Azure AD Application Gallery (včetně vlastní) mají zvláštní pokyny s ohledem na podpisových klíčů. [Další informace.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Místní aplikacích publikovaných prostřednictvím proxy aplikace nemusíte si dělat starosti o podpisových klíčů.

### <a name="nativeclient"></a>Nativní klientské aplikace přístup k prostředkům
Aplikace, které jsou pouze přístup k prostředkům (tj Microsoft Graph, KeyVault, rozhraní API aplikace Outlook a další APIs Microsoft) obecně pouze získat token a jejich předávání podél vlastníka prostředku. Vzhledem k tomu, že se nechrání žádné prostředky, není zkontrolujte token a proto není nutné, aby Ujistěte se, že je správně podepsaný.

Nativní klientské aplikace, zda desktop či mobile, do této kategorie patří a nejsou proto vliv výměny.

### <a name="webclient"></a>Webové aplikace / rozhraní API pro přístup k prostředkům
Aplikace, které jsou pouze přístup k prostředkům (tj Microsoft Graph, KeyVault, rozhraní API aplikace Outlook a další APIs Microsoft) obecně pouze získat token a jejich předávání podél vlastníka prostředku. Vzhledem k tomu, že se nechrání žádné prostředky, není zkontrolujte token a proto není nutné, aby Ujistěte se, že je správně podepsaný.

Webové aplikace a webové rozhraní API, která používají toku jen aplikace (pověření klienta nebo certifikát klienta), do této kategorie patří a nejsou proto vliv výměny.

### <a name="appservices"></a>Webové aplikace / rozhraní API Ochrana prostředků a vyvíjené v Azure App Services
Azure App Services ověřování / autorizace (EasyAuth) funkce již pomocí potřebné logiky pro zpracování výměna klíče automaticky.

### <a name="owin"></a>Webové aplikace / Ochrana prostředků pomocí rozhraní .NET OWIN OpenID Connect, WS-Fed nebo WindowsAzureActiveDirectoryBearerAuthentication middleware rozhraní API
Pokud vaše aplikace používá rozhraní .NET OWIN OpenID Connect, WS-Fed nebo WindowsAzureActiveDirectoryBearerAuthentication middleware, už je pomocí potřebné logiky pro zpracování výměna klíče automaticky.

Můžete potvrdit, že vaše aplikace používá některý z těchto tak, že vyhledá všechny následující fragmenty kódu v souboru Startup.cs nebo Startup.Auth.cs vaší aplikace

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

### <a name="owincore"></a>Webové aplikace / Ochrana prostředků pomocí rozhraní .NET Core OpenID Connect nebo JwtBearerAuthentication middleware rozhraní API
Pokud vaše aplikace používá rozhraní .NET Core OWIN OpenID Connect nebo JwtBearerAuthentication middleware, už je pomocí potřebné logiky pro zpracování výměna klíče automaticky.

Můžete potvrdit, že vaše aplikace používá některý z těchto tak, že vyhledá všechny následující fragmenty kódu v souboru Startup.cs nebo Startup.Auth.cs vaší aplikace

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

### <a name="passport"></a>Webové aplikace / rozhraní API Ochrana prostředků pomocí modulu passport-azure-ad Node.js
Pokud vaše aplikace používá modul Node.js ve službě Active Directory passport, už je pomocí potřebné logiky pro zpracování výměna klíče automaticky.

Potvrďte, že vaše aplikace passport-ad vyhledáním následující fragment kódu v app.js vaší aplikace

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Webové aplikace / rozhraní API Ochrana prostředků a vytvořené pomocí sady Visual Studio 2015 nebo Visual Studio 2017
Pokud vaše aplikace byla vytvořena pomocí šablony webové aplikace v sadě Visual Studio 2015 nebo Visual Studio 2017 a jste vybrali **pracovní a školní účty** z **změna ověřování** nabídce už je pomocí potřebné logiky pro zpracování výměna klíče automaticky. Tato logika vložených v middlewaru OWIN OpenID Connect načítá a ukládá do mezipaměti klíče ze zjišťování dokumentu OpenID Connect a je pravidelně aktualizuje.

Pokud jste přidali ověřování pro vaše řešení ručně, nemusí mít aplikaci logiky potřeby výměny klíčů. Budete muset napsat sami, nebo postupujte podle kroků v [webové aplikace / rozhraní API pomocí jiné knihovny nebo ručně implementace některé z podporovaných protokolů](#other).

### <a name="vs2013"></a>Webové aplikace Ochrana prostředků a vytvořené pomocí sady Visual Studio 2013
Pokud aplikace byla vytvořena pomocí šablony webové aplikace v sadě Visual Studio 2013 a jste vybrali **účty organizace** z **změna ověřování** nabídce už je pomocí potřebné logiky pro zpracování výměna klíče automaticky. Tato logika ukládá jedinečný identifikátor vaší organizace a podpisový klíč ve dvou tabulkách databáze přidružený k projektu. Připojovací řetězec databáze najdete v souboru Web.config projektu.

Pokud jste přidali ověřování pro vaše řešení ručně, nemusí mít aplikaci logiky potřeby výměny klíčů. Budete muset napsat sami, nebo postupujte podle kroků v [webové aplikace / rozhraní API pomocí jiné knihovny nebo ručně implementace některé z podporovaných protokolů.](#other).

Následující kroky vám pomohou ověřte, zda je správně funguje logiku ve vaší aplikaci.

1. V sadě Visual Studio 2013, otevřete řešení a potom klikněte na **Průzkumníka serveru** karty v pravém podokně okna.
2. Rozbalte položku **připojení dat**, **objekt DefaultConnection**a potom **tabulky**. Vyhledejte **IssuingAuthorityKeys** tabulky, klikněte pravým tlačítkem ji a pak klikněte na tlačítko **zobrazit Data tabulky**.
3. V **IssuingAuthorityKeys** tabulky, budou existovat alespoň jeden řádek, který odpovídá hodnotě kryptografický otisk pro klíč. Odstraňte všechny řádky v tabulce.
4. Klikněte pravým tlačítkem myši **klienty** tabulky a potom klikněte na **zobrazit Data tabulky**.
5. V **klienty** tabulky, budou existovat alespoň jeden řádek, který odpovídá identifikátor jedinečný directory klienta. Odstraňte všechny řádky v tabulce. Pokud nemáte odstranit řádky v obou **klienty** tabulky a **IssuingAuthorityKeys** tabulky, bude dojde k chybě za běhu.
6. Sestavte a spusťte aplikaci. Poté, co jste přihlášení k účtu, můžete zastavit aplikace.
7. Vraťte se do **Průzkumníka serveru** a podívejte se na hodnoty v **IssuingAuthorityKeys** a **klienty** tabulky. Můžete si všimnout, že se mají byla automaticky jeho plnění znovu s příslušnými informacemi z dokument federačních metadat.

### <a name="vs2013"></a>Webová rozhraní API Ochrana prostředků a vytvořené pomocí sady Visual Studio 2013
Pokud jste vytvořili pro webové aplikace rozhraní API v sadě Visual Studio 2013 pomocí šablony webového rozhraní API a pak vybrali **účty organizace** z **změna ověřování** nabídky, které již mají nezbytné Logika ve vaší aplikaci.

Pokud ručně nakonfigurované ověřování, postupujte podle pokynů níže se dozvíte, jak nakonfigurovat webové rozhraní API se automaticky aktualizovat informace o jeho klíči.

Následující fragment kódu ukazuje, jak získat nejnovější klíče z dokument federačních metadat a potom pomocí [obslužná rutina tokenu JWT](https://msdn.microsoft.com/library/dn205065.aspx) k ověření tokenu. Fragment kódu předpokládá, že použijete vlastní ukládání do mezipaměti mechanismus pro zachování klíč k ověření budoucí tokeny z Azure AD, zda byl v databázi, konfigurační soubor nebo jinde.

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

### <a name="vs2012"></a>Webové aplikace Ochrana prostředků a vytvořené pomocí sady Visual Studio 2012
Pokud vaše aplikace byla vytvořena v sadě Visual Studio 2012, pravděpodobně použili identita a přístup ke konfiguraci vaší aplikace. Je pravděpodobné, že používáte [ověřování vystavitele název registru (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). VINR zodpovídá za údržbu informace o poskytovatelích důvěryhodné identity (Azure AD) a slouží k ověření, tokeny vydané podle jejich klíče. VINR také usnadňuje automaticky aktualizovat klíče uložené v souboru Web.config stáhněte nejnovější dokument metadat federace spojené s adresářem, informace o kontrole, zda je aktuální pomocí nejnovější dokumentů, konfigurace a aktualizace aplikace pro používání nového klíče podle potřeby.

Pokud jste vytvořili vaší aplikace pomocí některé z ukázky kódu nebo návod dokumentaci od společnosti Microsoft, logice výměna klíče je již zahrnut ve vašem projektu. Si všimnete, že následující kód v projektu již existuje. Pokud vaše aplikace již tuto logiku nemá, použijte následující postup je přidat a ověřit, zda pracuje správně.

1. V **Průzkumníku řešení**, přidejte odkaz na **System.IdentityModel** sestavení pro příslušný projekt.
2. Otevřete **Global.asax.cs** souboru a přidejte následující direktivy using:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Přidejte následující metodu do **Global.asax.cs** souboru:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Vyvolání **RefreshValidationSettings()** metoda v **Application_Start()** metoda v **Global.asax.cs** znázorněné:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Jakmile jste postupovali podle těchto kroků, souboru Web.config vaší aplikace bude aktualizováno o nejnovější informace z dokument federačních metadat, včetně nejnovější klíčů. Tato aktualizace se objeví pokaždé, když recykluje fond aplikací ve službě IIS; ve výchozím nastavení nastavení služba IIS recyklovat aplikace každých 29 hodin.

Postupujte podle následujících kroků a ověřte, zda je funkční logice výměny klíčů.

1. Po ověření, že vaše aplikace používá výše uvedený kód, otevřete **Web.config** souboru a přejděte do **<issuerNameRegistry>** bloku, konkrétně hledá následující několika řádků:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. V **<add thumbprint=””>** nahrazením libovolný znak jiné nastavení, změňte hodnotu kryptografický otisk. Uložit **Web.config** souboru.
3. Sestavte aplikaci a potom ho spusťte. Pokud dokončíte proces přihlášení, je vaše aplikace úspěšně aktualizace stáhnout požadované informace z dokument federačních metadat svého adresáře na klíč. Pokud máte problémy s přihlášením, zkontrolujte změny v aplikaci jsou správné načtením [přidání přihlašování k vaší webové aplikace pomocí Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) článek, nebo stahování a zkontrolujete následující ukázka kódu: [ Víceklientská Cloudová aplikace pro Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="vs2010"></a>Webové aplikace Ochrana prostředků a vytvořené pomocí sady Visual Studio 2008 nebo 2010 a Windows Identity Foundation (WIF) verze 1.0 pro rozhraní .NET 3.5
Pokud jste vytvořili aplikaci na verzi WIF verze 1.0, neexistuje žádný zadaný mechanismus automaticky aktualizovat konfiguraci vaší aplikace pomocí nového klíče.

* *Nejjednodušším způsobem, jak* pomocí nástrojů FedUtil zahrnutý v sadě SDK WIF, které můžete načíst nejnovější dokument metadat a aktualizovat konfiguraci.
* Aktualizace aplikace .NET 4.5, který obsahuje nejnovější verzi WIF nachází v oboru názvů systému. Pak můžete použít [ověřování vystavitele název registru (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) provádění automatických aktualizací na konfiguraci aplikace.
* Proveďte ruční výměna podle pokynů na konci tohoto dokumentu pokyny.

Pokyny, jak pomocí FedUtil aktualizovat konfiguraci:

1. Ověřte, zda máte verze 1.0 WIF SDK pro Visual Studio 2008 nebo 2010 nainstalována na vývojovém počítači. Můžete [stáhnout odsud](https://www.microsoft.com/en-us/download/details.aspx?id=4451) Pokud jste ještě nenainstalovali ho.
2. V sadě Visual Studio otevřete řešení a potom klikněte pravým tlačítkem na příslušné projekt a vyberte **aktualizace federačních metadat**. Pokud tato možnost není k dispozici, nebyl nainstalován FedUtil nebo verze 1.0 WIF SDK.
3. Na řádku vyberte **aktualizace** zahájíte aktualizace federačních metadat. Pokud máte přístup k prostředí serveru, který je hostitelem aplikace, můžete volitelně použít na FedUtil [scheduler automatické metadata aktualizace](https://msdn.microsoft.com/library/ee517272.aspx).
4. Klikněte na tlačítko **Dokončit** k dokončení procesu aktualizace.

### <a name="other"></a>Webové aplikace / Ochrana prostředků pomocí jiné knihovny nebo některé z podporovaných protokolů ručně implementace rozhraní API
Pokud používáte některé jiné knihovny nebo ručně implementována některé z podporovaných protokolů, budete muset zkontrolovat knihovny nebo implementaci zajistit, že klíč je načítány ze zjišťování dokumentu OpenID Connect nebo federačních metadat dokument. Jeden způsob kontroly pro tento je hledání v kódu nebo knihovny kódu pro volání na dokument zjišťování OpenID nebo dokument federačních metadat.

Pokud se klíče ukládají někde nebo pevně zakódované ve vaší aplikaci, můžete ručně načíst klíč a aktualizovat ji podle toho pomocí provádění ruční výměna podle pokynů na konci tohoto dokumentu pokyny. **Důrazně doporučujeme, můžete zvýšit vaši aplikaci, aby podporovala automatické výměny** pomocí všechny přístupy obrysu v tomto článku, aby se zabránilo režii a budoucí přerušení, pokud Azure AD zvyšuje jeho výměny cadence nebo má naléhavém Out-of-band výměny.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Postup testování vaší aplikace k určení, pokud bude mít vliv
Můžete ověřit, jestli aplikace podporuje automatickou výměnu klíče stažením skripty a podle pokynů v [toto úložiště GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Jak provést ruční výměna, pokud vaše aplikace nepodporuje automatické výměny
Pokud aplikace nemá **není** podporuje automatickou výměnu, budete muset vytvořit proces, který pravidelně monitorování Azure AD podepisovací klíče a provede ruční výměna odpovídajícím způsobem. [Toto úložiště GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) obsahuje skripty a pokyny o tom, jak to udělat.

