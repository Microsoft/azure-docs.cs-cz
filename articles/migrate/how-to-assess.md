---
title: Přidat nástroje pro posouzení do Azure Migrate
description: Naučte se přidávat nástroje pro posouzení do Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 37f3748b4f0f3db47bbd6fbe9bc06a307781c2f8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786800"
---
# <a name="add-assessment-tools"></a>Přidání nástrojů pro vyhodnocení

Tento článek popisuje, jak přidat nástroje pro posouzení do [Azure Migrate](./migrate-services-overview.md). 

- Pokud chcete přidat nástroj pro vyhodnocení a ještě nemáte Azure Migrate projekt, postupujte podle pokynů v tomto [článku](create-manage-projects.md).
- Pokud jste přidali nástroj ISV nebo pracovníka pro posouzení, [postupujte podle kroků](prepare-isv-movere.md)a připravte se na práci s nástrojem.

## <a name="select-an-assessment-scenario"></a>Výběr scénáře posouzení

1. V projektu Azure Migrate klikněte na **Přehled**.
2. Vyberte scénář posouzení:

    - Pokud chcete zjišťovat, hodnotit a migrovat servery (fyzické nebo virtuální) z vašeho datového centra nebo jiných cloudů do Azure, vyberte **vyhledat, vyhodnotit a migrovat**. Pomocí tohoto cíle migrace teď můžete taky zjišťovat SQL Server z prostředí VMware a vyhodnocovat z něj vyhodnocení.
    - Pro vyhodnocení místních SQL Server databází vyberte možnost **vyhodnotit a migrovat databáze**.
    - Pokud chcete vyhodnotit nebo migrovat místní webové aplikace, vyberte **Prozkoumat další**  >  **Web Apps**.
    - Pokud chcete vyhodnotit infrastrukturu virtuálních klientských počítačů, vyberte **Prozkoumat další**  >  **Infrastruktura virtuálních klientských počítačů**.

    ![Možnosti výběru scénáře posouzení](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-discovery-and-assessment-tool"></a>Výběr nástroje pro zjišťování a vyhodnocení 


1. Přidat nástroj:

    - Pokud jste vytvořili projekt Azure Migrate pomocí možnosti **zhodnotit a migrovat servery** na portálu, nástroj pro zjišťování a hodnocení Azure Migrate je automaticky přidán do projektu. Pokud chcete přidat další nástroje pro posouzení, v **systému Windows, Linux a SQL Server** klikněte vedle **nástrojů pro posouzení** na **Přidat další nástroje**.

         ![Tlačítko pro přidání dalších nástrojů pro posouzení](./media/how-to-assess/add-assessment-tool.png)

    - Pokud jste vytvořili projekt pomocí jiné možnosti a ještě nemáte žádné nástroje pro posouzení, v nástrojích pro **Windows, Linux a SQL Server**  >  **Assessment Tools** vyberte **klikněte sem**.

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


1. Pokud nástroj pro vyhodnocení webové aplikace v projektu není, vyberte v   >  **nástroji Web Apps Assessment Tools** **možnost kliknutím sem**.
    
    ![Přidat nástroje pro posouzení webové aplikace](./media/how-to-assess/no-web-app-assessment-tool.png)


2. V **Azure Migrate**  >  **Přidat nástroje** vyberte nástroj Web App Assessment. Pak vyberte **Přidat nástroj**.

    ![Výběr nástroje pro migraci databáze ze seznamu](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>Další kroky

Zjišťování místních serverů pro posouzení pomocí nástroje Azure Migrate Discovery and Assessment Tool pro [VMware](./tutorial-discover-vmware.md), [Hyper-V](./tutorial-discover-hyper-v.md)nebo [fyzické servery](./tutorial-discover-physical.md)