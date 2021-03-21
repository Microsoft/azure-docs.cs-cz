---
title: Zabezpečení komunikace pro Microsoft Threat Modeling Tool
titleSuffix: Azure
description: Přečtěte si o zmírnění rizik pro komunikaci se hrozbami zabezpečení komunikace vystavenými ve Threat Modeling Tool. Podívejte se na informace o zmírnění rizik a zobrazte příklady kódu.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: d9a4eabf37101622ac69ae05f3bec232fb8d2fe6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517525"
---
# <a name="security-frame-communication-security--mitigations"></a>Rámec zabezpečení: zabezpečení komunikace | Hrozeb 
| Produkt/služba | Článek |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[Zabezpečená komunikace do centra událostí pomocí protokolu SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Ověřte oprávnění účtu služby a ověřte, že vlastní služby nebo stránky ASP.NET respektují zabezpečení CRM.](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Používat bránu pro správu dat při připojování místních SQL Server k Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Server identit** | <ul><li>[Zajistěte, aby veškerý provoz serveru identity byl přes připojení HTTPS.](#identity-https)</li></ul> |
| **Webová aplikace** | <ul><li>[Ověření připojení SSL, TLS a DTLS pro ověřování pomocí certifikátů X. 509](#x509-ssltls)</li><li>[Konfigurace certifikátu TLS/SSL pro vlastní doménu v Azure App Service](#ssl-appservice)</li><li>[Vynutit veškerý provoz na Azure App Service přes připojení HTTPS](#appservice-https)</li><li>[Povolit zabezpečení protokolu HTTP Strict Transport (HSTS)](#http-hsts)</li></ul> |
| **Databáze** | <ul><li>[Zajistěte šifrování připojení a ověřování certifikátů SQL serveru.](#sqlserver-validation)</li><li>[Vynutit šifrovanou komunikaci s SQL serverem](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Ujistěte se, že komunikace s Azure Storage je přes protokol HTTPS.](#comm-storage)</li><li>[Pokud není možné povolit protokol HTTPS, ověřte hash MD5 po stažení objektu BLOB.](#md5-https)</li><li>[Použití kompatibilního klienta s protokolem SMB 3,0 k zajištění šifrování dat v přenosu do sdílených složek Azure](#smb-shares)</li></ul> |
| **Mobilní klient** | <ul><li>[Implementace připnutí certifikátu](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Povolit transportní kanál zabezpečeného HTTPS](#https-transport)</li><li>[WCF: Nastavte úroveň ochrany zabezpečení zprávy na EncryptAndSign](#message-protection)</li><li>[WCF: pro spuštění služby WCF použijte účet s nejnižšími oprávněními](#least-account-wcf)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Vynutit veškerý provoz do webových rozhraní API přes připojení HTTPS](#webapi-https)</li></ul> |
| **Azure Cache for Redis** | <ul><li>[Ujistěte se, že komunikace do Azure cache pro Redis je přes protokol TLS.](#redis-ssl)</li></ul> |
| **Brána pole IoT** | <ul><li>[Zabezpečit komunikaci zařízení s bránou pole](#device-field)</li></ul> |
| **Cloudová brána IoT** | <ul><li>[Zabezpečte komunikaci zařízení s cloudovou bránou pomocí protokolu SSL/TLS.](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Zabezpečená komunikace do centra událostí pomocí protokolu SSL/TLS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Event Hub | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Přehled ověřování a modelu zabezpečení Event Hubs](../../event-hubs/authenticate-shared-access-signature.md) |
| **Kroky** | Zabezpečte připojení AMQP nebo HTTP k centru událostí pomocí protokolu SSL/TLS. |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Ověřte oprávnění účtu služby a ověřte, že vlastní služby nebo stránky ASP.NET respektují zabezpečení CRM.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Dynamics CRM | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Ověřte oprávnění účtu služby a ověřte, že vlastní služby nebo stránky ASP.NET respektují zabezpečení CRM. |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Používat bránu pro správu dat při připojování místních SQL Server k Azure Data Factory

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Data Factory | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Typy propojených služeb – Azure a místní |
| **Reference**              |[Přesun dat mezi místními a Azure Data Factory](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-gateway)a [bránou pro správu dat](../../data-factory/v1/data-factory-data-management-gateway.md) |
| **Kroky** | <p>Pro připojení ke zdrojům dat, které jsou chráněné za Corpnet nebo bránou firewall, se vyžaduje nástroj Správa dat Gateway (DMG).</p><ol><li>Uzamčení počítače izoluje nástroj DMG a zabraňuje nefunkčním programům v nebezpečném nebo neurčitém monitorování na zdrojích dat. Například. je nutné nainstalovat nejnovější aktualizace, povolit minimální požadované porty, zřizování řízených účtů, povolené auditování, povolené šifrování disku atd.)</li><li>Klíč brány dat musí být otočený v častých intervalech nebo vždy, když se heslo účtu služby DMG obnoví.</li><li>Přenos dat prostřednictvím služby propojení musí být zašifrovaný.</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Zajistěte, aby veškerý provoz serveru identity byl přes připojení HTTPS.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Server identit | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [IdentityServer3 – klíče, signatury a kryptografie](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3-Deployment](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroky** | Služba IdentityServer standardně vyžaduje, aby všechna příchozí připojení mohla být v rámci protokolu HTTPS. Je nezbytně nutné, aby komunikace se IdentityServer prováděla pouze přes zabezpečené přenosy. Existují některé scénáře nasazení, jako je přesměrování zpracování TLS, kde je možné tento požadavek zmírnit. Další informace najdete na stránce věnované nasazení serveru identit v odkazech. |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>Ověření připojení SSL, TLS a DTLS pro ověřování pomocí certifikátů X. 509

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | <p>Aplikace, které používají protokol SSL, TLS nebo DTLS, musí plně ověřit certifikáty X. 509 entit, ke kterým se připojují. To zahrnuje ověření certifikátů pro:</p><ul><li>Název domény</li><li>Data platnosti (datum zahájení i vypršení platnosti)</li><li>Stav odvolání</li><li>Využití (například ověřování serveru pro servery, ověřování klientů pro klienty)</li><li>Řetěz důvěryhodnosti Certifikáty musí být zřetězené na kořenovou certifikační autoritu (CA), která je pro platformu důvěryhodná nebo explicitně nakonfigurovaná správcem.</li><li>Délka klíče veřejného klíče certifikátu musí být >2048 bitů.</li><li>Algoritmus hash musí být SHA256 a vyšší. |

## <a name="configure-tlsssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>Konfigurace certifikátu TLS/SSL pro vlastní doménu v Azure App Service

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Reference**              | [Povolení HTTPS pro aplikaci v Azure App Service](../../app-service/configure-ssl-bindings.md) |
| **Kroky** | Ve výchozím nastavení Azure už pro každou aplikaci s certifikátem se zástupným znakem pro doménu *. azurewebsites.net povoluje protokol HTTPS. Stejně jako u všech domén se zástupnými znaky ale není tak bezpečné jako použití vlastní domény s vlastním [certifikátem](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Doporučuje se povolit TLS pro vlastní doménu, ke které se nasazená aplikace přistupovala prostřednictvím|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Vynutit veškerý provoz na Azure App Service přes připojení HTTPS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Reference**              | [Vynutilit HTTPS na Azure App Service](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Kroky** | <p>I když Azure už umožňuje protokol HTTPS pro služby Azure App Services s certifikátem se zástupným znakem pro doménu *. azurewebsites.net, neuplatňuje protokol HTTPS. Návštěvníci můžou k aplikaci přistupovat i přes protokol HTTP, což může ohrozit zabezpečení aplikace, a proto je nutné explicitně vyhovět HTTPS. Aplikace ASP.NET MVC by měly použít [Filtr RequireHttps](/dotnet/api/system.web.mvc.requirehttpsattribute) , který vynutí opětovné odeslání nezabezpečeného požadavku HTTP prostřednictvím protokolu HTTPS.</p><p>Alternativně je možné k vykonání protokolu HTTPS použít modul pro přepsání adresy URL, který je součástí Azure App Service. Modul pro přepis adres URL umožňuje vývojářům definovat pravidla, která se aplikují na příchozí požadavky před předáním požadavků do vaší aplikace. Pravidla pro přepis adres URL jsou definovaná v web.configm souboru uloženém v kořenovém adresáři aplikace.</p>|

### <a name="example"></a>Příklad
Následující příklad obsahuje pravidlo pro přepsání základní adresy URL, které vynucuje veškerý příchozí provoz na používání protokolu HTTPS.
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
Toto pravidlo funguje tak, že vrátí stavový kód HTTP 301 (trvalé přesměrování), když uživatel požádá o stránku pomocí protokolu HTTP. Rozhraní 301 přesměruje požadavek na stejnou adresu URL, jakou požadoval návštěvník, ale nahradí část požadavku HTTP pomocí protokolu HTTPS. Například by byl `HTTP://contoso.com` přesměrován na `HTTPS://contoso.com` . 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>Povolit zabezpečení protokolu HTTP Strict Transport (HSTS)

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [OWASP zabezpečený přenos tahák protokolu HTTP](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) |
| **Kroky** | <p>HTTP Strict Transport Security (HSTS) je rozšíření zabezpečení, které je určeno webovou aplikací prostřednictvím použití speciální hlavičky odpovědi. Jakmile podporovaný prohlížeč obdrží tuto hlavičku, v prohlížeči se zabrání odeslání jakékoli komunikace přes protokol HTTP do zadané domény a místo toho se pošle veškerá komunikace přes protokol HTTPS. Také zabrání protokolu HTTPS kliknout na dotazy v prohlížečích.</p><p>K implementaci HSTS je nutné nakonfigurovat následující hlavičku odpovědi na webu globálně, a to buď v kódu, nebo v konfiguraci. Striktní přenos – zabezpečení: max – stáří = 300; includeSubDomains HSTS řeší následující hrozby:</p><ul><li>Uživatelské záložky nebo ruční typy `https://example.com` a podléhají útočníkovi prostředníkem: HSTS automaticky přesměruje požadavky HTTP na https pro cílovou doménu.</li><li>Webové aplikace, které mají být čistě HTTPS, obsahují odkazy HTTP nebo obsluhují obsah přes HTTP: HSTS automaticky přesměruje požadavky HTTP na HTTPS pro cílovou doménu.</li><li>Útočník se zlými úmysly se pokusí zachytit provoz od uživatele, který používá neoprávněný certifikát, a hodlá, že uživatel bude akceptovat špatný certifikát: HSTS neumožní uživateli přepsat neplatnou zprávu certifikátu.</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Zajistěte šifrování připojení a ověřování certifikátů SQL serveru.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | SQL Azure  |
| **Atributy**              | SQL verze – V12 |
| **Reference**              | [Osvědčené postupy při psaní řetězců zabezpečeného připojení pro SQL Database](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Kroky** | <p>Veškerá komunikace mezi SQL Database a klientskou aplikací je šifrovaná pomocí protokolu TLS (Transport Layer Security), dříve označovaného jako SSL (Secure Sockets Layer) (SSL). SQL Database nepodporuje nešifrovaná připojení. Chcete-li ověřit certifikáty pomocí kódu aplikace nebo nástrojů, explicitně vyžádejte šifrované připojení a nedůvěřuje certifikátům serveru. Pokud kód aplikace nebo nástroje nevyžadují šifrované připojení, budou i nadále přijímat šifrovaná připojení.</p><p>Nemusí však ověřovat certifikáty serveru a proto budou náchylné k útokům typu "muž" v prostředním. Chcete-li ověřit certifikáty s kódem aplikace ADO.NET, nastavte `Encrypt=True` a `TrustServerCertificate=False` v připojovacím řetězci databáze. Pokud chcete certifikáty ověřit přes SQL Server Management Studio, otevřete dialogové okno připojit k serveru. Klikněte na šifrovat připojení na kartě Vlastnosti připojení.</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Vynutit šifrovanou komunikaci s SQL serverem

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Místní |
| **Atributy**              | SQL verze – MsSQL2016, verze SQL-MsSQL2012, verze SQL – MsSQL2014 |
| **Reference**              | [Povolení šifrovaných připojení k databázovému stroji](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)  |
| **Kroky** | Povolení šifrování TLS zvyšuje zabezpečení dat přenášených mezi instancemi SQL Server a aplikací mezi sítěmi. |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Ujistěte se, že komunikace s Azure Storage je přes protokol HTTPS.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Šifrování Transport-Level Azure Storage – pomocí protokolu HTTPS](../../storage/blobs/security-recommendations.md#networking) |
| **Kroky** | Aby bylo zajištěno zabezpečení Azure Storage dat při přenosu, vždy použijte protokol HTTPS při volání rozhraní REST API nebo přístupu k objektům v úložišti. Sdílené přístupové podpisy, které se dají použít k delegování přístupu k Azure Storage objektům, zahrnují taky možnost určit, že při použití sdílených přístupových podpisů se dá použít jenom protokol HTTPS, což zajistí, že kdokoli odesílající odkazy s tokeny SAS bude používat správný protokol.|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>Pokud není možné povolit protokol HTTPS, ověřte hash MD5 po stažení objektu BLOB.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | StorageType – objekt BLOB |
| **Reference**              | [Přehled MD5 Windows Azure Blob](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Kroky** | <p>Windows Azure Blob service poskytuje mechanismy pro zajištění integrity dat v aplikační i transportní vrstvě. Pokud z nějakého důvodu potřebujete použít protokol HTTP místo protokolu HTTPS a pracujete s objekty blob bloku, můžete použít kontrolu MD5, která vám umožní ověřit integritu přenášených objektů BLOB.</p><p>To vám pomůže s ochranou před chybami v síťové/transportní vrstvě, ale ne nutně u zprostředkovatelských útoků. Pokud můžete použít protokol HTTPS, který poskytuje zabezpečení na úrovni přenosu, je použití kontroly MD5 redundantní a zbytečné.</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>Použití kompatibilního klienta s protokolem SMB 3,0 k zajištění šifrování dat v přenosu do sdílených složek Azure

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Mobilní klient | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | StorageType – soubor |
| **Reference**              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Podpora Azure File Storage SMB pro klienty Windows](../../storage/files/storage-dotnet-how-to-use-files.md#understanding-the-net-apis) |
| **Kroky** | Azure File Storage podporuje při použití REST API HTTPS, ale častěji se používá jako sdílená složka SMB připojená k virtuálnímu počítači. SMB 2,1 nepodporuje šifrování, takže připojení jsou povolená jenom v rámci stejné oblasti v Azure. SMB 3,0 ale podporuje šifrování a dá se použít s Windows Serverem 2012 R2, Windows 8, Windows 8.1 a Windows 10, což umožňuje přístup mezi jednotlivými oblastmi a dokonce i přístup k ploše. |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>Implementace připnutí certifikátu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, Windows Phone |
| **Atributy**              | –  |
| **Reference**              | [Certifikát a připnutí veřejného klíče](https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning) |
| **Kroky** | <p>Připnutí certifikátů brání útokům MITM (man-in-the-middle). Připnutí je proces přidružení hostitele k očekávanému certifikátu x509 nebo veřejnému klíči. Jakmile je certifikát nebo veřejný klíč známý nebo zobrazený pro hostitele, certifikát nebo veřejný klíč je přidružený k hostiteli nebo "připnuté". </p><p>Proto když se nežádoucí osoba pokusí udělat útok TLS MITM, během protokolu TLS handshake se klíč ze serveru útočníka liší od klíče připnutého certifikátu a požadavek se zahodí, což zabrání v připnutí certifikátů MITM, a to implementací `ServerCertificateValidationCallback` delegáta Třída ServicePointManager.</p>|

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

## <a name="enable-https---secure-transport-channel"></a><a id="https-transport"></a>Povolit transportní kanál zabezpečeného HTTPS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Rozhraní .NET Framework 3 |
| **Atributy**              | –  |
| **Reference**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Kroky** | Konfigurace aplikace by měla zajistit, aby se protokol HTTPS používal pro veškerý přístup k citlivým informacím.<ul><li>**Vysvětlení:** Pokud aplikace zpracovává citlivé informace a nepoužívá šifrování na úrovni zpráv, měla by být povolena pouze komunikace přes zašifrovaný transportní kanál.</li><li>**Doporučení:** Zajistěte, aby byl zakázán přenos HTTP, a místo toho povolte přenos HTTPS. Například nahraďte `<httpTransport/>` `<httpsTransport/>` tag with. Nespoléhá na síťovou konfiguraci (bránu firewall), aby se zaručilo, že aplikaci lze používat jenom přes zabezpečený kanál. Z pohledu Philosophical by aplikace neměla záviset na síti pro své zabezpečení.</li></ul><p>Z praktického hlediska můžou osoby zodpovědné za zabezpečení sítě vždycky sledovat požadavky na zabezpečení aplikace při jejich vývoje.</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: Nastavte úroveň ochrany zabezpečení zprávy na EncryptAndSign

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | .NET Framework 3 |
| **Atributy**              | –  |
| **Reference**              | [MSDN](/previous-versions/msp-n-p/ff650862(v=pandp.10)) |
| **Kroky** | <ul><li>**Vysvětlení:** Pokud je úroveň ochrany nastavená na hodnotu žádné, zakáže se ochrana zpráv. Důvěrnost a integrita se dosahuje pomocí vhodné úrovně nastavení.</li><li>**DOPORUČIT**<ul><li>Když `Mode=None` – zakáže ochranu zpráv</li><li>When `Mode=Sign` -podepisuje, ale nešifruje zprávu; měla by být použita, pokud je integrita dat důležitá.</li><li>When `Mode=EncryptAndSign` -podepisuje a zašifruje zprávu</li></ul></li></ul><p>Zvažte vypnutí šifrování a podepsání zprávy pouze v případě, že pouze potřebujete ověřit integritu informací bez obav. To může být užitečné pro operace nebo kontrakty služby, ve kterých potřebujete ověřit původní odesílatele, ale neodesílají se žádná citlivá data. Při snižování úrovně ochrany buďte opatrní, že zpráva neobsahuje žádné osobní údaje.</p>|

### <a name="example"></a>Příklad
Konfigurace služby a operace pro podepsání zprávy se zobrazí v následujících příkladech. Příklad kontraktu služby `ProtectionLevel.Sign` : Následuje příklad použití technologie ProtectionLevel. Sign na úrovni kontraktu služby: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Příklad
Příklad kontraktu operace `ProtectionLevel.Sign` pro (pro podrobný ovládací prvek): Následující příklad je použití `ProtectionLevel.Sign` na úrovni OperationContract:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: pro spuštění služby WCF použijte účet s nejnižšími oprávněními

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | .NET Framework 3 |
| **Atributy**              | –  |
| **Reference**              | [MSDN](/previous-versions/msp-n-p/ff648826(v=pandp.10)) |
| **Kroky** | <ul><li>**Vysvětlení:** Nespouštějte služby WCF pod účtem správce nebo účet s vysokou úrovní oprávnění. v případě, že dojde k ohrožení služeb, bude výsledkem vysoký dopad.</li><li>**Doporučení:** Použijte účet s nejnižšími oprávněními k hostování služby WCF, protože omezí plochu útoku na vaši aplikaci a snížíte potenciální škodu, pokud jste napadeni. Pokud účet služby vyžaduje další přístupová práva k prostředkům infrastruktury, jako je MSMQ, protokol událostí, čítače výkonu a systém souborů, měli byste těmto prostředkům předávat příslušná oprávnění, aby služba WCF mohla běžet úspěšně.</li></ul><p>Pokud vaše služba potřebuje přístup k určitým prostředkům jménem původního volajícího, použijte zosobnění a delegování k vytvoření toku identity volajícího pro kontrolu autorizace pro příjem dat. Ve scénáři vývoje použijte účet místní síťové služby, což je speciální integrovaný účet, který má omezená oprávnění. V produkčním scénáři vytvořte vlastní účet služby domény s nejnižšími oprávněními.</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Vynutit veškerý provoz do webových rozhraní API přes připojení HTTPS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webové rozhraní API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC5, MVC6 |
| **Atributy**              | –  |
| **Reference**              | [Vynucování SSL v kontroleru webového rozhraní API](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Kroky** | Pokud má aplikace i vazbu HTTPS i HTTP, klienti stále můžou k přístupu k webu používat protokol HTTP. Pokud tomu chcete zabránit, použijte filtr akcí, abyste zajistili, že požadavky na chráněná rozhraní API jsou vždycky přes protokol HTTPS.|

### <a name="example"></a>Příklad 
Následující kód ukazuje filtr ověřování webového rozhraní API, který kontroluje protokol TLS: 
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
Přidejte tento filtr do všech akcí webového rozhraní API, které vyžadují protokol TLS: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-tls"></a><a id="redis-ssl"></a>Ujistěte se, že komunikace do Azure cache pro Redis je přes protokol TLS.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Cache for Redis | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Podpora Azure Redis TLS](../../azure-cache-for-redis/cache-faq.md) |
| **Kroky** | Server Redis nepodporuje protokol TLS mimo pole, ale mezipaměť Azure pro Redis. Pokud se připojujete ke službě Azure cache pro Redis a váš klient podporuje protokol TLS, jako je StackExchange. Redis, pak byste měli použít protokol TLS. Ve výchozím nastavení je port bez TLS zakázaný pro novou mezipaměť Azure pro instance Redis. Ujistěte se, že se zabezpečené výchozí hodnoty nemění, pokud neexistuje závislost na podpoře TLS pro klienty Redis. |

Upozorňujeme, že Redis je navržený tak, aby k nim měli k dispozici důvěryhodní klienti v důvěryhodných prostředích. To znamená, že obvykle není vhodné zveřejnit instanci Redis přímo na internetu nebo obecně k prostředí, kde mají nedůvěryhodní klienti přímý přístup k portu TCP Redis nebo k soketu UNIX. 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>Zabezpečit komunikaci zařízení s bránou pole

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána pole IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | V případě zařízení založených na protokolu IP by komunikační protokol mohl být obvykle zapouzdřený v kanálu SSL/TLS, aby chránil data při přenosu. Pro jiné protokoly, které nepodporují protokol SSL/TLS, zkontrolujte, zda jsou k dispozici zabezpečené verze protokolu, které poskytují zabezpečení při přenosu nebo vrstvě zpráv. |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Zabezpečte komunikaci zařízení s cloudovou bránou pomocí protokolu SSL/TLS.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Cloudová brána IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Zvolit komunikační protokol](../../iot-hub/iot-hub-devguide.md) |
| **Kroky** | Zabezpečte protokoly HTTP/AMQP nebo MQTT pomocí protokolu SSL/TLS. |