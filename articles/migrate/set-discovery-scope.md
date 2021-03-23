---
title: Nastavte obor pro zjišťování serverů ve VMware pomocí Azure Migrate
description: V této části najdete popis postupu nastavení rozsahu zjišťování pro servery hostované na stránce vyhodnocení a migrace VMware pomocí Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 29ac42da6560a717f12cd256fdd71282e0bd313f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775353"
---
# <a name="set-discovery-scope-for-servers-in-vmware-environment"></a>Nastavení rozsahu zjišťování pro servery v prostředí VMware

Tento článek popisuje, jak omezit obor zjišťování pro servery v prostředí VMware v těchto případech:

- Zjišťování serverů pomocí [Azure Migrate zařízení](migrate-appliance-architecture.md) , když používáte nástroj Azure Migrate: Discovery and Assessment Tool.
- Zjišťování serverů pomocí [Azure Migrate zařízení](migrate-appliance-architecture.md) , když používáte nástroj Azure Migrate: Server pro migraci, pro migraci serverů bez agenta z prostředí VMware do Azure.

Když zařízení nastavíte, připojí se k vCenter Server a spustí zjišťování. Než zařízení připojíte k vCenter Server, můžete zjišťování omezit na vCenter Server datová centra, clustery, složku clusterů, hostitele, složku hostitelů nebo jednotlivé servery. K nastavení oboru přiřadíte oprávnění k účtu, který zařízení používá pro přístup k vCenter Server.

## <a name="before-you-start"></a>Než začnete

Pokud jste nenastavili uživatelský účet vCenter, který Azure Migrate používá ke zjišťování, udělejte to teď pro účely [posouzení](./tutorial-discover-vmware.md#prepare-vmware) nebo [migrace bez agenta](./migrate-support-matrix-vmware-migration.md#agentless-migration).


## <a name="assign-permissions-and-roles"></a>Přiřazení oprávnění a rolí

Můžete přiřadit oprávnění k objektům inventáře VMware pomocí jedné ze dvou metod:

- Na účtu, který zařízení používá, přiřaďte roli, která má požadovaná oprávnění pro objekty, které chcete určit jako obor.
- Případně můžete přiřadit roli účtu na úrovni datového centra a rozšířit je na podřízené objekty. Potom udělte účtu roli **bez přístupu** pro každý objekt, který v oboru nechcete. Tento přístup nedoporučujeme, protože je nenáročný a může vystavit řízení přístupu, protože každému novému podřízenému objektu se automaticky udělí přístup zděděný z nadřazené položky.

Na úrovni složky vCenter serveru nemůžete obor zjišťování inventáře. Pokud potřebujete obor zjišťovat na servery ve složce, vytvořte uživatele a udělte každému požadovanému serveru přístup individuálně. Host a složky clusteru jsou podporovány.


### <a name="assign-a-role-for-assessment"></a>Přiřazení role k posouzení

1. Na účtu vCenter zařízení, který používáte pro zjišťování, použijte roli jen **pro čtení** u všech nadřazených objektů, které hostují servery, které chcete zjišťovat a vyhodnocovat (hostitel, cluster, složka hostitelů, složka clusterů, až do Datacenter).
2. Rozšiřte tato oprávnění na podřízené objekty v hierarchii.

    ![Přiřazení oprávnění](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Přiřazení role pro migraci bez agentů

1. Na účtu vCenter zařízení, který používáte pro migraci, použijte uživatelem definovanou roli, která má [potřebná oprávnění](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless), do všech nadřazených objektů, které hostují servery, které chcete zjišťovat a migrovat.
2. Roli můžete pojmenovat s něčím, co je snazší identifikovat. Například <em>Azure_Migrate</em>.

## <a name="work-around-for-server-folder-restriction"></a>Řešení pro omezení složky na serveru

V současné době nástroj Azure Migrate: Discovery and Assessment nemůže vyhledat servery, pokud je přístup povolen na úrovni složky vCenter serveru. Pokud chcete určit obor zjišťování a vyhodnocení pomocí složek na serveru, použijte toto alternativní řešení.

1. Přiřaďte oprávnění jen pro čtení na všech serverech umístěných ve složkách, které chcete určit pro obor zjišťování a posouzení.
2. Udělte přístup jen pro čtení ke všem nadřazeným objektům, které jsou hostiteli serverů Host, cluster, hosts, složka clustery, až po datové centrum. Nemusíte šířit oprávnění ke všem podřízeným objektům.
3. Chcete-li použít pověření pro zjišťování, vyberte datové centrum jako **Rozsah shromažďování**.


Nastavení řízení přístupu na základě rolí zajišťuje, aby měl odpovídající uživatelský účet vCenter přístup pouze k serverům pro konkrétního tenanta.


## <a name="next-steps"></a>Další kroky

[Nastavení zařízení](how-to-set-up-appliance-vmware.md)