---
title: " Spravovat servery VMware vCenter server v Azure Site Recovery | Microsoft Docs"
description: "Tento článek popisuje, jak přidávat a spravovat v Azure Site Recovery serveru VMware vCenter."
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: be415340da09043eccd361b0168bb304d8904bef
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="manage-vmware-vcenter-servers"></a>Spravovat servery VMware vCenter server 

Tento článek popisuje různé operace obnovení lokality, které lze provést na VMware vCenter. Ověřte [požadavky](vmware-physical-azure-support-matrix.md#replicated-machines) před zahájením.


## <a name="set-up-an-account-for-automatic-discovery"></a>Nastavit účet pro automatické zjišťování

Site Recovery potřebuje přístup k VMware pro procesový server se automaticky zjistit virtuální počítače a pro převzetí služeb při selhání a navrácení služeb po obnovení virtuálních počítačů. Vytvoření účtu pro přístup k následujícím způsobem:

1. Přihlaste se k počítači konfigurace serveru.
2. Otevřete spuštění cspsconfigtool.exe pomocí zástupce na ploše.
3. Klikněte na tlačítko **přidat účet** na **spravovat účet** kartě.

  ![Přidat účet](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Zadejte podrobnosti o účtu a klikněte na **OK** ho přidejte.  Účet by měl mít oprávnění shrnuté v následující tabulce. 

Jak dlouho trvá asi 15 minut pro informace o účtu možné synchronizovat se službou Site Recovery.

### <a name="account-permissions"></a>Oprávnění pro uživatelský účet

|**Úkol** | **Účet** | **Oprávnění** | **Podrobnosti**|
|--- | --- | --- | ---|
|**Automatické zjišťování nebo migrací (bez navrácení služeb po obnovení)** | Je třeba alespoň uživatele jen pro čtení | Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Pokud chcete omezit přístup, přiřadit **žádný přístup** role s **Propagate na podřízené** objekt, pro podřízený objekt (hostitelů vSphere, datastores, virtuální počítače a sítě).|
|**Replikace nebo převzetí služeb při selhání** | Je třeba alespoň uživatele jen pro čtení| Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Pokud chcete omezit přístup, přiřadit **žádný přístup** role s **Propagate na podřízené** objekt, který má podřízené objekty (hostitelů vSphere, datastores, virtuální počítače a sítě).<br/><br/> Tato možnost je užitečná pro účely migrace, ale není úplná replikace, převzetí služeb při selhání a navrácení služeb po obnovení.|
|**Replikace, převzetí služeb při selhání nebo navrácení** | Doporučujeme vytvořit roli (AzureSiteRecoveryRole) s požadovanými oprávněními a potom přiřadit roli VMware uživatele nebo skupinu | Objekt datového centra –> Propagate pro podřízený objekt role = AzureSiteRecoveryRole<br/><br/> Úložiště dat –> Přidělit prostor, procházet úložiště dat, operace se soubory nízké úrovně, odebrat soubor, aktualizovat soubory virtuálního počítače<br/><br/> Síť –> Přiřazení sítě<br/><br/> Prostředek –> Přiřadit virtuální počítač k fondu prostředků, migrovat vypnutý virtuální počítač, migrovat zapnutý virtuální počítač<br/><br/> Úlohy –> Vytvořit úlohu, aktualizovat úlohu<br/><br/> Virtuální počítač –> Konfigurace<br/><br/> Virtuální počítač –> Interakce –> zodpovědět dotazy, připojení zařízení, konfigurovat disk CD, konfigurovat disketu, vypnout, zapnout, instalace nástrojů VMware<br/><br/> Virtuální počítač –> Inventář –> Vytvořit, zaregistrovat, zrušit registraci<br/><br/> Virtuální počítač –> Zřizování –> Povolit stažení virtuálního počítače, povolit nahrávání souborů virtuálního počítače<br/><br/> Virtuální počítač –> Snímky –> Odebrat snímky | Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v datacentru.<br/><br/> Pokud chcete omezit přístup, přiřadit **žádný přístup** role s **Propagate na podřízené** objekt, pro podřízený objekt (hostitelů vSphere, datastores, virtuální počítače a sítě).|


## <a name="add-vmware-server-to-the-vault"></a>Přidat VMware server do trezoru

1. Na portálu Azure otevřete svůj trezor > **infrastruktura Site Recovery** > **konfigurace servery**a otevřete konfigurační server.
2. Na **podrobnosti** klikněte na tlačítko **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Upravit přihlašovací údaje

Upravte přihlašovací údaje používané k připojení k serveru vCenter nebo hostiteli ESXi následujícím způsobem:

1. Přihlaste se konfigurační server a spusťte cspsconfigtool.exe z plochy.
2. Klikněte na tlačítko **přidat účet** na **spravovat účet** kartě.

  ![Přidat účet](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Zadejte nové podrobnosti účtu a klikněte na **OK** ho přidejte. Účet musí mít oprávnění uvedené [výše](#account-permissions).
4. Na portálu Azure otevřete trezoru > **infrastruktura Site Recovery** > **konfigurace servery**a otevřete konfigurační server.
5. V **podrobnosti** klikněte na tlačítko **aktualizace serveru**.
6. Po dokončení úlohy aktualizace serveru vyberte systému vCenter Server, otevřete vCenter **Souhrn** stránky.
7. Vyberte nově přidaný účet v **účet hostitele server vSphere vCenter** pole a klikněte na tlačítko **Uložit**.

    ![Upravit účet](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Odstranění serveru vCenter

1. Na portálu Azure otevřete svůj trezor > **infrastruktura Site Recovery** > **konfigurace servery**a otevřete konfigurační server.
2. Na **podrobnosti** vyberte vCenter server.
3. Klikněte na **odstranit** tlačítko.

  ![Odstranění účtu](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Pokud potřebujete změnit IP adresa vCenter, plně kvalifikovaný název domény nebo port, budete muset odstranění serveru vCenter a přidejte ji zpět na portál.
