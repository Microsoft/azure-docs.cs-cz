---
title: Vyhledávání aplikací na místních serverech pomocí Azure Migrate
description: Naučte se zjišťovat aplikace, role a funkce na místních serverech pomocí vyhodnocení Azure Migrate serveru.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: eb589c08122cd47747c005c13d12b336319fd558
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752001"
---
# <a name="discover-machine-apps-roles-and-features"></a>Objevte aplikace pro počítače, role a funkce

Tento článek popisuje, jak zjišťovat aplikace, role a funkce na místních serverech pomocí Azure Migrate: posouzení serveru.

Zjišťování inventáře aplikací, rolí a funkcí, které běží na místních počítačích, pomáhá identifikovat a přizpůsobit cestu migrace do Azure pro vaše úlohy. Zjišťování aplikací používá zařízení Azure Migrate k provádění zjišťování pomocí pověření hosta virtuálního počítače. Zjišťování aplikací je bez agenta. Na virtuálních počítačích není nic nainstalované.

> [!NOTE]
> Zjišťování aplikací je momentálně ve verzi Preview jenom pro virtuální počítače VMware a je omezené jenom na zjišťování. Ještě nenabízíme vyhodnocení na základě aplikace. 


## <a name="before-you-start"></a>Než začnete

- Ujistěte se, že jste udělali toto:
    - Byl [vytvořen](./create-manage-projects.md) Azure Migrate projekt.
    - [Přidání](how-to-assess.md) nástroje Azure Migrate: Nástroj pro vyhodnocení serveru do projektu.
- Kontrola [požadavků a podpory zjišťování aplikací](migrate-support-matrix-vmware.md#vmware-requirements)
- Ujistěte se, že virtuální počítače, ve kterých spouštíte zjišťování aplikací, mají nainstalované prostředí PowerShell verze 2,0 nebo novější a nástroje VMware (novější než 10.2.0) jsou nainstalované.
- Ověřte [požadavky](migrate-appliance.md) na nasazení zařízení Azure Migrate.


## <a name="deploy-the-azure-migrate-appliance"></a>Nasazení zařízení se službou Azure Migrate

1. [Projděte si](migrate-appliance.md#appliance---vmware) požadavky na nasazení zařízení Azure Migrate.
2. Zkontrolujte adresy URL Azure, které bude zařízení potřebovat pro přístup k [veřejným](migrate-appliance.md#public-cloud-urls) a [státním cloudům](migrate-appliance.md#government-cloud-urls).
3. [Zkontrolujte data](migrate-appliance.md#collected-data---vmware) , která zařízení shromažďuje během zjišťování a posouzení.
4. [Poznamenejte si](migrate-support-matrix-vmware.md#port-access-requirements) požadavky na přístup k portu pro dané zařízení.
5. [Nasaďte zařízení Azure Migrate](how-to-set-up-appliance-vmware.md) a spusťte zjišťování. Pokud chcete zařízení nasadit, Stáhněte a importujte šablonu vajíček do VMware a vytvořte zařízení jako virtuální počítač VMware. Zařízení nakonfigurujete a pak ho zaregistrujete Azure Migrate.
6. Při nasazování zařízení můžete spustit průběžné zjišťování zadáním následujících možností:
    - Název vCenter Server, ke kterému se chcete připojit
    - Přihlašovací údaje, které jste vytvořili pro zařízení pro připojení k vCenter Server.
    - Přihlašovací údaje účtu, které jste vytvořili pro zařízení pro připojení k virtuálním počítačům s Windows/Linux.

Po nasazení zařízení a zadání přihlašovacích údajů zařízení spustí průběžné zjišťování metadat a dat o výkonu virtuálních počítačů společně se službou a zjišťováním aplikací, funkcí a rolí.  Doba zjišťování aplikací závisí na tom, kolik virtuálních počítačů máte. Zjišťování aplikací pro virtuální počítače 500 obvykle trvá hodinu.

## <a name="verify-permissions"></a>Ověření oprávnění

[Vytvořili jste účet vCenter Server jen pro čtení](./tutorial-discover-vmware.md#prepare-vmware) pro zjišťování a posouzení. Účet jen pro čtení potřebuje oprávnění povolená pro **Virtual Machines**  >  **operace hostů**, aby bylo možné pracovat s virtuálním počítačem pro zjišťování aplikací.

### <a name="add-the-user-account-to-the-appliance"></a>Přidat uživatelský účet do zařízení

Přidejte uživatelský účet následujícím způsobem:

1. Otevřete aplikaci pro správu zařízení. 
2. Přejděte na panel **poskytnout podrobnosti vCenter** .
3. V nabídce **zjistit aplikaci a závislosti na virtuálních počítačích** klikněte na **Přidat přihlašovací údaje** .
3. Vyberte **operační systém**, zadejte popisný název účtu a heslo pro **uživatelské jméno** / **Password** .
6. Klikněte na **Uložit**.
7. Klikněte na **Uložit a spusťte zjišťování**.

    ![Přidat uživatelský účet VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)


## <a name="review-and-export-the-inventory"></a>Kontrola a export inventáře

Pokud jste po dokončení zjišťování zadali přihlašovací údaje pro zjišťování aplikací, můžete zkontrolovat a vyexportovat inventář aplikací v Azure Portal.

1. V **Azure Migrate-servery**  >  **Azure Migrate: vyhodnocování serveru** klikněte na zobrazený počet a otevřete stránku **zjištěné servery** .

    > [!NOTE]
    > V této fázi můžete také volitelně nastavit analýzu závislostí pro zjištěné počítače, abyste mohli vizualizovat závislosti mezi počítači, které chcete vyhodnotit. [Přečtěte si další informace](concepts-dependency-visualization.md) o analýze závislostí.

2. V části **zjištěné aplikace** klikněte na zobrazený počet.
3. V **inventáři aplikací** můžete zkontrolovat zjištěné aplikace, role a funkce.
4. Pokud chcete exportovat inventář, klikněte na stránce **zjištěné servery** na **exportovat inventář aplikací**.

Inventář aplikací je exportovaný a stažený ve formátu aplikace Excel. V listu **inventáře aplikací** se zobrazí všechny zjištěné aplikace napříč všemi počítači.

## <a name="next-steps"></a>Další kroky

- [Vytvořte posouzení](how-to-create-assessment.md) pro zjištěné servery.
- Vyhodnotit SQL Server databáze pomocí [Azure Migrate: vyhodnocení databáze](/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).