---
title: Zabezpečení komunikace pro nástroj Microsoft Threat Modeling Tool
titleSuffix: Azure
description: zmírnění hrozeb vystavených v nástroji pro modelování hrozeb
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 39ee0fa2dc973cd6c20756cae2024af79d1375dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294151"
---
# <a name="security-frame-communication-security--mitigations"></a>Bezpečnostní rámec: Bezpečnost komunikace | Skutečnosti snižující závažnost rizika 
| Produkt/služba | Článek |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[Zabezpečená komunikace do centra událostí pomocí protokolu SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Zkontrolujte oprávnění účtu služby a zkontrolujte, zda vlastní služby nebo ASP.NET stránky respektují zabezpečení aplikace CRM](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Použití brány pro správu dat při připojování místního SQL Serveru k Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Server identit** | <ul><li>[Ujistěte se, že veškerý provoz na serveru Identity Server je přes připojení HTTPS](#identity-https)</li></ul> |
| **Webová aplikace** | <ul><li>[Ověření certifikátů X.509 používaných k ověření připojení SSL, TLS a DTLS](#x509-ssltls)</li><li>[Konfigurace certifikátu SSL pro vlastní doménu ve službě Azure App Service](#ssl-appservice)</li><li>[Vynucení veškerého provozu na azure app service přes připojení HTTPS](#appservice-https)</li><li>[Povolit http přísné zabezpečení přenosu (HSTS)](#http-hsts)</li></ul> |
| **Databáze** | <ul><li>[Zajištění šifrování připojení k serveru SQL server a ověření certifikátu](#sqlserver-validation)</li><li>[Vynutit šifrovanou komunikaci se serverem SQL](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Ujistěte se, že komunikace s Azure Storage je přes HTTPS](#comm-storage)</li><li>[Ověření hash MD5 po stažení objektu blob, pokud nelze povolit protokol HTTPS](#md5-https)</li><li>[Použití klienta kompatibilního s rozhraním SMB 3.0 k zajištění šifrování dat na cestě do sdílených složek Azure](#smb-shares)</li></ul> |
| **Mobilní klient** | <ul><li>[Implementace připnutí certifikátu](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Povolit protokol HTTPS – kanál zabezpečeného přenosu](#https-transport)</li><li>[WCF: Nastavte úroveň ochrany zabezpečení zprávy na ŠifrovatAndSign](#message-protection)</li><li>[WCF: Ke spuštění služby WCF použijte nejméně privilegovaný účet.](#least-account-wcf)</li></ul> |
| **Web API** | <ul><li>[Vynucení veškerý provoz webových api přes připojení HTTPS](#webapi-https)</li></ul> |
| **Azure Cache for Redis** | <ul><li>[Ujistěte se, že komunikace s Azure Cache pro Redis je přes SSL](#redis-ssl)</li></ul> |
| **Brána ioT pole** | <ul><li>[Komunikace zabezpečeného zařízení k bráně v terénu](#device-field)</li></ul> |
| **Cloudová brána IoT** | <ul><li>[Komunikace zabezpečeného zařízení ke cloudové bráně pomocí ssl/tls](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Zabezpečená komunikace do centra událostí pomocí protokolu SSL/TLS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Event Hub | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Přehled modelu ověřování a zabezpečení centra událostí](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroky** | Zabezpečit připojení AMQP nebo HTTP k centru událostí pomocí protokolu SSL/TLS |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Zkontrolujte oprávnění účtu služby a zkontrolujte, zda vlastní služby nebo ASP.NET stránky respektují zabezpečení aplikace CRM

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Zkontrolujte oprávnění účtu služby a zkontrolujte, zda vlastní služby nebo ASP.NET stránky respektují zabezpečení aplikace CRM |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Použití brány pro správu dat při připojování místního SQL Serveru k Azure Data Factory

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Data Factory | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Typy propojených služeb – Azure a místní |
| **Odkazy**              |[Přesouvání dat mezi místním a Azure Data Factory](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [brána pro správu dat](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Kroky** | <p>Nástroj Brána pro správu dat (DMG) je vyžadován pro připojení ke zdrojům dat, které jsou chráněny za korpusem nebo bránou firewall.</p><ol><li>Uzamčení stroje izoluje nástroj DMG a zabraňuje poruchám programů v poškození nebo špehování na počítači se zdrojem dat. (Např. musí být nainstalovány nejnovější aktualizace, povolit minimální požadované porty, řízené zřizování účtů, auditování povoleno, šifrování disku povoleno atd.)</li><li>Klíč brány dat musí být otočen v častých intervalech nebo při každém obnovení hesla účtu služby DMG</li><li>Přenosy dat prostřednictvím služby Link Service musí být šifrovány.</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Ujistěte se, že veškerý provoz na serveru Identity Server je přes připojení HTTPS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Server identit | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [IdentityServer3 – klíče, podpisy a kryptografie](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 – nasazení](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroky** | Ve výchozím nastavení IdentityServer vyžaduje, aby všechna příchozí připojení přijít přes HTTPS. Je naprosto povinné, aby komunikace s IdentityServer se provádí pouze přes zabezpečené přenosy. Existují určité scénáře nasazení, jako je snižování zátěže SSL, kde tento požadavek může být uvolněn. Další informace naleznete na stránce nasazení serveru Identity Server v odkazech. |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>Ověření certifikátů X.509 používaných k ověření připojení SSL, TLS a DTLS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Aplikace, které používají SSL, TLS nebo DTLS musí plně ověřit certifikáty X.509 entit, ke kterým se připojují. To zahrnuje ověření certifikátů pro:</p><ul><li>Název domény</li><li>Data platnosti (data začátku i vypršení platnosti)</li><li>Stav odvolání</li><li>Použití (například ověřování serveru pro servery, ověřování klienta pro klienty)</li><li>Řetězec důvěry. Certifikáty se musí zřetězit s kořenovým certifikačním úřadem (CA), kterému platforma důvěřuje nebo je explicitně nakonfigurovánsprávcem.</li><li>Délka klíče veřejného klíče certifikátu musí být >2048 bitů</li><li>Algoritmus hash musí být SHA256 a vyšší |

## <a name="configure-ssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>Konfigurace certifikátu SSL pro vlastní doménu ve službě Azure App Service

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Odkazy**              | [Povolení protokolu HTTPS pro aplikaci ve službě Azure App Service](../../app-service/configure-ssl-bindings.md) |
| **Kroky** | Ve výchozím nastavení Azure již povoluje protokol HTTPS pro každou aplikaci s certifikátem se zástupnými kódy pro doménu *.azurewebsites.net. Stejně jako všechny domény se zástupnými symboly však není tak bezpečná jako použití vlastní domény s vlastním [certifikátem.](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/) Doporučujeme povolit ssl pro vlastní doménu, ke které bude nasazená aplikace přístupná prostřednictvím|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Vynucení veškerého provozu na azure app service přes připojení HTTPS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Odkazy**              | [Vynucení protokolu HTTPS ve službě Azure App Service](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Kroky** | <p>I když Azure už povolí https pro služby aplikací Azure s certifikátem se zástupnými symboly pro doménu *.azurewebsites.net, nevynucuje protokol HTTPS. Návštěvníci mohou stále přistupovat k aplikaci pomocí protokolu HTTP, což může ohrozit zabezpečení aplikace, a proto musí být vynuceno https explicitně. ASP.NET aplikace MVC by měly používat [filtr RequireHttps,](https://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) který vynutí opětovné odeslání nezabezpečeného požadavku HTTP prostřednictvím protokolu HTTPS.</p><p>Případně modul přepisování adres URL, který je součástí služby Azure App Service, lze použít k vynucení protokolu HTTPS. Modul přepisování adres URL umožňuje vývojářům definovat pravidla, která se použijí na příchozí požadavky před předáním požadavků vaší aplikaci. Pravidla přepisu adresy URL jsou definována v souboru web.config uloženém v kořenovém adresáři aplikace</p>|

### <a name="example"></a>Příklad
Následující příklad obsahuje základní pravidlo přepisu adresy URL, které vynutí použití protokolu HTTPS veškerý příchozí provoz
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
Toto pravidlo funguje tak, že vrátí stavový kód HTTP 301 (trvalé přesměrování), když uživatel požaduje stránku pomocí protokolu HTTP. 301 přesměruje požadavek na stejnou adresu URL, jak návštěvník požadoval, ale nahradí část HTTP požadavku https. Například `HTTP://contoso.com` by být přesměrovány na `HTTPS://contoso.com`. 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>Povolit http přísné zabezpečení přenosu (HSTS)

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [OWASP HTTP Strict Transport Security Cheat Sheet](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **Kroky** | <p>HTTP Strict Transport Security (HSTS) je opt-in vylepšení zabezpečení, které je určeno webovou aplikací pomocí hlavičky zvláštní odpovědi. Jakmile podporovaný prohlížeč obdrží tuto hlavičku, tento prohlížeč zabrání odesílání jakékoli komunikace přes protokol HTTP do zadané domény a místo toho odešle veškerou komunikaci přes protokol HTTPS. Zabraňuje také tomu, aby protokol HTTPS proklikává výzvy v prohlížečích.</p><p>Chcete-li implementovat HSTS, následující hlavička odpovědi musí být nakonfigurována pro webové stránky globálně, buď v kódu nebo v konfiguraci. Strict-Transport-Security: max-age=300; includeSubDomains HSTS řeší následující hrozby:</p><ul><li>Uživatelské záložky nebo `https://example.com` ručně zadá a je předmětem útočníka typu man-in-the-middle: HSTS automaticky přesměruje požadavky HTTP na HTTPS pro cílovou doménu</li><li>Webová aplikace, která má být čistě HTTPS neúmyslně obsahuje odkazy HTTP nebo slouží obsah přes HTTP: HSTS automaticky přesměruje http požadavky na HTTPS pro cílovou doménu</li><li>Útočník typu man-in-the-middle se pokusí zachytit provoz od uživatele oběti pomocí neplatného certifikátu a doufá, že uživatel přijme chybný certifikát: HSTS neumožňuje uživateli přepsat neplatnou zprávu certifikátu</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Zajištění šifrování připojení k serveru SQL server a ověření certifikátu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | SQL Azure  |
| **Atributy**              | Verze SQL - V12 |
| **Odkazy**              | [Doporučené postupy pro zápis zabezpečených připojovacích řetězců pro databázi SQL](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Kroky** | <p>Veškerá komunikace mezi databází SQL a klientskou aplikací je vždy šifrována pomocí ssl (Secure Sockets Layer). SQL Database nepodporuje nešifrovaná připojení. Chcete-li ověřit certifikáty pomocí kódu aplikace nebo nástrojů, explicitně požádejte o šifrované připojení a nedůvěřujte certifikátům serveru. Pokud kód aplikace nebo nástroje nevyžadují šifrované připojení, budou stále přijímat šifrovaná připojení</p><p>Však nemusí ověřit certifikáty serveru, a proto budou náchylné k útokům "muž uprostřed". Chcete-li ověřit certifikáty s `Encrypt=True` `TrustServerCertificate=False` ADO.NET kódem aplikace, nastavte a v připojovacím řetězci databáze. Chcete-li ověřit certifikáty prostřednictvím sady SQL Server Management Studio, otevřete dialogové okno Připojit k serveru. Na kartě Vlastnosti připojení klikněte na Šifrovat připojení.</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Vynutit šifrovanou komunikaci se serverem SQL

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | OnPrem |
| **Atributy**              | VERZE SQL - MSSQL2016, VERZE SQL - MSSQL2012, VERZE SQL - MsSQL2014 |
| **Odkazy**              | [Povolení šifrovaných připojení k databázovému stroji](https://msdn.microsoft.com/library/ms191192)  |
| **Kroky** | Povolení šifrování SSL zvyšuje zabezpečení dat přenášených mezi sítěmi mezi instancemi serveru SQL Server a aplikacemi. |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Ujistěte se, že komunikace s Azure Storage je přes HTTPS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Šifrování na úrovni úložiště Azure – použití protokolu HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Kroky** | Chcete-li zajistit zabezpečení dat Azure Storage při přenosu, vždy použijte protokol HTTPS při volání REST API nebo přístupu k objektům v úložišti. Sdílené přístupové podpisy, které lze použít k delegování přístupu k objektům Úložiště Azure, také obsahují možnost určit, že při použití sdílených přístupových podpisů lze použít pouze protokol HTTPS, což zajistí, že kdokoli odesílající odkazy s tokeny SAS bude používat správný protokol.|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>Ověření hash MD5 po stažení objektu blob, pokud nelze povolit protokol HTTPS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | StorageType – objekt blob |
| **Odkazy**              | [Přehled objektu blob Windows Azure MD5](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Kroky** | <p>Služba objektů blob Windows Azure poskytuje mechanismy k zajištění integrity dat na vrstvách aplikace i přenosu. Pokud z nějakého důvodu potřebujete použít protokol HTTP místo protokolu HTTPS a pracujete s objekty BLOB bloku, můžete pomocí kontroly MD5 ověřit integritu přenesených objektů BLOB</p><p>To pomůže s ochranou před chybami sítě/transportní vrstvy, ale ne nutně s zprostředkujícími útoky. Pokud můžete použít protokol HTTPS, který poskytuje zabezpečení na úrovni přenosu, je použití kontroly MD5 nadbytečné a zbytečné.</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>Použití klienta kompatibilního s rozhraním SMB 3.0 k zajištění šifrování dat na cestě do sdílených složek Azure

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilní klient | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | StorageType - soubor |
| **Odkazy**              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [podpora SMB úložiště souborů Azure pro klienty Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Kroky** | Azure File Storage podporuje protokol HTTPS při použití rozhraní REST API, ale běžně se používá jako sdílená složka SMB připojená k virtuálnímu počítači. SMB 2.1 nepodporuje šifrování, takže připojení jsou povolena jenom ve stejné oblasti v Azure. SMB 3.0 však podporuje šifrování a lze jej použít se systémy Windows Server 2012 R2, Windows 8, Windows 8.1 a Windows 10, což umožňuje přístup napříč oblastmi a dokonce i přístup na ploše. |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>Implementace připnutí certifikátu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecný, Windows Phone |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Připnutí certifikátu a veřejného klíče](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **Kroky** | <p>Připnutí certifikátu brání útoky Man-In-The-Middle (MITM). Připnutí je proces přilnavosti hostitele k očekávanému certifikátu X509 nebo veřejnému klíči. Jakmile je certifikát nebo veřejný klíč známý nebo viděný pro hostitele, certifikát nebo veřejný klíč je přidružený nebo "připnutý" k hostiteli. </p><p>Pokud se tedy protivník pokusí provést útok SSL MITM, během handshake ssl klíč ze serveru útočníka se bude lišit od klíče připnutého certifikátu a požadavek bude zahozen, `ServerCertificateValidationCallback` čímž se zabrání připnutí certifikátu MITM pomocí implementace delegáta ServicePointManager.</p>|

### <a name="example"></a>Příklad
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

## <a name="enable-https---secure-transport-channel"></a><a id="https-transport"></a>Povolit protokol HTTPS – kanál zabezpečeného přenosu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | NET Framework 3 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Opevnitkrálovství](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Kroky** | Konfigurace aplikace by měla zajistit, že protokol HTTPS se používá pro veškerý přístup k citlivým informacím.<ul><li>**VYSVĚTLENÍ:** Pokud aplikace zpracovává citlivé informace a nepoužívá šifrování na úrovni zprávy, pak by mělo být povoleno pouze komunikovat přes šifrovaný přenosový kanál.</li><li>**DOPORUČENÍ:** Ujistěte se, že přenos HTTP je zakázán a povolte přenos HTTPS místo. Například `<httpTransport/>` nahraďte `<httpsTransport/>` značku. Nespoléhejte se na konfiguraci sítě (brána firewall), která zaručuje, že aplikace bude přístupná pouze prostředně prostředně zabezpečeného kanálu. Z filozofického hlediska by aplikace neměla záviset na síti pro její bezpečnost.</li></ul><p>Z praktického hlediska osoby odpovědné za zabezpečení sítě ne vždy sledují požadavky na zabezpečení aplikace při jejich vývoji.</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: Nastavte úroveň ochrany zabezpečení zprávy na ŠifrovatAndSign

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Rozhraní .NET Framework 3 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Kroky** | <ul><li>**VYSVĚTLENÍ:** Pokud je úroveň ochrany nastavena na "none", zakáže ochranu zpráv. Důvěrnosti a integrity je dosaženo s odpovídající úrovní nastavení.</li><li>**Doporučení:**<ul><li>kdy `Mode=None` - Zakáže ochranu zpráv</li><li>when `Mode=Sign` - Podepisuje, ale nešifruje zprávu; by měly být použity, pokud je důležitá integrita údajů</li><li>kdy `Mode=EncryptAndSign` - Podepisuje a šifruje zprávu</li></ul></li></ul><p>Zvažte vypnutí šifrování a podepsání zprávy pouze v případě, že potřebujete ověřit integritu informací bez obav o důvěrnost. To může být užitečné pro operace nebo servisní smlouvy, ve kterých je třeba ověřit původníodesílatel, ale žádná citlivá data jsou přenášeny. Při snižování úrovně ochrany dávejte pozor, aby zpráva neobsahovala žádné osobní údaje.</p>|

### <a name="example"></a>Příklad
Konfigurace služby a operace pouze podepsat zprávu se zobrazí v následujících příkladech. Příklad servisní `ProtectionLevel.Sign`smlouvy : Následující je příklad použití protectionlevel.sign na úrovni servisní smlouvy: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Příklad
Příklad operace `ProtectionLevel.Sign` kontrakt (pro podrobné řízení): Následující je `ProtectionLevel.Sign` příklad použití na úrovni OperationContract:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: Ke spuštění služby WCF použijte nejméně privilegovaný účet.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Rozhraní .NET Framework 3 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Kroky** | <ul><li>**VYSVĚTLENÍ:** Nespouštějte služby WCF pod účtem správce nebo vysokými oprávněními. v případě ohrožení služeb to bude mít velký dopad.</li><li>**DOPORUČENÍ:** Použijte nejméně privilegovaný účet k hostování služby WCF, protože sníží prostor pro útok vaší aplikace a sníží potenciální poškození, pokud jste napadeni. Pokud účet služby vyžaduje další přístupová práva k prostředkům infrastruktury, jako je například služba MSMQ, protokol událostí, čítače výkonu a systém souborů, měla by být těmto prostředkům udělena příslušná oprávnění, aby mohla být služba WCF úspěšně spuštěna.</li></ul><p>Pokud vaše služba potřebuje přístup k určité prostředky jménem původního volajícího, použijte zosobnění a delegování toku volajícího identity pro kontrolu autorizace příjem dat. Ve scénáři vývoje použijte účet místní síťové služby, což je speciální předdefinovaný účet, který má snížená oprávnění. V produkčním scénáři vytvořte účet služby vlastní domény s nejnižšími oprávněními.</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Vynucení veškerý provoz webových api přes připojení HTTPS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC5, MVC6 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Vynucení ssl v řadiči webového rozhraní API](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Kroky** | Pokud aplikace má vazby PROTOKOLU HTTPS i HTTP, klienti mohou stále používat protokol HTTP pro přístup k lokalitě. Chcete-li tomu zabránit, použijte filtr akce, abyste zajistili, že požadavky na chráněná api budou vždy přes protokol HTTPS.|

### <a name="example"></a>Příklad 
Následující kód zobrazuje filtr ověřování webového rozhraní API, který kontroluje protokol SSL: 
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
Přidejte tento filtr do všech akcí webového rozhraní API, které vyžadují ssl: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-ssl"></a><a id="redis-ssl"></a>Ujistěte se, že komunikace s Azure Cache pro Redis je přes SSL

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Cache for Redis | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Podpora azure redis ssl](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Kroky** | Redis server nepodporuje SSL po vybalení z krabice, ale Azure Cache pro Redis dělá. Pokud se připojujete k Azure Cache pro Redis a váš klient podporuje SSL, jako stackExchange.Redis, pak byste měli použít SSL. Ve výchozím nastavení je port bez SSL zakázán pro nové instance Azure Cache for Redis. Ujistěte se, že se nezmění zabezpečené výchozí hodnoty, pokud neexistuje závislost na podpoře SSL pro klienty redis. |

Vezměte prosím na vědomí, že redis je navržen tak, aby byl přístupný důvěryhodným klientům v důvěryhodných prostředích. To znamená, že obvykle není vhodné vystavit instanci Redis přímo na internet nebo obecně do prostředí, kde nedůvěryhodní klienti mohou přímo přistupovat k portu Redis TCP nebo unixovému soketu. 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>Komunikace zabezpečeného zařízení k bráně v terénu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána ioT pole | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | U zařízení založených na protokolu IP může být komunikační protokol obvykle zapouzdřen v kanálu SSL/TLS za účelem ochrany dat při přenosu. Pro jiné protokoly, které nepodporují SSL/TLS zjistit, zda existují zabezpečené verze protokolu, které poskytují zabezpečení na přenos nebo vrstvy zpráv. |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Komunikace zabezpečeného zařízení ke cloudové bráně pomocí ssl/tls

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Cloudová brána IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Vyberte si komunikační protokol](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Kroky** | Zabezpečte protokoly HTTP/AMQP nebo MQTT pomocí protokolu SSL/TLS. |
