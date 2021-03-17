---
title: Příprava řešení Azure VMware na zotavení po havárii na Azure Site Recovery
description: Naučte se připravit servery řešení Azure VMware pro zotavení po havárii do Azure pomocí služby Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 8e77ede7b04c95bfd6b6b8f660c8d811e7434c0f
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395440"
---
# <a name="prepare-azure-vmware-solution-for-disaster-recovery-to-azure-site-recovery"></a>Příprava řešení Azure VMware na zotavení po havárii na Azure Site Recovery

Tento článek popisuje, jak připravit servery řešení Azure VMware pro zotavení po havárii do Azure pomocí služby [Azure Site Recovery](site-recovery-overview.md) Services. 

Toto je druhý kurz v řadě, ve kterém se dozvíte, jak nastavit zotavení po havárii do Azure pro virtuální počítače řešení Azure VMware. V prvním kurzu jsme [nastavili komponenty Azure](avs-tutorial-prepare-azure.md) potřebné pro zotavení po havárii řešení Azure VMware.


V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Připravte si účet na serveru vCenter nebo hostiteli ESXi vSphere, abyste mohli automatizovat zjišťování virtuálních počítačů.
> * Připravte účet na automatickou instalaci služby mobility na virtuální počítače VMware.
> * Přečtěte si požadavky a podporu k VMware serveru a VIRTUÁLNÍm počítačům.
> * Příprava na připojení k virtuálním počítačům Azure po převzetí služeb při selhání.

> [!NOTE]
> Kurzy ukazují nejjednodušší cestu k nasazení scénáře. V rámci možností používají jen výchozí možnosti a neuvádějí všechny varianty nastavení ani všechny cesty. Podrobné pokyny najdete v článku v části jak Site Recovery obsahu.

## <a name="before-you-start"></a>Než začnete

Ujistěte se, že jste připravili Azure, jak je popsáno v [prvním kurzu této série](avs-tutorial-prepare-azure.md).

## <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Site Recovery potřebuje přístup k serverům řešení Azure VMware pro:

- Automatické zjišťování virtuálních počítačů. Vyžaduje se alespoň účet jen pro čtení.
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení. Potřebujete účet, který může spouštět operace, jako jsou vytváření a odebírání disků a práce s virtuálními počítači.

Vytvořte účet následujícím způsobem:

1. Pokud chcete použít vyhrazený účet, vytvořte roli na úrovni vCenter. Roli pojmenujte například **Azure_Site_Recovery**.
2. Přiřaďte roli oprávnění uvedená v následující tabulce.
3. Vytvořte uživatele na serveru vCenter nebo hostiteli vSphere. Přiřaďte uživateli roli.

### <a name="vmware-account-permissions"></a>Oprávnění účtu VMware

**Úkol** | **Role/oprávnění** | **Podrobnosti**
--- | --- | ---
**Zjišťování virtuálních počítačů** | Alespoň uživatel jen pro čtení<br/><br/> Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **bez přístupu** s **podřízeným objektem rozšířit do podřízeného** objektu.
**Úplná replikace, převzetí služeb při selhání a navrácení služeb po obnovení** |  Vytvořte roli (Azure_Site_Recovery) s požadovanými oprávněními a pak ji přiřaďte uživateli nebo skupině VMware.<br/><br/> Objekt datového centra –> Rozšířit na podřízený objekt, role=Azure_Site_Recovery<br/><br/> Úložiště dat –> Přidělit prostor, procházet úložiště dat, operace se soubory nízké úrovně, odebrat soubor, aktualizovat soubory virtuálního počítače<br/><br/> Síť –> Přiřazení sítě<br/><br/> Prostředek –> Přiřadit virtuální počítač k fondu prostředků, migrovat vypnutý virtuální počítač, migrovat zapnutý virtuální počítač<br/><br/> Úlohy –> Vytvořit úlohu, aktualizovat úlohu<br/><br/> Virtuální počítač –> Konfigurace<br/><br/> Virtuální počítač –> Interakce –> zodpovědět dotazy, připojení zařízení, konfigurovat disk CD, konfigurovat disketu, vypnout, zapnout, instalace nástrojů VMware<br/><br/> Virtuální počítač –> Inventář –> Vytvořit, zaregistrovat, zrušit registraci<br/><br/> Virtuální počítač –> Zřizování –> Povolit stažení virtuálního počítače, povolit nahrávání souborů virtuálního počítače<br/><br/> Virtuální počítač –> Snímky –> Odebrat snímky | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **bez přístupu** s **podřízeným objektem rozšířit do podřízeného** objektu.

## <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na počítače, které chcete replikovat, bude nutné nainstalovat službu Mobility. Pokud povolíte replikaci počítače, může Site Recovery provést nabízenou instalaci této služby, nebo ji můžete nainstalovat ručně nebo pomocí instalačních nástrojů.

- V tomto kurzu provedeme instalaci služby Mobility pomocí nabízené instalace.
- U nabízené instalace musíte připravit účet, který služba Site Recovery může použít k získání přístupu k virtuálnímu počítači. Tento účet zadáte při nastavování zotavení po havárii v konzole Azure.

Připravte účet následujícím způsobem:

