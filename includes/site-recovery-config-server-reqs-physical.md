---
title: zahrnout soubor
description: zahrnout soubor
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: c32d1bbe5c2f735333a312638c553d7a2c434c0b
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702659"
---
**Požadavky Configuration/Process server pro replikaci fyzických serverů**

**Komponenta** | **Požadavek** 
--- | ---
**NASTAVENÍ HARDWARU** | 
Procesorová jádra | 8 
Paměť RAM | 16 GB
Počet disků | 3, včetně operačního systému disku, disk mezipaměti procesového serveru a jednotky pro uchovávání dat pro navrácení služeb po obnovení 
Volného místa na disku (mezipaměť procesového serveru) | 600 GB
Volné místo na disku (disk pro uchování) | 600 GB
 | 
**NASTAVENÍ SOFTWARU** | 
Operační systém | Windows Server 2012 R2 <br> Windows Server 2016
Národní prostředí operačního systému | Angličtina (en-us)
Role Windows Serveru | Nepovolí pracovníci v těchto rolích: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V 
Zásady skupiny | Nepovolí tyto zásady skupiny: <br> -Zabránit přístupu do příkazového řádku. <br> -Zabránit přístupu k nástrojům pro úpravu registru. <br> – Logika důvěryhodnosti pro přiložené soubory. <br> -Zapnutí provádění skriptů. <br> [Další informace](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Žádné dříve existující výchozí web <br> -Žádné stávající web/aplikace naslouchá na portu 443 <br>-Aktivovat [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivovat [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) nastavení.
Typ IP adresy | Statická 
| 
**NASTAVENÍ PŘÍSTUPU** | 
MYSQL | MySQL musí být nainstalován na konfiguračním serveru. Můžete nainstalovat ručně, nebo Site Recovery můžete nainstalovat během nasazování. Site Recovery k instalaci, zkontrolujte, že počítač může dosáhnout http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
Adresy URL | Konfigurační server potřebuje přístup k těmto adresám URL (přímo nebo prostřednictvím proxy serveru):<br/><br/> Azure AD: ``login.microsoftonline.com``; ``login.microsoftonline.us``; ``*.accesscontrol.windows.net``<br/><br/> Přenos dat replikace: ``*.backup.windowsazure.com``; ``*.backup.windowsazure.us``<br/><br/> Správa replikace: ``*.hypervrecoverymanager.windowsazure.com``; ``*.hypervrecoverymanager.windowsazure.us``; ``https://management.azure.com``; ``*.services.visualstudio.com``<br/><br/> Přístup k úložišti: ``*.blob.core.windows.net``; ``*.blob.core.usgovcloudapi.net``<br/><br/> Synchronizace času: ``time.nist.gov``; ``time.windows.com<br/><br/> Telemetry (optional): ``adresu dc.services.visualstudio.com.
Brána firewall | Pravidla brány firewall založená na adresu IP by měly umožňovat komunikace k adresám URL služby Azure. Pro zjednodušení a omezit rozsahy IP adres, doporučujeme použít filtrování adres URL.<br/><br/>**Pro komerční IP adresy:**<br/><br/>– Povolte [Azure rozsahů IP adres Datacentra](https://www.microsoft.com/download/confirmation.aspx?id=41653)a port HTTPS (443).<br/><br/> – Povolte rozsahy IP adres pro oblast západní USA (používá se pro správu identit a řízení přístupu).<br/><br/> – Povolte rozsahy IP adres pro oblast Azure svého předplatného, pro podporu adresy URL potřebné pro Azure Active Directory, zálohování, replikaci a úložiště.<br/><br/> **Pro státní správu IP adres:**<br/><br/> – Povolte Azure Government rozsahů IP adres Datacentra a port HTTPS (443).<br/><br/> – Povolte rozsahy IP adres pro všechny US Gov oblasti (Virginie, Texas, Arizona a Iowa) pro podporu adresy URL potřebné pro Azure Active Directory, zálohování, replikaci a úložiště.
Porty | Povolit 443 (Orchestrace řídicího kanálu)<br/><br/> Povolit 9443 (přenos dat) 


**Configuration/Process server velikosti požadavky**

**CPU** | **Paměť** | **Disk mezipaměti** | **Frekvence změny dat** | **Replikované počítače**
--- | --- | --- | --- | ---
8 virtuálních CPU<br/><br/> sokety 2 * 4 jádra \@ 2,5 GHz | 16GB | 300 GB | 500 GB nebo méně | < 100 počítačů
12 virtuálních procesorů<br/><br/> 2 socks * 6 jader \@ 2,5 GHz | 18 GB | 600 GB | 500 GB AŽ 1 TB | 100 až 150 počítačů
16 virtuálních procesorů<br/><br/> 2 socks * 8 jader \@ 2,5 GHz | 32 GB | 1 TB | 1 – 2 TB | 150 -200 počítačů

