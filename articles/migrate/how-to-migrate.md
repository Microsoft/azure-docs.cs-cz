---
title: Přidání nástrojů pro migraci do Azure Migrate
description: Naučte se přidávat nástroje pro migraci do Azure Migrate.
ms.topic: article
ms.date: 04/26/2020
ms.openlocfilehash: b6ac8ed64d3f12783cc16f0428874a19d027adf9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86109803"
---
# <a name="add-migration-tools"></a>Přidání nástrojů pro migraci

Tento článek popisuje, jak přidat nástroje pro migraci do [Azure Migrate](./migrate-services-overview.md).

- Pokud chcete přidat nástroj pro migraci, který ještě nevytvořil projekt Azure Migrate, postupujte podle pokynů v tomto [článku](how-to-add-tool-first-time.md).
- Pokud jste přidali nástroj ISV pro migraci, [postupujte podle kroků](prepare-isv-movere.md)a připravte se na práci s nástrojem.

## <a name="select-a-migration-scenario"></a>Výběr scénáře migrace

1. V projektu Azure Migrate klikněte na **Přehled**.
2. Vyberte scénář migrace, který chcete použít:

    - K migraci počítačů a úloh do Azure vyberte možnost **zhodnotit a migrovat servery**.
    - Chcete-li migrovat místní databáze SQL Server, vyberte možnost **vyhodnotit a migrovat databáze**.
    - Pokud chcete migrovat místní webové aplikace, vyberte možnost **vyhodnotit a migrovat webové aplikace**.
    - Pokud chcete migrovat velké objemy místních dat do Azure v režimu offline, vyberte **objednat data box**.

    ![Scénář posouzení](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Výběr nástroje pro migraci serveru

1. Klikněte na možnost **zhodnotit a migrovat servery**.
2. Pokud jste v **Azure Migrate-servery**ještě nepřidali nástroje pro migraci, vyberte v části **Nástroje pro migraci**možnost **kliknutím sem přidejte Nástroj pro migraci**. Pokud jste již přidali nástroje pro migraci, vyberte v **nabídce přidat další nástroje pro migraci**možnost **změnit**.

    > [!NOTE]
    > Pokud potřebujete přejít na jiný projekt, klikněte v **Azure Migrate-servery**vedle **Zobrazit podrobnosti o jiném projektu migrace**kliknutím **na tlačítko sem**.

3. V **Azure Migrate**vyberte nástroj pro migraci, který chcete použít.
    - Pokud používáte migraci Azure Migrate serveru, můžete nastavit a spustit migrace přímo v projektu Azure Migrate.
    - Pokud používáte nástroj pro vyhodnocení třetí strany, přejděte na odkaz poskytnutý pro nezávislého výrobce softwaru a spusťte migraci podle pokynů, které poskytují.

## <a name="select-a-database-migration-tool"></a>Výběr nástroje pro migraci databáze

1. Klikněte na **vyhodnotit a migrovat databáze** .
2. V nabídce **databáze**klikněte na možnost **Přidat nástroje**.
3. V okně Přidat nástroj > **Vyberte nástroj pro migraci**, vyberte nástroj, který chcete použít k migraci databáze.

## <a name="select-a-web-app-migration-tool"></a>Výběr nástroje pro migraci webové aplikace

1. Klikněte na **zhodnotit a migrovat webové aplikace**.
2. Použijte odkaz na nástroj pro migraci Azure App Service. Nástroj pro migraci použijte k těmto akcím:

    - **Vyhodnoťte aplikace online**: pomocí Pomocník s migrací Azure App Service můžete vyhodnocovat a migrovat aplikace pomocí veřejné adresy URL online.
    - **.NET/php**: pro interní aplikace .NET a PHP můžete stáhnout a spustit Pomocník s migrací.

## <a name="order-an-azure-data-box"></a>Objednat Azure Data Box

Pokud chcete migrovat velké objemy dat do Azure, můžete objednat Azure DAta box pro offline přenos dat.

1. Klikněte na příkaz **objednat data box**.
2. V **Azure Data box vyberte své**předplatné. 
3. Přenos bude importován do Azure. Zadejte zdroj dat a cíl oblasti Azure pro data.

## <a name="next-steps"></a>Další kroky

Vyzkoušejte migraci pomocí Azure Migrate migrace serveru pro virtuální počítače [Hyper-V](tutorial-migrate-hyper-v.md) nebo [VMware](tutorial-migrate-vmware.md) .
