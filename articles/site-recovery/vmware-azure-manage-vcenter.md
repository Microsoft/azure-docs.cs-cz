---
title: Servery VMware vCenter pro zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak přidávat a spravovat systém VMware vCenter pro zotavení po havárii virtuálních počítačů VMware do Azure pomocí Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 6c00ed6f3bec1e16b2a3cb8588335c1741a92883
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849033"
---
# <a name="manage-vmware-vcenter-server"></a>Spravovat VMware vCenter server

Tento článek popisuje různé operace Site Recovery, které lze provést na serveru VMware vCenter. Ověření [požadavky](vmware-physical-azure-support-matrix.md#replicated-machines) před zahájením.


## <a name="set-up-an-account-for-automatic-discovery"></a>Nastavení účtu pro automatické zjišťování

Site Recovery potřebuje přístup k VMware na procesovém serveru a automatické zjišťování virtuálních počítačů a převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů. Vytvořte účet pro přístup k následujícím způsobem:

1. Přihlaste se do počítače konfigurační server.
2. Otevřete spuštění cspsconfigtool.exe pomocí zástupce na ploše.
3. Klikněte na tlačítko **přidat účet** na **spravovat účet** kartu.

  ![Přidat účet](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Zadejte podrobnosti o účtu a klikněte na **OK** a přidejte ji.  Tento účet by měl mít oprávnění shrnuté v následující tabulce. 

Trvá přibližně 15 minut pro informace o účtu možné synchronizovat se službou Site Recovery.

### <a name="account-permissions"></a>Oprávnění účtu

|**Úkol** | **Účet** | **Oprávnění** | **Podrobnosti**|
|--- | --- | --- | ---|
|**Automatické zjišťování/migrace (bez navrácení služeb po obnovení)** | Budete potřebovat alespoň uživatel jen pro čtení | Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte **bez přístupu** role s **rozšířit na podřízený** objekt podřízeným objektům (hostitelé vSphere, úložiště, virtuální počítače a sítě).|
|**Replikace a převzetí služeb při selhání** | Budete potřebovat alespoň uživatel jen pro čtení| Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte **bez přístupu** role s **rozšířit na podřízený** objekt podřízeným objektům (hostitelé vSphere, úložiště, virtuální počítače a sítě).<br/><br/> Užitečné pro účely migrace, ale ne úplná replikace, převzetí služeb při selhání, navrácení služeb po obnovení.|
|**Replikace a převzetí služeb při selhání a navrácení** | Doporučujeme vytvořit roli (AzureSiteRecoveryRole) s požadovanými oprávněními a pak přiřadíte roli uživateli nebo skupině VMware | Objekt datového centra –> rozšířit na podřízený objekt, role = AzureSiteRecoveryRole<br/><br/> Úložiště dat –> Přidělit prostor, procházet úložiště dat, operace se soubory nízké úrovně, odebrat soubor, aktualizovat soubory virtuálního počítače<br/><br/> Síť –> Přiřazení sítě<br/><br/> Prostředek –> Přiřadit virtuální počítač k fondu prostředků, migrovat vypnutý virtuální počítač, migrovat zapnutý virtuální počítač<br/><br/> Úlohy –> Vytvořit úlohu, aktualizovat úlohu<br/><br/> Virtuální počítač –> Konfigurace<br/><br/> Virtuální počítač –> Interakce –> zodpovědět dotazy, připojení zařízení, konfigurovat disk CD, konfigurovat disketu, vypnout, zapnout, instalace nástrojů VMware<br/><br/> Virtuální počítač –> Inventář –> Vytvořit, zaregistrovat, zrušit registraci<br/><br/> Virtuální počítač –> Zřizování –> Povolit stažení virtuálního počítače, povolit nahrávání souborů virtuálního počítače<br/><br/> Virtuální počítač –> Snímky –> Odebrat snímky | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Chcete-li omezit přístup, přiřaďte **bez přístupu** role s **rozšířit na podřízený** objekt podřízeným objektům (hostitelé vSphere, úložiště, virtuální počítače a sítě).|


## <a name="add-vmware-server-to-the-vault"></a>Chcete do trezoru přidat VMware server

1. Na webu Azure portal otevřete svůj trezor > **infrastruktura Site Recovery** > **konfigurace přeruší**a otevřete konfigurační server.
2. Na **podrobnosti** klikněte na **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Upravit přihlašovací údaje

Upravte přihlašovací údaje použité pro připojení k serveru vCenter nebo hostiteli ESXi následujícím způsobem:

1. Přihlaste se konfigurační server a spusťte cspsconfigtool.exe z plochy.
2. Klikněte na tlačítko **přidat účet** na **spravovat účet** kartu.

  ![Přidat účet](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Zadejte podrobnosti o novém účtu a klikněte na **OK** a přidejte ji. Tento účet by měl mít oprávnění uvedená [nad](#account-permissions).
4. Na webu Azure portal, otevřete trezor > **infrastruktura Site Recovery** > **konfigurace přeruší**a otevřete konfigurační server.
5. V **podrobnosti** klikněte na **aktualizovat Server**.
6. Po dokončení úlohy aktualizace serveru vyberte vCenter Server vCenter otevřete **Souhrn** stránky.
7. Vyberte nově přidaný účet v **účet vCenter serveru nebo serveru vSphere hostitele** pole a klikněte na tlačítko **Uložit**.

    ![Upravit účet](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Odstranění serveru vCenter

1. Na webu Azure Portal otevřete svůj trezor > **infrastruktura Site Recovery** > **konfigurace přeruší**a otevřete konfigurační server.
2. Na **podrobnosti** stránky, vyberte vCenter server.
3. Klikněte na **odstranit** tlačítko.

  ![Odstranit účet](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Pokud potřebujete změnit IP adresu vCenter, plně kvalifikovaný název domény nebo port, budete muset odstranit vCenter server a přidejte ji zpět na portál.
