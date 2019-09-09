---
title: Požadavky na konfigurační server pro zotavení po havárii VMware do Azure s Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje podporu a požadavky při nasazování konfiguračního serveru pro zotavení po havárii VMware do Azure pomocí Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 86fa817128dc89eb97bee18f4f8a6de1f650c265
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814296"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Požadavky na konfigurační server pro zotavení po havárii VMware do Azure

Místní konfigurační server nasadíte při použití [Azure Site Recovery](site-recovery-overview.md) k zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure.

- Konfigurační server koordinuje komunikaci mezi místními VMware a Azure. Také spravuje replikaci dat.
- [Přečtěte si další informace](vmware-azure-architecture.md) o součástech a procesech konfiguračního serveru.

## <a name="configuration-server-deployment"></a>Nasazení konfiguračního serveru

Pro zotavení po havárii virtuálních počítačů VMware do Azure nasadíte konfigurační server jako virtuální počítač VMware.

- Site Recovery poskytuje šablonu vajíček, kterou stáhnete z Azure Portal a naimportujte do vCenter Server k nastavení virtuálního počítače konfiguračního serveru.
- Když nasadíte konfigurační server pomocí šablony vajíček, virtuální počítač automaticky splňuje požadavky uvedené v tomto článku.
- Důrazně doporučujeme, abyste nastavili konfigurační server pomocí šablony vajíček. Pokud však nastavujete zotavení po havárii pro virtuální počítače VMware a nemůžete použít šablonu vajíček, můžete konfigurační server nasadit pomocí uvedených [pokynů](physical-azure-set-up-source.md).
- Pokud nasazujete konfigurační server pro zotavení po havárii místních fyzických počítačů do Azure, postupujte podle pokynů v [tomto článku](physical-azure-set-up-source.md). 


## <a name="hardware-requirements"></a>Hardwarové požadavky

**Komponenta** | **Požadavek** 
--- | ---
Procesorová jádra | 8 
Paměť RAM | 16 GB
Počet disků | 3, včetně disku operačního systému, disku mezipaměti procesového serveru a jednotky pro uchovávání pro navrácení služeb po obnovení 
Volné místo na disku (mezipaměť procesového serveru) | 600 GB
Volné místo na disku (disk pro uchovávání) | 600 GB

## <a name="software-requirements"></a>Požadavky na software

**Komponenta** | **Požadavek** 
--- | ---
Operační systém | Windows Server 2012 R2 <br> Windows Server 2016
Národní prostředí operačního systému | Angličtina (en-us)
Role Windows Serveru | Nepovolujte tyto role: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V 
Zásady skupiny | Nepovolujte tyto zásady skupiny: <br> – Zabraňte přístupu k příkazovému řádku. <br> – Zabraňte přístup k nástrojům pro úpravu registru. <br> – Logika vztahu důvěryhodnosti pro přílohy souborů. <br> -Zapnout provádění skriptu. <br> [Víc se uč](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Žádný stávající výchozí web <br> -Žádný existující web nebo aplikace nenaslouchá na portu 443. <br>-Povolit [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Povolit nastavení [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 

## <a name="network-requirements"></a>Síťové požadavky

**Komponenta** | **Požadavek** 
--- | --- 
Typ IP adresy | Static 
Přístup k internetu | Server potřebuje přístup k těmto adresám URL (přímo nebo prostřednictvím proxy serveru): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> -https:\//Management.Azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF také potřebuje přístup k následujícím adresám URL: <br> -https:\//Login.microsoftonline.com <br> -https:\//Secure.aadcdn.microsoftonline-p.com <br> -https:\//Login.Live.com  <br> -https:\//auth.gfx.MS <br> -https:\//Graph.Windows.NET <br> -https:\//Login.Windows.NET <br> -https:\//www.Live.com <br> -https:\//www.Microsoft.com <br> -https:\//dev.MySQL.com/get/downloads/MySQLInstaller/MySQL-Installer-Community-5.7.20.0.msi 
Porty | 443 (orchestrace řídicího kanálu)<br>9443 (přenos dat) 
Typ síťové karty | VMXNET3 (Pokud je konfigurační server virtuálním počítačem VMware)

## <a name="required-software"></a>Požadovaný software

**Komponenta** | **Požadavek** 
--- | ---
VMware vSphere PowerCLI | Pokud konfigurační server běží na virtuálním počítači VMware, měla by být nainstalovaná [verze PowerCLI 6,0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) .
MYSQL | Je potřeba nainstalovat MySQL. Instalaci můžete provést ručně, nebo ji Site Recovery nainstalovat.

## <a name="sizing-and-capacity-requirements"></a>Požadavky na velikost a kapacitu

Následující tabulka shrnuje požadavky na kapacitu pro konfigurační server. Pokud provádíte replikaci několika virtuálních počítačů VMware, měli byste si projít [požadavky na plánování kapacity](site-recovery-plan-capacity-vmware.md)a spustit nástroj [Plánovač nasazení služby Azure Site Recovery](site-recovery-deployment-planner.md) pro replikaci VMware. Přečtěte si 

**Komponenta** | **Požadavek** 
--- | ---

| **CPU** | **Paměť** | **Disk mezipaměti** | **Frekvence změny dat** | **Replikované počítače** |
| --- | --- | --- | --- | --- |
| 8 vCPU<br/><br/> 2 sokety × 4 jádra \@ 2,5 GHz | 16 GB | 300 GB | 500 GB nebo méně | Les než 100 počítačů |
| 12 vCPU<br/><br/> 2 SOCKS * 6 jader \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 až 150 počítačů |
| 16 vCPU<br/><br/> 2 SOCKS * 8 jader \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 počítačů | 



## <a name="next-steps"></a>Další kroky
Nastavte zotavení po havárii [virtuálních počítačů VMware](vmware-azure-tutorial.md) do Azure.
