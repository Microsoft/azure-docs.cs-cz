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
ms.openlocfilehash: afeae4af9b41bf434b26833a3bd927118a4697ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175233"
---
**Požadavky na konfigurační a procesní server pro replikaci fyzického serveru**

**Komponenta** | **Požadavek** 
--- | ---
**NASTAVENÍ HARDWARU** | 
Procesorová jádra | 8 
Paměť RAM | 16 GB
Počet disků | 3, včetně disku operačního systému, disku mezipaměti procesu a retenční jednotky pro navrácení služeb po selhání 
Volné místo na disku (mezipaměť procesního serveru) | 600 GB
Volné místo na disku (retenční disk) | 600 GB
 | 
**NASTAVENÍ SOFTWARU** | 
Operační systém | Windows Server 2012 R2 <br> Windows Server 2016
Národní prostředí operačního systému | Angličtina (en-us)
Role Windows Serveru | Nepovolujte tyto role: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V 
Zásady skupiny | Nepovolujte tyto zásady skupiny: <br> - Zabránit přístupu k příkazovému řádku. <br> - Zabránit přístupu k nástrojům pro úpravu registru. <br> - Logika důvěryhodnosti pro přílohy souborů. <br> - Zapněte spuštění skriptu. <br> [Další informace](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Žádné již existující výchozí webové stránky <br> - Žádné již existující webové stránky / aplikace poslech na portu 443 <br>- Povolit [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Povolte nastavení [FastCGI.](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
Typ IP adresy | Statická 
| 
**NASTAVENÍ APLIKACE ACCESS** | 
Mysql | MySQL by měl být nainstalován na konfiguračním serveru. Můžete nainstalovat ručně nebo site recovery můžete nainstalovat během nasazení. Chcete-li aplikaci Site Recovery nainstalovat, zkontrolujte, zda počítač může dosáhnout http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
URL – adresy | Konfigurační server potřebuje přístup k těmto adresám URL (přímo nebo přes proxy server):<br/><br/> Azure AD: `login.microsoftonline.com`; `login.microsoftonline.us`;`*.accesscontrol.windows.net`<br/><br/> Přenos dat `*.backup.windowsazure.com`replikace: ;`*.backup.windowsazure.us`<br/><br/> Správa replikace: `*.hypervrecoverymanager.windowsazure.com`; `*.hypervrecoverymanager.windowsazure.us`; `https://management.azure.com`;`*.services.visualstudio.com`<br/><br/> Přístup k `*.blob.core.windows.net`úložišti: ;`*.blob.core.usgovcloudapi.net`<br/><br/> Synchronizace času: `time.nist.gov`;`time.windows.com`<br/><br/> Telemetrie (volitelně):`dc.services.visualstudio.com`
Brána firewall | Pravidla brány firewall založená na IP adresách by měla umožňovat komunikaci s adresami URL Azure. Chcete-li zjednodušit a omezit rozsahy IP adres, doporučujeme použít filtrování adres URL.<br/><br/>**Pro komerční IP adresy:**<br/><br/>- Povolte [rozsahy IP adres datového centra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)a port HTTPS (443).<br/><br/> - Povolit rozsahy IP adres pro západní USA (používá se pro řízení přístupu a správu identit).<br/><br/> - Povolit rozsahy IP adres pro oblast Azure vašeho předplatného, pro podporu adres URL potřebných pro Azure Active Directory, zálohování, replikace a úložiště.<br/><br/> **Pro vládní IP adresy:**<br/><br/> - Povolit rozsahy IP ip centra Azure Government datacenter a port HTTPS (443).<br/><br/> - Povolit rozsahy IP adres pro všechny oblasti vlády USA (Virginie, Texas, Arizona a Iowa), pro podporu adres URL potřebných pro Azure Active Directory, zálohování, replikace a úložiště.
Porty | Povolit 443 (Orchestrace řídicího kanálu)<br/><br/> Povolit 9443 (přenos dat) 


**Požadavky na změnu velikosti konfiguračního a procesního serveru**

**Cpu** | **Paměti** | **Disk mezipaměti** | **Rychlost změny dat** | **Replikované počítače**
--- | --- | --- | --- | ---
8 virtuálních procesorů<br/><br/> 2 zásuvky * 4 \@ jádra 2,5 GHz | 16 GB | 300 GB | 500 GB nebo méně | < 100 strojů
12 virtuálních procesorů<br/><br/> 2 ponožky * \@ 6 jader 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 až 150 strojů
16 virtuálních procesorů<br/><br/> 2 ponožky * \@ 8 jader 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 strojů

