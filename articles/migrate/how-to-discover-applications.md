---
title: Objevte aplikace, role a funkce na místních serverech pomocí Migrace Azure
description: Zjistěte, jak zjistit aplikace, role a funkce na místních serverech pomocí Azure Migrate Server Assessment.
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: e8ce279afc845ebf37ad4ab8b2ce7236cb18137a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453578"
---
# <a name="discover-machine-apps-roles-and-features"></a>Seznamte se s aplikacemi, rolemi a funkcemi strojů

Tento článek popisuje, jak zjistit aplikace, role a funkce na místních serverech pomocí Azure Migrate: Server Assessment.

Zjišťování inventáře aplikací a rolí a funkcí spuštěných na vašich místních počítačích vám pomůže identifikovat a naplánovat cestu migrace do Azure, která je přizpůsobená vašim úlohám.

> [!NOTE]
> Zjišťování aplikací je aktuálně ve verzi preview jenom pro virtuální virtuální měny VMware a je omezeno jenom na zjišťování. Hodnocení založené na aplikacích zatím nenabízíme. Posouzení na základě počítače pro místní virtuální počítače VMware, virtuální počítače Hyper-V a fyzické servery.

Zjišťování aplikací pomocí migrace Azure: Vyhodnocení serveru je bez agenta. Na počítačích a virtuálních počítačích se nic neinstaluje. Vyhodnocení serveru používá zařízení Azure Migrate k provádění zjišťování spolu s pověřeními hosta počítače. Zařízení vzdáleně přistupuje k počítačům VMware pomocí api vsystému VMware.


## <a name="before-you-start"></a>Než začnete

1. Ujistěte se, že jste [vytvořili](how-to-add-tool-first-time.md) projekt Migrace Azure.
2. Ujistěte se, že jste do projektu [přidali](how-to-assess.md) nástroj Azure Migrate: Server Assessment.
4. Zkontrolujte [požadavky na vmware](migrate-support-matrix-vmware.md#vmware-requirements) pro zjišťování a hodnocení virtuálních počítačích VMware pomocí zařízení Azure Migrate.
5. Zkontrolujte [požadavky](migrate-appliance.md) na nasazení zařízení Azure Migrate.
6. [Ověřte podporu a požadavky](migrate-support-matrix-vmware.md#application-discovery) na zjišťování aplikací.

## <a name="prepare-for-app-discovery"></a>Příprava na zjišťování aplikace

1. [Připravte se na nasazení zařízení](tutorial-prepare-vmware.md). Příprava zahrnuje ověření nastavení zařízení a nastavení účtu, který bude zařízení používat pro přístup k serveru vCenter.
2. Ujistěte se, že máte uživatelský účet (každý pro servery windows a linux) s oprávněními správce pro počítače, na kterých chcete zjistit aplikace, role a funkce.
3. [Nasazení zařízení Azure Migrate](how-to-set-up-appliance-vmware.md) a zahájení zjišťování. Chcete-li nasadit zařízení, stáhněte a importujte šablonu OVA do společnosti VMware a vytvořte jej jako virtuální počítače VMware. Nakonfigurujete zařízení a pak ho zaregistrujete pomocí Azure Migrate.
2. Při nasazování zařízení, chcete-li spustit průběžné zjišťování, zadáte následující:
    - Název serveru vCenter, ke kterému se chcete připojit.
    - Pověření, která jste vytvořili pro zařízení pro připojení k serveru vCenter.
    - Přihlašovací údaje účtu, které jste vytvořili pro zařízení pro připojení k virtuálním počítačům se systémem Windows/Linux.

Po nasazení zařízení a zadání přihlašovacích údajů spustí zařízení průběžné zjišťování metadat virtuálních aplikací a dat o výkonu spolu s a zjišťováním aplikací, funkcí a rolí.  Doba zjišťování aplikací závisí na tom, kolik virtuálních počítačů máte. Obvykle trvá hodinu pro zjišťování aplikací 500 virtuálních počítačů.

## <a name="review-and-export-the-inventory"></a>Kontrola a export zásob

Po ukončení zjišťování, pokud jste zadali přihlašovací údaje pro zjišťování aplikací, můžete zkontrolovat a exportovat inventář aplikací na webu Azure Portal.

1. V **Migraci Azure – servery, které** > **Azure migruje: Vyhodnocení serveru**, kliknutím na zobrazený počet otevřete stránku **Zjištěné servery.**

    > [!NOTE]
    > V této fázi můžete také volitelně nastavit mapování závislostí pro zjištěné počítače, takže můžete vizualizovat závislosti mezi počítači, které chcete posoudit. [Další informace](how-to-create-group-machine-dependencies.md).

2. V **části Zjištěné aplikace**klepněte na zobrazený počet.
3. V **inventáři aplikací**můžete zkontrolovat zjištěné aplikace, role a funkce.
4. Chcete-li exportovat inventář, klepněte v části **Zjištěné servery**na **položku Exportovat inventář aplikací**.

Inventář aplikací se exportuje a stáhne ve formátu Excelu. Na listu **Zásoby aplikací** se zobrazí všechny aplikace zjištěné ve všech počítačích.

## <a name="next-steps"></a>Další kroky

- [Vytvořte hodnocení](how-to-create-assessment.md) pro migraci zjištěných serverů výtahem a posunem.
- Vyhodnoťte databáze serveru SQL Server pomocí [migrace Azure: Vyhodnocení databáze](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
