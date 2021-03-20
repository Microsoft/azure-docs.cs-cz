---
title: Správa serverů VMware vCenter Server v Azure Site Recovery
description: Tento článek popisuje, jak přidat a spravovat VMware vCenter pro zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84692515"
---
# <a name="manage-vmware-vcenter-server"></a>Správa vCenter Server VMware

Tento článek shrnuje akce správy na vCenter Server VMware v [Azure Site Recovery](site-recovery-overview.md).

## <a name="verify-prerequisites-for-vcenter-server"></a>Ověření předpokladů pro vCenter Server

Požadavky na servery a virtuální počítače vCenter během zotavení po havárii virtuálních počítačů VMware do Azure jsou uvedené v části [support Matrix](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="set-up-an-account-for-automatic-discovery"></a>Nastavení účtu pro automatické zjišťování

Když nastavíte zotavení po havárii pro místní virtuální počítače VMware, Site Recovery potřebuje přístup k hostiteli vCenter Server/vSphere. Procesový Server Site Recovery pak může automaticky zjistit virtuální počítače a v případě potřeby je převzít. Ve výchozím nastavení se procesový Server spouští na Site Recovery konfiguračním serveru. Přidejte účet pro konfigurační server pro připojení k hostiteli vCenter Server/vSphere následujícím způsobem:

1. Přihlaste se ke konfiguračnímu serveru.
1. Pomocí zástupce na ploše otevřete nástroj konfiguračního serveru (_cspsconfigtool.exe_).
1. Na kartě **Spravovat účet** klikněte na **Přidat účet**.

   ![Přidat účet](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Zadejte podrobnosti účtu a kliknutím na tlačítko **OK** ho přidejte. Účet by měl mít tato oprávnění shrnutá v tabulce oprávnění účtu.

   > [!NOTE]
   > Synchronizace informací o účtu pomocí Site Recovery trvá přibližně 15 minut.

### <a name="account-permissions"></a>Oprávnění účtu

|**Úkol** | **Zohledňují** | **Oprávnění** | **Podrobnosti**|
|--- | --- | --- | ---|
|**Zjišťování/migrace virtuálních počítačů (bez navrácení služeb po obnovení)** | Alespoň účet uživatele jen pro čtení. | Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **bez přístupu** s **podřízeným objektem rozšířit do podřízeného** objektu.|
|**Replikace/převzetí služeb při selhání** | Alespoň účet uživatele jen pro čtení. | Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte k podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **bez přístupu** s **podřízeným objektem rozšířit k podřízenému** objektu.<br/><br/> Užitečné pro účely migrace, ale ne pro úplnou replikaci, převzetí služeb při selhání a navrácení služeb po obnovení|
|**Replikace/převzetí služeb při selhání** | Doporučujeme vytvořit roli (AzureSiteRecoveryRole) s požadovanými oprávněními a pak přiřadit roli uživateli nebo skupině VMware. | Objekt datového centra – > rozšířit na podřízený objekt, role = AzureSiteRecoveryRole<br/><br/> Úložiště dat –> Přidělit prostor, procházet úložiště dat, operace se soubory nízké úrovně, odebrat soubor, aktualizovat soubory virtuálního počítače<br/><br/> Síť –> Přiřazení sítě<br/><br/> Prostředek –> Přiřadit virtuální počítač k fondu prostředků, migrovat vypnutý virtuální počítač, migrovat zapnutý virtuální počítač<br/><br/> Úlohy –> Vytvořit úlohu, aktualizovat úlohu<br/><br/> Virtuální počítač –> Konfigurace<br/><br/> Virtuální počítač –> Interakce –> zodpovědět dotazy, připojení zařízení, konfigurovat disk CD, konfigurovat disketu, vypnout, zapnout, instalace nástrojů VMware<br/><br/> Virtuální počítač –> Inventář –> Vytvořit, zaregistrovat, zrušit registraci<br/><br/> Virtuální počítač –> Zřizování –> Povolit stažení virtuálního počítače, povolit nahrávání souborů virtuálního počítače<br/><br/> Virtuální počítač –> Snímky –> Odebrat snímky | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **bez přístupu** s **podřízeným objektem rozšířit do podřízeného** objektu.|

## <a name="add-vmware-server-to-the-vault"></a>Přidání serveru VMware do trezoru

Když nakonfigurujete zotavení po havárii pro místní virtuální počítače VMware, přidáte hostitele vCenter Server/vSphere, na kterém zjišťujíte virtuální počítače Site Recovery trezoru následujícím způsobem:

1. V trezoru > **Site Recovery**  >  **konfiguračních** serverech infrastruktury otevřete konfigurační server.
1. Na stránce **Podrobnosti** klikněte na **vCenter**.
1. V části **Přidat vCenter** zadejte popisný název hostitele vSphere nebo vCenter Server.
1. Zadejte IP adresu nebo plně kvalifikovaný název domény serveru.
1. Pokud vaše servery VMware nejsou konfigurované k naslouchání požadavkům na jiném portu, ponechte port 443.
1. Vyberte účet, který se používá pro připojení k serveru VMware vCenter nebo vSphere ESXi. Pak klikněte na **OK**.

## <a name="modify-credentials"></a>Upravit přihlašovací údaje

V případě potřeby můžete přihlašovací údaje používané pro připojení k hostiteli vCenter Server nebo vSphere upravit následujícím způsobem:

1. Přihlaste se ke konfiguračnímu serveru.
1. Pomocí zástupce na ploše otevřete nástroj konfiguračního serveru (_cspsconfigtool.exe_).
1. Na kartě **Spravovat účet** klikněte na **Přidat účet** .

   ![Přidat účet](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Zadejte podrobnosti o novém účtu a klikněte na **OK**. Účet potřebuje oprávnění uvedená v tabulce [oprávnění účtu](#account-permissions) .
1. V trezoru > **Site Recovery**  >  **konfiguracích** infrastruktury, otevřete konfigurační server.
1. V **podrobnostech** klikněte na **aktualizovat server**.
1. Po dokončení úlohy aktualizovat server vyberte vCenter Server.
1. V části **Souhrn** vyberte nově přidaný účet v **účtu hostitele vCenter Server/vSphere** a klikněte na **Uložit**.

   ![Upravit – účet](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Odstranit vCenter Server

1. V trezoru > **Site Recovery**  >  **konfiguracích** infrastruktury, otevřete konfigurační server.
1. Na stránce **Podrobnosti** vyberte Server vCenter.
1. Klikněte na tlačítko **Odstranit** .

   ![Odstranit účet](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Úprava IP adresy a portu

Můžete upravit IP adresu vCenter Server nebo porty používané pro komunikaci mezi serverem a Site Recovery. Ve výchozím nastavení Site Recovery přistupuje k informacím o hostiteli vCenter Server/vSphere prostřednictvím portu 443.

1. V trezoru > **Site Recovery**  >  **konfiguračním** serveru infrastruktury klikněte na server konfigurace, ke kterému se vCenter Server přidá.
1. V **vCenter servery** klikněte na vCenter Server, které chcete upravit.
1. V části **Souhrn** aktualizujte IP adresu a port a změny uložte.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. Aby se změny projevily v platnosti, počkejte 15 minut nebo [Aktualizujte konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-vms-to-a-new-server"></a>Migrace všech virtuálních počítačů na nový server

Pokud chcete migrovat všechny virtuální počítače tak, aby používaly nové vCenter Server, stačí aktualizovat IP adresu přiřazenou vCenter Server. Nepřidávejte další účet VMware, protože by to mohlo vést k duplicitním položkám. Aktualizujte adresu následujícím způsobem:

1. V trezoru > **Site Recovery**  >  **konfiguračním** serveru infrastruktury klikněte na server konfigurace, ke kterému se vCenter Server přidá.
1. V části **servery vCenter** klikněte na vCenter Server, ze kterého chcete provést migraci.
1. V části **Souhrn** aktualizujte IP adresu na novou vCenter Server a uložte změny.
1. Jakmile se IP adresa aktualizuje, Site Recovery začne přijímat informace o zjišťování virtuálních počítačů z nového vCenter Server. To nemá vliv na probíhající aktivity replikace.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Migrace několika virtuálních počítačů na nový server

Pokud chcete migrovat jenom několik z vašich replikačních virtuálních počítačů na nový vCenter Server, udělejte toto:

1. [Přidejte](#add-vmware-server-to-the-vault) novou vCenter Server ke konfiguračnímu serveru.
1. [Zakáže replikaci](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) pro virtuální počítače, které se přesunou na nový server.
1. V prostředí VMware migrujte virtuální počítače do nové vCenter Server.
1. [Povolte znovu replikaci](vmware-azure-tutorial.md#enable-replication) migrovaných virtuálních počítačů a vyberte novou vCenter Server.

## <a name="migrate-most-vms-to-a-new-server"></a>Migrace většiny virtuálních počítačů na nový server

Pokud je počet virtuálních počítačů, které chcete migrovat do nového vCenter Server, vyšší než počet virtuálních počítačů, které zůstanou na původním vCenter Server, udělejte toto:

1. [Aktualizujte IP adresu](#modify-the-ip-address-and-port) přiřazenou k vCenter Server v nastavení konfiguračního serveru na adresu nového vCenter Server.
1. [Zakažte replikaci](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) pro několik virtuálních počítačů, které zůstávají na starém serveru.
1. [Přidejte starou vCenter Server](#add-vmware-server-to-the-vault) a jeho IP adresu ke konfiguračnímu serveru.
1. [Znovu povolte replikaci](vmware-azure-tutorial.md#enable-replication) pro virtuální počítače, které zůstávají na starém serveru.

## <a name="next-steps"></a>Další kroky

Pokud máte nějaké problémy, přečtěte si téma [řešení potíží se selháním zjišťování vCenter Server](vmware-azure-troubleshoot-vcenter-discovery-failures.md).
