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
ms.openlocfilehash: 1aaec104e9130eeef723c6505e04e3317271566b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234236"
---
**Požadavky na konfigurační a procesní server**


## <a name="hardware-requirements"></a>Požadavky na hardware

**Komponenta** | **Požadavek** 
--- | ---
Procesorová jádra | 8 
Paměť RAM | 16 GB
Počet disků | 3, včetně disku operačního systému, disku mezipaměti procesu a retenční jednotky pro navrácení služeb po selhání 
Volné místo na disku (mezipaměť procesního serveru) | 600 GB
Volné místo na disku (retenční disk) | 600 GB
 | 

## <a name="software-requirements"></a>Požadavky na software

**Komponenta** | **Požadavek** 
--- | ---
Operační systém | Windows Server 2012 R2 <br> Windows Server 2016
Národní prostředí operačního systému | Angličtina (en-*)
Role Windows Serveru | Nepovolujte tyto role: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V 
Zásady skupiny | Nepovolujte tyto zásady skupiny: <br> - Zabránit přístupu k příkazovému řádku. <br> - Zabránit přístupu k nástrojům pro úpravu registru. <br> - Logika důvěryhodnosti pro přílohy souborů. <br> - Zapněte spuštění skriptu. <br> [Další informace](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Žádné pre-existující výchozí webové stránky <br> - Žádné pre-existující webové stránky / aplikace poslech na portu 443 <br>- Povolit [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Povolit nastavení [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 
FIPS (Federální standardy pro zpracování informací) | Nepovoluje režim FIPS
|

## <a name="network-requirements"></a>Síťové požadavky

**Komponenta** | **Požadavek** 
--- | --- 
Typ IP adresy | Statická 
Porty | 443 (orchestrace řídicího kanálu)<br>9443 (přenos dat) 
Typ nic | VMXNET3 (pokud konfigurační server je VMware VM)
 |
**Přístup k internetu** (server potřebuje přístup k následujícím adresám URL, přímo nebo prostřednictvím proxy serveru):|
\*.backup.windowsazure.com | Používá se pro replikovaný přenos dat a koordinaci
\*.store.core.windows.net | Používá se pro replikovaný přenos dat a koordinaci
\*.blob.core.windows.net | Slouží k přístupu k účtu úložiště, který ukládá replikovaná data.
\*.hypervrecoverymanager.windowsazure.com | Používá se pro operace správy replikace a koordinaci
https:\//management.azure.com | Používá se pro operace správy replikace a koordinaci 
*.services.visualstudio.com | Používá se pro telemetrické účely (volitelné)
time.nist.gov | Slouží ke kontrole synchronizace času mezi systémem a globálním časem.
time.windows.com | Slouží ke kontrole synchronizace času mezi systémem a globálním časem.
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | Nastavení OVF vyžaduje přístup k těmto adresám URL. Používají se pro řízení přístupu a správu identit službou Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi  | Chcete-li dokončit MySQL ke stažení. </br> V několika oblastech může být stahování přesměrováno na adresu URL CDN. Ujistěte se, že adresa URL CDN je také na seznamu povolených, v případě potřeby.
|

## <a name="required-software"></a>Požadovaný software

**Komponenta** | **Požadavek** 
--- | ---
VMware vSphere PowerCLI | [PowerCLI verze 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) by měla být nainstalována, pokud je konfigurační server spuštěn na virtuálním počítači VMware VMware.
Mysql | MySQL by měla být nainstalována. Můžete nainstalovat ručně nebo site recovery můžete nainstalovat. (Další informace naleznete v [části Konfigurace nastavení)](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings)
|

## <a name="sizing-and-capacity-requirements"></a>Požadavky na dimenzování a kapacitu

Následující tabulka shrnuje požadavky na kapacitu konfiguračního serveru. Pokud replikujete více virtuálních počítačích VMware, přečtěte si [aspekty plánování kapacity](../articles/site-recovery/site-recovery-plan-capacity-vmware.md) a spusťte [nástroj Plánovač nasazení obnovení webu Azure](../articles/site-recovery/site-recovery-deployment-planner.md).


**Cpu** | **Paměti** | **Disk mezipaměti** | **Rychlost změny dat** | **Replikované počítače**
--- | --- | --- | --- | ---
8 virtuálních procesorů<br/><br/> 2 zásuvky * 4 \@ jádra 2,5 GHz | 16 GB | 300 GB | 500 GB nebo méně | < 100 strojů
12 virtuálních procesorů<br/><br/> 2 ponožky * \@ 6 jader 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 až 150 strojů
16 virtuálních procesorů<br/><br/> 2 ponožky * \@ 8 jader 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150 -200 strojů
|

