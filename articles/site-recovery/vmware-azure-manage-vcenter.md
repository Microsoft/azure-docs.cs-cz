---
title: Servery VMware vCenter pro zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak přidávat a spravovat systém VMware vCenter pro zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 9694c682f171ab715812b05fed2064c9bbcd36b3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60600411"
---
# <a name="manage-vmware-vcenter-server"></a>Spravovat VMware vCenter server

Tento článek popisuje různé operace Site Recovery, které lze provést na serveru VMware vCenter. Ověření [požadavky](vmware-physical-azure-support-matrix.md#replicated-machines) před zahájením.


## <a name="set-up-an-account-for-automatic-discovery"></a>Nastavení účtu pro automatické zjišťování

Site Recovery potřebuje přístup k VMware na procesovém serveru a automatické zjišťování virtuálních počítačů a převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů. Vytvořte účet pro přístup k následujícím způsobem:

1. Přihlaste se do počítače konfigurační server.
2. Otevřete spuštění cspsconfigtool.exe pomocí zástupce na ploše.
3. Klikněte na tlačítko **přidat účet** na **spravovat účet** kartu.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Zadejte podrobnosti o účtu a klikněte na **OK** a přidejte ji.  Tento účet by měl mít oprávnění shrnuté v následující tabulce. 

Trvá přibližně 15 minut pro informace o účtu možné synchronizovat se službou Site Recovery.

### <a name="account-permissions"></a>Oprávnění účtu

|**Úloha** | **Účet** | **Oprávnění** | **Podrobnosti**|
|--- | --- | --- | ---|
|**Automatické zjišťování/migrace (bez navrácení služeb po obnovení)** | Budete potřebovat alespoň uživatel jen pro čtení | Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte **bez přístupu** role s **rozšířit na podřízený** objekt podřízeným objektům (hostitelé vSphere, úložiště, virtuální počítače a sítě).|
|**Replikace a převzetí služeb při selhání** | Budete potřebovat alespoň uživatel jen pro čtení| Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte **bez přístupu** role s **rozšířit na podřízený** objekt podřízeným objektům (hostitelé vSphere, úložiště, virtuální počítače a sítě).<br/><br/> Užitečné pro účely migrace, ale ne úplná replikace, převzetí služeb při selhání, navrácení služeb po obnovení.|
|**Replikace a převzetí služeb při selhání a navrácení** | Doporučujeme vytvořit roli (AzureSiteRecoveryRole) s požadovanými oprávněními a pak přiřadíte roli uživateli nebo skupině VMware | Objekt datového centra –> rozšířit na podřízený objekt, role = AzureSiteRecoveryRole<br/><br/> Úložiště dat –> Přidělit prostor, procházet úložiště dat, operace se soubory nízké úrovně, odebrat soubor, aktualizovat soubory virtuálního počítače<br/><br/> Síť –> Přiřazení sítě<br/><br/> Prostředek –> Přiřadit virtuální počítač k fondu prostředků, migrovat vypnutý virtuální počítač, migrovat zapnutý virtuální počítač<br/><br/> Úlohy –> Vytvořit úlohu, aktualizovat úlohu<br/><br/> Virtuální počítač –> Konfigurace<br/><br/> Virtuální počítač –> Interakce –> zodpovědět dotazy, připojení zařízení, konfigurovat disk CD, konfigurovat disketu, vypnout, zapnout, instalace nástrojů VMware<br/><br/> Virtuální počítač –> Inventář –> Vytvořit, zaregistrovat, zrušit registraci<br/><br/> Virtuální počítač –> Zřizování –> Povolit stažení virtuálního počítače, povolit nahrávání souborů virtuálního počítače<br/><br/> Virtuální počítač –> Snímky –> Odebrat snímky | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte **bez přístupu** role s **rozšířit na podřízený** objekt podřízeným objektům (hostitelé vSphere, úložiště, virtuální počítače a sítě).|


## <a name="add-vmware-server-to-the-vault"></a>Chcete do trezoru přidat VMware server

1. Na webu Azure Portal otevřete svůj trezor > **infrastruktura Site Recovery** > **konfigurace přeruší**a otevřete konfigurační server.
2. Na **podrobnosti** klikněte na **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Upravit přihlašovací údaje

Upravte přihlašovací údaje použité pro připojení k serveru vCenter nebo hostiteli ESXi následujícím způsobem:

1. Přihlaste se konfigurační server a spusťte cspsconfigtool.exe z plochy.
2. Klikněte na tlačítko **přidat účet** na **spravovat účet** kartu.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Zadejte podrobnosti o novém účtu a klikněte na **OK** a přidejte ji. Tento účet by měl mít oprávnění uvedená [nad](#account-permissions).
4. Na webu Azure portal, otevřete trezor > **infrastruktura Site Recovery** > **konfigurace přeruší**a otevřete konfigurační server.
5. V **podrobnosti** klikněte na **aktualizovat Server**.
6. Po dokončení úlohy aktualizace serveru vyberte vCenter Server vCenter otevřete **Souhrn** stránky.
7. Vyberte nově přidaný účet v **účet vCenter serveru nebo serveru vSphere hostitele** pole a klikněte na tlačítko **Uložit**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Odstranění serveru vCenter

1. Na webu Azure Portal otevřete svůj trezor > **infrastruktura Site Recovery** > **konfigurace přeruší**a otevřete konfigurační server.
2. Na **podrobnosti** stránky, vyberte vCenter server.
3. Klikněte na **odstranit** tlačítko.

   ![Odstranit účet](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>Upravit IP adresu vCenter a port

1. Přihlaste se k portálu Azure.
2. Přejděte do **trezor služby Recovery Services** > **infrastruktura Site Recovery** > **konfigurační servery**.
3. Kliknutím na konfiguračním serveru vCenter se přiřadí.
4. V **serverů vCenter** části, klikněte na vCenter, kterou chcete upravit.
5. Na stránce Souhrn serveru vCenter aktualizujte IP adresu a port serveru vCenter do příslušných polí a potom změny uložte.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Změny začnou platit, počkejte 15 minut nebo [aktualizovat konfigurační server](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Všechny chráněné virtuální počítače migrovat do nového serveru vCenter

Pokud chcete migrovat všechny virtuální počítače na nový server vCenter, nepřidávejte jiný účet vCenter. To může vést k duplicitní položky. Jen aktualizujte IP adresu vcenter nové:

1. Přihlaste se k portálu Azure.
2. Přejděte do **trezor služby Recovery Services** > **infrastruktura Site Recovery** > **konfigurační servery**.
3. Kliknutím na konfiguračním serveru starý server vCenter je přiřazen.
4. V **serverů vCenter** části, klikněte na vCenter, máte v úmyslu migrovat z.
5. Na stránce Souhrn serveru vCenter, aktualizujte IP adresu nového serveru vCenter v poli **vCenter serveru nebo serveru vSphere název hostitele nebo IP adresa**. Uložte provedené změny.

Poté, co se aktualizuje IP adresu, součásti Site Recovery se začít přijímat informace o zjišťování virtuálních počítačů z nového serveru vCenter. Nebude to mít vliv na činnosti probíhající replikaci.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Několik chráněné virtuální počítače migrovat do nového serveru vCenter

> [!NOTE]
> Tato část se vztahuje pouze když migrujete některé z vašich chráněných virtuálních počítačů do nového serveru vCenter. Pokud chcete chránit nové sady virtuálních počítačů z nové vCenter [přidat nové podrobnosti vCenter do konfiguračního serveru](#add-vmware-server-to-the-vault) a začínat  **[povolit ochranu](vmware-azure-tutorial.md#enable-replication)** .

Přesunout do nového serveru vCenter několik virtuálních počítačů:

1. [Přidat nové podrobnosti vCenter do konfiguračního serveru](#add-vmware-server-to-the-vault).
2. [Zakázat replikaci virtuálních počítačů](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) máte v úmyslu migrovat.
3. Dokončení migrace vybrané virtuální počítače na nový server vCenter.
4. Nyní, ochrana migrovaných virtuálních počítačů podle [vyberete nový vCenter při povolení ochrany](vmware-azure-tutorial.md#enable-replication).

> [!TIP]
> Pokud je počet migrovaných virtuálních počítačů **vyšší** , že počet virtuálních počítačů v původní server vCenter, aktualizovat IP adresu nového vcenter pomocí podle pokynů. Pro několik virtuálních počítačů, které se zachovají na starý server vCenter [zakázat replikaci](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure); [přidat nové podrobnosti vCenter do konfiguračního serveru](#add-vmware-server-to-the-vault)a spusťte  **[povolit ochranu](vmware-azure-tutorial.md#enable-replication)** .

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

1. Pokud chráněných virtuálních počítačů jsou přesunuty z jednoho hostitele ESXi do druhého, ovlivní to replikace?

    Ne, nebude to mít vliv probíhající replikaci. Ale [nezapomeňte nasadit hlavní cílový server s dostatečnými oprávněními](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. Co jsou čísla portů používaných k ke komunikaci mezi vCenter a dalších Site Recovery komponenty?

    Výchozí port je 443. Konfigurační server bude přístup k serveru vCenter nebo hostiteli vSphere informace přes tento port. Pokud chcete tyto informace aktualizujete, klikněte na tlačítko [tady](#modify-the-vcenter-ip-address-and-port).
