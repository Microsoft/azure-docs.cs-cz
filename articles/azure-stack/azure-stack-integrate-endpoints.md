---
title: Azure Stack – integrace datových center – publikování koncových bodů | Dokumentace Microsoftu
description: Zjistěte, jak publikovat koncové body služby Azure Stack ve vašem datovém centru
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/06/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: c3b27291fc413310393cd0270ec750de14a4985b
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270058"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure Stack – integrace datových center – publikování koncových bodů

Azure Stack nastaví virtuálních IP adres (VIP) pro jeho infrastrukturu role. Tyto virtuální IP adresy se přidělují z fondu veřejných IP adres. Každý virtuální IP adresy je zabezpečený pomocí seznam řízení přístupu (ACL) ve vrstvě softwarově definované sítě. Seznamy ACL se taky používají napříč fyzické přepínače (tory a BMC) k další posílení zabezpečení řešení. Položka DNS se vytvoří pro každý koncový bod v zóně DNS o externí zadaný v době nasazení.


Následující diagram architektury ukazuje různé síťové vrstvy a seznamy ACL:

![Strukturální přehled](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Porty a protokoly (příchozí)

Sada infrastrukturu virtuálních IP adres je vyžadován pro publikování koncových bodů služby Azure Stack k externím sítím. *Koncový bod (VIP)* tabulka ukazuje, každý koncový bod, požadovaný port a protokol. Najdete v dokumentaci poskytovatele nasazení konkrétní prostředek pro koncové body, které vyžadují poskytovatelů dalších prostředků, jako je například poskytovatele prostředků SQL.

Interní infrastrukturu virtuálních IP adres nejsou uvedené, protože nejsou požadovány pro publikování Azure Stack.

> [!Note]  
> Uživatel virtuální IP adresy je dynamická a určené samotných uživatelů s žádnou kontrolu operátorem služby Azure Stack.

> [!Note]
> Od aktualizace 1811 porty v rozsahu 12495 30015 jsou už musí být z důvodu přidání [hostitel rozšíření](azure-stack-extension-host-prepare.md).

|Koncový bod (VIP)|DNS host A record|Protocol (Protokol)|Porty|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portál (správce)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Resource Manager (správce)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portál (uživatel)|Portál.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|HTTPS|443|
|Azure Resource Manageru (uživatel)|Správa.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|HTTPS|443|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Seznam odvolaných certifikátů|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP A UDP|53|
|Hostování | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Key Vault (uživatel)|&#42;.Vault.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|HTTPS|443|
|Key Vault (správce)|&#42;.adminvault.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|HTTPS|443|
|Fronta úložiště|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Table|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Poskytovatel prostředků SQL|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Poskytovatel prostředků MySQL|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|Brány VPN Gateway|     |     |[V tématu Nejčastější dotazy k branám VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Porty a adresy URL (odchozí)

Azure Stack podporuje pouze transparentní proxy servery. V nasazení tam, kde transparentní proxy server odchozí připojení k tradiční proxy server, musíte povolit následující porty a adresy URL pro odchozí komunikaci:

> [!Note]  
> Azure Stack nepodporuje použití ExpressRoute pro dosažení služby Azure, které jsou uvedeny v následující tabulce.

|Účel|Cílová adresa URL|Protocol (Protokol)|Porty|Zdrojová síť|
|---------|---------|---------|---------|---------|
|Identita|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https://secure.aadcdn.microsoftonline-p.com<br>office.com|HTTP<br>HTTPS|80<br>443|Veřejné virtuální IP adresy – velikost/27<br>Infrastruktura veřejných sítě|
|Syndikace Marketplace|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|Veřejné virtuální IP adresy – velikost/27|
|Opravy a aktualizace|https://&#42;.azureedge.net|HTTPS|443|Veřejné virtuální IP adresy – velikost/27|
|Registrace|https://management.azure.com|HTTPS|443|Veřejné virtuální IP adresy – velikost/27|
|Využití|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.net |HTTPS|443|Veřejné virtuální IP adresy – velikost/27|
|Windows Defender|.wdcp.microsoft.com<br>.wdcpalt.microsoft.com<br>*.updates.microsoft.com<br>*.download.microsoft.com<br>https://msdl.microsoft.com/download/symbols<br>`https://www.microsoft.com/pkiops/crl`<br>`https://www.microsoft.com/pkiops/certs`<br>`https://crl.microsoft.com/pki/crl/products`<br>`https://www.microsoft.com/pki/certs`<br>https://secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Veřejné virtuální IP adresy – velikost/27<br>Infrastruktura veřejných sítě|
|NTP|(K dispozici pro nasazení serveru IP NTP)|UDP|123|Veřejné virtuální IP adresy – velikost/27|
|DNS|(K dispozici pro nasazení IP adresy DNS serveru)|TCP<br>UDP|53|Veřejné virtuální IP adresy – velikost/27|
|CRL|(Adresa URL v rámci distribučních bodů seznamu CRL na váš certifikát)|HTTP|80|Veřejné virtuální IP adresy – velikost/27|
|LDAP|K dispozici pro integraci Graphu doménové struktury služby Active Directory|TCP<br>UDP|389|Veřejné virtuální IP adresy – velikost/27|
|LDAP SSL|K dispozici pro integraci Graphu doménové struktury služby Active Directory|TCP|636|Veřejné virtuální IP adresy – velikost/27|
|LDAP GC|K dispozici pro integraci Graphu doménové struktury služby Active Directory|TCP|3268|Veřejné virtuální IP adresy – velikost/27|
|LDAP GC SSL|K dispozici pro integraci Graphu doménové struktury služby Active Directory|TCP|3269|Veřejné virtuální IP adresy – velikost/27|
|AD FS|AD FS metadat koncového bodu k dispozici pro integraci služby AD FS|TCP|443|Veřejné virtuální IP adresy – velikost/27|
|     |     |     |     |     |

> [!Note]  
> Odchozí adresy URL jsou vyrovnávání zatížení pomocí Azure traffic Manageru k zajištění nejlepší možné připojení zeměpisné umístění. Pomocí adresy URL s vyrovnáváním zatížení, Microsoft můžete aktualizovat a změnit koncových bodů back-end bez dopadu na zákazníky. Společnost Microsoft neposkytuje seznam IP adres pro adresy URL na skupinu s vyrovnáváním zatížení. Měli byste použít zařízení, která podporuje filtrování podle adresy URL, nikoli podle IP.

## <a name="next-steps"></a>Další postup

[Požadavky služby Azure Stack infrastruktury veřejných KLÍČŮ](azure-stack-pki-certs.md)
