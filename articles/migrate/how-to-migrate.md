---
title: Přidání nástrojů pro migraci do Azure Migrate
description: Naučte se přidávat nástroje pro migraci do Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74185964"
---
# <a name="add-migration-tools"></a>Přidání nástrojů pro migraci

Tento článek popisuje, jak přidat nástroje pro migraci do [Azure Migrate](migrate-overview.md).

Azure Migrate poskytuje centrum nástrojů pro posouzení a migraci do Azure. Zahrnuje nativní nástroje, nástroje poskytované jinými službami Azure a nabídky nezávislého výrobce softwaru (ISV) třetích stran.

Pokud chcete přidat nástroj pro migraci, který ještě nevytvořil projekt Azure Migrate, postupujte podle pokynů v tomto [článku](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Výběr nástroje ISV

Pokud zvolíte [Nástroj ISV](migrate-services-overview.md#isv-integration) pro migraci, můžete začít získáním licence nebo registrací bezplatné zkušební verze v souladu se zásadami ISV. V každém nástroji je k dispozici možnost připojit se k Azure Migrate. Nasaďte nástroj a podle pokynů a dokumentace k nástroji připojte pracovní prostor nástroje s Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Výběr scénáře migrace

1. V projektu Azure Migrate klikněte na **Přehled**.
2. Vyberte scénář migrace, který chcete použít:

    - K migraci počítačů a úloh do Azure vyberte možnost **zhodnotit a migrovat servery**.
    - Chcete-li migrovat místní počítače SQL, vyberte možnost **vyhodnotit a migrovat databáze**.
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
