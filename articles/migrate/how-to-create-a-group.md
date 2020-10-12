---
title: Seskupit počítače pro posouzení pomocí Azure Migrate | Microsoft Docs
description: Popisuje, jak seskupit počítače před spuštěním posouzení pomocí služby Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 867124a08bbad88b7dac5386ee6bc1c9c4d99c12
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85549898"
---
# <a name="create-a-group-for-assessment"></a>Vytvořit skupinu pro posouzení

Tento článek popisuje, jak vytvořit skupiny počítačů pro posouzení pomocí Azure Migrate: posouzení serveru.

[Azure Migrate](migrate-services-overview.md) vám pomůže migrovat do Azure. Azure Migrate poskytuje centralizované centrum pro sledování zjišťování, hodnocení a migrace místní infrastruktury, aplikací a dat do Azure. Centrum poskytuje nástroje Azure pro posuzování a migraci a také nabídky nezávislého výrobce softwaru (ISV) třetích stran. 

## <a name="grouping-machines"></a>Seskupení počítačů

Do skupin budete shromažďovat počítače, abyste posoudili, jestli jsou vhodné pro migraci do Azure, a abyste získali odhad velikosti Azure a jejich odhadování na náklady. Existuje několik způsobů, jak vytvořit skupiny:

- Pokud víte, které počítače je potřeba migrovat dohromady, můžete skupinu vytvořit ručně v Azure Migrate.
- Pokud si nejste jistí, jaké počítače je potřeba seskupit dohromady, můžete k vytváření skupin použít funkci Vizualizace závislostí v Azure Migrate. 

> [!NOTE]
> Funkce vizualizace závislosti není v Azure Government k dispozici.

## <a name="create-a-group-manually"></a>Vytvořit skupinu ručně

Můžete vytvořit skupinu ve stejné době, kdy [vytvoříte posouzení](how-to-create-assessment.md).

Pokud chcete vytvořit skupinu ručně mimo vytváření posouzení, udělejte toto:

1. V projektu Azure Migrate > **Přehled**klikněte na možnost **zhodnotit a migrovat servery**. V **Azure Migrate: vyhodnocování serveru**, klikněte na **skupiny** .
    - Pokud jste ještě nepřidali Azure Migrate: Nástroj pro vyhodnocení serveru, klikněte na tlačítko a přidejte ho. [Další informace](how-to-assess.md).
    - Pokud jste ještě nevytvořili projekt Azure Migrate, [Přečtěte si další informace](how-to-add-tool-first-time.md).

    ![Vybrat skupiny](./media/how-to-create-a-group/select-groups.png)

2. Klikněte na ikonu **skupiny** .
3. V části **vytvořit skupinu**zadejte název skupiny a do pole **název zařízení**vyberte zařízení Azure Migrate, které používáte pro zjišťování počítačů.
4. V seznamu počítač vyberte počítače, které chcete přidat do skupiny > **vytvořit**.

    ![Vytvoření skupiny](./media/how-to-create-a-group/create-group.png)

Tuto skupinu teď můžete používat při [vytváření posouzení virtuálních počítačů Azure](how-to-create-assessment.md) nebo [vyhodnocení řešení Azure VMware (AVS)](how-to-create-azure-vmware-solution-assessment.md). Všimněte si, že můžete vytvořit posouzení služby AVS u skupin s pouze virtuálními počítači VMware. 

## <a name="refine-a-group-with-dependency-mapping"></a>Upřesnění skupiny pomocí mapování závislostí

Mapování závislostí pomáhá vizualizovat závislosti mezi počítači. Mapování závislostí se obvykle používá, pokud chcete vyhodnotit skupiny počítačů s vyšší úrovní spolehlivosti.
- Před spuštěním posouzení vám pomůže křížově kontrolovat závislosti počítačů. 
- Pomůže vám taky efektivně naplánovat migraci do Azure tím, že zajistí, že se nic nestane, a tím se vyhnete výpadkům při migraci.
- Můžete zjistit vzájemné závislé systémy, které potřebují migrovat dohromady, a určit, jestli operační systém stále obsluhuje uživatele, nebo je kandidátem na vyřazení z provozu místo migrace.

Pokud jste už [nastavili mapování závislostí](how-to-create-group-machine-dependencies.md)a chcete upravit existující skupinu, udělejte toto:

1. Na kartě **servery** na dlaždici **Azure Migrate: vyhodnocování serveru** klikněte na **skupiny**.
2. Klikněte na skupinu, kterou chcete upřesnit.
    - Pokud jste ještě nenastavili mapování závislostí, zobrazí se ve sloupci **závislosti** stav **instalace vyžaduje** . Pro každý virtuální počítač, pro který chcete vizualizovat závislosti, klikněte na **vyžaduje instalaci**. Než budete moci mapovat závislosti počítačů, nainstalujte na každý virtuální počítač několik agentů. [Další informace](how-to-create-group-machine-dependencies.md).

        ![Přidat mapování závislostí](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Pokud jste již nastavili mapování závislostí, otevřete na stránce skupina kliknutím na možnost **Zobrazit závislosti** mapu závislostí skupiny.

3. Po kliknutí na **Zobrazit závislosti**se v mapě závislosti skupin zobrazí následující:

    - Příchozí (klienti) a odchozí (servery) připojení TCP k a ze všech počítačů ve skupině, ve které jsou nainstalováni agenti závislostí.
    - Závislé počítače, které nemají nainstalované agenty závislosti, jsou seskupeny podle čísel portů.
    - Závislé počítače s nainstalovanými agenty závislostí se zobrazují jako samostatná pole.
    - Procesy běžící v počítači. Rozbalením jednotlivých polí počítače zobrazíte procesy.
    - Vlastnosti počítače (včetně plně kvalifikovaného názvu domény, operačního systému, adresy MAC) Kliknutím na jednotlivá pole počítačů zobrazíte podrobnosti.

4. Pokud chcete zobrazit závislosti v časovém intervalu dle vašeho výběru, upravte časový rozsah (ve výchozím nastavení hodinu) zadáním počátečního a koncového data nebo doby trvání.

    > [!NOTE]
    > Časový rozsah může být až hodinu. Pokud potřebujete delší rozsah, použijte [Azure monitor k dotazování závislých dat](how-to-create-group-machine-dependencies.md) po delší dobu.

5. Jakmile identifikujete závislosti, které byste chtěli přidat do skupiny nebo je z ní odebrat, můžete skupinu upravit. K přidání nebo odebrání počítačů ze skupiny použijte Ctrl + kliknutí.

    - Je možné přidat pouze počítače, které byly zjištěny.
    - Přidání a odebrání počítačů zruší platnost minulých posouzení pro skupinu.
    - Při úpravě skupiny můžete volitelně vytvořit nové hodnocení.


## <a name="next-steps"></a>Další kroky

Naučte se nastavit a používat [Mapování závislostí](how-to-create-group-machine-dependencies.md) k vytváření vysoce důvěryhodných skupin.

