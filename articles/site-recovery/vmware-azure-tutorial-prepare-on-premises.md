---
title: Příprava místních serverů VMware na zotavení po havárii virtuálních počítačů VMware do Azure | Microsoft Docs
description: Zjistěte, jak připravit místní servery VMware na zotavení po havárii do Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/08/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 07f62775c9286250d33635febe01dbad4362df12
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>Příprava místních serverů VMware na zotavení po havárii do Azure

V tomto kurzu se dozvíte, jak připravit místní infrastrukturu VMware v případě, že chcete replikovat virtuální počítače VMware do Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Příprava účtu na serveru vCenter nebo hostiteli vSphere ESXi pro automatizaci zjišťování virtuálních počítačů
> * Příprava účtu pro automatickou instalaci služby Mobility na virtuální počítače VMware
> * Kontrola požadavků na servery VMware
> * Kontrola požadavků na virtuální počítače VMware

V této sérii kurzů ukazujeme, jak pomocí služby Azure Site Recovery zálohovat jeden virtuální počítač. Pokud plánujete ochranu více virtuálních počítačů VMware, měli byste si pro replikaci VMware stáhnout [nástroj Plánovač nasazení](https://aka.ms/asr-deployment-planner). Tento nástroj shromažďuje informace o kompatibilitě virtuálních počítačů, discích jednotlivých virtuálních počítačů a četnosti změn dat na jednotlivých discích. Tento nástroj pokrývá i požadavky na šířku pásma sítě a infrastrukturu Azure potřebnou k úspěšné replikaci a testovacímu převzetí služeb při selhání. [Další informace](site-recovery-deployment-planner.md) o používání tohoto nástroje.

Tento kurz je druhou částí série. Ujistěte se, že jste [nastavili komponenty Azure](tutorial-prepare-azure.md), jak je popsáno v předchozím kurzu.

## <a name="prepare-an-account-for-automatic-discovery"></a>Příprava účtu pro automatické zjišťování

Site Recovery potřebuje přístup k serverům VMware z těchto důvodů:

- Automatické zjišťování virtuálních počítačů. Vyžaduje se alespoň účet jen pro čtení.
- Orchestrace replikace, převzetí služeb při selhání a navrácení služeb po obnovení. Potřebujete účet, který může spouštět operace, jako jsou vytváření a odebírání disků a práce s virtuálními počítači.

Vytvořte účet následujícím způsobem:

1. Pokud chcete použít vyhrazený účet, vytvořte roli na úrovni vCenter. Roli pojmenujte například **Azure_Site_Recovery**.
2. Přiřaďte roli oprávnění uvedená v následující tabulce.
3. Vytvořte uživatele na serveru vCenter nebo hostiteli vSphere. Přiřaďte uživateli roli.

### <a name="vmware-account-permissions"></a>Oprávnění účtu VMware

**Úkol** | **Role/oprávnění** | **Podrobnosti**
--- | --- | ---
**Zjišťování virtuálních počítačů** | Alespoň uživatel jen pro čtení<br/><br/> Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Pokud chcete omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **Žádný přístup** s objektem **Rozšířit na podřízený objekt**.
**Úplná replikace, převzetí služeb při selhání a navrácení služeb po obnovení** |  Vytvořte roli (Azure_Site_Recovery) s požadovanými oprávněními a pak ji přiřaďte uživateli nebo skupině VMware.<br/><br/> Objekt datového centra –> Rozšířit na podřízený objekt, role=Azure_Site_Recovery<br/><br/> Úložiště dat –> Přidělit prostor, procházet úložiště dat, operace se soubory nízké úrovně, odebrat soubor, aktualizovat soubory virtuálního počítače<br/><br/> Síť –> Přiřazení sítě<br/><br/> Prostředek –> Přiřadit virtuální počítač k fondu prostředků, migrovat vypnutý virtuální počítač, migrovat zapnutý virtuální počítač<br/><br/> Úlohy –> Vytvořit úlohu, aktualizovat úlohu<br/><br/> Virtuální počítač –> Konfigurace<br/><br/> Virtuální počítač –> Interakce –> zodpovědět dotazy, připojení zařízení, konfigurovat disk CD, konfigurovat disketu, vypnout, zapnout, instalace nástrojů VMware<br/><br/> Virtuální počítač –> Inventář –> Vytvořit, zaregistrovat, zrušit registraci<br/><br/> Virtuální počítač –> Zřizování –> Povolit stažení virtuálního počítače, povolit nahrávání souborů virtuálního počítače<br/><br/> Virtuální počítač –> Snímky –> Odebrat snímky | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Pokud chcete omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **Žádný přístup** s objektem **Rozšířit na podřízený objekt**.

## <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na virtuálním počítači, který chcete replikovat, musí být nainstalovaná služba Mobility. Site Recovery tuto službu nainstaluje automaticky, když u virtuálního počítače povolíte replikaci. U automatické instalace musíte připravit účet, který služba Site Recovery použije k získání přístupu k virtuálnímu počítači. Tento účet zadáte při nastavování zotavení po havárii v konzole Azure.

1. Připravte účet domény nebo místní účet s oprávněními k instalaci na virtuální počítač.
2. Pokud chcete instalaci provést na virtuální počítače s Windows a nepoužíváte účet domény, zakažte na místním počítači vzdálené řízení přístupu uživatele.
   - V registru pod položku **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** přidejte položku DWORD **LocalAccountTokenFilterPolicy** s hodnotou 1.
3. Pokud chcete instalaci provést na virtuální počítače s Linuxem, připravte na zdrojovém serveru s Linuxem kořenový účet.


## <a name="check-vmware-server-requirements"></a>Kontrola požadavků na servery VMware

Ujistěte se, že servery VMware splňují následující požadavky.

**Komponenta** | **Požadavek**
--- | ---
**Server vCenter** | vCenter 6.5, 6.0 nebo 5.5
**Hostitel vSphere** | vSphere 6.5, 6.0, 5.5

## <a name="check-vmware-vm-requirements"></a>Kontrola požadavků na virtuální počítač VMware

Ujistěte se, že virtuální počítač splňuje požadavky Azure uvedené v následující tabulce.

**Požadavek na virtuální počítač** | **Podrobnosti**
--- | ---
**Velikost disku s operačním systémem** | Až 2 048 GB
**Počet disků s operačním systémem** | 1
**Počet datových disků** | 64 nebo méně
**Velikost datového virtuálního pevného disku** | Až 4 095 GB
**Síťové adaptéry** | Podporuje se více adaptérů
**Sdílený virtuální pevný disk** | Nepodporuje se
**Disk FC** | Nepodporuje se
**Formát pevného disku** | VHD nebo VHDX.<br/><br/> Přestože Azure v současné době formát VHDX nepodporuje, Site Recovery při převzetí služeb při selhání do Azure automaticky převede formát VHDX na VHD. Když provedete navrácení služeb po obnovení zpět na místní virtuální počítače, bude se nadále používat formát VHDX.
**Bitlocker** | Nepodporuje se. Zakažte ho, než povolíte replikaci virtuálního počítače.
**Název virtuálního počítače** | 1 až 63 znaků.<br/><br/> Pouze písmena, číslice a pomlčky. Název virtuálního počítače musí začínat a končit písmenem nebo číslicí.
**Typ virtuálního počítače** | Generace 1 – Linux nebo Windows<br/><br/>Generace 2 – Pouze Windows

Na virtuálním počítači musí být také podporovaný operační systém. Úplný seznam podporovaných verzí najdete v [matici podpory pro VMware a fyzické servery](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Příprava připojení k virtuálním počítačům Azure po převzetí služeb při selhání

Ve scénáři převzetí služeb při selhání se možná budete chtít připojit ke svým replikovaným virtuálním počítačům v Azure ze své místní sítě.

Pokud se po převzetí služeb při selhání chcete připojit k virtuálním počítačům s Windows pomocí protokolu RDP, postupujte následovně:

1. Pokud chcete mít přístup přes internet, před převzetím služeb při selhání povolte na místním virtuálním počítači protokol RDP. Ujistěte se, že jsou přidaná pravidla TCP a UDP pro **Veřejný** profil a že v části **Brána Windows Firewall** > **Povolené aplikace** je povolený protokol RDP pro všechny profily.
2. Pokud chcete mít přístup přes síť VPN typu Site-to-Site, povolte na místním počítači protokol RDP. Protokol RDP musí být povolený v části **Brána Windows Firewall** -> **Povolené aplikace a funkce** pro **doménovou a privátní** síť.
   Zkontrolujte, že je zásada SAN operačního systému nastavená na **OnlineAll**. [Další informace](https://support.microsoft.com/kb/3031135). Při aktivaci převzetí služeb při selhání by na virtuálním počítači neměly být žádné čekající aktualizace Windows. Jinak se nebudete moci k virtuálnímu počítači přihlásit, dokud se aktualizace nedokončí.
3. Po převzetí služeb při selhání na virtuálním počítači Azure s Windows zkontrolujte **diagnostiku spuštění**, kde se zobrazí snímek obrazovky virtuálního počítače. Pokud se nemůžete připojit, zkontrolujte, že je virtuální počítač spuštěný, a přečtěte si tyto [tipy pro řešení potíží](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Pokud se po převzetí služeb při selhání chcete připojit k virtuálním počítačům s Linuxem pomocí protokolu SSH, postupujte následovně:

1. Před provedením převzetí služeb při selhání zkontrolujte, že je na místním počítači nastavené automatické spuštění služby Secure Shell při spuštění systému. Zkontrolujte, že pravidla brány firewall umožňují připojení SSH.

2. Po převzetí služeb při selhání na virtuálním počítači Azure povolte příchozí připojení k portu SSH pro pravidla skupiny na virtuálním počítači, u kterého proběhlo převzetí služeb při selhání, a pro podsíť Azure, ke které je připojený.
   [Přidejte veřejnou IP adresu](site-recovery-monitoring-and-troubleshooting.md) pro tento virtuální počítač. Můžete zkontrolovat **diagnostiku spuštění**, kde se zobrazí snímek obrazovky virtuálního počítače.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii do Azure pro virtuální počítače VMware](vmware-azure-tutorial.md)
