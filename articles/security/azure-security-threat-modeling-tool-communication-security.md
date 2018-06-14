---
title: Komunikace zabezpečení – nástroj Microsoft Threat modelování – Azure | Microsoft Docs
description: způsoby zmírnění hrozeb, které jsou zveřejněné v nástroji pro modelování hrozeb
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 71bbe53595f2afab50d6220f335d615ada957a85
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019479"
---
# <a name="security-frame-communication-security--mitigations"></a>Zabezpečení rámce: Zabezpečení komunikace | Způsoby zmírnění rizik 
| Produktům a službám | Článek |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[Zabezpečené komunikace do centra událostí pomocí protokolu SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Zkontrolujte oprávnění k účtu služby a zkontrolujte, že vlastní služby nebo stránek ASP.NET respektují CRM na zabezpečení](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Brána pro správu dat použít při připojení k Azure Data Factory na místní systém SQL Server](#sqlserver-factory)</li></ul> |
| **Serveru identit** | <ul><li>[Zajistěte, aby veškerý provoz do serveru identit přes připojení HTTPS](#identity-https)</li></ul> |
| **Webové aplikace** | <ul><li>[Ověřte X.509 certifikáty používané k ověření připojení SSL, TLS a DTLS](#x509-ssltls)</li><li>[Nakonfigurovat certifikát SSL pro vlastní doménu, ve službě Azure App Service](#ssl-appservice)</li><li>[Vynutit veškerý provoz do služby Azure App Service přes připojení HTTPS](#appservice-https)</li><li>[Povolit zabezpečení striktní přenosu HTTP (HSTS)](#http-hsts)</li></ul> |
| **Database** | <ul><li>[Ujistěte se, SQL server šifrování a certifikát pro ověření platnosti připojení](#sqlserver-validation)</li><li>[Vynutit šifrovaný komunikaci pro SQL server](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Zajistěte, aby byla komunikace do úložiště Azure přes protokol HTTPS](#comm-storage)</li><li>[Ověřit hodnotu hash MD5 po stažení objektů blob, pokud nelze povolit protokol HTTPS](#md5-https)</li><li>[Pomocí protokolu SMB 3.0 kompatibilní klienta zajistit šifrování během přenosu dat do sdílené složky Azure](#smb-shares)</li></ul> |
| **Mobilního klienta** | <ul><li>[Implementace Připnutí certifikátu](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Povolit protokol HTTPS – zabezpečený kanál přenosu](#https-transport)</li><li>[WCF: Zabezpečení zpráv sada úroveň ochrany na hodnotu EncryptAndSign](#message-protection)</li><li>[WCF: Nejméně privilegovaným účtem použijte ke spuštění služby WCF](#least-account-wcf)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Vynutit všechny přenosy přes připojení HTTPS k webovým rozhraním API](#webapi-https)</li></ul> |
| **Azure Redis Cache** | <ul><li>[Zajistěte, aby byl k Azure Redis Cache komunikaci přes protokol SSL](#redis-ssl)</li></ul> |
| **Brána pole IoT** | <ul><li>[Zabezpečit zařízení a brána pole](#device-field)</li></ul> |
| **Brána IoT cloudu** | <ul><li>[Zabezpečit zařízení pro Cloudová brána komunikaci pomocí protokolu SSL/TLS](#device-cloud)</li></ul> |

## <a id="comm-ssltls"></a>Zabezpečené komunikace do centra událostí pomocí protokolu SSL/TLS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Event Hub | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověřování a zabezpečení modelu přehled služby Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | Zabezpečené připojení AMQP nebo HTTP do centra událostí pomocí protokolu SSL/TLS |

## <a id="priv-aspnet"></a>Zkontrolujte oprávnění k účtu služby a zkontrolujte, že vlastní služby nebo stránek ASP.NET respektují CRM na zabezpečení

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Zkontrolujte oprávnění k účtu služby a zkontrolujte, že vlastní služby nebo stránek ASP.NET respektují CRM na zabezpečení |

## <a id="sqlserver-factory"></a>Brána pro správu dat použít při připojení k Azure Data Factory na místní systém SQL Server

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Data Factory | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | Typy propojené služby - Azure a místním |
| **Odkazy**              |[Přesun dat mezi místní ve a Azure Data Factory](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [Brána pro správu dat](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Kroky** | <p>Nástroj Data Management Gateway (DMG) je vyžadováno pro připojení ke zdrojům dat, které jsou chráněné za corpnet nebo brána firewall.</p><ol><li>Zamykání počítač izoluje nástroj DMG a brání chybně fungující programy z poškození nebo sledování na zdrojovém počítači data. (Např.) musí být nainstalované nejnovější aktualizace, povolit minimální požadované porty řízené účty zřizování a auditování povolené, disku povolené šifrování atd.)</li><li>V pravidelných intervalech, nebo vždy, když heslo účtu služby DMG obnovuje se musí otočit klíč brány dat</li><li>Data tranzitů přes odkaz služby musí být šifrovaný.</li></ol> |

## <a id="identity-https"></a>Zajistěte, aby veškerý provoz do serveru identit přes připojení HTTPS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Serveru identit | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [IdentityServer3 - klíčů, podpisů a šifrování](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 – nasazení](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroky** | Ve výchozím nastavení vyžaduje IdentityServer všechna příchozí připojení do režimu přes protokol HTTPS. Je absolutně povinné, že komunikace s IdentityServer probíhá přes pouze zabezpečené přenosy. Existují určité scénáře nasazení jako snižování zátěže protokolu SSL kde můžete zmírnit tento požadavek. Naleznete na stránce nasazení serveru identit v odkazech na další informace. |

## <a id="x509-ssltls"></a>Ověřte X.509 certifikáty používané k ověření připojení SSL, TLS a DTLS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Aplikace, které používají protokol SSL, TLS a DTLS musí plně ověřit certifikáty X.509 entity, které se připojují k. To zahrnuje certifikáty pro ověření:</p><ul><li>Název domény</li><li>Data platnosti (začátku a datum vypršení platnosti)</li><li>Stav odvolání</li><li>Využití (například ověřování serveru u serverů, ověření klienta pro klienty)</li><li>Důvěřujete řetězu. Certifikáty musí být propojeny s kořenovou certifikační autoritu (CA), která je důvěryhodná platformu nebo explicitně nakonfigurovaný správcem</li><li>Délka klíče veřejný klíč certifikátu musí být > 2048 bitů</li><li>Algoritmus hash musí být SHA256 a vyšší |

## <a id="ssl-appservice"></a>Nakonfigurovat certifikát SSL pro vlastní doménu, ve službě Azure App Service

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Odkazy**              | [Povolit HTTPS pro aplikace v Azure App Service](../app-service/app-service-web-tutorial-custom-ssl.md) |
| **Kroky** | Ve výchozím nastavení, Azure již umožňuje HTTPS pro každou aplikaci s certifikát se zástupným znakem pro *. azurewebsites.net domény. Podobně jako všechny zástupné domény, je však není tak bezpečné jako použití vlastní domény s vlastní certifikát [najdete](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Se doporučuje povolit protokol SSL pro vlastní doménu, která budou mít přístup aplikace nasazené prostřednictvím|

## <a id="appservice-https"></a>Vynutit veškerý provoz do služby Azure App Service přes připojení HTTPS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Odkazy**              | [Vynutit HTTPS v Azure App Service](../app-service/app-service-web-tutorial-custom-ssl.md#enforce-https) |
| **Kroky** | <p>I když Azure již umožňuje HTTPS pro Azure aplikace služby s certifikát se zástupným znakem pro domény *. azurewebsites.net, Nevynucovat HTTPS. Návštěvníky může stále přístup k aplikaci pomocí protokolu HTTP, což může ohrozit zabezpečení aplikace, a proto HTTPS musí být explicitně vynucené. Aplikace ASP.NET MVC by měly používat [RequireHttps filtru](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) který vynutí opětovné odeslání přes protokol HTTPS nezabezpečeného požadavku HTTP.</p><p>Modul přepisování adres URL, který je součástí služby Azure App Service můžete alternativně použije k vynucení HTTPS. Modul přepisování adres URL umožňuje vývojářům umožňují definovat pravidla, která se použijí na příchozí požadavky před žádosti jsou předávány do vaší aplikace. Přepisování adres URL pravidla jsou definovány v souboru web.config uložená v kořenovém adresáři aplikace</p>|

### <a name="example"></a>Příklad:
Následující příklad obsahuje základní pravidlo přepisování adres URL, které vynutí veškerý příchozí provoz na používání protokolu HTTPS
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
Toto pravidlo funguje tak, že vrací stavový kód protokolu HTTP 301 (trvalé přesměrování) když uživatel požádá o stránku pomocí protokolu HTTP. 301 přesměruje požadavek na stejnou adresu URL jako návštěvníka požadována, ale nahradí část s protokolem HTTP požadavku HTTPS. Například HTTP://contoso.com přesunula do HTTPS://contoso.com. 

## <a id="http-hsts"></a>Povolit zabezpečení striktní přenosu HTTP (HSTS)

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Zabezpečení striktní přenosu HTTP OWASP Tahák](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **Kroky** | <p>Striktní přenos HTTP zabezpečení (HSTS) je určený parametrem webové aplikace prostřednictvím hlavičky odpovědi speciální vylepšení zabezpečení opt-in. Jakmile podporovaného prohlížeče obdrží tuto hlavičku prohlížeč tohoto zabrání veškerou komunikaci odesílání prostřednictvím protokolu HTTP k zadané doméně a místo toho odešle veškerou komunikaci přes protokol HTTPS. Rovněž zamezí klikněte na protokol HTTPS přes výzvy v prohlížečích.</p><p>K implementaci HSTS, následující hlavičku odpovědi musí být nakonfigurované pro web globálně, v kódu nebo v konfiguraci. Strict přenosu zabezpečení: maximální stáří = 300; includeSubDomains HSTS řeší hrozby následující:</p><ul><li>Uživatel záložky nebo ručně typy http://example.com a mohou podléhat man-in-the-middle útočník: HSTS automaticky přesměruje požadavky HTTP do HTTPS pro cílové domény</li><li>Webovou aplikaci, která má být čistě HTTPS nechtěně obsahuje odkazy HTTP nebo poskytuje obsah prostřednictvím protokolu HTTP: HSTS automaticky přesměruje požadavky HTTP do HTTPS pro cílové domény</li><li>Man-in-the-middle útočník pokusí zachytávat provoz z postižené uživatele s využitím neplatný certifikát a doufá, uživatel bude přijímat chybný certifikát: HSTS neumožňuje uživatelům přepsat zpráva Neplatný certifikát</li></ul>|

## <a id="sqlserver-validation"></a>Ujistěte se, SQL server šifrování a certifikát pro ověření platnosti připojení

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | SQL Azure  |
| **Atributy**              | Verze SQL - V12 |
| **Odkazy**              | [Osvědčené postupy na psaní zabezpečené připojovací řetězce pro databázi SQL.](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Kroky** | <p>Veškerá komunikace mezi SQL Database a klientské aplikace jsou šifrované pomocí Secure Sockets Layer (SSL) za všech okolností. Databáze SQL nepodporuje nezašifrované připojení. K ověření certifikátů pomocí kódu aplikace nebo nástroje, explicitně požadovat šifrované připojení a nedůvěřujete certifikáty serveru. Pokud váš kód aplikace nebo nástroje vyžádat šifrované připojení, bude stále obdrží šifrované připojení</p><p>Však nemusí ověřit certifikáty serveru a tak nebude náchylné k útokům "man uprostřed". Chcete-li ověřit, certifikáty s kódem aplikace ADO.NET, nastavte `Encrypt=True` a `TrustServerCertificate=False` v připojovací řetězec databáze. K ověření certifikátů přes SQL Server Management Studio, otevřete připojení k serveru, dialogové okno. Klikněte na možnost šifrování připojení na kartě Vlastnosti připojení</p>|

## <a id="encrypted-sqlserver"></a>Vynutit šifrovaný komunikaci pro SQL server

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | OnPrem |
| **Atributy**              | SQL verze - MsSQL2016, SQL verze - MsSQL2012, verze SQL - MsSQL2014 |
| **Odkazy**              | [Povolit šifrované připojení databázový stroj](https://msdn.microsoft.com/library/ms191192)  |
| **Kroky** | Povolení protokolu SSL šifrování zvyšuje zabezpečení dat přenášených v rámci sítí mezi instance systému SQL Server a aplikací. |

## <a id="comm-storage"></a>Zajistěte, aby byla komunikace do úložiště Azure přes protokol HTTPS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Šifrování transportní vrstvy úložiště Azure – pomocí protokolu HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Kroky** | Aby se zajistila ochrana z Azure Storage data na cestě, používejte protokol HTTPS při volání rozhraní API REST nebo přístupu k nim objektů v úložišti. Navíc sdílené přístupové podpisy, které se dají použít delegovat přístup k objektům Azure Storage, zahrnují možnost určit, že při použití sdílené přístupové podpisy, zajistíte, že bude používat kdokoliv odesílání se propojení s tokeny SAS lze použít pouze protokol HTTPS protokol správné.|

## <a id="md5-https"></a>Ověřit hodnotu hash MD5 po stažení objektů blob, pokud nelze povolit protokol HTTPS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | StorageType – objekt Blob |
| **Odkazy**              | [Přehled služby Windows Azure Blob MD5](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Kroky** | <p>Služba objektů Blob v systému Windows Azure poskytuje mechanismy k zajištění integrity dat, jak v transportní vrstvy a aplikaci. Pokud z nějakého důvodu, které je třeba používat protokol HTTP místo protokolu HTTPS a práci s objekty BLOB bloku, můžete použít MD5 kontroluje k ověření integrity objektů BLOB přenášení</p><p>To vám pomůže s ochranou z chyb sítě/transport layer, ale nemusí nutně jít s zprostředkující útoky. Pokud použijete protokol HTTPS, který poskytuje zabezpečení na úrovni přenosu, pak pomocí MD5 kontrola je redundantní a zbytečné.</p>|

## <a id="smb-shares"></a>Pomocí protokolu SMB 3.0 kompatibilní klienta zajistit šifrování během přenosu dat do sdílené složky Azure

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilního klienta | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | StorageType – soubor |
| **Odkazy**              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [podpora protokolu SMB Azure File Storage pro klienty se systémem Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Kroky** | Úložiště Azure File podporuje protokol HTTPS, když pomocí rozhraní REST API, ale je víc často používá jako sdílené složky SMB připojený k virtuálnímu počítači. SMB 2.1 nepodporuje šifrování, takže připojení jsou povoleny pouze v rámci stejné oblasti v Azure. Ale podporuje šifrování protokolu SMB 3.0 a lze použít s Windows Server 2012 R2, Windows 8, Windows 8.1 a Windows 10, což mezi oblastmi přístup a dokonce i přístup na ploše. |

## <a id="cert-pinning"></a>Implementace Připnutí certifikátu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné, Windows Phone |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Certifikát a veřejný klíč připojení](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **Kroky** | <p>Připnutí certifikátu obranu proti útokům Man-In-The-Middle typu MITM (). Připnutí je proces přidružení hostitele k jejich očekávané X509 certifikát nebo veřejný klíč. Jakmile certifikát nebo veřejný klíč známý nebo je vidět pro hostitele, certifikát nebo veřejný klíč je přidružené nebo připnutá k hostiteli. </p><p>Proto když se pokusí nežádoucí osoba provést útokům typu MITM SSL, během metody handshake SSL klíč ze serveru útočníka bude liší od klíče definovaného certifikátu a žádost se zahodí, takže si certifikát MITM Připnutí lze dosáhnout implementace je ServicePointManager – `ServerCertificateValidationCallback` delegovat.</p>|

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

## <a id="https-transport"></a>Povolit protokol HTTPS – zabezpečený kanál přenosu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | NET Framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Kroky** | Konfigurace aplikace se ujistěte, že protokol HTTPS se používá pro veškerý přístup k citlivé informace.<ul><li>**Vysvětlení:** Pokud aplikace zpracovává citlivé informace a nepoužívá šifrování na úrovni zpráv, pak by měla povoleno pouze pro komunikaci přes přenosu šifrovaný kanál.</li><li>**DOPORUČENÍ:** zkontrolujte, zda je vypnutá přenos HTTP a místo toho povolit přenos HTTPS. Například nahradit `<httpTransport/>` s `<httpsTransport/>` značky. Nespoléhejte na konfiguraci sítě (brány firewall) Chcete-li zaručit, že aplikaci lze přistupovat pouze prostřednictvím zabezpečeného kanálu. Aplikace by neměl z filozofické hlediska, závisí na síti pro jeho zabezpečení.</li></ul><p>Z praktického hlediska osoby zodpovídají za zabezpečení sítě nejsou vždy sledovat požadavky na zabezpečení aplikace vývoj.</p>|

## <a id="message-protection"></a>WCF: Zabezpečení zpráv sada úroveň ochrany na hodnotu EncryptAndSign

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Rozhraní .NET framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Kroky** | <ul><li>**Vysvětlení:** při ochrany úroveň je nastavena na "žádný" jej vypne ochranu zprávy. Důvěrnost a integrita je dosaženo s odpovídající úroveň nastavení.</li><li>**DOPORUČENÍ:**<ul><li>Když `Mode=None` – zakáže ochranu zpráv</li><li>Když `Mode=Sign` -přihlásí, ale nešifruje zprávy; by měl být použit při integritu dat je důležité</li><li>Když `Mode=EncryptAndSign` -přihlásí a šifruje zprávy</li></ul></li></ul><p>Zvažte vypnutí šifrování a pouze podepisování zprávy, když potřebujete jen ověřit integritu informace, abyste nemuseli dělat starosti utajení. To může být užitečné pro operace nebo servisní smlouvy ve které je nutné ověřit původní odesílatele, ale žádná citlivá data se přenáší. Je-li snížit úroveň ochrany, dbejte na to, zda zpráva neobsahuje žádné identifikovatelné osobní údaje (PII).</p>|

### <a name="example"></a>Příklad:
Konfigurace služby a operace se pouze přihlásit zpráva se zobrazí v následujících příkladech. Příklad kontraktu služby `ProtectionLevel.Sign`: tady je příklad použití ProtectionLevel.Sign na úrovni kontrakt služby: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Příklad:
Příklad kontrakt operaci `ProtectionLevel.Sign` (pro přesná kontrola): tady je příklad použití `ProtectionLevel.Sign` na úrovni OperationContract:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a id="least-account-wcf"></a>WCF: Nejméně privilegovaným účtem použijte ke spuštění služby WCF

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Rozhraní .NET framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Kroky** | <ul><li>**Vysvětlení:** se nespustí služby WCF v části správce nebo účtu vysokou úrovní oprávnění. v případě ohrožení zabezpečení služeb způsobí vysoký dopad.</li><li>**DOPORUČENÍ:** nejméně privilegovaným účtem použít k hostování služby WCF, protože bude omezte prostor pro útoky vaší aplikace a omezit možné škody, pokud jsou napadení. Pokud účet služby vyžaduje další oprávnění na prostředcích infrastruktury, například služby MSMQ, v protokolu událostí, čítače výkonu a systému souborů, má být poskytnut příslušná oprávnění k tyto prostředky tak, že lze úspěšně spustit službu WCF.</li></ul><p>Pokud vaše služba potřebuje pro přístup k určitým prostředkům jménem původní volající, použijte zosobnění a delegování tok identitu volajícího pro kontrolu podřízené autorizace. Ve scénáři vývoj pomocí služby účet místní sítě, což je speciální předdefinovaný účet, který má omezená oprávnění. V případě produkční vytvoření účtu služby nejméně privilegovaným vlastní doménu.</p>|

## <a id="webapi-https"></a>Vynutit všechny přenosy přes připojení HTTPS k webovým rozhraním API

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Vynucování SSL v Kontroleru webového rozhraní API](http://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Kroky** | Pokud má aplikace HTTPS a vazbu protokolu HTTP, můžete klientům dál používat pro přístup k webu HTTP. Chcete-li tomu zabránit, použijte filtr akce zajistit, že požadavky na chráněný rozhraní API jsou vždy prostřednictvím protokolu HTTPS.|

### <a name="example"></a>Příklad: 
Následující kód ukazuje filtr ověřování webového rozhraní API, který kontroluje pro protokol SSL: 
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
 
## <a id="redis-ssl"></a>Zajistěte, aby byl k Azure Redis Cache komunikaci přes protokol SSL

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Redis Cache | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Podpora Azure Redis SSL](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Kroky** | Redis serveru SSL mimo pole nepodporuje, ale v Azure Redis Cache. Pokud se připojujete k Azure Redis Cache a váš klient podporuje protokol SSL, jako je StackExchange.Redis, měli byste použít protokol SSL. Ve výchozím nastavení port bez SSL pro nové instance služby Azure Redis Cache zakázán. Ujistěte se, že zabezpečené výchozí nastavení se nezmění, dokud je závislost na podporu protokolu SSL pro klienty redis. |

Upozorňujeme, že je Redis určený přístup důvěryhodné klienty uvnitř důvěryhodného prostředí. To znamená, že obvykle není vhodné vystavit Redis instance přímo k Internetu, nebo Obecné, prostředí, kde nedůvěryhodní klienti mohou přímý přístup k portu Redis TCP nebo UNIX soketu. 

## <a id="device-field"></a>Zabezpečit zařízení a brána pole

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána pole IoT | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Pro zařízení, na základě IP může komunikační protokol obvykle zapouzdřené v kanál SSL/TLS k ochraně dat během přenosu. Pro jiné protokoly, které nepodporují SSL/TLS zjistěte, jestli existují zabezpečené verze protokolu, které poskytují zabezpečení ve vrstvě přenosu nebo zprávy. |

## <a id="device-cloud"></a>Zabezpečit zařízení pro Cloudová brána komunikaci pomocí protokolu SSL/TLS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT cloudu | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Zvolte komunikační protokol](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Kroky** | Zabezpečení protokolu HTTP nebo AMQP nebo protokoly MQTT pomocí protokolu SSL/TLS. |
