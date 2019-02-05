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
ms.openlocfilehash: 4346b347994f49774584caf31a96ff2f81fdc0e1
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55701212"
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
IIS | -Žádné existující výchozí web <br> -Žádné stávající web/aplikace naslouchá na portu 443 <br>-Aktivovat [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivovat [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) nastavení 
| 
**NASTAVENÍ SÍTĚ** | 
Typ IP adresy | Statická 
Porty | 443 (orchestrace řídicího kanálu)<br>9443 (přenos dat) 
Typ NIC | VMXNET3 (pokud jde o virtuální počítač VMware)
 |
**Přístup k Internetu** (server potřebuje přístup k následujícím adresám URL - přímo nebo prostřednictvím proxy serveru):|
\*.backup.windowsazure.com | Používá pro koordinaci a přenosu replikovaných dat
\*.store.core.windows.net | Používá pro koordinaci a přenosu replikovaných dat
\*.blob.core.windows.net | Používá pro přístup k účtu úložiště, který ukládá replikovaná data
\*.hypervrecoverymanager.windowsazure.com | Používá pro koordinaci a operace správy replikací.
https:\//management.azure.com | Používá pro koordinaci a operace správy replikací. 
*.services.visualstudio.com | Používá pro účely telemetrie (je volitelný)
time.nist.gov | Používá se ke kontrole synchronizace mezi systémovým a globálním časem.
time.windows.com | Používá se ke kontrole synchronizace mezi systémovým a globálním časem.
-https:\//login.microsoftonline.com <br> -https:\//secure.aadcdn.microsoftonline-p.com <br> -https:\//login.live.com  <br> - https:\//graph.windows.net <br> - https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com | Nastavit OVF potřebuje přístup k těmto adresám URL. Používají se pro přístup k řízení a správě identit Azure Active Directory
protokol https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | K dokončení stahování MySQL
|
**SOFTWARE PRO INSTALACI** | 
VMware vSphere PowerCLI | [Nástroj PowerCLI verze 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) by měly být nainstalovány, pokud je konfigurační Server běží na virtuálním počítači VMware.
MYSQL | Je třeba nainstalovat MySQL. Můžete nainstalovat ručně nebo ji můžete nainstalovat Site Recovery. (Odkazovat [nastavení](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) Další informace)

**Configuration/Process server velikosti požadavky**

**CPU** | **Paměť** | **Disk mezipaměti** | **Frekvence změny dat** | **Replikované počítače**
--- | --- | --- | --- | ---
8 virtuálních CPU<br/><br/> sokety 2 * 4 jádra \@ 2,5 GHz | 16GB | 300 GB | 500 GB nebo méně | < 100 počítačů
12 virtuálních procesorů<br/><br/> 2 socks * 6 jader \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 až 150 počítačů
16 virtuálních procesorů<br/><br/> 2 socks * 8 jader \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 počítačů

