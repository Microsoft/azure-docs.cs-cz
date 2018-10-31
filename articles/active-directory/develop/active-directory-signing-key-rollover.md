---
title: Výměna podpisových klíčů ve službě Azure AD
description: Tento článek popisuje osvědčené postupy podepisování výměny klíčů pro Azure Active Directory
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
ms.date: 10/20/2018
ms.author: celested
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: eaaeaf1b37c0d732d8d0009ad5a66f2118674b66
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240450"
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Výměna podpisových klíčů ve službě Azure Active Directory
Tento článek popisuje, co potřebujete vědět o veřejných klíčů, které se používají ve službě Azure Active Directory (Azure AD) k podepisování tokenů zabezpečení. Je důležité si uvědomit, že tyto výměny klíčů a v pravidelných intervalech a ve stavu nouze, může být převracet okamžitě. Všechny aplikace, které používají službu Azure AD by možné programově zpracovávat procesu výměny klíčů nebo vytvořit proces periodické ruční výměna. Pokračujte ve čtení pochopit, jak fungují klíče, jak posoudit dopad efekt přechodu do vaší aplikace a jak aktualizovat vaše aplikace nebo vytvoření procesu periodické ruční výměna zpracování výměny klíčů, v případě potřeby.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Přehled podpisových klíčů ve službě Azure AD
Azure AD používá založená na standardech kryptografie využívající veřejný klíč k navázání vztahu důvěryhodnosti mezi samostatně a aplikace, které ji používají. V praxi, tento postup funguje následujícím způsobem: Azure AD používá podpisového klíče, které obsahuje pár veřejného a privátního klíče. Když se uživatel přihlásí k aplikaci, která používá Azure AD pro ověřování Azure AD vytvoří token zabezpečení, který obsahuje informace o uživateli. Tento token je podepsaná pomocí jeho privátní klíč, před odesláním zpět do aplikace Azure AD. Pokud chcete ověřit, že je token platný a pocházející ze ze služby Azure AD, musí aplikace ověřit podpis tokenu pomocí veřejný klíč vystavený službou Azure AD, která je součástí vašeho tenanta [dokument zjišťování OpenID Connect](http://openid.net/specs/openid-connect-discovery-1_0.html) nebo SAML / WS-Fed [dokument metadat federace](azure-ad-federation-metadata.md).

Z bezpečnostních důvodů Azure AD podpisový klíč zobrazí v pravidelných intervalech a v případě nouze, může provádět jednotlivě okamžitě. Všechny aplikace, která se integruje s Azure AD by měli být připraveni zpracování události bez ohledu na to, jak často může dojít k výměně klíče. Pokud tomu tak není a aplikace se pokusí pomocí vypršela platnost klíče ověřování podpisu tokenu, žádost o přihlášení selže.

V dokumentu zjišťování OpenID Connect a dokumentu federačních metadat existuje více než jeden platný klíč. Vaše aplikace by měla být připravený k používání některé z klíče specifikované v dokumentu, protože jeden z nich může být vrácena brzy, jiné mohou být jeho nahrazení a tak dále.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Jak můžete vyhodnotit, jestli vaše aplikace bude mít vliv a co mají dělat
Způsob, jakým aplikace zpracovává výměny klíčů, závisí na proměnné, jako je například typ aplikace nebo jaké identity protokolu a knihovny se použil. Následující části vyhodnoťte, jestli se nejčastěji používané typy aplikací je postiženo výměny klíčů a pokyny o tom, jak aktualizovat aplikaci pro podporu automatického přechodu nebo ručně aktualizovat klíč.

* [Nativní klientské aplikace přístup k prostředkům](#nativeclient)
* [Webová aplikace / rozhraní API pro přístup k prostředkům](#webclient)
* [Webová aplikace / rozhraní API Ochrana prostředků a vytvořené pomocí služby Azure App Services](#appservices)
* [Webová aplikace / Ochrana prostředků pomocí .NET OWIN OpenID Connect, WS-Fed nebo WindowsAzureActiveDirectoryBearerAuthentication middleware rozhraní API](#owin)
* [Webové aplikace nebo API Ochrana prostředků pomocí .NET Core OpenID Connect nebo JwtBearerAuthentication middlewaru](#owincore)
* [Webová aplikace / Ochrana prostředků pomocí Node.js passport-azure-ad modulu rozhraní API](#passport)
* [Webová aplikace / rozhraní API Ochrana prostředků a vytvořené pomocí sady Visual Studio 2015 nebo Visual Studio 2017](#vs2015)
* [Webové aplikace Ochrana prostředků a vytvořené pomocí sady Visual Studio 2013](#vs2013)
* [Webové rozhraní API v ochraně prostředků a vytvořené pomocí sady Visual Studio 2013](#vs2013_webapi)
* [Webové aplikace Ochrana prostředků a vytvořené pomocí sady Visual Studio 2012](#vs2012)
* [Webové aplikace Ochrana prostředků a vytvořené sadou Visual Studio 2010, o 2008 pomocí technologie Windows Identity Foundation](#vs2010)
* [Webová aplikace / Ochrana prostředků pomocí jiné knihovny nebo některý z podporovaných protokolů ručně implementaci rozhraní API](#other)

Tyto doprovodné materiály jsou **není** platí pro:

* Aplikace přidat z Galerie aplikací Azure AD (včetně vlastních) mají zvláštní pokyny s ohledem na podpisových klíčů. [Další informace.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Místní aplikace publikované prostřednictvím proxy aplikací není nutné starat o podpisových klíčů.

### <a name="nativeclient"></a>Nativní klientské aplikace přístup k prostředkům
Aplikace, které mají přístup jenom k prostředkům (např.) Microsoft Graphu, trezor klíčů, rozhraní API aplikace Outlook a další Microsoft APIs) obecně pouze získat token a předejte ho spolu vlastníka prostředku. Vzhledem k tomu, že se nechrání žádné prostředky, nezkoumání token a proto není nutné zajistit, že je správně podepsaná.

Nativní klientské aplikace, desktopové nebo mobilní zařízení, do této kategorie patří a nejsou proto vliv výměny.

### <a name="webclient"></a>Webová aplikace / rozhraní API pro přístup k prostředkům
Aplikace, které mají přístup jenom k prostředkům (např.) Microsoft Graphu, trezor klíčů, rozhraní API aplikace Outlook a další Microsoft APIs) obecně pouze získat token a předejte ho spolu vlastníka prostředku. Vzhledem k tomu, že se nechrání žádné prostředky, nezkoumání token a proto není nutné zajistit, že je správně podepsaná.

Webových aplikací a rozhraní API, která používá tok jen pro aplikace (přihlašovací údaje pro klienta / klientského certifikátu), do této kategorie patří a proto nejsou ovlivněny výměny.

### <a name="appservices"></a>Webová aplikace / rozhraní API Ochrana prostředků a vytvořené pomocí služby Azure App Services
Služba Azure App Services ověřování / autorizace (EasyAuth) funkce již obsahuje logiku potřebnou k výměně klíče umožňují automaticky zpracovat.

### <a name="owin"></a>Webová aplikace / Ochrana prostředků pomocí .NET OWIN OpenID Connect, WS-Fed nebo WindowsAzureActiveDirectoryBearerAuthentication middleware rozhraní API
Pokud vaše aplikace používá .NET OWIN OpenID Connect, WS-Fed nebo WindowsAzureActiveDirectoryBearerAuthentication middleware, již obsahuje logiku potřebnou k výměně klíče umožňují automaticky zpracovat.

Můžete potvrdit, že vaše aplikace používá některé z těchto tím, že hledají všechny následující fragmenty kódu v souboru Startup.cs nebo Startup.Auth.cs vaší aplikace

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

### <a name="owincore"></a>Webové aplikace nebo API Ochrana prostředků pomocí .NET Core OpenID Connect nebo JwtBearerAuthentication middlewaru
Pokud vaše aplikace používá .NET Core OWIN OpenID Connect nebo JwtBearerAuthentication middleware, již obsahuje logiku potřebnou k výměně klíče umožňují automaticky zpracovat.

Můžete potvrdit, že vaše aplikace používá některé z těchto tím, že hledají všechny následující fragmenty kódu v souboru Startup.cs nebo Startup.Auth.cs vaší aplikace

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

### <a name="passport"></a>Webová aplikace / Ochrana prostředků pomocí Node.js passport-azure-ad modulu rozhraní API
Pokud vaše aplikace používá modul Node.js passport-ad, již obsahuje logiku potřebnou k výměně klíče umožňují automaticky zpracovat.

Můžete potvrdit, že vaše aplikace passport-ad tak, že následující fragment kódu do vaší aplikace app.js

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Webová aplikace / rozhraní API Ochrana prostředků a vytvořené pomocí sady Visual Studio 2015 nebo Visual Studio 2017
Pokud vaše aplikace byla sestavena pomocí šablony webové aplikace v sadě Visual Studio 2015 nebo Visual Studio 2017 a jste vybrali **pracovní a školní účty** z **změna ověřování** nabídky, už má logiku potřebnou k výměně klíče umožňují automaticky zpracovat. Tato logika součástí middleware OWIN OpenID Connect načítá a ukládá do mezipaměti klíče z dokument zjišťování OpenID Connect a je pravidelně aktualizuje.

Pokud ručně přidáte ověřování do vašeho řešení, nemusí mít vaše aplikace logiky nezbytné výměny klíčů. Budete muset napsat sami, nebo postupujte podle kroků v [webových aplikací / rozhraní API pomocí jiné knihovny nebo ručně implementaci některý z podporovaných protokolů](#other).

### <a name="vs2013"></a>Webové aplikace Ochrana prostředků a vytvořené pomocí sady Visual Studio 2013
Pokud vaše aplikace byla sestavena pomocí šablony webové aplikace v sadě Visual Studio 2013 a vyberete **účty organizace** z **změna ověřování** nabídky, již obsahuje nezbytnou logiku k výměně klíče umožňují automaticky zpracovat. Tuto logiku ukládá ve dvou tabulkách databáze spojený s projektem jedinečný identifikátor vaší organizace a podpisový klíč. Připojovací řetězec pro databázi najdete v souboru Web.config v projektu.

Pokud ručně přidáte ověřování do vašeho řešení, nemusí mít vaše aplikace logiky nezbytné výměny klíčů. Budete muset napsat sami, nebo postupujte podle kroků v [webových aplikací / rozhraní API pomocí jiné knihovny nebo ručně implementaci některý z podporovaných protokolů.](#other).

Následující postup vám pomůže ověřit, že je ve vaší aplikaci správně funguje logiku.

1. V sadě Visual Studio 2013, otevřete řešení a potom klikněte na **Průzkumníka serveru** karty na pravé okno.
2. Rozbalte **datová připojení**, **objekt DefaultConnection**a potom **tabulky**. Vyhledejte **IssuingAuthorityKeys** tabulky, pravým tlačítkem myši a potom klikněte na tlačítko **zobrazit Data tabulky**.
3. V **IssuingAuthorityKeys** tabulky, bude existovat alespoň jeden řádek, který odpovídá hodnotu kryptografického otisku klíče. Odstraňte všechny řádky v tabulce.
4. Klikněte pravým tlačítkem myši **Tenantů** tabulku a pak klikněte na tlačítko **zobrazit Data tabulky**.
5. V **Tenantů** tabulky, bude existovat alespoň jeden řádek, který odpovídá identifikátor tenanta jedinečný adresář. Odstraňte všechny řádky v tabulce. Pokud neprovedete odstranění řádků v obou **Tenantů** tabulky a **IssuingAuthorityKeys** tabulky, se zobrazí chyba za běhu.
6. Sestavte a spusťte aplikaci. Až se přihlásíte ke svému účtu, můžete aplikaci zastavit.
7. Zpět **Průzkumníka serveru** a podívejte se na hodnoty v **IssuingAuthorityKeys** a **Tenantů** tabulky. Můžete si všimnout, že se mají byl automaticky provést jeho plnění znovu odpovídajícími informacemi z dokumentu federačních metadat.

### <a name="vs2013"></a>Webové rozhraní API v ochraně prostředků a vytvořené pomocí sady Visual Studio 2013
Pokud jste vytvořili webovou aplikaci s rozhraním API v sadě Visual Studio 2013 pomocí šablony webové rozhraní API a pak vybrat **účty organizace** z **změna ověřování** nabídky, které již mají nezbytné logika v aplikaci.

Pokud jste ručně nakonfigurovali ověřování, postupujte podle níže uvedených pokynů a další informace o konfiguraci vašeho webového rozhraní API se automaticky aktualizovat jeho informace o klíči.

Následující fragment kódu ukazuje, jak získat nejnovější klíče z dokumentu federačních metadat a pak použít [obslužná rutina tokenů JWT](https://msdn.microsoft.com/library/dn205065.aspx) token ověří. Fragment kódu předpokládá, že použijete vlastní mechanizmus ukládání do mezipaměti pro trvalé klíč pro ověření budoucí tokenů z Azure AD, ať to v databázi, konfigurační soubor nebo jinde.

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
Pokud vaše aplikace byla vytvořena v sadě Visual Studio 2012, pravděpodobně použijete nástroj Identity and Access Tool ke konfiguraci vaší aplikace. Je pravděpodobné, že používáte [ověřování Issuer Name Registry (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). Rozšíření VINR je odpovědná za správu informací o důvěryhodné zprostředkovatele identity (Azure AD) a klíče používané k ověření tokeny vystavené službou je. Rozšíření VINR také umožňuje snadno automaticky aktualizovat informace o klíči uložené v souboru Web.config, stáhněte si nejnovější dokumentu federačních metadat přidružený k adresáři, probíhá kontrola, zda je aktuální pomocí nejnovější dokumentů, konfigurace a aktualizace aplikace na používání nového klíče podle potřeby.

Pokud jste vytvořili aplikaci pomocí některé z ukázek kódu nebo názorná dokumentace poskytovaných microsoftem, logiku výměny klíčů již součástí projektu. Můžete si všimnout, že kód uvedený níže v projektu již existuje. Pokud vaše aplikace již nemá tuto logiku podle následujících pokynů přidejte a ověřte, že funguje správně.

1. V **Průzkumníka řešení**, přidejte odkaz na **System.IdentityModel** sestavení pro projekt.
2. Otevřít **Global.asax.cs** soubor a přidejte následující direktivy using:
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
4. Vyvolat **RefreshValidationSettings()** metodu **Application_Start()** metoda ve **Global.asax.cs** jak je znázorněno:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Jakmile jste postupovali podle těchto kroků, Web.config vaší aplikace se aktualizují s nejnovějšími informacemi z dokumentu federačních metadat, včetně nejnovějších klíčů. Tato aktualizace dojde pokaždé, když se recykluje fond aplikací ve službě IIS; ve výchozím nastavení služba IIS recyklovat aplikace každých 29 hodin.

Postupujte podle kroků níže. Tím ověříte, že funguje logiky výměny klíčů.

1. Až si ověříte, že vaše aplikace používá výše uvedený kód, otevřete **Web.config** soubor a přejděte **<issuerNameRegistry>** bloku, konkrétně analyzuje vracení pro následující po zadání několika řádků:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. V **<add thumbprint="">** změňte hodnotu kryptografického otisku nahrazením libovolný znak jiný. Uložit **Web.config** souboru.
3. Sestavení aplikace a pak ho spusťte. Pokud dokončíte proces přihlašování, vaše aplikace úspěšně aktualizuje klíč stažením požadované informace z vašeho adresáře dokumentu federačních metadat. Pokud máte potíže s přihlášením, zkontrolujte změny v aplikaci jsou správné načtením [přidání přihlašování do vaší webové aplikace pomocí Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) článku, nebo stahování a zkontrolujete následující ukázka kódu: [ Víceklientské cloudové aplikace pro službu Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="vs2010"></a>Technologie Windows Identity Foundation (WIF) verze 1.0 pro rozhraní .NET 3.5 a webových aplikací chrání prostředky a vytvořené pomocí sady Visual Studio 2008 nebo 2010
Pokud jste sestavili aplikaci na technologie WIF v1.0, neexistuje žádný zadaný mechanismus automaticky aktualizovat konfigurace vaší aplikace, aby používala nový klíč.

* *Nejjednodušší způsob* použití nástrojů řádku FedUtil součástí sady SDK technologie WIF, která můžete načíst nejnovější dokument metadat a aktualizujte konfiguraci.
* Aktualizace aplikace na rozhraní .NET 4.5, která zahrnuje nejnovější verzi technologie WIF nachází v oboru názvů System. Pak můžete použít [ověřování Issuer Name Registry (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) provádět automatické aktualizace konfigurace vaší aplikace.
* Proveďte ruční výměna podle pokynů na konci tohoto dokumentu pokyny.

Pokyny k použití řádku FedUtil aktualizovat konfiguraci:

1. Ověřte, že máte v1.0 technologie WIF sady SDK pro Visual Studio 2008 nebo 2010 nainstalována na vývojovém počítači. Je možné [stáhnout odsud](https://www.microsoft.com/en-us/download/details.aspx?id=4451) Pokud jste to ještě nenainstalovali.
2. V sadě Visual Studio, otevřete řešení a pak klikněte pravým tlačítkem na příslušný projekt a vyberte **aktualizujte federační metadata**. Pokud tato možnost není k dispozici, nebyl nainstalován řádku FedUtil a/nebo technologie WIF v1.0 SDK.
3. V příkazovém řádku vyberte **aktualizace** zahájíte aktualizace federačních metadat. Pokud máte přístup k prostředí serveru, který je hostitelem aplikace, můžete volitelně použít na řádku FedUtil [Plánovač automatického metadata aktualizací](https://msdn.microsoft.com/library/ee517272.aspx).
4. Klikněte na tlačítko **Dokončit** k dokončení procesu aktualizace.

### <a name="other"></a>Webová aplikace / Ochrana prostředků pomocí jiné knihovny nebo některý z podporovaných protokolů ručně implementaci rozhraní API
Pokud používáte nějaké jiné knihovny nebo ručně implementované některý z podporovaných protokolů, budete muset zkontrolovat, knihovny nebo implementace Ujistěte se, že klíč je načtena z dokument zjišťování OpenID Connect nebo federačních metadat dokument. Jeden způsob, jak zkontrolovat, je provést vyhledávání v kódu nebo knihovny kódu pro volání na dokument zjišťování OpenID nebo dokumentu federačních metadat.

Pokud tento klíč ukládají někde nebo pevně zakódované v aplikaci, můžete ručně načíst klíč a aktualizujte je odpovídajícím způsobem pomocí provádí ruční výměna podle pokynů na konci tohoto dokumentu pokyny. **Důrazně doporučujeme, vylepšení vaší aplikace pro podporu automatického přechodu** jedním z přístupů obrysu v tomto článku vyhnout budoucí přerušení a režijní náklady, pokud Azure AD, zvýší jeho výměny podpisových nebo má nouze Změna Out-of-band.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Testování vaší aplikace a určí, pokud bude mít vliv
Můžete ověřit, jestli aplikace podporuje automatickou výměnu klíče stažením skripty a postupujte podle pokynů v [úložiště GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Jak provést ruční výměna, pokud aplikace nepodporuje automatického přechodu
Pokud vaše aplikace dělá **není** podpora automatického přechodu, budete muset vytvořit proces, který pravidelně podepisování monitorování služby Azure AD klíče a provede ruční výměna odpovídajícím způsobem. [Úložiště GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) obsahuje skripty a pokyny, jak to udělat.

