---
title: Seskupit servery pro posouzení pomocí Azure Migrate | Microsoft Docs
description: Popisuje, jak seskupit servery před spuštěním posouzení pomocí služby Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 0570ed73b86223025b250e269d7e2f358473f004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780850"
---
# <a name="create-a-group-for-assessment"></a>Vytvořit skupinu pro posouzení

Tento článek popisuje, jak vytvořit skupiny serverů pro posouzení pomocí Azure Migrate: zjišťování a posouzení.

[Azure Migrate](migrate-services-overview.md) vám pomůže migrovat do Azure. Azure Migrate poskytuje centralizované centrum pro sledování zjišťování, hodnocení a migrace místní infrastruktury, aplikací a dat do Azure. Centrum poskytuje nástroje Azure pro posuzování a migraci a také nabídky nezávislého výrobce softwaru (ISV) třetích stran. 

## <a name="grouping-servers"></a>Servery seskupení

Servery shromáždíte do skupin, abyste posoudili, jestli jsou vhodné pro migraci do Azure, a abyste získali odhad velikosti Azure a odhad nákladů. Existuje několik způsobů, jak vytvořit skupiny:

- Pokud znáte servery, které je potřeba migrovat společně, můžete skupinu vytvořit ručně v Azure Migrate.
- Pokud si nejste jistí, jak servery, které je potřeba seskupit dohromady, můžete k vytváření skupin použít funkci Vizualizace závislostí v Azure Migrate. 

> [!NOTE]
> Funkce vizualizace závislosti není v Azure Government k dispozici.

## <a name="create-a-group-manually"></a>Vytvořit skupinu ručně

Můžete vytvořit skupinu ve stejné době, kdy [vytvoříte posouzení](how-to-create-assessment.md).

Pokud chcete vytvořit skupinu ručně mimo vytváření posouzení, udělejte toto:

1. V projektu Azure Migrate > **Přehled** klikněte na možnost **zhodnotit a migrovat servery**. V **Azure Migrate: zjišťování a hodnocení** klikněte na **skupiny** .
    - Pokud jste ještě nepřidali nástroj Azure Migrate: Discovery and Assessment Tool, kliknutím ho přidejte. [Další informace](how-to-assess.md).
    - Pokud jste ještě nevytvořili projekt Azure Migrate, [Přečtěte si další informace](./create-manage-projects.md).

    ![Vybrat skupiny](./media/how-to-create-a-group/select-groups.png)

2. Klikněte na ikonu **skupiny** .
3. V části **vytvořit skupinu** zadejte název skupiny a v části **název zařízení** vyberte zařízení Azure Migrate, které používáte pro zjišťování serveru.
4. V seznamu Server vyberte servery, které chcete přidat do skupiny > **vytvořit**.

    ![Vytvoření skupiny](./media/how-to-create-a-group/create-group.png)

Tuto skupinu teď můžete používat při vytváření posouzení [virtuálních počítačů Azure](how-to-create-assessment.md) nebo vyhodnocení [Řešení Azure VMware (AVS)](how-to-create-azure-vmware-solution-assessment.md) nebo [posouzení Azure SQL](how-to-create-azure-sql-assessment.md).

## <a name="refine-a-group-with-dependency-mapping"></a>Upřesnění skupiny pomocí mapování závislostí

Mapování závislostí pomáhá vizualizovat závislosti mezi servery. Mapování závislostí se obvykle používá, pokud chcete vyhodnotit skupiny serverů s vyšší úrovní spolehlivosti.
- Umožňuje křížovou kontrolu závislostí serveru před spuštěním posouzení. 
- Pomůže vám taky efektivně naplánovat migraci do Azure tím, že zajistí, že se nic nestane, a tím se vyhnete výpadkům při migraci.
- Můžete zjistit vzájemné závislé systémy, které potřebují migrovat dohromady, a určit, jestli operační systém stále obsluhuje uživatele, nebo je kandidátem na vyřazení z provozu místo migrace.

Pokud jste už [nastavili mapování závislostí](how-to-create-group-machine-dependencies.md)a chcete upravit existující skupinu, udělejte toto:

1. Na kartě **servery** na dlaždici **Azure Migrate: zjišťování a posouzení** klikněte na **skupiny**.
2. Klikněte na skupinu, kterou chcete upřesnit.
    - Pokud jste ještě nenastavili mapování závislostí, zobrazí se ve sloupci **závislosti** stav **instalace vyžaduje** . Pro každý server, pro který chcete vizualizovat závislosti, klikněte na **vyžaduje instalaci**. Než budete moci mapovat závislosti serveru, nainstalujte na každý server několik agentů. [Další informace](how-to-create-group-machine-dependencies.md).

        ![Přidat mapování závislostí](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Pokud jste již nastavili mapování závislostí, otevřete na stránce skupina kliknutím na možnost **Zobrazit závislosti** mapu závislostí skupiny.

3. Po kliknutí na **Zobrazit závislosti** se v mapě závislosti skupin zobrazí následující:

    - Příchozí (klienti) a odchozí (servery) TCP připojení k a ze všech serverů ve skupině, které mají nainstalovaného agenta závislostí.
    - Závislé servery, které nemají nainstalované agenty závislostí, jsou seskupeny podle čísel portů.
    - Závislé servery s nainstalovanými agenty závislostí se zobrazují jako samostatná pole.
    - Procesy běžící v rámci serveru. Rozbalte jednotlivá pole serveru a zobrazte procesy.
    - Vlastnosti serveru (včetně plně kvalifikovaného názvu domény, operačního systému, adresy MAC). Kliknutím na každé serverové pole zobrazíte podrobnosti.

4. Pokud chcete zobrazit závislosti v časovém intervalu dle vašeho výběru, upravte časový rozsah (ve výchozím nastavení hodinu) zadáním počátečního a koncového data nebo doby trvání.

    > [!NOTE]
    > Časový rozsah může být až hodinu. Pokud potřebujete delší rozsah, použijte [Azure monitor k dotazování závislých dat](how-to-create-group-machine-dependencies.md) po delší dobu.

5. Jakmile identifikujete závislosti, které byste chtěli přidat do skupiny nebo je z ní odebrat, můžete skupinu upravit. Pomocí Ctrl + kliknutí můžete přidat nebo odebrat servery ze skupiny.

    - Je možné přidat pouze servery, které byly zjištěny.
    - Přidání a odebrání serverů zruší platnost minulých posouzení pro skupinu.
    - Při úpravě skupiny můžete volitelně vytvořit nové hodnocení.


## <a name="next-steps"></a>Další kroky

Naučte se nastavit a používat [Mapování závislostí](how-to-create-group-machine-dependencies.md) k vytváření vysoce důvěryhodných skupin.