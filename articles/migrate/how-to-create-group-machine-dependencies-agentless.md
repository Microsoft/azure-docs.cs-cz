---
title: Nastavení analýzy závislostí bez agenta v azure migrate server assessment
description: Nastavení analýzy závislostí bez agenta v Azure Migrate Server Assessment.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: af767bf73a3b9a6f2a91298987f11974499fd694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455702"
---
# <a name="set-up-agentless-dependency-visualization"></a>Nastavení vizualizace závislosti bez agenta 

Tento článek popisuje, jak nastavit analýzu závislostí bez agenta v Azure Migrate:Server Assessment. [Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat a pochopit závislosti napříč počítači, které chcete posoudit a migrovat do Azure.


> [!IMPORTANT]
> Vizualizace závislostí bez agenta je aktuálně ve verzi preview jenom pro virtuální počítače VMware, která byla zjištěna pomocí nástroje Azure Migrate:Server Assessment.
> Funkce mohou být omezené nebo neúplné.
> Tato předběžná verze je pokryta zákaznickou podporou a lze ji použít pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



## <a name="before-you-start"></a>Než začnete

- [Další informace o](concepts-dependency-visualization.md#agentless-analysis) analýze závislostí bez agentů.
- [Kontrola](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) požadavků na požadavky a požadavky na podporu pro nastavení vizualizace závislostí bez agenta pro virtuální zařízení VMware
- Ujistěte se, že jste [vytvořili](how-to-add-tool-first-time.md) projekt Migrace Azure.
- Pokud jste už vytvořili projekt, ujistěte se, že jste [přidali](how-to-assess.md) nástroj Azure Migrate:Server Assessment.
- Ujistěte se, že jste nastavili [zařízení Azure Migrate,](migrate-appliance.md) abyste zjistili vaše místní počítače. Přečtěte si, jak nastavit zařízení pro virtuální zařízení [VMware.](how-to-set-up-appliance-vmware.md) Zařízení zjišťuje místní počítače a odesílá metadata a data o výkonu do Azure Migrate:Server Assessment.


## <a name="current-limitations"></a>Aktuální omezení

- Právě teď nemůžete přidat nebo odebrat server ze skupiny v zobrazení analýzy závislostí.
- Mapa závislostí pro skupinu serverů není momentálně k dispozici.
- V současné době nelze data závislostí stáhnout ve tabulkovém formátu.

## <a name="create-a-user-account-for-discovery"></a>Vytvoření uživatelského účtu pro zjišťování

Nastavte uživatelský účet tak, aby vyhodnocení serveru přístup k virtuálnímu účtu pro zjišťování. [Přečtěte si o](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) požadavcích na účet.


## <a name="add-the-user-account-to-the-appliance"></a>Přidejte uživatelský účet do zařízení

Přidejte uživatelský účet do zařízení.

1. Otevřete aplikaci pro správu zařízení. 
2. Přejděte na panel **Poskytnout podrobnosti vCenter.**
3. V **aplikaci Discover application and dependencies on VM klikněte**na **Add credentials** .
3. Zvolte **operační systém**, zadejte popisný název účtu a uživatelské **jméno**/**Heslo**
6. Klikněte na **Uložit**.
7. Klepněte na tlačítko **Uložit a spustit zjišťování**.

    ![Přidání uživatelského účtu virtuálního uživatele](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Spustit zjišťování závislostí

Vyberte počítače, na kterých chcete povolit zjišťování závislostí.

1. V **Azure Migrate: Vyhodnocení serveru**klikněte na **Zjištěné servery**.
2. Klikněte na ikonu **analýzy závislostí.**
3. Klepněte na tlačítko **Přidat servery**.
3. Na stránce **Přidat servery** zvolte zařízení, které zjišťuje příslušné počítače.
4. Ze seznamu strojů vyberte stroje.
5. Klepněte na tlačítko **Přidat servery**.

    ![Spustit zjišťování závislostí](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Můžete vizualizovat závislosti přibližně šest hodin po spuštění zjišťování závislostí.

## <a name="visualize-dependencies"></a>Vizualizace závislostí

1. V **Azure Migrate: Vyhodnocení serveru**klikněte na **Zjištěné servery**.
2. Vyhledejte počítač, který chcete zobrazit.
3. Ve sloupci **Závislosti** klikněte na **Zobrazit závislosti.**
4. Změňte časové období, pro které chcete mapu zobrazit, pomocí rozevíracího přehledu **Doba trvání.**
5. Rozbalte skupinu **Klient** a seznam počítačů se závislostí na vybraném počítači.
6. Rozbalte skupinu **Port** a seznam počítačů, které mají závislost na vybraném počítači.
7. Chcete-li přejít do zobrazení mapy některého ze závislých počítačů, klikněte na název počítače > **mapa serveru Zatížení**

    ![Rozbalte skupinu portů serveru a načíst mapu serveru](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Rozbalit skupinu klientů ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Rozbalte vybraný počítač a zobrazte podrobnosti na úrovni procesu pro každou závislost.

    ![Rozbalte server tak, aby zobrazoval procesy](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Informace o procesu pro závislost nejsou vždy k dispozici. Pokud není k dispozici, závislost je zobrazen s procesem označeným jako "Neznámý proces".

## <a name="stop-dependency-discovery"></a>Zastavit zjišťování závislostí

Vyberte počítače, na kterých chcete zastavit zjišťování závislostí.

1. V **Azure Migrate: Vyhodnocení serveru**klikněte na **Zjištěné servery**.
2. Klikněte na ikonu **analýzy závislostí.**
3. Klepněte na **tlačítko Odebrat servery**.
3. Na stránce **Odebrat servery** zvolte **zařízení,** které zjišťuje virtuální chod, na kterém chcete zastavit zjišťování závislostí.
4. Ze seznamu strojů vyberte stroje.
5. Klepněte na **tlačítko Odebrat servery**.


## <a name="next-steps"></a>Další kroky

[Seskupte stroje](how-to-create-a-group.md) k posouzení.