Připravte účet domény nebo místní účet s oprávněními k instalaci na virtuální počítač.

- **Virtuální počítače s Windows** : Pokud chcete instalaci provést na virtuální počítače s Windows a nepoužíváte účet domény, zakažte na místním počítači vzdálené řízení přístupu uživatele. Provedete to tak, že v registru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** přidáte položku DWORD **LocalAccountTokenFilterPolicy** s hodnotou 1.
- **Virtuální počítače s Linuxem** : Pokud chcete instalaci provést na virtuální počítače s Linuxem, připravte na zdrojovém serveru s Linuxem kořenový účet.


## <a name="check-vmware-requirements"></a>Kontrola požadavků na VMware

Ujistěte se, že servery a virtuální počítače VMware splňují požadavky.

1. Ověřte [verze softwaru](../azure-vmware/concepts-private-clouds-clusters.md#vmware-software-versions)řešení Azure VMware.
2. Ověřte [požadavky na server VMware](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers).
3. V případě virtuálních počítačů s Linuxem [zkontrolujte](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) požadavky na systém souborů a úložiště. 
4. Ověřte podporu [sítě](vmware-physical-azure-support-matrix.md#network) a [úložiště](vmware-physical-azure-support-matrix.md#storage) . 
5. Zkontrolujte podporované [sítě Azure](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [úložiště](vmware-physical-azure-support-matrix.md#azure-storage) a [výpočetní prostředí](vmware-physical-azure-support-matrix.md#azure-compute) po převzetí služeb při selhání.
6. Vaše virtuální počítače řešení Azure VMware, které replikete do Azure, musí splňovat [požadavky na virtuální počítače Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
7. V případě virtuálních počítačů se systémem Linux by měl být název zařízení nebo přípojný bod jedinečný. Zajistěte, aby v žádném ze dvou zařízení/přípojných bodů nebyly stejné názvy. Všimněte si, že název nerozlišuje velká a malá písmena. Například pojmenování dvou zařízení pro stejný virtuální počítač jako _zařízení1_ a _zařízení1_ není povoleno.


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Po převzetí služeb při selhání se můžete chtít připojit k virtuálním počítačům Azure ze sítě řešení Azure VMware.

Pokud se po převzetí služeb při selhání chcete připojit k virtuálním počítačům s Windows pomocí protokolu RDP, postupujte následovně:

- **Přístup k Internetu**. Před převzetím služeb při selhání Povolte protokol RDP na virtuálním počítači řešení Azure VMware. Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **Veřejný** profil a že v části **Brána Windows Firewall** > **Povolené aplikace** je pro všechny profily povolený protokol RDP.
- **Přístup k S2S (Site-to-site) VPN** :
    - Před převzetím služeb při selhání Povolte protokol RDP na virtuálním počítači řešení Azure VMware.
    - Protokol RDP by měl být povolený v **bráně Windows Firewall**  ->  **povolené aplikace a funkce** pro **domény a privátní** sítě.
    - Zkontrolujte, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Přečtěte si další informace](https://support.microsoft.com/kb/3031135).
- Při aktivaci převzetí služeb při selhání by na virtuálním počítači neměly být žádné čekající aktualizace Windows. V takovém případě se k virtuálnímu počítači nebudete moct přihlásit, dokud se aktualizace nedokončí.
- Po převzetí služeb při selhání na virtuálním počítači Azure s Windows zkontrolujte **diagnostiku spuštění** , kde se zobrazí snímek obrazovky virtuálního počítače. Pokud se nemůžete připojit, zkontrolujte, že je virtuální počítač spuštěný, a přečtěte si tyto [tipy pro řešení potíží](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Pokud se po převzetí služeb při selhání chcete připojit k virtuálním počítačům s Linuxem pomocí protokolu SSH, postupujte následovně:

- Na virtuálním počítači řešení Azure VMware před převzetím služeb při selhání ověřte, že je služba Secure Shell nastavená tak, aby se spouštěla automaticky při spuštění systému.
- Zkontrolujte, že pravidla brány firewall umožňují připojení SSH.
- Po převzetí služeb při selhání na virtuálním počítači Azure povolte příchozí připojení k portu SSH pro pravidla skupiny na virtuálním počítači, u kterého proběhlo převzetí služeb při selhání, a pro podsíť Azure, ke které je připojený.
- [Přidejte veřejnou IP adresu](./site-recovery-monitor-and-troubleshoot.md) pro tento virtuální počítač.
- Můžete zkontrolovat **diagnostiku spuštění** , kde se zobrazí snímek obrazovky virtuálního počítače.


## <a name="failback-requirements"></a>Požadavky na navrácení služeb po obnovení
Pokud máte v úmyslu navrátit služby po obnovení do cloudu řešení Azure VMware, je k dispozici několik [požadavků na navrácení služeb po obnovení](avs-tutorial-reprotect.md#before-you-begin). Můžete je připravit teď, ale nemusíte to. Po převzetí služeb při selhání do Azure se můžete připravit.




## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii](avs-tutorial-replication.md)
- Pokud budete replikovat víc virtuálních počítačů, [proveďte plánování kapacity](site-recovery-deployment-planner.md).
