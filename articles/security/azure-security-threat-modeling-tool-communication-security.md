---
title: Komunikace zabezpečení – nástroj pro modelování hrozeb Microsoftu – Azure | Dokumentace Microsoftu
description: zmírnění rizik pro hrozby v nástroj pro modelování hrozeb
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: be0dd7147e3864befa90434ade86b4032cd45cc3
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53013181"
---
# <a name="security-frame-communication-security--mitigations"></a>Zabezpečení Frame: Zabezpečení komunikace | Zmírnění rizik 
| Produkt nebo službu | Článek |
| --------------- | ------- |
| **Centrum událostí Azure** | <ul><li>[Zabezpečená komunikace do centra událostí pomocí protokolu SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Zkontrolujte oprávnění k účtu služby a zkontrolujte, že vlastní služby nebo stránky ASP.NET respektovat zabezpečení CRM](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Brána správy dat používané k připojení na místní SQL Server do služby Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Serveru identit** | <ul><li>[Ujistěte se, že veškerý provoz do serveru identit přes připojení HTTPS](#identity-https)</li></ul> |
| **Webové aplikace** | <ul><li>[Ověření X.509 certifikáty sloužící k ověření připojení SSL, TLS a DTLS](#x509-ssltls)</li><li>[Konfigurace certifikátu SSL pro vlastní domény ve službě Azure App Service](#ssl-appservice)</li><li>[Vynutit veškerý provoz do služby Azure App Service přes připojení HTTPS](#appservice-https)</li><li>[Povolit zabezpečení striktní přenosu HTTP (HSTS)](#http-hsts)</li></ul> |
| **Database** | <ul><li>[Ujistěte se SQL server šifrování a certifikát ověření platnosti připojení](#sqlserver-validation)</li><li>[Platnost šifrované komunikace se serverem SQL](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Ujistěte se, že komunikace s Azure Storage prostřednictvím protokolu HTTPS](#comm-storage)</li><li>[Ověřit hodnotu hash MD5 po stažení objektů blob, pokud nelze povolit HTTPS](#md5-https)</li><li>[Použití protokolu SMB 3.0 kompatibilní klient k zajištění šifrování během přenosu dat do sdílených složek Azure](#smb-shares)</li></ul> |
| **Mobilního klienta** | <ul><li>[Implementace Připnutí certifikátu](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Povolení HTTPS – Zabezpečte přenosový kanál](#https-transport)</li><li>[WCF: Zabezpečení zpráv sada úroveň ochrany EncryptAndSign](#message-protection)</li><li>[WCF: Nejméně privilegovaný účet použijte ke spuštění služby WCF](#least-account-wcf)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Vynutit veškerý provoz přes připojení HTTPS k webovým rozhraním API](#webapi-https)</li></ul> |
| **Azure mezipaměti Redis** | <ul><li>[Ujistěte se, že komunikaci do mezipaměti Azure Redis pomocí protokolu SSL](#redis-ssl)</li></ul> |
| **Brána IoT pole** | <ul><li>[Hraniční brána komunikaci zabezpečit zařízení](#device-field)</li></ul> |
| **Brána IoT cloudu** | <ul><li>[Zabezpečení zařízení pro Cloudová brána komunikaci pomocí protokolu SSL/TLS](#device-cloud)</li></ul> |

## <a id="comm-ssltls"></a>Zabezpečená komunikace do centra událostí pomocí protokolu SSL/TLS

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Centrum událostí Azure | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování a zabezpečení modelu přehled služby Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | Zabezpečení připojení AMQP nebo HTTP do centra událostí pomocí protokolu SSL/TLS |

## <a id="priv-aspnet"></a>Zkontrolujte oprávnění k účtu služby a zkontrolujte, že vlastní služby nebo stránky ASP.NET respektovat zabezpečení CRM

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Zkontrolujte oprávnění k účtu služby a zkontrolujte, že vlastní služby nebo stránky ASP.NET respektovat zabezpečení CRM |

## <a id="sqlserver-factory"></a>Brána správy dat používané k připojení na místní SQL Server do služby Azure Data Factory

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Data Factory | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | Typy propojené služby – Azure a v místním nasazení |
| **Odkazy**              |[ Přesun dat mezi místním nasazení ve a Azure Data Factory](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [brána správy dat](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Kroky** | <p>Nástroj Data Management Gateway (DMG) je nutná pro připojení ke zdrojům dat, které jsou chráněné za bránou corpnetu a bránou firewall.</p><ol><li>Uzamčení počítače izoluje nástroj DMG a zabraňuje nefunkční programům poškození nebo monitorování na zdrojovém počítači data. (Např.) musí být nainstalované nejnovější aktualizace, povolit minimální požadované porty řízené účty zřizování a auditování povoleno, na disku povoleno šifrování atd.)</li><li>Musí v pravidelných intervalech nebo pokaždé, když se tato možnost obnoví heslo účtu služby DMG obměnit klíč brány dat</li><li>Musí být šifrovaná data tranzitů přes službu propojení</li></ol> |

## <a id="identity-https"></a>Ujistěte se, že veškerý provoz do serveru identit přes připojení HTTPS

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Serveru identit | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [IdentityServer3 - klíče, podpisů a kryptografických](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 – nasazení](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroky** | Ve výchozím nastavení vyžaduje IdentityServer všechna příchozí připojení do režimu přes protokol HTTPS. Je absolutně povinné, komunikace s IdentityServer probíhá přes zabezpečené přenosy jenom. Existují určité scénáře nasazení, jako je přesměrování zpracování SSL ve kterém můžete tento požadavek zmírnit. Najdete na stránce nasazení serveru identit v odkazy na další informace. |

## <a id="x509-ssltls"></a>Ověření X.509 certifikáty sloužící k ověření připojení SSL, TLS a DTLS

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Aplikace, které používají protokol SSL, TLS a DTLS plně ověřte certifikáty X.509 entity, které se připojují. To zahrnuje certifikáty pro ověření:</p><ul><li>Název domény</li><li>Datum platnosti (datum zahájení a ukončení platnosti)</li><li>Stav odvolání</li><li>Využití (například Server ověřování pro servery, ověření klienta pro klienty)</li><li>Důvěřujete řetězu. Certifikáty musí být propojeny s kořenové certifikační autority (CA), která je důvěryhodná pro platformu, nebo explicitně nakonfigurované správcem</li><li>Klíč o délce veřejný klíč certifikátu musí být > 2 048 bitů</li><li>Algoritmus hash musí být SHA256 a vyšší |

## <a id="ssl-appservice"></a>Konfigurace certifikátu SSL pro vlastní domény ve službě Azure App Service

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Odkazy**              | [Povolení HTTPS pro aplikaci ve službě Azure App Service](../app-service/app-service-web-tutorial-custom-ssl.md) |
| **Kroky** | Ve výchozím nastavení, Azure už povolí protokol HTTPS pro každou aplikaci s certifikát se zástupným znakem pro *. azurewebsites.net domény. Však stejně jako všechny zástupné domény není tak bezpečné jako použití vlastní domény s vlastním certifikátem [projděte si informace o](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Doporučuje povolit protokol SSL pro vlastní doménu, ke kterému nasazené aplikace se přistupuje přes|

## <a id="appservice-https"></a>Vynutit veškerý provoz do služby Azure App Service přes připojení HTTPS

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Odkazy**              | [Vynucení protokolu HTTPS ve službě Azure App Service](../app-service/app-service-web-tutorial-custom-ssl.md#enforce-https) |
| **Kroky** | <p>I když Azure již povolí protokol HTTPS pro Azure app Service s certifikát se zástupným znakem domény *. azurewebsites.net, Nevynucovat HTTPS. Návštěvníci může pořád přístup k aplikaci pomocí protokolu HTTP, což může ohrozit zabezpečení aplikace a proto HTTPS musí být explicitně vynutit. Aplikace ASP.NET MVC by měly používat [atribut RequireHttps filtr](https://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) , který vynutí opětovné odeslání přes protokol HTTPS nezabezpečeného požadavku HTTP.</p><p>Alternativně modulem přepisu adresy URL, která je zahrnutá ve službě Azure App Service slouží k vynucení protokolu HTTPS. Modul přepisování adres URL umožňuje vývojářům definovat pravidla, která se použijí pro příchozí požadavky před požadavky jsou předány do vaší aplikace. Přepisování adres URL pravidla jsou definovány v souboru web.config uložená v kořenovém adresáři aplikace</p>|

### <a name="example"></a>Příklad:
Následující příklad obsahuje základní pravidlo přepisování adres URL, která vynutí pro veškeré příchozí přenosy na používání protokolu HTTPS
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Toto pravidlo funguje tak, že vrací stavový kód HTTP 301 (trvalé přesměrování) když uživatel požádá o stránku pomocí protokolu HTTP. 301 přesměruje požadavek na stejnou adresu URL jako návštěvníka požadovaná, ale nahradí část s protokolem HTTP požadavku HTTPS. Například HTTP://contoso.com bude přesměrován na HTTPS://contoso.com. 

## <a id="http-hsts"></a>Povolit zabezpečení striktní přenosu HTTP (HSTS)

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Zabezpečení striktní přenosu HTTP OWASP Tahák](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **Kroky** | <p>Striktní přenos HTTP zabezpečení (HSTS) je vylepšení zabezpečení přihlášení, která je zadána webovou aplikaci pomocí hlavičky speciální odpovědi. Jakmile podporovaný prohlížeč obdrží toto záhlaví prohlížeče zabrání jakékoli komunikační nezabránil odesílání prostřednictvím protokolu HTTP k zadané doméně a místo toho pošle veškerou komunikaci přes protokol HTTPS. Zabrání také klikněte na protokol HTTPS pomocí pokynů v prohlížečích.</p><p>K implementaci HSTS následující hlavičky odpovědi musí být nakonfigurované pro web globálně, v kódu nebo v konfiguraci. Strict –-zabezpečení přenosu: max-age = 300; includeSubDomains HSTS řeší následující hrozby:</p><ul><li>Záložky uživatele, nebo ručně typy http://example.com a se může útočník man-in-the-middle: HSTS automaticky přesměruje požadavky HTTP na HTTPS pro cílové domény</li><li>Webovou aplikaci, která má být čistě HTTPS neúmyslně obsahuje odkazy protokolu HTTP nebo poskytuje obsah prostřednictvím protokolu HTTP: HSTS automaticky přesměruje požadavky HTTP na HTTPS pro cílové domény</li><li>Man-in-the-middle útočník pokusí zachytávat provoz z victim uživatele s využitím neplatný certifikát a doufá, uživatel bude nesprávný certifikát přijmout: HSTS neumožňuje uživateli ignorovat neplatný certifikát</li></ul>|

## <a id="sqlserver-validation"></a>Ujistěte se SQL server šifrování a certifikát ověření platnosti připojení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | SQL Azure  |
| **Atributy**              | Verze SQL - V12 |
| **Odkazy**              | [Osvědčené postupy při psaní zabezpečené připojovací řetězce pro službu SQL Database](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Kroky** | <p>Veškerá komunikace mezi SQL Database a klientské aplikace jsou šifrované pomocí vrstvy SSL (Secure Sockets) po celou dobu. SQL Database nepodporuje nezašifrované připojení. K ověření certifikátů pomocí kódu aplikace nebo nástroje, explicitně vyžadoval šifrované připojení a nepovažujete certifikáty serveru. Pokud váš kód aplikace nebo nástroje žádosti šifrované připojení, se stále mu šifrovaná připojení</p><p>Ale nemusí být ověřeny certifikáty serveru a proto může být napadeno útočníky "man in the middle" útoky. Chcete-li ověřit certifikáty s kódem aplikace ADO.NET, nastavte `Encrypt=True` a `TrustServerCertificate=False` připojovacího řetězce databáze. K ověření certifikátů přes SQL Server Management Studio, otevřete připojení k serveru dialogovému oknu. Klikněte na tlačítko šifrovat připojení na kartě Vlastnosti připojení</p>|

## <a id="encrypted-sqlserver"></a>Platnost šifrované komunikace se serverem SQL

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | OnPrem |
| **Atributy**              | SQL verze - MsSQL2016, SQL verze – MsSQL2012, verze SQL – MsSQL2014 |
| **Odkazy**              | [Povolit šifrované připojení databázový stroj](https://msdn.microsoft.com/library/ms191192)  |
| **Kroky** | Povolení protokolu SSL šifrování zvyšuje zabezpečení dat přenášených mezi sítěmi mezi instancí systému SQL Server a aplikace. |

## <a id="comm-storage"></a>Ujistěte se, že komunikace s Azure Storage prostřednictvím protokolu HTTPS

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Šifrování transportní vrstvy Azure Storage – pomocí protokolu HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Kroky** | Aby bylo zajištěno zabezpečení služby Azure Storage dat během přenosu, vždy používejte protokol HTTPS při volání rozhraní REST API nebo přístup k objektům v úložišti. Navíc sdílené přístupové podpisy, které je možné delegovat přístup k objektům služby Azure Storage, zahrnují možnost určit, že při použití sdílených přístupových podpisů, zajištění, že se někdo odesláním propojení s tokeny SAS používá lze použít pouze protokolu HTTPS správný protokol.|

## <a id="md5-https"></a>Ověřit hodnotu hash MD5 po stažení objektů blob, pokud nelze povolit HTTPS

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | StorageType – objekt Blob |
| **Odkazy**              | [Přehled MD5 Windows objektů Blob v Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Kroky** | <p>Služba Windows Azure Blob service poskytuje mechanismus k zajištění integrity dat jak v aplikaci a přenosové vrstvy. Pokud z nějakého důvodu potřebujete používat protokol HTTP místo protokolu HTTPS a práci s objekty BLOB bloku, můžete použít MD5 kontrola k ověření integrity přenášených objekty BLOB</p><p>To vám pomůže s ochranou z chyb sítě nebo přenosu vrstvy, ale ne nutně zprostředkující útoky. Pokud používáte protokol HTTPS, který poskytuje zabezpečení na úrovni přenosu, pak pomocí kontroly MD5 je redundantní a zbytečné.</p>|

## <a id="smb-shares"></a>Použití protokolu SMB 3.0 kompatibilní klient k zajištění šifrování během přenosu dat do sdílené složky Azure

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilního klienta | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | StorageType – soubor |
| **Odkazy**              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [podpora protokolu SMB Azure File Storage pro klienty Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Kroky** | Azure File Storage podporuje protokol HTTPS, při použití rozhraní REST API, ale je běžněji používaný jako sdílené složky protokolu SMB připojen k virtuálnímu počítači. Protokol SMB 2.1 nepodporuje šifrování, takže připojení jsou povolené jenom v rámci stejné oblasti v Azure. Ale protokolu SMB 3.0 podporuje šifrování a je možné s Windows serverem 2012 R2, Windows 8, Windows 8.1 a Windows 10, což mezi různými oblastmi přístup a dokonce i přístup v klientských počítačích. |

## <a id="cert-pinning"></a>Implementace Připnutí certifikátu

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné, Windows Phone |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Certifikát a veřejný klíč Připnutí](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **Kroky** | <p>Připnutí certifikátu chrání před útoky Man-In-The-Middle typu MITM (). Připnutí je proces přidružení hostitele k jejich očekávaný X509 certifikátu nebo veřejný klíč. Jakmile certifikát a veřejný klíč je známý nebo uvedené pro hostitele, certifikát nebo veřejný klíč je spojené nebo připnutá k hostiteli. </p><p>Proto když nežádoucí osoba pokusí provést SSL MITM útokům, během metody handshake SSL klíč ze serveru útočníka bude liší od klíče připnutého certifikátu a žádost se zahodí, zamezuje tak MITM certifikát Připnutí se dá dosáhnout Třída ServicePointManager vaší implementace `ServerCertificateValidationCallback` delegovat.</p>|

### <a name="example"></a>Příklad:
```csharp
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a id="https-transport"></a>Povolení HTTPS – Zabezpečte přenosový kanál

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | NET Framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Kroky** | Konfigurace aplikace by měl zajistit, aby používal protokol HTTPS pro veškerý přístup k citlivým údajům.<ul><li>**Vysvětlení:** Pokud aplikace zpracovává citlivé informace a nepoužívá šifrování na úrovni zpráv, pak by měly povoleny pouze pro komunikaci přes kanál přenosu zašifrované.</li><li>**DOPORUČENÍ:** Ujistěte se, že je zakázána přenos pomocí protokolu HTTP a místo toho povolit přenos protokolu HTTPS. Nahraďte třeba `<httpTransport/>` s `<httpsTransport/>` značky. Nespoléhejte na konfiguraci sítě (Brána firewall) k zajištění, že aplikaci lze přistupovat pouze prostřednictvím zabezpečeného kanálu. Aplikace by neměl z filozofické pohledu, závisí na síť pro jeho zabezpečení.</li></ul><p>Z praktického pohledu osoby odpovědné za zabezpečení sítě vždy nesledovaly požadavky na zabezpečení aplikace, jak se vyvíjí.</p>|

## <a id="message-protection"></a>WCF: Zabezpečení zpráv sada úroveň ochrany EncryptAndSign

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Rozhraní .NET framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Kroky** | <ul><li>**Vysvětlení:** Pokud zpráva ochrana se vypnout ochranu úroveň je nastavená na "none" ho. Důvěrnost a integrita se dosahuje prostřednictvím odpovídající úroveň nastavení.</li><li>**DOPORUČENÍ:**<ul><li>Když `Mode=None` – zakáže ochranu zprávy</li><li>Když `Mode=Sign` -příznaky ale nešifruje zprávy; má být použit při integritu dat je důležité</li><li>Když `Mode=EncryptAndSign` – značky a šifruje zprávy</li></ul></li></ul><p>Zvažte vypnutí šifrování a pouze podepisování zpráv při stačí k ověření integrity informace důvěrnost bez starostí. To může být užitečné pro operace nebo servisní smlouvy ve které je potřeba ověřit původního odesilatele, ale přenášet citlivá data. Při snížení úrovně ochrany, buďte opatrní, že zpráva neobsahuje žádné identifikovatelné osobní údaje (PII).</p>|

### <a name="example"></a>Příklad:
Konfigurace služby a operace pouze podepisování zpráv se zobrazí v následujících příkladech. Příklad kontraktu služby `ProtectionLevel.Sign`: následující je příklad použití ProtectionLevel.Sign na úrovni kontraktu služby: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Příklad:
Operace kontraktu příklad `ProtectionLevel.Sign` (pro detailní kontrolu): následující je příklad použití `ProtectionLevel.Sign` na úrovni OperationContract:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a id="least-account-wcf"></a>WCF: Nejméně privilegovaný účet použijte ke spuštění služby WCF

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Rozhraní .NET framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Kroky** | <ul><li>**Vysvětlení:** nespouštějte služeb WCF v části správce nebo vysoká oprávnění účtu. v případě ohrožení bezpečnosti služby způsobí vysoký dopad.</li><li>**DOPORUČENÍ:** použít k hostování služby WCF, protože bude omezíte prostor pro možný útok vaší aplikace a omezit možné škody, jestliže jsou napadených nejméně privilegovaný účet. Pokud účet služby vyžaduje další přístupová práva pro prostředky infrastruktury jako služby MSMQ, do protokolu událostí, čítače výkonu a systému souborů, příslušná oprávnění by měla být věnována tyto prostředky tak, že můžete úspěšně spustit službu WCF.</li></ul><p>Pokud vaše služba potřebuje přístup k určitým prostředkům jménem původního volajícího, použijte zosobnění a delegování tok identitu volajícího pro kontrolu příjem dat autorizace. Ve scénáři vývoje pomocí účtu služby místní sítě, což je zvláštní předdefinovaný účet, který má omezená oprávnění. V případě produkčního prostředí vytvořte účet služby vlastní domény nejnižší úrovní oprávnění.</p>|

## <a id="webapi-https"></a>Vynutit veškerý provoz přes připojení HTTPS k webovým rozhraním API

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Vynucování SSL v Kontroleru webového rozhraní API](http://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Kroky** | Pokud aplikace má vazbu HTTP i HTTPS, klienti můžou pořád používat HTTP přístup k webu. Chcete-li tomu zabránit, použijte filtr akce ujistit, že požadavky na chráněné rozhraní API jsou vždy prostřednictvím protokolu HTTPS.|

### <a name="example"></a>Příklad: 
Následující kód ukazuje filtr ověřování webové rozhraní API, která kontroluje pro protokol SSL: 
```csharp
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Přidejte tento filtr pro všechny akce webového rozhraní API, které vyžadují protokol SSL: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a id="redis-ssl"></a>Ujistěte se, že komunikaci do mezipaměti Azure Redis pomocí protokolu SSL

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Cache for Redis | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Podpora Azure redis Cache SSL](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Kroky** | Redis server nepodporuje SSL úprav, ale nemá mezipaměti Azure Redis. Pokud se připojujete k mezipaměti Azure pro Redis a váš klient podporuje protokol SSL, jako je StackExchange.Redis, měli byste použít protokol SSL. Ve výchozím nastavení port bez SSL je zakázaná pro nové mezipaměti Azure pro instance Redis. Ujistěte se, že zabezpečené výchozí hodnoty se nezmění, pokud neexistuje závislost na podporu protokolu SSL pro klientů redis. |

Všimněte si, že Redis je určený ke přístupná pomocí důvěryhodné klienty uvnitř důvěryhodného prostředí. To znamená, že obvykle není vhodné zpřístupnit instance Redis přímo k Internetu, nebo obecně platí, prostředí, ve kterém nedůvěryhodní klienti mohou přímý přístup k portu Redis TCP nebo UNIXOVÉMU soketu. 

## <a id="device-field"></a>Hraniční brána komunikaci zabezpečit zařízení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT pole | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Pro zařízení na základě IP adresy komunikační protokol by mohl obvykle zapouzdřit kanál SSL/TLS k ochraně dat během přenosu. Pro jiné protokoly, které nepodporují SSL/TLS prozkoumat, jestli existují bezpečné verze protokolu, které poskytují zabezpečení ve vrstvě přenosu nebo zprávy. |

## <a id="device-cloud"></a>Zabezpečení zařízení pro Cloudová brána komunikaci pomocí protokolu SSL/TLS

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT cloudu | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Vyberte komunikační protokol](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Kroky** | Zabezpečení protokolu HTTP nebo AMQP nebo protokoly MQTT pomocí protokolu SSL/TLS. |
