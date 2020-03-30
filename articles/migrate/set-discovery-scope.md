---
title: Nastavení oboru pro zjišťování virtuálních počítačů VMware pomocí migrace Azure
description: Popisuje, jak nastavit obor zjišťování pro vyhodnocení virtuálních počítačů VMware a migrace pomocí Migrace Azure.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337633"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Nastavení oboru zjišťování pro virtuální měny VMware

Tento článek popisuje, jak omezit rozsah zjišťování pro virtuální počítače VMware, které jsou zjištěny [zařízení Azure Migrate](migrate-appliance-architecture.md).

Zařízení Azure Migrate zjišťuje místní virtuální počítače VMware, když: 

- Pomocí nástroje [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) můžete vyhodnotit virtuální počítače VMware pro migraci do Azure.
- Nástroj [Migrace:migrace serveru Azure](migrate-services-overview.md#azure-migrate-server-migration-tool) slouží k [migraci](server-migrate-overview.md) virtuálních počítačů VMware bez agentů do Azure.

## <a name="set-discovery-scope"></a>Nastavit obor zjišťování


Po nastavení zařízení Azure Migrate pro vyhodnocení nebo migraci virtuálních počítačích VMware zařízení začne zjišťovat virtuální počítače a odesílat metadata virtuálních zařízení do Azure. Před připojením zařízení k serveru vCenter server pro zjišťování, můžete nastavit obor zjišťování omezit zjišťování na vCenter Server datových center, clustery, složku clusterů, hostitelé, složku hostitelů nebo jednotlivých virtuálních hostitelů.

Chcete-li nastavit obor, přiřaďte oprávnění k účtu, který Azure Migrate používá pro přístup k serveru vCenter.

## <a name="create-a-vcenter-user-account"></a>Vytvoření uživatelského účtu vCenter

Pokud jste ještě nenastavili uživatelský účet vCenter, který zařízení Azure Migrate používá ke zjišťování, vyhodnocuje a migruje virtuální počítače VMware, udělejte to jako první.

1.    Přihlaste se k webovému klientovi vSphere jako správce serveru vCenter.
2.    Vyberte > **Možnost Uživatelé a skupiny správu spřipovou**a klikněte na kartu **Uživatelé.** **Administration**
3.    Vyberte ikonu **Nový uživatel.**
4.    Vyplňte nové informace o uživateli > **OK**.

Oprávnění účtu závisí na tom, co nasazujete.

**Funkce** | **Oprávnění účtu**
--- | ---
[Posoudit](tutorial-assess-vmware.md)| Účet potřebuje přístup jen pro čtení.
[Zjišťování aplikací/rolí/funkcí](how-to-discover-applications.md) | Účet potřebuje přístup jen pro čtení, s oprávněními povolenými pro virtuální počítače > operace hosta.
[Analyzovat závislosti (bez agenta)](how-to-create-group-machine-dependencies-agentless.md) | Účet potřebuje přístup jen pro čtení, s oprávněními povolenými pro virtuální počítače > operace hosta.
[Migrace (bez agenta)](tutorial-migrate-vmware.md) | Potřebujete roli, které jsou přiřazena správná oprávnění.<br/><br/> Chcete-li vytvořit roli, přihlaste se k webovému klientovi vSphere jako správce serveru vCenter. Vyberte instanci serveru vCenter > **role Vytvořit**. Zadejte název role, například <em>Azure_Migrate</em>, a přiřaďte roli [požadovaná oprávnění.](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)


## <a name="assign-permissions-on-vcenter-objects"></a>Přiřazení oprávnění k objektům vCenter

K objektům inventáře můžete přiřadit oprávnění pomocí jedné ze dvou metod:

- Přiřazení role s požadovanými oprávněními k účtu, který používáte, pro všechny nadřazené objekty, které hostují virtuální uživatele, které chcete zjistit nebo migrovat.
- Případně můžete přiřadit roli a uživatelský účet na úrovni datového centra a šířit je do podřízených objektů. Potom uložte účtu roli **Žádný přístup** pro každý objekt, který nechcete zjistit nebo migrovat. Tento přístup nedoporučujeme, protože je těžkopádný a může vystavit ovládací prvky přístupu, protože každý nový podřízený objekt je automaticky udělen přístup zděděný od nadřazeného objektu.

Chcete-li přiřadit roli účtu, který používáte pro všechny relevantní objekty, postupujte takto:

- **Pro posouzení**: Pro posouzení **virtuálních účtů** použijte roli jen pro čtení na uživatelský účet vCenter pro všechny nadřazené objekty hostující virtuální chod, které chcete zjistit. Nadřazené objekty zahrnuty: hostitel, složka hostitelů, cluster a složka clusterů v hierarchii, až do datového centra. Rozšíříte tato oprávnění na podřízené objekty v hierarchii.

    ![Přiřazení oprávnění](./media/tutorial-assess-vmware/assign-perms.png)

- **Pro migraci bez agenta**: Pro migraci bez agenta použijte uživatelem definovanou roli s [požadovanými oprávněními](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) pro uživatelský účet pro všechny nadřazené objekty hostující virtuální uživatele, které chcete zjistit. Roli můžete pojmenovat <em>Azure_Migrate</em>.

### <a name="scope-support"></a>Podpora oboru

V současné době nástroj pro vyhodnocení serveru nemůže zjistit virtuální chod, pokud má účet vCenter přístup udělený na úrovni složky virtuálního virtuálního připojení vCenter. Složky hostitelů a clusterů jsou podporovány.

Pokud chcete obor zjišťování podle složek virtuálních počítačových společností, proveďte další postup, abyste zajistili, že účet vCenter má přístup jen pro čtení přiřazený na úrovni virtuálního počítače.

1. Přiřaďte oprávnění jen pro čtení všem virtuálním počítačům ve složkách virtuálních počítačích, které chcete obor pro zjišťování.
2. Udělit přístup jen pro čtení pro všechny nadřazené objekty, které jsou hostitelské virtuální chod.
    - Jsou zahrnuty všechny nadřazené objekty (hostitel, složka hostitelů, cluster, složka clusterů) v hierarchii až po datové centrum.
    - Není nutné šířit oprávnění pro všechny podřízené objekty.
3. Pověření pro zjišťování použijte výběrem datového centra jako **oboru kolekce**. Nastavení řízení přístupu založené na rolích zajišťuje, že odpovídající uživatelský účet vCenter má přístup pouze k virtuálním počítačům specifickým pro klienta.


## <a name="next-steps"></a>Další kroky

[Nastavte přístroj](how-to-set-up-appliance-vmware.md) a [začněte průběžně objevovat](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential).
