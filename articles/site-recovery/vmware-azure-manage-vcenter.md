---
title: Správa serverů vCenter VMware v Azure Site Recovery
description: Tento článek popisuje, jak přidat a spravovat vMware vCenter pro zotavení po havárii virtuálních počítačů VMware do Azure s Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257261"
---
# <a name="manage-vmware-vcenter-server"></a>Správa serveru vCenter vMware

Tento článek shrnuje akce správy na serveru VMware vCenter v [Azure Site Recovery](site-recovery-overview.md).

## <a name="verify-prerequisites-for-vcenter-server"></a>Ověření požadavků pro server vCenter

Předpoklady pro servery vCenter a virtuální počítače během zotavení po havárii virtuálních počítačích VMware do Azure jsou uvedeny v [matici podpory](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="set-up-an-account-for-automatic-discovery"></a>Nastavení účtu pro automatické zjišťování

Když nastavíte zotavení po havárii pro místní virtuální počítačová zařízení VMware, site recovery potřebuje přístup k hostiteli vCenter Server/vSphere. Server procesu obnovení webu pak můžete automaticky zjišťovat virtuální chod a převzetí služeb při selhání je podle potřeby. Ve výchozím nastavení je procesový server spuštěn na konfiguračním serveru Site Recovery. Přidejte účet pro konfigurační server pro připojení k hostiteli vCenter Server/vSphere následujícím způsobem:

1. Přihlaste se ke konfiguračnímu serveru.
1. Otevřete nástroj konfiguračního serveru _(cspsconfigtool.exe_) pomocí zástupce plochy.
1. Na kartě **Spravovat účet** klikněte na **Přidat účet**.

   ![přidat-účet](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Zadejte podrobnosti o účtu a kliknutím na **OK** je přidejte. Účet by měl mít sumarizované oprávnění v tabulce oprávnění účtu.

   > [!NOTE]
   > Synchronizace informací o účtu s site recovery trvá přibližně 15 minut.

### <a name="account-permissions"></a>Oprávnění účtu

|**Úkol** | **Účet** | **Oprávnění** | **Podrobnosti**|
|--- | --- | --- | ---|
|**Zjišťování/migrace virtuálního zařízení (bez navrácení služeb po selhání)** | Alespoň uživatelský účet jen pro čtení. | Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte roli **Žádný přístup** s **objektem Propagate podřízenému** objektu, podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě).|
|**Replikace/převzetí služeb při selhání** | Alespoň uživatelský účet jen pro čtení. | Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte roli **Žádný přístup** s **objektem Propagate podřízenému** objektu podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě).<br/><br/> Užitečné pro účely migrace, ale ne úplnou replikaci, převzetí služeb při selhání, navrácení služeb po selhání.|
|**Replikace/převzetí služeb při selhání/navrácení služeb po selhání** | Doporučujeme vytvořit roli (AzureSiteRecoveryRole) s požadovanými oprávněními a pak roli přiřadit uživateli nebo skupině VMware. | Objekt datového centra – > rozšíří se na podřízený objekt, role=AzureSiteRecoveryRole<br/><br/> Úložiště dat –> Přidělit prostor, procházet úložiště dat, operace se soubory nízké úrovně, odebrat soubor, aktualizovat soubory virtuálního počítače<br/><br/> Síť –> Přiřazení sítě<br/><br/> Prostředek –> Přiřadit virtuální počítač k fondu prostředků, migrovat vypnutý virtuální počítač, migrovat zapnutý virtuální počítač<br/><br/> Úlohy –> Vytvořit úlohu, aktualizovat úlohu<br/><br/> Virtuální počítač –> Konfigurace<br/><br/> Virtuální počítač –> Interakce –> zodpovědět dotazy, připojení zařízení, konfigurovat disk CD, konfigurovat disketu, vypnout, zapnout, instalace nástrojů VMware<br/><br/> Virtuální počítač –> Inventář –> Vytvořit, zaregistrovat, zrušit registraci<br/><br/> Virtuální počítač –> Zřizování –> Povolit stažení virtuálního počítače, povolit nahrávání souborů virtuálního počítače<br/><br/> Virtuální počítač –> Snímky –> Odebrat snímky | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte roli **Žádný přístup** s **objektem Propagate podřízenému** objektu, podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě).|

## <a name="add-vmware-server-to-the-vault"></a>Přidání serveru VMware do úložiště

Když nastavíte zotavení po havárii pro místní virtuální počítačové sítě VMware, přidáte hostitele vCenter Server/vSphere, na kterém zjišťujete virtuální počítač, do trezoru site recovery takto:

1. V trezoru > konfigurace **infrastruktury** > obnovení**sítě Severs**otevřete konfigurační server.
1. Na stránce **Podrobnosti** klikněte na **vCenter**.
1. V **poli Přidat vCenter**zadejte popisný název hostitele vSphere nebo serveru vCenter.
1. Zadejte adresu IP nebo vícenežnatovku serveru.
1. Pokud vaše servery VMware nejsou konfigurované k naslouchání požadavkům na jiném portu, ponechte port 443.
1. Vyberte účet použitý pro připojení k serveru VMware vCenter nebo vSphere ESXi. Pak klikněte na **OK**.

