---
title: Zjišťování aplikací, rolí a funkcí nainstalovaných na místních serverech pomocí posouzení Azure Migrate serveru
description: Popisuje, jak zjišťovat aplikace, role a funkce na místních serverech pomocí vyhodnocení Azure Migrate serveru.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 11/20/2019
ms.author: snehaa
ms.openlocfilehash: 279e326ace308b354d7bcb8366d3286980e7b8c6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278478"
---
# <a name="discover-machine-apps-roles-and-features"></a>Objevte aplikace pro počítače, role a funkce

Tento článek popisuje, jak zjišťovat aplikace, role a funkce na místních serverech pomocí Azure Migrate: posouzení serveru.

Zjišťování inventáře aplikací a rolí a funkcí spuštěných v místních počítačích vám pomůže identifikovat a naplánovat migraci do Azure, která je přizpůsobená pro vaše úlohy.

> [!NOTE]
> Zjišťování aplikací se momentálně podporuje jenom pro virtuální počítače VMware a je omezené jenom na zjišťování. Ještě nenabízíme vyhodnocení na základě aplikace.  Nyní posouzení serveru aktuálně vyhodnocuje místní virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery na úrovni počítače, a to pro migrace přenesených a Shift.

Zjišťování aplikací pomocí Azure Migrate: posouzení serveru není bez agenta. Na počítačích a virtuálních počítačích není potřeba nic instalovat. Posouzení serveru používá zařízení Azure Migrate k provádění zjišťování společně s přihlašovacími údaji hosta počítače. Zařízení vzdáleně přistupuje k počítačům VMware pomocí rozhraní API VMware.


## <a name="before-you-start"></a>Než začnete

1. Zkontrolujte [omezení podpory](migrate-support-matrix-vmware.md#application-discovery) pro zjišťování na úrovni aplikace.
2. Ujistěte se, že jste [vytvořili](how-to-add-tool-first-time.md) projekt Azure Migrate.
3. Pokud jste již vytvořili projekt, ujistěte se, že jste [přidali](how-to-assess.md) Azure Migrate: nástroj Server Assessment Tool.
4. Podívejte se na [požadavky VMware](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) pro zjišťování a vyhodnocování virtuálních počítačů VMware pomocí zařízení Azure Migrate.
4. Ověřte [požadavky](migrate-support-matrix-vmware.md#assessment-appliance-requirements) na nasazení zařízení Azure Migrate.

## <a name="prepare-for-app-discovery"></a>Příprava na zjišťování aplikací

1. [Příprava na nasazení zařízení](https://docs.microsoft.com/azure/migrate/tutorial-prepare-vmware) Příprava zahrnuje ověření nastavení zařízení a nastavení účtu, který bude zařízení používat pro přístup k vCenter Server.
2. Ujistěte se, že máte uživatelský účet (jeden pro servery se systémem Windows a Linux) s oprávněními správce pro počítače, na kterých chcete zjistit aplikace, role a funkce.
3. [Nasaďte zařízení VMware](how-to-set-up-appliance-vmware.md) pro spuštění zjišťování. Pokud chcete zařízení nasadit, Stáhněte a importujte šablonu vajíček do VMware a vytvořte zařízení jako virtuální počítač VMware. Zařízení nakonfigurujete a pak ho zaregistrujete Azure Migrate.
2. Při nasazování zařízení můžete spustit průběžné zjišťování zadáním následujících možností:
    - Název vCenter Server, ke kterému se chcete připojit
    - Přihlašovací údaje, které jste vytvořili pro zařízení pro připojení k vCenter Server.
    - Přihlašovací údaje účtu, které jste vytvořili pro zařízení pro připojení k virtuálním počítačům s Windows/Linux.

Po nasazení zařízení a zadání přihlašovacích údajů zařízení spustí průběžné zjišťování metadat a dat o výkonu virtuálních počítačů společně se službou a zjišťováním aplikací, funkcí a rolí.  Doba zjišťování aplikací závisí na tom, kolik virtuálních počítačů máte. Zjišťování aplikací pro virtuální počítače 500 obvykle trvá hodinu.

## <a name="review-and-export-the-inventory"></a>Kontrola a export inventáře

Pokud jste po dokončení zjišťování zadali přihlašovací údaje pro zjišťování aplikací, můžete zkontrolovat a vyexportovat inventář aplikací v Azure Portal.

1. V **Azure Migrate-servery** > **Azure Migrate: posouzení serveru**, kliknutím na zobrazený počet otevřete stránku **zjištěné servery** .

    > [!NOTE]
    > V této fázi můžete také volitelně nastavit mapování závislostí pro zjištěné počítače, abyste mohli vizualizovat závislosti mezi počítači, které chcete vyhodnotit. [Další informace](how-to-create-group-machine-dependencies.md).

2. V části **zjištěné aplikace**klikněte na zobrazený počet.
3. V **inventáři aplikací**můžete zkontrolovat zjištěné aplikace, role a funkce.
4. Pokud chcete exportovat inventář, klikněte na stránce **zjištěné servery**na **exportovat inventář aplikací**.

Inventář aplikací je exportovaný a stažený ve formátu aplikace Excel. V listu **inventáře aplikací** se zobrazí všechny zjištěné aplikace napříč všemi počítači.

## <a name="next-steps"></a>Další kroky

- [Vytvořte posouzení](how-to-create-assessment.md) pro migraci zjištěných serverů a přesunutí.
- Vyhodnotit SQL Server databáze pomocí [Azure Migrate: vyhodnocení databáze](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
