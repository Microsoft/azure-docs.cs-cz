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
ms.openlocfilehash: 3a8a7be6f437687a4de31ce8e0ac62588f64e2eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96016895"
---
**Požadavky na konfigurační server a procesový Server pro replikaci fyzických serverů**

**Komponenta** | **Požadavek** 
--- | ---
**NASTAVENÍ HARDWARU** | 
Procesorová jádra | 8 
Paměť RAM | 16 GB
Počet disků | 3, včetně disku operačního systému, disku mezipaměti procesového serveru a jednotky pro uchovávání pro navrácení služeb po obnovení 
Volné místo na disku (mezipaměť procesového serveru) | 600 GB
Volné místo na disku (disk pro uchovávání) | 600 GB
 | 
**NASTAVENÍ SOFTWARU** | 
Operační systém | Windows Server 2012 R2 <br> Windows Server 2016
Národní prostředí operačního systému | Angličtina (en-us)
Role Windows Serveru | Nepovolujte tyto role: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V 
Zásady skupiny | Nepovolujte tyto zásady skupiny: <br> – Zabraňte přístupu k příkazovému řádku. <br> – Zabraňte přístup k nástrojům pro úpravu registru. <br> – Logika vztahu důvěryhodnosti pro přílohy souborů. <br> -Zapnout provádění skriptu. <br> [Další informace](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | -Žádný stávající výchozí web <br> -Žádný existující web nebo aplikace nenaslouchá na portu 443. <br>-Povolit  [anonymní ověřování](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> -Povolit nastavení [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) .
Typ IP adresy | Static 
| 
**NASTAVENÍ PŘÍSTUPU** | 
MYSQL | MySQL by měl být nainstalovaný na konfiguračním serveru. Instalaci můžete provést ručně, nebo ji Site Recovery můžete nainstalovat během nasazování. Pokud chcete Site Recovery nainstalovat, ověřte, že počítač má přístup http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi .
Adresy URL | Konfigurační server potřebuje přístup k těmto adresám URL (přímo nebo prostřednictvím proxy serveru):<br/><br/> Azure AD: `login.microsoftonline.com` ; `login.microsoftonline.us` ; `*.accesscontrol.windows.net`<br/><br/> Přenos replikačních dat: `*.backup.windowsazure.com` ; `*.backup.windowsazure.us`<br/><br/> Správa replikace: `*.hypervrecoverymanager.windowsazure.com` ; `*.hypervrecoverymanager.windowsazure.us` ;; `https://management.azure.com``*.services.visualstudio.com`<br/><br/> Přístup k úložišti: `*.blob.core.windows.net` ; `*.blob.core.usgovcloudapi.net`<br/><br/> Synchronizace času: `time.nist.gov` ; `time.windows.com`<br/><br/> Telemetrie (volitelné): `dc.services.visualstudio.com`
Brána firewall | Pravidla brány firewall založená na IP adrese by měla umožňovat komunikaci s adresami URL Azure. Pro zjednodušení a omezení rozsahu IP adres doporučujeme použít filtrování adres URL.<br/><br/>**Pro komerční IP adresy:**<br/><br/>– Povolte [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)a port HTTPS (443).<br/><br/> – Povoluje rozsahy IP adres pro Západní USA (používá se pro Access Control a správu identit).<br/><br/> – Povolte rozsahy IP adres pro oblast Azure svého předplatného, aby se podporovaly adresy URL, které jsou potřeba pro Azure Active Directory, zálohování, replikaci a úložiště.<br/><br/> **Pro státní IP adresy:**<br/><br/> – Povolí Azure Government rozsahy IP adres datového centra a port HTTPS (443).<br/><br/> – Povoluje rozsahy IP adres pro všechny oblasti US Gov () – Virginia, Texas, Arizona a Iowa), aby podporovaly adresy URL, které jsou potřeba pro Azure Active Directory, zálohování, replikaci a úložiště.
Porty | Povolení 443 (orchestrace řídicích kanálů)<br/><br/> Povolení 9443 (přenos dat) 


**Požadavky na velikost konfiguračního a procesového serveru**

**Procesor** | **Memory (Paměť)** | **Disk mezipaměti** | **Frekvence změny dat** | **Replikované počítače**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 sokety × 4 jádra \@ 2,5 GHz | 16GB | 300 GB | 500 GB nebo méně | Počítače s < 100
12 vCPU<br/><br/> 2 SOCKS * 6 jader \@ 2,5 GHz | 18 GB | 600 GB | 500 GB AŽ 1 TB | 100 až 150 počítačů
16 vCPU<br/><br/> 2 SOCKS * 8 jader \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 – 200 počítačů