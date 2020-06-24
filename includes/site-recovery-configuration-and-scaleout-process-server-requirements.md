---
title: zahrnout soubor
description: zahrnout soubor
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/23/2020
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: c8c51d671cd98a606c11a39b6cf489aa288d71b3
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85292137"
---
**Požadavky na konfigurační a procesní Server**


## <a name="hardware-requirements"></a>Požadavky na hardware

**Komponenta** | **Požadavek** 
--- | ---
Procesorová jádra | 8 
Paměť RAM | 16 GB
Počet disků | 3, včetně disku operačního systému, disku mezipaměti procesového serveru a jednotky pro uchovávání pro navrácení služeb po obnovení 
Volné místo na disku (mezipaměť procesového serveru) | 600 GB
Volné místo na disku (disk pro uchovávání) | 600 GB
 | 

## <a name="software-requirements"></a>Požadavky na software

**Komponenta** | **Požadavek** 
--- | ---
Operační systém | Windows Server 2012 R2 <br> Windows Server 2016
Národní prostředí operačního systému | Angličtina (EN-*)
Role Windows Serveru | Nepovolujte tyto role: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V 
Zásady skupiny | Nepovolujte tyto zásady skupiny: <br> – Zabraňte přístupu k příkazovému řádku. <br> – Zabraňte přístup k nástrojům pro úpravu registru. <br> – Logika vztahu důvěryhodnosti pro přílohy souborů. <br> -Zapnout provádění skriptu. <br> [Další informace](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Žádný předdefinovaný výchozí web <br> -Žádný existující web nebo aplikace nenaslouchá na portu 443. <br>-Povolit [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Povolit nastavení [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 
FIPS (Federal Information Processing Standards) | Nepovolit režim FIPS
|

## <a name="network-requirements"></a>Požadavky sítě

**Komponenta** | **Požadavek** 
--- | --- 
Typ IP adresy | Static 
Porty | 443 (orchestrace řídicího kanálu)<br>9443 (přenos dat) 
Typ síťové karty | VMXNET3 (Pokud je konfigurační server virtuálním počítačem VMware)
 |
**Přístup k Internetu** (server potřebuje přístup k následujícím adresám URL, přímo nebo prostřednictvím proxy serveru):|
\*.backup.windowsazure.com | Slouží k přenosu replikovaných dat a jejich koordinaci.
\*.blob.core.windows.net | Používá se pro přístup k účtu úložiště, který ukládá replikovaná data. Můžete zadat konkrétní adresu URL vašeho účtu úložiště mezipaměti.
\*.hypervrecoverymanager.windowsazure.com | Slouží k operacím správy replikace a jejich koordinaci.
https:\//login.microsoftonline.com | Slouží k operacím správy replikace a jejich koordinaci. 
time.nist.gov | Používá se ke kontrole časové synchronizace mezi systémovým a globálním časem.
time.windows.com | Používá se ke kontrole časové synchronizace mezi systémovým a globálním časem.
| <ul> <li> https:\//management.azure.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https: \/ /Login.Live.com </li><li> https: \/ /Graph.Windows.NET </li><li> https:\//login.windows.net </li><li> *. services.visualstudio.com (volitelné) </li><li> https: \/ /www.Live.com </li><li> https: \/ /www.Microsoft.com </li></ul> | Instalace OVF potřebuje přístup k těmto dalším adresám URL. Používají se k řízení přístupu a správě identit pomocí Azure Active Directory.
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | Pro dokončení stažení MySQL. </br> V několika oblastech může být stahování Přesměrováno na adresu URL CDN. V případě potřeby zkontrolujte, jestli je v případě potřeby také povolená adresa URL CDN.
|

## <a name="required-software"></a>Požadovaný software

**Komponenta** | **Požadavek** 
--- | ---
VMware vSphere PowerCLI | Pokud konfigurační server běží na virtuálním počítači VMware, měla by být nainstalovaná [verze PowerCLI 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) .
MYSQL | Je potřeba nainstalovat MySQL. Instalaci můžete provést ručně, nebo ji Site Recovery nainstalovat. (Další informace najdete v tématu [Konfigurace nastavení](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings) .)
|

## <a name="sizing-and-capacity-requirements"></a>Požadavky na velikost a kapacitu

Následující tabulka shrnuje požadavky na kapacitu pro konfigurační server. Pokud provádíte replikaci několika virtuálních počítačů VMware, Projděte si téma [požadavky na plánování kapacity](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) a spusťte [nástroj Plánovač nasazení služby Azure Site Recovery](../articles/site-recovery/site-recovery-deployment-planner.md).


**Procesor** | **Memory (Paměť)** | **Disk mezipaměti** | **Frekvence změny dat** | **Replikované počítače**
--- | --- | --- | --- | ---
8 vCPU<br/><br/> 2 sokety × 4 jádra \@ 2,5 GHz | 16 GB | 300 GB | 500 GB nebo méně | Počítače s < 100
12 vCPU<br/><br/> 2 SOCKS * 6 jader \@ 2,5 GHz | 18 GB | 600 GB | 500 GB AŽ 1 TB | 100 až 150 počítačů
16 vCPU<br/><br/> 2 SOCKS * 8 jader \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 – 200 počítačů
|