## <a name="modify-credentials"></a>Upravit pověření

V případě potřeby můžete upravit pověření použitá pro připojení k hostiteli vCenter Server/vSphere následujícím způsobem:

1. Přihlaste se ke konfiguračnímu serveru.
1. Otevřete nástroj konfiguračního serveru _(cspsconfigtool.exe_) pomocí zástupce plochy.
1. Na kartě **Spravovat účet** klikněte na **Přidat účet.**

   ![přidat-účet](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Zadejte podrobnosti o novém účtu a klepněte na tlačítko **OK**. Účet potřebuje oprávnění uvedená v tabulce [oprávnění účtu.](#account-permissions)
1. V trezoru > konfigurace **infrastruktury** > obnovení**sítě Severs**otevřete konfigurační server.
1. V **části Podrobnosti**klepněte na tlačítko **Aktualizovat server**.
1. Po dokončení úlohy aktualizovat server vyberte server vCenter.
1. V **části Souhrn**vyberte nově přidaný účet v **hostitelském účtu vCenter/vSphere**a klepněte na tlačítko **Uložit**.

   ![upravit účet](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Odstranění serveru vCenter

1. V trezoru > konfigurace **infrastruktury** > obnovení**sítě Severs**otevřete konfigurační server.
1. Na stránce **Podrobnosti** vyberte server vCenter.
1. Klikněte na tlačítko **Odstranit.**

   ![odstranit účet](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Změna adresy IP a portu

Můžete upravit IP adresu serveru vCenter nebo porty používané pro komunikaci mezi serverem a site recovery. Ve výchozím nastavení přistupuje site recovery k informacím o hostiteli serveru vCenter/vSphere prostřednictvím portu 443.

1. V trezoru >**konfiguračních serverech** **infrastruktury** > obnovení sítě klepněte na konfigurační server, na který je server vCenter přidán.
1. Na **serverech vCenter**klikněte na server vCenter, který chcete upravit.
1. V **souhrnu**aktualizujte adresu IP a port a uložte změny.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. Aby změny začaly být účinné, počkejte 15 minut nebo [aktualizujte konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-vms-to-a-new-server"></a>Migrace všech virtuálních mitů na nový server

Pokud chcete migrovat všechny virtuální servery, abyste použili nový server vCenter, stačí aktualizovat IP adresu přiřazenou serveru vCenter. Nepřidávejte další účet VMware, protože to může vést k duplicitním položkám. Adresu aktualizujte takto:

1. V trezoru >**konfiguračních serverech** **infrastruktury** > obnovení sítě klepněte na konfigurační server, na který je server vCenter přidán.
1. V části **servery vCenter** klikněte na server vCenter, ze kterého chcete migrovat.
1. V **souhrnu**aktualizujte adresu IP na adresu nového serveru vCenter a uložte změny.
1. Jakmile je ip adresa aktualizována, site recovery začne přijímat informace o zjišťování virtuálního počítače z nového serveru vCenter. To nemá vliv na probíhající aktivity replikace.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Migrace několika virtuálních připojení na nový server

Pokud chcete migrovat jenom několik replikujících se virtuálních počítačích na nový server vCenter, postupujte takto:

1. [Přidejte](#add-vmware-server-to-the-vault) nový server vCenter na konfigurační server.
1. [Zakažte replikaci](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) pro virtuální servery, které se přesunou na nový server.
1. Ve společnosti VMware migrujte virtuální servery na nový server vCenter.
1. [Povolte replikaci](vmware-azure-tutorial.md#enable-replication) pro migrované virtuální servery znovu a vyberte nový server vCenter.

## <a name="migrate-most-vms-to-a-new-server"></a>Migrace většiny virtuálních mitů na nový server

Pokud je počet virtuálních počítačů, které chcete migrovat na nový server vCenter, vyšší než počet virtuálních počítačích, které zůstanou na původním serveru vCenter, postupujte takto:

1. [Aktualizujte adresu IP přiřazenou](#modify-the-ip-address-and-port) serveru vCenter server v nastavení konfiguračního serveru na adresu nového serveru vCenter.
1. [Zakažte replikaci](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) pro několik virtuálních počítačů, které zůstávají na starém serveru.
1. [Přidejte starý server vCenter](#add-vmware-server-to-the-vault) a jeho adresu IP na konfigurační server.
1. [Znovu povolte replikaci](vmware-azure-tutorial.md#enable-replication) pro virtuální servery, které zůstávají na starém serveru.

## <a name="next-steps"></a>Další kroky

Pokud máte nějaké problémy, [přečtěte si článek Poradce při potížích s zjišťováním serveru vCenter](vmware-azure-troubleshoot-vcenter-discovery-failures.md).
