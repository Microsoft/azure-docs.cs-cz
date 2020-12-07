---
title: Přidat nástroje pro posouzení do Azure Migrate
description: Naučte se přidávat nástroje pro posouzení do Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 9a4ed5542945b8d281ec750d9bbd3a8f444e44d2
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752256"
---
# <a name="add-assessment-tools"></a>Přidání nástrojů pro vyhodnocení

Tento článek popisuje, jak přidat nástroje pro posouzení do [Azure Migrate](./migrate-services-overview.md). 

- Pokud chcete přidat nástroj pro vyhodnocení a ještě nemáte Azure Migrate projekt, postupujte podle pokynů v tomto [článku](create-manage-projects.md).
- Pokud jste přidali nástroj ISV nebo pracovníka pro posouzení, [postupujte podle kroků](prepare-isv-movere.md)a připravte se na práci s nástrojem.

## <a name="select-an-assessment-scenario"></a>Výběr scénáře posouzení

1. V projektu Azure Migrate klikněte na **Přehled**.
2. Vyberte scénář posouzení:

    - Pokud chcete zjišťovat a hodnotit počítače a úlohy pro migraci do Azure, vyberte **vyhodnotit a migrovat servery**.
    - Pro vyhodnocení místních SQL Server databází vyberte možnost **vyhodnotit a migrovat databáze**.
    - Pokud chcete vyhodnotit nebo migrovat místní webové aplikace, vyberte **Prozkoumat další**  >  **Web Apps**.
    - Pokud chcete vyhodnotit infrastrukturu virtuálních klientských počítačů, vyberte **Prozkoumat další**  >  **Infrastruktura virtuálních klientských počítačů**.

    ![Možnosti výběru scénáře posouzení](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Výběr nástroje pro vyhodnocení serveru 


1. Přidat nástroj:

    - Pokud jste vytvořili Azure Migrate projekt pomocí možnosti **zhodnotit a migrovat servery** na portálu, nástroj pro vyhodnocení Azure Migrate serveru se automaticky přidá do projektu. Pokud chcete přidat další nástroje pro posouzení, vyberte na **serverech** vedle **nástrojů pro posouzení** možnost **Přidat další nástroje**.
    
         ![Tlačítko pro přidání dalších nástrojů pro posouzení](./media/how-to-assess/add-assessment-tool.png)

    - Pokud jste vytvořili projekt pomocí jiné možnosti a zatím nemáte žádné nástroje pro posouzení, v nástroji servery pro **Servers**  >  **vyhodnocení** vyberte **možnost klikněte sem**.

        ![Tlačítko pro přidání prvního nástroje pro posouzení](./media/how-to-assess/no-assessment-tool.png)

2. V **Azure Migrate**  >  **Přidat nástroje** vyberte nástroje, které chcete přidat. Pak vyberte **Přidat nástroj**.

    ![Vybrat nástroje pro posouzení ze seznamu](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>Výběr nástroje pro vyhodnocení databáze

1. Přidat nástroj:

    - Pokud jste vytvořili Azure Migrate projekt pomocí možnosti **vyhodnocení a migrace databáze** na portálu, nástroj pro vyhodnocení databáze je automaticky přidán do projektu. Chcete-li přidat další nástroje pro posouzení, v nabídce **databáze** vedle **Možnosti nástroje pro posouzení** vyberte **Přidat další nástroje**.

    - Pokud jste vytvořili projekt pomocí jiné možnosti a ještě nemáte žádné **Nástroje pro vyhodnocení** databáze, v  >  **nástrojích pro vyhodnocení** databáze vyberte **klikněte sem**.

2. V **Azure Migrate**  >  **Přidat nástroje** vyberte nástroje, které chcete přidat. Pak vyberte **Přidat nástroj**.

    ![Výběr nástrojů pro vyhodnocení databáze ze seznamu](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>Výběr nástroje pro vyhodnocení webové aplikace

Pokud jste vytvořili Azure Migrate projekt pomocí možnosti **Prozkoumat další**  >  **webapps** na portálu, nástroj pro vyhodnocení webové aplikace se automaticky přidá do projektu. 


1. Pokud nástroj pro vyhodnocení webové aplikace v projektu není, vyberte v **Web Apps**  >  **nástroji Web Apps Assessment Tools** **možnost kliknutím sem**.
    
    ![Přidat nástroje pro posouzení webové aplikace](./media/how-to-assess/no-web-app-assessment-tool.png)


2. V **Azure Migrate**  >  **Přidat nástroje** vyberte nástroj Web App Assessment. Pak vyberte **Přidat nástroj**.

    ![Výběr nástroje pro migraci databáze ze seznamu](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>Další kroky

Zjištění místních počítačů pro posouzení pomocí nástroje Azure Migrate Server Assessment Tool pro virtuální počítače [VMware](./tutorial-discover-vmware.md) , [Hyper-V](./tutorial-discover-hyper-v.md)nebo [fyzické servery](./tutorial-discover-physical.md)