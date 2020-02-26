---
title: Nastavení Vizualizace závislostí bez agentů v Azure Migrate
description: Nastavte skupiny pomocí Vizualizace závislostí bez agentů v Azure Migrate Server Assessment.
ms.topic: article
ms.date: 2/24/2020
ms.openlocfilehash: c9425ad1fa78f14a194d3fe13c259dadf4eb5eb6
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589126"
---
# <a name="set-up-agentless-dependency-visualization"></a>Nastavení vizualizace závislosti bez agentů 

Tento článek popisuje, jak nastavit vizualizaci závislostí v Azure Migrate: vyhodnocení serveru. [Vizualizace závislostí](concepts-dependency-visualization.md#what-is-dependency-visualization) vám pomůže identifikovat a pochopit závislosti mezi počítači, které chcete vyhodnotit a migrovat do Azure.

Vizualizace závislostí bez agentů vám pomůže identifikovat závislosti počítačů bez instalace agentů do počítačů. Funguje tak, že zachytává data připojení TCP z počítačů, pro které je povolená.

> [!IMPORTANT]
> Vizualizace závislostí bez agentů je momentálně ve verzi Preview jenom pro virtuální počítače Azure VMware, zjištěná pomocí nástroje Azure Migrate: Server Assessment Tool.
> Funkce můžou být omezené nebo neúplné.
> Tuto verzi Preview pokrývá zákaznická podpora a je možné ji použít pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Aktuální omezení

- Teď nemůžete přidat nebo odebrat server ze skupiny v zobrazení analýzy závislostí.
- Mapování závislostí pro skupinu serverů není aktuálně k dispozici.
- V současné době nelze data závislostí stáhnout v tabulkovém formátu.

## <a name="before-you-start"></a>Než začnete

- [Zkontrolujte](concepts-dependency-visualization.md#agentless-visualization) požadavky a náklady spojené s vizualizací závislostí bez agentů.
- Přečtěte si [požadavky na podporu](migrate-support-matrix-vmware.md#agentless-dependency-visualization) pro nastavení vizualizace závislosti bez agentů.
- Ujistěte se, že jste [vytvořili](how-to-add-tool-first-time.md) projekt Azure Migrate.
- Pokud jste již vytvořili projekt, ujistěte se, že jste [přidali](how-to-assess.md) Azure Migrate: nástroj Server Assessment Tool.
- Ujistěte se, že jste nastavili [zařízení Azure Migrate](migrate-appliance.md) pro zjišťování vašich místních počítačů. Přečtěte si, jak nastavit zařízení pro virtuální počítače [VMware](how-to-set-up-appliance-vmware.md) . Zařízení zjišťuje místní počítače a odesílá data o metadatech a výkonu Azure Migrate: posouzení serveru.


## <a name="create-a-user-account-for-discovery"></a>Vytvoření uživatelského účtu pro zjišťování

Nastavte uživatelský účet, aby vyhodnocování serveru mělo přístup k virtuálnímu počítači pro zjišťování. Můžete zadat jeden uživatelský účet.

- **Virtuální počítače s Windows**: uživatelský účet musí být místní nebo správce domény.
- **Virtuální počítače se systémem Linux**: na účtu se vyžaduje oprávnění root. Tento uživatelský účet také vyžaduje tyto dvě funkce na/bin/netstat a/bin/LS soubory: CAP_DAC_READ_SEARCH a CAP_SYS_PTRACE.

## <a name="add-the-user-account-to-the-appliance"></a>Přidat uživatelský účet do zařízení

Přidejte uživatelský účet do zařízení.

1. Otevřete aplikaci pro správu zařízení. 
2. Přejděte na panel **poskytnout podrobnosti vCenter** .
3. V nabídce **zjistit aplikaci a závislosti na virtuálních počítačích**klikněte na **Přidat přihlašovací údaje** .
3. Vyberte **operační systém**, zadejte popisný název účtu a **uživatelské jméno**/**heslo** .
6. Klikněte na možnost **Uložit**.
7. Klikněte na **Uložit a spusťte zjišťování**.

    ![Přidat uživatelský účet VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Spustit zjišťování závislosti

Vyberte počítače, u kterých chcete povolit zjišťování závislostí.

1. V **Azure Migrate: vyhodnocování serveru**klikněte na **zjištěné servery**.
2. Klikněte na ikonu **Analýza závislostí** .
3. Klikněte na **Přidat servery**.
3. Na stránce **Přidat servery** vyberte zařízení, které zjišťuje příslušné počítače.
4. V seznamu počítač vyberte počítače.
5. Klikněte na **Přidat servery**.

    ![Spustit zjišťování závislosti](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Po spuštění zjišťování závislostí můžete vizualizovat závislosti přibližně po šesti hodinách.

## <a name="visualize-dependencies"></a>Vizualizace závislostí

1. V **Azure Migrate: vyhodnocování serveru**klikněte na **zjištěné servery**.
2. Vyhledejte počítač, který chcete zobrazit.
3. Ve sloupci **závislosti** klikněte na **Zobrazit závislosti.**
4. Změňte časové období, pro které chcete zobrazit mapu pomocí rozevíracího seznamu doba **Trvání** .
5. Rozbalte skupinu **klientů** pro výpis počítačů se závislostí na vybraném počítači.
6. Rozbalte skupinu **portů** a seznam počítačů, které mají závislost z vybraného počítače.
7. Chcete-li přejít k zobrazení mapy libovolného závislého počítače, klikněte na možnost název počítače > **načíst Server mapa** .

    ![Rozbalte položku Skupina portů serveru a mapa zátěžového serveru.](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Rozbalit skupinu klientů ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Rozbalte vybraný počítač pro zobrazení podrobností na úrovni procesu pro každou závislost.

    ![Rozbalte možnost Server a zobrazte procesy.](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Informace o procesu pro závislost není vždy k dispozici. Pokud není k dispozici, závislost je znázorněna s procesem označeným jako "Neznámý proces".

## <a name="stop-dependency-discovery"></a>Zastavit zjišťování závislosti

Vyberte počítače, u kterých chcete zastavit zjišťování závislostí.

1. V **Azure Migrate: vyhodnocování serveru**klikněte na **zjištěné servery**.
2. Klikněte na ikonu **Analýza závislostí** .
3. Klikněte na **odebrat servery**.
3. Na stránce **odebrat servery** vyberte **zařízení** , které zjišťuje virtuální počítače, na kterých jste se vyhledali zjišťování závislostí.
4. V seznamu počítač vyberte počítače.
5. Klikněte na **odebrat servery**.


## <a name="next-steps"></a>Další kroky

[Seskupte počítače](how-to-create-a-group.md) pro posouzení.
