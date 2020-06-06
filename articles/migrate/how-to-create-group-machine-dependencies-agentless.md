---
title: Nastavení analýzy závislostí bez agentů v serveru Azure Migrate Assessment
description: Nastavte analýzu závislostí bez agentů v Azure Migrate Server Assessment.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: 3259c861b0e64b560eb2a17a832a02b87855bebf
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449188"
---
# <a name="set-up-agentless-dependency-visualization"></a>Nastavení vizualizace závislosti bez agentů 

Tento článek popisuje, jak nastavit analýzu závislostí bez agentů v Azure Migrate: posouzení serveru. [Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat a pochopit závislosti mezi počítači, které chcete vyhodnotit a migrovat do Azure.


> [!IMPORTANT]
> Vizualizace závislostí bez agentů je aktuálně ve verzi Preview pro virtuální počítače VMware, zjištěná pomocí nástroje Azure Migrate: Nástroj pro vyhodnocení serveru.
> Funkce můžou být omezené nebo neúplné.
> Tuto verzi Preview pokrývá zákaznická podpora a je možné ji použít pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



## <a name="before-you-start"></a>Než začnete

- [Přečtěte si o](concepts-dependency-visualization.md#agentless-analysis) analýze závislostí bez agentů.
- [Přečtěte](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) si požadavky a požadavky na podporu pro nastavení vizualizace závislosti bez agentů pro virtuální počítače VMware.
- Ujistěte se, že jste [vytvořili](how-to-add-tool-first-time.md) projekt Azure Migrate.
- Pokud jste již vytvořili projekt, ujistěte se, že jste [přidali](how-to-assess.md) Azure Migrate: nástroj Server Assessment Tool.
- Ujistěte se, že jste nastavili [zařízení Azure Migrate](migrate-appliance.md) pro zjišťování vašich místních počítačů. Přečtěte si, jak nastavit zařízení pro virtuální počítače [VMware](how-to-set-up-appliance-vmware.md) . Zařízení zjišťuje místní počítače a odesílá data o metadatech a výkonu Azure Migrate: posouzení serveru.


## <a name="current-limitations"></a>Aktuální omezení

- Teď nemůžete přidat nebo odebrat server ze skupiny v zobrazení analýzy závislostí.
- Mapování závislostí pro skupinu serverů není aktuálně k dispozici.
- V současné době nelze data závislostí stáhnout v tabulkovém formátu.

## <a name="create-a-user-account-for-discovery"></a>Vytvoření uživatelského účtu pro zjišťování

Nastavte uživatelský účet, aby vyhodnocování serveru mělo přístup k virtuálnímu počítači pro zjišťování. [Přečtěte si informace](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) o požadavcích na účet.


## <a name="add-the-user-account-to-the-appliance"></a>Přidat uživatelský účet do zařízení

Přidejte uživatelský účet do zařízení.

1. Otevřete aplikaci pro správu zařízení. 
2. Přejděte na panel **poskytnout podrobnosti vCenter** .
3. V nabídce **zjistit aplikaci a závislosti na virtuálních počítačích**klikněte na **Přidat přihlašovací údaje** .
3. Vyberte **operační systém**, zadejte popisný název účtu a heslo pro **uživatelské jméno** / **Password** .
6. Klikněte na **Uložit**.
7. Klikněte na **Uložit a spusťte zjišťování**.

    ![Přidat uživatelský účet VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Spustit zjišťování závislosti

Vyberte počítače, u kterých chcete povolit zjišťování závislostí.

1. V **Azure Migrate: vyhodnocování serveru**klikněte na **zjištěné servery**.
2. Klikněte na ikonu **Analýza závislostí** .
3. Klikněte na **Přidat servery**.
4. Na stránce **Přidat servery** vyberte zařízení, které zjišťuje příslušné počítače.
5. V seznamu počítač vyberte počítače.
6. Klikněte na **Přidat servery**.

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

## <a name="export-dependency-data"></a>Exportovat data závislostí

1. V **Azure Migrate: vyhodnocování serveru**klikněte na **zjištěné servery**.
2. Klikněte na ikonu **Analýza závislostí** .
3. Klikněte na **exportovat závislosti aplikací**.
4. Na stránce **exportovat závislosti aplikací** vyberte zařízení, které zjišťuje příslušné počítače.
5. Vyberte čas spuštění a čas ukončení. Všimněte si, že data si můžete stáhnout jenom za posledních 30 dní.
6. Klikněte na **exportovat závislost**.

Data závislosti se exportují a stahují ve formátu CSV. Stažený soubor obsahuje data závislostí napříč všemi počítači, které jsou povoleny pro analýzu závislostí. 

    ![Export dependencies](./media/how-to-create-group-machine-dependencies-agentless/export.png)


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
