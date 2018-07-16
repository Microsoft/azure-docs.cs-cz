---
title: zahrnout soubor
description: zahrnout soubor
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 669966ce21c5c6c2d0653eb51c81fe78aa0b3a12
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057306"
---
**Požadavky na Configuration/Process server**

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
IIS | -Žádné dříve existující výchozí web <br> -Žádné stávající web/aplikace naslouchá na portu 443 <br>-Aktivovat [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivovat [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) nastavení 
| 
**NASTAVENÍ SÍTĚ** | 
Typ IP adresy | Statická 
Přístup k internetu | Server potřebuje přístup k těmto adresám URL (přímo nebo prostřednictvím proxy serveru) <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> - https://management.azure.com <br> -*. services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF také potřebují přístup k následujícím adresám URL <br> - https://login.microsoftonline.com <br> - https://secure.aadcdn.microsoftonline-p.com <br> - https://login.live.com  <br> - https://auth.gfx.ms <br> - https://graph.windows.net <br> - https://login.windows.net <br> - https://www.live.com <br> - https://www.microsoft.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Porty | 443 (orchestrace řídicího kanálu)<br>9443 (přenos dat) 
Typ NIC | VMXNET3 (pokud jde o virtuální počítač VMware)
 | 
**SOFTWARE PRO INSTALACI** | 
VMware vSphere PowerCLI. | [Nástroj PowerCLI verze 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) by měly být nainstalovány, pokud je konfigurační Server běží na virtuálním počítači VMware.
MYSQL | Je třeba nainstalovat MySQL. Můžete nainstalovat ručně nebo ji můžete nainstalovat Site Recovery.

**Configuration/Process server velikosti požadavky**

**CPU** | **Paměť** | **Disk mezipaměti** | **Frekvence změny dat** | **Replikované počítače**
--- | --- | --- | --- | ---
8 virtuálních CPU<br/><br/> sokety 2 * 4 jádra \@ 2,5 GHz | 16GB | 300 GB | 500 GB nebo méně | < 100 počítačů
12 virtuálních procesorů<br/><br/> 2 socks * 6 jader \@ 2,5 GHz | 18 GB | 600 GB | 500 GB AŽ 1 TB | 100 až 150 počítačů
16 virtuálních procesorů<br/><br/> 2 socks * 8 jader \@ 2,5 GHz | 32 GB | 1 TB | 1 – 2 TB | 150 -200 počítačů

