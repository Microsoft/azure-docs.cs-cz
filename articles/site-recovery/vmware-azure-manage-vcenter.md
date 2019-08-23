---
title: Správa serverů VMware vCenter pro zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje, jak přidat a spravovat VMware vCenter pro zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 59088d8351bf89c859312774e3e9e396be8dd532
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904256"
---
# <a name="manage-vmware-vcenter-server"></a>Správa serveru VMware vCenter Server

Tento článek popisuje různé operace Site Recovery, které je možné provést na serveru VMware vCenter. Než začnete, ověřte [požadavky](vmware-physical-azure-support-matrix.md#replicated-machines) .


## <a name="set-up-an-account-for-automatic-discovery"></a>Nastavení účtu pro automatické zjišťování

Site Recovery potřebuje pro procesový Server přístup k VMware, aby mohl automaticky zjišťovat virtuální počítače a převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů. Vytvořte účet pro přístup následujícím způsobem:

1. Přihlaste se do počítače konfiguračního serveru.
2. Otevřete soubor cspsconfigtool. exe pomocí zástupce na ploše.
3. Na kartě **Spravovat účet** klikněte na **Přidat účet** .

   ![Přidat účet](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Zadejte podrobnosti účtu a kliknutím na tlačítko **OK** ho přidejte.  Účet by měl mít tato oprávnění shrnutá v následující tabulce. 

Synchronizace informací o účtu ve službě Site Recovery trvá přibližně 15 minut.

### <a name="account-permissions"></a>Oprávnění účtu

|**Úloha** | **Účet** | **Oprávnění** | **Podrobnosti**|
|--- | --- | --- | ---|
|**Automatické zjišťování/migrace (bez navrácení služeb po obnovení)** | Potřebujete alespoň uživatele, který je jen pro čtení. | Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **bez přístupu** s podřízeným objektem **rozšířit do podřízeného** objektu.|
|**Replikace/převzetí služeb při selhání** | Potřebujete alespoň uživatele, který je jen pro čtení.| Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte k podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **bez přístupu** s podřízeným objektem **rozšířit k podřízenému** objektu.<br/><br/> Užitečné pro účely migrace, ale ne pro úplnou replikaci, převzetí služeb při selhání a navrácení služeb po obnovení|
|**Replikace/převzetí služeb při selhání** | Doporučujeme vytvořit roli (AzureSiteRecoveryRole) s požadovanými oprávněními a pak přiřadit roli uživateli nebo skupině VMware. | Objekt datového centra – > rozšířit na podřízený objekt, role = AzureSiteRecoveryRole<br/><br/> Úložiště dat –> Přidělit prostor, procházet úložiště dat, operace se soubory nízké úrovně, odebrat soubor, aktualizovat soubory virtuálního počítače<br/><br/> Síť –> Přiřazení sítě<br/><br/> Prostředek –> Přiřadit virtuální počítač k fondu prostředků, migrovat vypnutý virtuální počítač, migrovat zapnutý virtuální počítač<br/><br/> Úlohy –> Vytvořit úlohu, aktualizovat úlohu<br/><br/> Virtuální počítač –> Konfigurace<br/><br/> Virtuální počítač –> Interakce –> zodpovědět dotazy, připojení zařízení, konfigurovat disk CD, konfigurovat disketu, vypnout, zapnout, instalace nástrojů VMware<br/><br/> Virtuální počítač –> Inventář –> Vytvořit, zaregistrovat, zrušit registraci<br/><br/> Virtuální počítač –> Zřizování –> Povolit stažení virtuálního počítače, povolit nahrávání souborů virtuálního počítače<br/><br/> Virtuální počítač –> Snímky –> Odebrat snímky | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **bez přístupu** s podřízeným objektem **rozšířit do podřízeného** objektu.|


## <a name="add-vmware-server-to-the-vault"></a>Přidání serveru VMware do trezoru

1. V Azure Portal otevřete trezor > **Site Recovery** > **severy konfigurace**infrastruktury a otevřete konfigurační server.
2. Na stránce **Podrobnosti** klikněte na **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Upravit přihlašovací údaje

Upravte přihlašovací údaje použité pro připojení k serveru vCenter nebo hostiteli ESXi následujícím způsobem:

1. Přihlaste se ke konfiguračnímu serveru a spusťte cspsconfigtool. exe z plochy.
2. Na kartě **Spravovat účet** klikněte na **Přidat účet** .

   ![Přidat účet](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Zadejte podrobnosti nového účtu a kliknutím na tlačítko **OK** ho přidejte. Účet by měl mít oprávnění uvedená [výše](#account-permissions).
4. Na Azure Portal otevřete > trezoru **Site Recovery** > **severy konfigurace**infrastruktury a otevřete konfigurační server.
5. Na stránce **Podrobnosti** klikněte na možnost **obnovit server**.
6. Po dokončení úlohy aktualizovat server vyberte vCenter Server, abyste otevřeli stránku se souhrnem vCenter.
7. Vyberte nově přidaný účet v poli **účet hostitele vCenter Server/vSphere** a klikněte na **Uložit**.

   ![Upravit – účet](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Odstranění serveru vCenter

1. V Azure Portal otevřete trezor > **Site Recovery** > **severy konfigurace**infrastruktury a otevřete konfigurační server.
2. Na stránce **Podrobnosti** vyberte Server vCenter.
3. Klikněte na tlačítko **Odstranit** .

   ![Odstranit účet](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>Úprava IP adresy a portu vCenter

1. Přihlaste se k portálu Azure.
2. Přejděte na **Recovery Services trezor** > **Site Recovery** > **konfigurační servery**infrastruktury.
3. Klikněte na konfigurační server, ke kterému je vCenter přiřazený.
4. V části **servery vCenter** klikněte na vCenter, který chcete upravit.
5. Na stránce Souhrn vCenter aktualizujte IP adresu a port vCenter v příslušných polích a uložte provedené změny.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Aby se změny projevily v platnosti, počkejte 15 minut nebo [Aktualizujte konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Migrovat všechny chráněné virtuální počítače na nový vCenter

Pokud chcete migrovat všechny virtuální počítače na nový vCenter, nepřidávejte další účet vCenter. To může vést k duplicitním položkám. Jenom aktualizujte IP adresu nového vCenter:

1. Přihlaste se k portálu Azure.
2. Přejděte na **Recovery Services trezor** > **Site Recovery** > **konfigurační servery**infrastruktury.
3. Klikněte na konfigurační server, ke kterému je přiřazený starý vCenter.
4. V části **servery vCenter** klikněte na vCenter, ze kterého plánujete migrovat.
5. Na stránce Souhrn vCenter aktualizujte IP adresu nového vCenter v poli **název serveru/hostitele vSphere nebo IP adresa**. Uložte provedené změny.

Jakmile se IP adresa aktualizuje, Site Recovery komponenty začnou získávat informace o zjišťování virtuálních počítačů z nového vCenter. Tato akce nebude mít vliv na probíhající aktivity replikace.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Migrace několika chráněných virtuálních počítačů do nového vCenter

> [!NOTE]
> Tato část je platná jenom v případě, že migrujete několik vašich chráněných virtuálních počítačů na nový vCenter. Pokud chcete chránit novou sadu virtuálních počítačů z nového vCenter, [přidejte do konfiguračního serveru nové podrobnosti vCenter](#add-vmware-server-to-the-vault) a začněte s **[povolením ochrany](vmware-azure-tutorial.md#enable-replication)** .

Postup přesunutí několika virtuálních počítačů do nového vCenter:

1. [Přidejte nové podrobnosti vCenter na konfigurační server](#add-vmware-server-to-the-vault).
2. [Zakažte replikaci virtuálních počítačů](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) , které plánujete migrovat.
3. Dokončete migraci vybraných virtuálních počítačů do nového vCenter.
4. Nyní proveďte ochranu migrovaných virtuálních počítačů tak, že [při povolení ochrany vyberete nový vCenter](vmware-azure-tutorial.md#enable-replication).

> [!TIP]
> Pokud je počet migrovaných virtuálních počítačů **vyšší** než počet virtuálních počítačů uchovávaných ve starém serveru vCenter, aktualizujte IP adresu nového vCenter podle pokynů uvedených tady. U několika virtuálních počítačů, které jsou uchovány na starém serveru vCenter, [zakažte replikaci](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure). [přidejte nové podrobnosti vCenter ke konfiguračnímu serveru](#add-vmware-server-to-the-vault)a spusťte **[možnost Povolit ochranu](vmware-azure-tutorial.md#enable-replication)** .

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

1. Pokud jsou chráněné virtuální počítače přesunuté z jednoho hostitele ESXi na jiný, bude to mít vliv na replikaci?

    Ne, nebude to mít vliv na probíhající replikaci. Nezapomeňte ale [nasadit hlavní cílový server s dostatečnými oprávněními](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) .

2. Jaká jsou čísla portů používaná pro komunikaci mezi vCenter a dalšími součástmi Site Recovery?

    Výchozí port je 443. Konfigurační server bude mít přístup k informacím o hostiteli vCenter/vSphere prostřednictvím tohoto portu. Pokud chcete tyto informace aktualizovat, klikněte [sem](#modify-the-vcenter-ip-address-and-port).
