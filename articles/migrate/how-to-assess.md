---
title: Přidat nástroje pro posouzení do Azure Migrate
description: Naučte se přidávat nástroje pro posouzení do Azure Migrate.
ms.topic: how-to
ms.date: 04/26/2020
ms.openlocfilehash: 5151135a57b7c2780485d85299ead5df83ea851e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86122111"
---
# <a name="add-assessment-tools"></a>Přidání nástrojů pro vyhodnocení

Tento článek popisuje, jak přidat nástroje pro posouzení do [Azure Migrate](./migrate-services-overview.md). 

- Pokud chcete přidat nástroj pro posouzení a ještě nemáte Azure Migrate projekt, postupujte podle tohoto [článku](how-to-add-tool-first-time.md).-
- Pokud jste přidali nástroj ISV nebo pracovníka pro posouzení, [postupujte podle kroků](prepare-isv-movere.md)a připravte se na práci s nástrojem.

## <a name="select-an-assessment-scenario"></a>Výběr scénáře posouzení

1. V projektu Azure Migrate klikněte na **Přehled**.
2. Vyberte scénář posouzení, který chcete použít:

    - Pokud chcete zjišťovat a hodnotit počítače a úlohy pro migraci do Azure, vyberte **vyhodnotit a migrovat servery**.
    - Pro vyhodnocení místních SQL Server databází vyberte možnost **vyhodnotit a migrovat databáze**.
    - Pokud chcete vyhodnotit místní webové aplikace, vyberte **vyhodnotit a migrovat webové aplikace**.

    ![Scénář posouzení](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Výběr nástroje pro vyhodnocení serveru 

1. Klikněte na možnost **zhodnotit a migrovat servery**.
2. Pokud jste v **Azure Migrate-servery**nepřidali Nástroj pro posouzení, vyberte v části **Nástroje pro posouzení**možnost **kliknutím sem přidejte Nástroj pro posouzení**. Pokud jste již přidali nástroje pro posouzení, vyberte v **nabídce přidat další nástroje pro posouzení**možnost **změnit**.

    > [!NOTE]
    > Pokud potřebujete přejít na jiný projekt, klikněte v **Azure Migrate-servery**vedle **Zobrazit podrobnosti o jiném projektu migrace**kliknutím **na tlačítko sem**.

3. V **Azure Migrate**vyberte nástroj pro vyhodnocení, který chcete použít.

    - Pokud používáte hodnocení serveru Azure Migrate, můžete nastavit, spustit a zobrazit hodnocení přímo v projektu Azure Migrate.
    - Pokud používáte jiný nástroj pro vyhodnocení, přejděte na odkaz poskytnutý pro svůj web a spusťte posouzení podle pokynů, které poskytují.


## <a name="select-a-database-assessment-tool"></a>Výběr nástroje pro vyhodnocení databáze

1. Klikněte na **vyhodnotit a migrovat databáze** .
2. V nabídce **databáze**klikněte na možnost **Přidat nástroje**.
3. V části přidat nástroj > **Vyberte nástroj pro vyhodnocení**, vyberte nástroj, který chcete použít k vyhodnocení vaší databáze.

## <a name="select-a-web-app-assessment-tool"></a>Výběr nástroje pro vyhodnocení webové aplikace

1. Klikněte na **zhodnotit a migrovat webové aplikace**.
2. Použijte odkaz na nástroj pro migraci Azure App Service. Nástroj pro migraci použijte k těmto akcím:

    - **Vyhodnotit aplikace online**: můžete vyhodnotit aplikace pomocí veřejné adresy URL online pomocí Pomocník s migrací Azure App Service.
    - **.NET/php**: pro interní aplikace .NET a PHP můžete stáhnout a spustit Pomocník s migrací.



## <a name="next-steps"></a>Další kroky

Vyzkoušejte vyhodnocení pomocí Azure Migrate posouzení serveru pro virtuální počítače [VMware](tutorial-prepare-vmware.md) , [Hyper-V](tutorial-prepare-hyper-v.md)nebo [fyzické servery](tutorial-prepare-physical.md)
