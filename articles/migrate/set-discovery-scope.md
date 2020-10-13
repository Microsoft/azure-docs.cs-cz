---
title: Nastavte obor pro zjišťování virtuálních počítačů VMware pomocí Azure Migrate
description: V této části najdete popis postupu nastavení rozsahu zjišťování pro vyhodnocení virtuálních počítačů VMware a migraci pomocí Azure Migrate.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: dfc9c12edd93fc720ef716fd43b04e0c193d5803
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88919722"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Nastavení rozsahu zjišťování pro virtuální počítače VMware

Tento článek popisuje, jak omezit rozsah virtuálních počítačů VMware, které se objevují, když máte tyto možnosti:

- Zjišťují se virtuální počítače pomocí [Azure Migrate zařízení](migrate-appliance-architecture.md) , když používáte Azure Migrate: Nástroj pro vyhodnocení serveru.
- Zjišťují se virtuální počítače pomocí [Azure Migrate zařízení](migrate-appliance-architecture.md) , když používáte nástroj Azure Migrate: Server pro migraci, pro migraci virtuálních počítačů VMware do Azure bez agenta.

Když zařízení nastavíte, připojí se k vCenter Server a spustí zjišťování. Než zařízení připojíte k vCenter Server, můžete zjišťování omezit na vCenter Server datová centra, clustery, složku clusterů, hostitele, složku hostitelů nebo jednotlivé virtuální počítače. K nastavení oboru přiřadíte oprávnění k účtu, který zařízení používá pro přístup k vCenter Server.

## <a name="before-you-start"></a>Než začnete

Pokud jste nenastavili uživatelský účet vCenter, který Azure Migrate používá ke zjišťování, udělejte to teď pro účely [posouzení](tutorial-prepare-vmware.md#set-up-permissions-for-assessment) nebo [migrace bez agenta](tutorial-prepare-vmware.md#assign-permissions-to-an-account).


## <a name="assign-permissions-and-roles"></a>Přiřazení oprávnění a rolí

Můžete přiřadit oprávnění k objektům inventáře VMware pomocí jedné ze dvou metod:

- Na účtu, který zařízení používá, přiřaďte roli, která má požadovaná oprávnění pro objekty, které chcete určit jako obor.
- Případně můžete přiřadit roli účtu na úrovni datového centra a rozšířit je na podřízené objekty. Potom udělte účtu roli **bez přístupu** pro každý objekt, který v oboru nechcete. Tento přístup nedoporučujeme, protože je nenáročný a může vystavit řízení přístupu, protože každému novému podřízenému objektu se automaticky udělí přístup zděděný z nadřazené položky.

Na úrovni složky virtuálního počítače vCenter nemůžete obor zjišťování inventáře. Pokud potřebujete obor vyhledat na virtuální počítače ve složce virtuálních počítačů, vytvořte uživatele a udělte každému požadovanému virtuálnímu počítači přístup individuálně. Host a složky clusteru jsou podporovány.


### <a name="assign-a-role-for-assessment"></a>Přiřazení role k posouzení

1. Na účtu vCenter zařízení, který používáte pro zjišťování, použijte roli jen **pro čtení** u všech nadřazených objektů, které hostují virtuální počítače, které chcete zjišťovat a vyhodnocovat (hostitel, cluster, složka hostitelů, složka clusterů, až do Datacenter).
2. Rozšiřte tato oprávnění na podřízené objekty v hierarchii.

    ![Přiřazení oprávnění](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Přiřazení role pro migraci bez agentů

1. Na účtu vCenter zařízení, který používáte pro migraci, použijte uživatelem definovanou roli, která má [potřebná oprávnění](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless), do všech nadřazených objektů, které hostují virtuální počítače, které chcete zjišťovat a migrovat.
2. Roli můžete pojmenovat s něčím, co je snazší identifikovat. Například <em>Azure_Migrate</em>.

## <a name="work-around-vm-folder-restriction"></a>Řešení omezení složky VM

Nástroj pro vyhodnocení serveru v současné době nemůže zjistit virtuální počítače, pokud je přístup povolen na úrovni složky virtuálního počítače vCenter. Pokud chcete určit obor zjišťování a hodnocení pomocí složek virtuálních počítačů, použijte toto alternativní řešení.

1. Přiřaďte oprávnění jen pro čtení na všech virtuálních počítačích umístěných ve složkách virtuálních počítačů, které chcete určit pro obor zjišťování a posouzení.
2. Udělte přístup jen pro čtení všem nadřazeným objektům, které jsou hostiteli virtuálních počítačů, clusteru, složky hostitelů, složky clustery, až po datové centrum. Nemusíte šířit oprávnění ke všem podřízeným objektům.
3. Chcete-li použít pověření pro zjišťování, vyberte datové centrum jako **Rozsah shromažďování**.


Nastavení řízení přístupu na základě rolí zajišťuje, aby měl odpovídající uživatelský účet vCenter přístup jenom k virtuálním počítačům specifickým pro tenanta.


## <a name="next-steps"></a>Další kroky

[Nastavení zařízení](how-to-set-up-appliance-vmware.md)
