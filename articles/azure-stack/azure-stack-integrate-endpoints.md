---
title: Azure zásobníku datacenter integrace – Publikování koncové body | Microsoft Docs
description: Zjistěte, jak publikovat koncové body Azure zásobníku ve vašem datovém centru
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 04/06/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: ''
ms.openlocfilehash: d7384d8d1d8c0378e1a9dd68a4f7b71196330b8e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736690"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure zásobníku datacenter integrace – Publikování koncové body
Azure zásobníku nastaví virtuální IP adresy (VIP) pro její role infrastruktury. Tyto virtuální IP adresy jsou přiděleny z fondu veřejných IP adres. Každý virtuální IP adresy, je zabezpečen seznam řízení přístupu (ACL) ve vrstvě softwarově definované sítě. Seznamy ACL se také používají ve fyzické přepínače (mandátu a BMC) pro další posílení zabezpečení řešení. Položka DNS se vytvoří pro každý koncový bod v externí zóně DNS, který zadaný v době nasazení.


Následující diagram architektury ukazuje vrstvy jinou síť a seznamy řízení přístupu:

![Strukturální obrázek](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Porty a protokoly (příchozí)

Sada infrastruktury virtuální IP adresy jsou požadovány pro publikování koncové body Azure zásobníku k externím sítím. *Koncový bod (VIP)* tabulce jsou uvedeny jednotlivé koncový bod, požadovaný port a protokol. Naleznete v dokumentaci konkrétní prostředek zprostředkovatele nasazení pro koncové body, které vyžadují další prostředků poskytovatelé, například poskytovatel prostředků SQL.

Interní infrastruktury virtuální IP adresy nejsou uvedené, protože není jsou vyžadována pro publikování zásobník Azure.

> [!NOTE]
> Virtuální IP adresy uživatele je dynamická definované sami uživatelé s žádnou kontrolu operátorem zásobník Azure.


|Koncový bod (VIP)|Záznam hostitele DNS|Protocol (Protokol)|Porty|
|---------|---------|---------|---------|
|AD FS|Služba AD FS.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|HTTPS|443|
|Portál (správce)|Adminportal.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015|
|Azure Resource Manager (správce)|Adminmanagement.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|HTTPS|443<br>30024|
|Portál (uživatel)|Portál.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003|
|Azure Resource Manager (uživatel)|Správa.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|HTTPS|443<br>30024|
|Graph|Graf.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|HTTPS|443|
|Seznam odvolaných certifikátů|Seznam CRL.*&lt;oblast >.&lt; plně kvalifikovaný název domény >*|HTTP|80|
|DNS|&#42;.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|TCP A UDP|53|
|Key Vault (uživatel)|&#42;.Vault.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|HTTPS|443|
|Key Vault (správce)|&#42;.adminvault.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|HTTPS|443|
|Fronta úložiště|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tabulka úložiště|&#42;.Table.  *&lt;oblast >.&lt; plně kvalifikovaný název domény >*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Zprostředkovatel prostředků SQL|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Poskytovatel prostředků MySQL|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-101000 (FTP)<br>990 (FTPS)|

## <a name="ports-and-urls-outbound"></a>Porty a adresy URL (odchozí)

Azure zásobníku podporuje pouze transparentní proxy servery. V nasazení, kde transparentní proxy odchozí připojení pro tradiční proxy server, musíte povolit následující porty a adresy URL pro odchozí komunikace:


|Účel|zprostředkovatele identity|Protocol (Protokol)|Porty|
|---------|---------|---------|---------|
|Identita|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https://secure.aadcdn.microsoftonline-p.com|HTTP<br>HTTPS|80<br>443|
|Syndikace Marketplace.|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|Opravy a aktualizace|https://&#42;.azureedge.net|HTTPS|443|
|Registrace|https://management.azure.com|HTTPS|443|
|Využití|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.NET|HTTPS|443|
|Program Windows Defender|. wdcp.microsoft.com<br>. wdcpalt.microsoft.com<br>*. updates.microsoft.com<br>*. download.microsoft.com<br>https://msdl.microsoft.com/download/symbols<br>http://www.microsoft.com/pkiops/crl<br>http://www.microsoft.com/pkiops/certs<br>http://crl.microsoft.com/pki/crl/products<br>http://www.microsoft.com/pki/certs<br>https://secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|



## <a name="next-steps"></a>Další postup

[Požadavky pro Azure zásobníku infrastruktury veřejných KLÍČŮ](azure-stack-pki-certs.md)
