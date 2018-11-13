---
title: Požadavky na konfiguraci serveru pro zotavení po havárii VMware do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje podporu a požadavky na nasazení konfiguračního serveru pro zotavení po havárii VMware do Azure pomocí Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/11/2018
ms.author: raynew
ms.openlocfilehash: 85484c0d4c1838e158fe388afdfcbebc6de2f289
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571569"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Požadavky na konfiguraci serveru pro zotavení po havárii VMware do Azure

Při použití se nasadíte místní konfigurační server [Azure Site Recovery](site-recovery-overview.md) pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure.

- Konfigurace serveru souřadnice komunikaci mezi místní VMware a Azure. Spravuje taky data replikace.
- Konfigurace serveru souřadnice komunikaci mezi místní VMware a Azure. Spravuje taky data replikace.
- [Další informace](vmware-azure-architecture.md) o součásti konfiguračního serveru a procesů.

## <a name="configuration-server-deployment"></a>Nasazení konfiguračního serveru

Pro zotavení po havárii virtuálních počítačů VMware do Azure nasazení konfiguračního serveru jako virtuálního počítače VMware.

- Site Recovery poskytuje šablonu OVA, kterou můžete stáhnout z webu Azure portal a importovat do vCenter serveru a nastavit konfigurační server virtuálního počítače.
- Při nasazení konfiguračního serveru pomocí šablony pro soubory OVA virtuální počítač automaticky splňuje požadavky uvedené v tomto článku.
- Důrazně doporučujeme, abyste nastavili konfiguračního serveru pomocí šablony pro soubory OVA. Nicméně pokud nastavování zotavení po havárii pro virtuální počítače VMware a nejde použít šablonu vajíčka, můžete nasadit konfigurační server pomocí [těchto pokynů](physical-azure-set-up-source.md).
- Pokud nasazujete konfiguračního serveru pro zotavení po havárii místních fyzických počítačů do Azure, postupujte podle pokynů v [v tomto článku](physical-azure-set-up-source.md). 


## <a name="hardware-requirements"></a>Požadavky na hardware

**Komponenta** | **Požadavek** 
--- | ---
Procesorová jádra | 8 
Paměť RAM | 16 GB
Počet disků | 3, včetně operačního systému disku, disk mezipaměti procesového serveru a jednotky pro uchovávání dat pro navrácení služeb po obnovení 
Volného místa na disku (mezipaměť procesového serveru) | 600 GB
Volné místo na disku (disk pro uchování) | 600 GB

## <a name="software-requirements"></a>Požadavky na software

**Komponenta** | **Požadavek** 
--- | ---
Operační systém | Windows Server 2012 R2 <br> Windows Server 2016
Národní prostředí operačního systému | Angličtina (en-us)
Role Windows Serveru | Nepovolí pracovníci v těchto rolích: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V 
Zásady skupiny | Nepovolí tyto zásady skupiny: <br> -Zabránit přístupu do příkazového řádku. <br> -Zabránit přístupu k nástrojům pro úpravu registru. <br> – Logika důvěryhodnosti pro přiložené soubory. <br> -Zapnutí provádění skriptů. <br> [Další informace](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Žádné dříve existující výchozí web <br> -Žádné stávající web/aplikace naslouchá na portu 443 <br>-Aktivovat [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivovat [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) nastavení 

## <a name="network-requirements"></a>Síťové požadavky

**Komponenta** | **Požadavek** 
--- | --- 
Typ IP adresy | Statická 
Přístup k internetu | Server potřebuje přístup k těmto adresám URL (přímo nebo prostřednictvím proxy serveru): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> -https:\//management.azure.com <br> -*. services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF potřebuje také přístup k následujícím adresám URL: <br> -https:\//login.microsoftonline.com <br> -https:\//secure.aadcdn.microsoftonline-p.com <br> -https:\//login.live.com  <br> -https:\//auth.gfx.ms <br> -https:\//graph.windows.net <br> -https:\//login.windows.net <br> -https:\//www.live.com <br> -https:\//www.microsoft.com <br> -https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Porty | 443 (orchestrace řídicího kanálu)<br>9443 (přenos dat) 
Typ NIC | VMXNET3 (pokud jde o virtuální počítač VMware)

## <a name="required-software"></a>Požadovaný software

**Komponenta** | **Požadavek** 
--- | ---
VMware vSphere PowerCLI. | [Nástroj PowerCLI verze 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) by měly být nainstalovány, pokud je konfigurační Server běží na virtuálním počítači VMware.
MYSQL | Je třeba nainstalovat MySQL. Můžete nainstalovat ručně nebo ji můžete nainstalovat Site Recovery.

## <a name="sizing-and-capacity-requirements"></a>Dimenzování a požadavky na kapacitu

Následující tabulka shrnuje požadavky na kapacitu pro konfigurační server. Pokud replikujete více virtuálních počítačů VMware, měli byste zkontrolovat [důležité informace o plánování kapacity](site-recovery-plan-capacity-vmware.md)a spusťte [Azure Site Recovery Deployment Planner](site-recovery-deployment-planner.md) nástroj pro VMWare replication.read 

**Komponenta** | **Požadavek** 
--- | ---
**CPU** | **Paměť** | **Disk mezipaměti** | **Frekvence změny dat** | **Replikované počítače**
--- | --- | --- | --- | ---
8 virtuálních CPU<br/><br/> sokety 2 * 4 jádra \@ 2,5 GHz | 16 GB | 300 GB | 500 GB nebo méně | Les než 100 počítačů
12 virtuálních procesorů<br/><br/> 2 socks * 6 jader \@ 2,5 GHz | 18 GB | 600 GB | 500 GB AŽ 1 TB | 100 až 150 počítačů
16 virtuálních procesorů<br/><br/> 2 socks * 8 jader \@ 2,5 GHz | 32 GB | 1 TB | 1 – 2 TB | 150 až 200 počítačů



## <a name="next-steps"></a>Další postup
Nastavení zotavení po havárii [virtuálních počítačů VMware](vmware-azure-tutorial.md) do Azure.
