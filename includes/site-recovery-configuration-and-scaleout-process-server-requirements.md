---
title: zahrnout soubor
description: zahrnout soubor
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 05/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 2464fa41deaa1e9c43e5f53e1a900ca11b582040
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
**Požadavky na server nebo proces konfigurace**

**Komponenta** | **Požadavek** 
--- | ---
**HARDWARE** | 
**Jader procesoru** | 8 
**PAMĚŤ RAM** | 16 GB
**Počet disků** | 3, včetně disku operačního systému, proces disku mezipaměti serveru a jednotka pro uchování pro navrácení služeb po obnovení 
**Volné místo na disku (mezipaměti serveru procesu)** | 600 GB
**Volné místo na disku (disku pro uchování)** | 600 GB
**SOFTWARE** | 
**Operační systém** | Windows Server 2012 R2 <br> Windows Server 2016
**Národní prostředí operačního systému** | Angličtina (en-us)
**Role serveru Windows** | Nepovolíte tyto role: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V 
**Zásady skupiny** | Nemáte povolit tyto zásady skupiny: <br> -Zabránit přístupu do příkazového řádku. <br> -Znemožnit přístup k registru nástroje pro úpravy. <br> -Důvěřujete logiku pro přílohy souborů. <br> -Zapněte provádění skriptu. <br> [Další informace](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
**SLUŽBY IIS** | -Žádné dříve existující výchozí web <br> -Žádná dříve existující web nebo aplikace naslouchá na portu 443 <br>-Aktivovat [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivovat [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) nastavení 
**SÍTĚ** | 
**Typ adresy IP** | Statická 
**Přístup k Internetu** | Server potřebuje přístup k tyto adresy URL (přímo nebo prostřednictvím proxy serveru) <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (Pokud instalujete konfigurační server) <br> - time.nist.gov <br> - time.windows.com 
**Porty** | 443 (orchestrace řídicího kanálu)<br>9443 (přenos dat) 
**VMWARE (při nastavování konfigurace nebo procesový server jako virtuální počítač VMware)**
**seskupování typ** | VMXNET3  
**VMware vSphere PowerCLI spuštěna na virtuálním počítači* | [PowerCLI verze 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")

**Změna velikosti požadavky konfigurace nebo procesového serveru**

**CPU** | **Paměť** | **Diskové mezipaměti** | **Míry změny dat** | **Replikované počítače**
--- | --- | --- | --- | ---
8 Vcpu<br/><br/> 2 sockets * 4 jádra @ 2,5 GHz | 16GB | 300 GB | 500 GB nebo méně | < 100 počítače
12 Vcpu<br/><br/> 2 socks * 6 jader @ 2,5 GHz | 18 GB | 600 GB | 500 GB - 1 TB | 100 až 150 počítačů
16 Vcpu<br/><br/> 2 socks * 8 jader @ 2,5 GHz | 32 GB | 1 TB | 1 – 2 TB | 150 -200 počítačů

