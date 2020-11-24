---
title: Přidání nástrojů pro migraci do Azure Migrate
description: Naučte se přidávat nástroje pro migraci do Azure Migrate.
ms.topic: article
ms.date: 11/23/2020
ms.openlocfilehash: 5ff5fb54e077896fb6169ad53ce29483cd2c2f89
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545292"
---
# <a name="add-migration-tools"></a>Přidání nástrojů pro migraci

Tento článek popisuje, jak přidat nástroje pro migraci do [Azure Migrate](./migrate-services-overview.md).

- Pokud chcete přidat nástroj pro migraci, který ještě nevytvořil projekt Azure Migrate, postupujte podle pokynů v tomto [článku](create-manage-projects.md).
- Pokud jste přidali nástroj ISV pro migraci, [postupujte podle kroků](prepare-isv-movere.md)a připravte se na práci s nástrojem.

## <a name="select-a-migration-scenario"></a>Výběr scénáře migrace

1. V projektu Azure Migrate klikněte na **Přehled**.
2. Vyberte scénář migrace, který chcete použít:

    - K migraci počítačů a úloh do Azure vyberte možnost **zhodnotit a migrovat servery**.
    - Chcete-li migrovat místní databáze, vyberte možnost **zhodnotit a migrovat databáze**.
    - Pokud chcete migrovat místní webové aplikace, vyberte **Prozkoumat další**  >  **Web Apps**.
    - Pokud chcete migrovat data do Azure pomocí datového pole, vyberte **Prozkoumat další**  >  **data box**.

    ![Možnosti výběru scénáře migrace](./media/how-to-migrate/migrate-scenario.png)


## <a name="select-a-server-migration-tool"></a>Výběr nástroje pro migraci serveru

1. Přidat nástroj:

    - Pokud jste vytvořili Azure Migrate projekt pomocí možnosti **zhodnotit a migrovat servery** na portálu, nástroj pro migraci Azure Migrate serveru se automaticky přidá do projektu. Pokud chcete přidat další nástroje pro migraci, vyberte na **serverech** vedle **Možnosti nástroje pro migraci** možnost **Přidat další nástroje**.
    
         ![Tlačítko pro přidání dalších nástrojů pro migraci](./media/how-to-migrate/add-migration-tools.png)

    - Pokud jste vytvořili projekt s použitím jiné možnosti a ještě nemáte žádné nástroje pro migraci, v nástroji **servery** pro  >  **migraci** vyberte **možnost klikněte sem**.

    ![Tlačítko pro přidání nástrojů pro první migraci](./media/how-to-migrate/no-migration-tool.png)

2. V **Azure Migrate**  >  **Přidat nástroje** vyberte nástroje, které chcete přidat. Pak vyberte **Přidat nástroj**.

    ![Vybrat nástroje pro posouzení ze seznamu](./media/how-to-migrate/select-migration-tool.png)


## <a name="select-a-database-migration-tool"></a>Výběr nástroje pro migraci databáze

Pokud jste vytvořili Azure Migrate projekt pomocí možnosti **vyhodnocení a migrace databáze** na portálu, nástroj pro migraci databáze je automaticky přidán do projektu. 

1. Pokud nástroj pro migraci databáze není v projektu, vyberte v části **databáze**  >  **nástroje** pro vyhodnocení **klikněte na možnost klikněte sem**.
    
    ![Přidat nástroje pro migraci databáze](./media/how-to-migrate/no-database-migration-tool.png)


2. V **Azure Migrate**  >  **Přidat nástroje** vyberte nástroj pro migraci databáze. Pak vyberte **Přidat nástroj**.

    ![Výběr nástroje pro migraci databáze ze seznamu](./media/how-to-migrate/select-database-migration-tool.png)

    

## <a name="select-a-web-app-migration-tool"></a>Výběr nástroje pro migraci webové aplikace

Pokud jste vytvořili Azure Migrate projekt pomocí možnosti **Prozkoumat další**  >  **webapps** na portálu, nástroj pro migraci webové aplikace se automaticky přidá do projektu. 

1. Pokud nástroj pro migraci webové aplikace v projektu není, vyberte v **Web apps**  >  **nástrojích pro vyhodnocení** Web Apps možnost **klikněte sem**.

    ![Přidat nástroje pro migraci webové aplikace](./media/how-to-migrate/no-web-app-migration-tool.png)
 

2. V **Azure Migrate**  >  **Přidat nástroje** vyberte nástroj pro migraci webové aplikace. Pak vyberte **Přidat nástroj**.

    ![Vybrat nástroje pro posouzení WebApp ze seznamu](./media/how-to-migrate/select-web-app-migration-tool.png)


## <a name="order-an-azure-data-box"></a>Objednat Azure Data Box

Pokud chcete migrovat velké objemy dat do Azure, můžete objednat Azure Data Box pro offline přenos dat.

1. V **přehledu** vyberte **Prozkoumat další**.
2. V možnosti **Prozkoumat další** vyberte **data box**.
3. V části **Začínáme s data box** vyberte předplatné a skupinu prostředků, které chcete použít při objednávání data box.
4. **Typ přenosu** je import do Azure. Zadejte zemi, ve které se data nacházejí, a oblast Azure, do které chcete data přenést. 
5. Kliknutím na **použít** uložte nastavení.

## <a name="next-steps"></a>Další kroky

Vyzkoušejte migraci pomocí nástroje pro migraci Azure Migrate serveru pro virtuální počítače [s technologií Hyper-V](tutorial-migrate-hyper-v.md) nebo [VMware](tutorial-migrate-vmware.md) .
