---
title: Nastavte obor pro zjišťování virtuálních počítačů VMware pomocí Azure Migrate
description: V této části najdete popis postupu nastavení rozsahu zjišťování pro vyhodnocení virtuálních počítačů VMware a migraci pomocí Azure Migrate.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337633"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Nastavení rozsahu zjišťování pro virtuální počítače VMware

Tento článek popisuje, jak omezit rozsah zjišťování pro virtuální počítače VMware zjištěné [zařízením Azure Migrate](migrate-appliance-architecture.md).

Zařízení Azure Migrate zjistí místní virtuální počítače VMware, když máte tyto možnosti: 

- K vyhodnocení virtuálních počítačů VMware pro migraci do Azure použijte nástroj [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool.
- K [migraci](server-migrate-overview.md) virtuálních počítačů VMware do Azure použijte nástroj [Azure Migrate: Server pro migraci](migrate-services-overview.md#azure-migrate-server-migration-tool) .

## <a name="set-discovery-scope"></a>Nastavit rozsah vyhledávání


Po nastavení zařízení Azure Migrate pro vyhodnocení nebo migraci virtuálních počítačů VMware začne zařízení zjišťovat virtuální počítače a odesílat metadata virtuálních počítačů do Azure. Než zařízení připojíte k vCenter Server ke zjišťování, můžete nastavit rozsah zjišťování tak, aby bylo zjišťování omezeno na vCenter Server datová centra, clustery, složku clusterů, hostitele, složku hostitelů nebo jednotlivé virtuální počítače.

K nastavení oboru přiřadíte oprávnění k účtu, který Azure Migrate používá pro přístup k vCenter Server.

## <a name="create-a-vcenter-user-account"></a>Vytvoření uživatelského účtu vCenter

Pokud jste ještě nenastavili uživatelský účet vCenter, který zařízení Azure Migrate používá ke zjišťování, vyhodnocení a migraci virtuálních počítačů VMware, udělejte to jako první.

1.    Přihlaste se k webovému klientovi vSphere jako správce vCenter Server.
2.    Vyberte možnost **Správa** > **uživatelů a skupin jednotného přihlašování**a klikněte na kartu **Uživatelé** .
3.    Vyberte ikonu **nového uživatele** .
4.    Do pole informace o novém uživateli zadejte > **OK**.

Oprávnění k účtu závisí na tom, co právě nasazujete.

**Funkce** | **Oprávnění účtu**
--- | ---
[Místa](tutorial-assess-vmware.md)| Účet potřebuje přístup jen pro čtení.
[Zjišťování aplikací/rolí/funkcí](how-to-discover-applications.md) | Účet potřebuje přístup jen pro čtení, s oprávněními povolenými pro virtuální počítače > operací hostů.
[Analyzovat závislosti (bez agentů)](how-to-create-group-machine-dependencies-agentless.md) | Účet potřebuje přístup jen pro čtení, s oprávněními povolenými pro virtuální počítače > operací hostů.
[Migrace (bez agentů)](tutorial-migrate-vmware.md) | Potřebujete roli, která má přiřazená správná oprávnění.<br/><br/> Chcete-li vytvořit roli, přihlaste se k webovému klientovi vSphere jako správce vCenter Server. Vyberte instanci vCenter Server > **vytvořit roli**. Zadejte název role, například <em>Azure_Migrate</em>, a přiřaďte roli [požadovaná oprávnění](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) .


## <a name="assign-permissions-on-vcenter-objects"></a>Přiřazení oprávnění pro objekty vCenter

Můžete přiřadit oprávnění k objektům inventáře pomocí jedné ze dvou metod:

- U všech nadřazených objektů, které hostují virtuální počítače, které chcete zjišťovat nebo migrovat, přiřaďte k účtu, který používáte, roli s požadovanými oprávněními.
- Případně můžete přiřadit roli a uživatelský účet na úrovni datového centra a rozšířit je do podřízených objektů. Potom udělte účtu roli **bez přístupu** pro každý objekt, který nechcete zjišťovat nebo migrovat. Tento přístup nedoporučujeme, protože je nenáročný a může vystavit řízení přístupu, protože každému novému podřízenému objektu se automaticky udělí přístup zděděný z nadřazené položky.

Pokud chcete přiřadit roli k účtu, který používáte pro všechny relevantní objekty, udělejte toto:

- **Pro posouzení**: pro vyhodnocení virtuálních počítačů použijte roli jen **pro čtení** pro uživatelský účet vCenter pro všechny nadřazené objekty hostující virtuální počítače, které chcete zjišťovat. Zahrnuje nadřazené objekty: hostitel, složka hostitelů, cluster a složka clusterů v hierarchii, a to až do datacentra. Rozšiřte tato oprávnění na podřízené objekty v hierarchii.

    ![Přiřazení oprávnění](./media/tutorial-assess-vmware/assign-perms.png)

- Pro **migraci bez agenta**: u všech nadřazených objektů hostujících virtuální počítače, které chcete zjistit, použijte pro migraci bez agenta uživatelsky definovanou roli s [požadovanými oprávněními](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) pro uživatelský účet. Můžete pojmenovat <em>Azure_Migrate</em>rolí.

### <a name="scope-support"></a>Podpora oboru

Nástroj pro vyhodnocení serveru v současné době nemůže zjistit virtuální počítače, pokud má účet vCenter udělen přístup na úrovni složky virtuálních počítačů vCenter. Jsou podporovány složky hostitelů a clusterů.

Pokud chcete určit obor zjišťování pomocí složek virtuálních počítačů, dokončete následující postup a ujistěte se, že účet vCenter má přístup jen pro čtení přiřazený na úrovni virtuálního počítače.

1. Přiřaďte oprávnění jen pro čtení na všech virtuálních počítačích ve složkách virtuálních počítačů, které chcete určit pro obor zjišťování.
2. Udělte přístup jen pro čtení ke všem nadřazeným objektům, které hostují virtuální počítače.
    - Součástí jsou všechny nadřazené objekty (hostitel, složka hostitelů, cluster, složka clusterů) v hierarchii až do datacentra.
    - Nemusíte šířit oprávnění ke všem podřízeným objektům.
3. Použijte přihlašovací údaje pro zjišťování tak, že vyberete datový datacentrum jako **Rozsah shromažďování**. Nastavení řízení přístupu na základě rolí zajišťuje, aby měl odpovídající uživatelský účet vCenter přístup jenom k virtuálním počítačům specifickým pro tenanta.


## <a name="next-steps"></a>Další kroky

[Nastavte zařízení](how-to-set-up-appliance-vmware.md) a [Spusťte nepřetržité zjišťování](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential).
