---
title: Přidání nástrojů pro migraci v Azure Migrate
description: Přečtěte si, jak přidat nástroje pro migraci v Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185964"
---
# <a name="add-migration-tools"></a>Přidání nástrojů pro migraci

Tento článek popisuje, jak přidat nástroje pro migraci v [Azure Migrate](migrate-overview.md).

Azure Migrate poskytuje centrum nástrojů pro hodnocení a migraci do Azure. Zahrnuje nativní nástroje, nástroje poskytované jinými službami Azure a nabídky nezávislého dodavatele softwaru (ISV) třetích stran.

Pokud chcete přidat nástroj pro migraci a ještě jste nenastavili projekt Migrace Azure, postupujte podle tohoto [článku](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Výběr nástroje pro nešíření podle rváčů

Pokud pro migraci zvolíte [nástroj isv,](migrate-services-overview.md#isv-integration) můžete začít získáním licence nebo registrací k bezplatné zkušební verzi v souladu se zásadami isv. V každém nástroji je možnost připojení k Azure Migrate. Nasaďte nástroj a postupujte podle pokynů a dokumentace nástroje a připojte pracovní prostor nástrojů pomocí Migrace Azure. 

## <a name="select-a-migration-scenario"></a>Výběr scénáře migrace

1. V projektu Migrace Azure klikněte na **Přehled**.
2. Vyberte scénář migrace, který chcete použít:

    - Pokud chcete migrovat počítače a úlohy do Azure, vyberte **Posoudit a migrovat servery**.
    - Chcete-li migrovat místní počítače SQL, vyberte **možnost Posoudit a migrovat databáze**.
    - Pokud chcete migrovat místní webové aplikace, vyberte **Posoudit a migrovat webové aplikace**.
    - Pokud chcete migrovat velké množství místních dat do Azure v režimu offline, vyberte **Objednat datovou schránku**.

    ![Hodnotící scénář](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Výběr nástroje pro migraci serveru

1. Klepněte na **tlačítko Posoudit a migrovat servery**.
2. Pokud jste v **Azure Migrate – Servers**ještě nepřidali nástroje pro migraci, vyberte v části Nástroje pro **migraci**možnost **Kliknutím sem přidáte nástroj pro migraci**. Pokud jste už přidali nástroje pro migraci, vyberte v **přidejte další nástroje pro migraci**vyberte **Změnit**.

    > [!NOTE]
    > Pokud potřebujete přejít na jiný projekt, v **Azure Migrate – Servery**, vedle **Zobrazit podrobnosti pro jiný projekt migrace**, klikněte na Tlačítko **Klikněte zde**.

3. V **Azure Migrate**vyberte nástroj pro migraci, který chcete použít.
    - Pokud používáte migraci serveru Azure, můžete nastavit a spustit migrace přímo v projektu Migrace Azure.
    - Pokud používáte nástroj pro hodnocení jiného výrobce, přejděte na odkaz pro isv a spusťte migraci v souladu s pokyny, které poskytují.

## <a name="select-a-database-migration-tool"></a>Výběr nástroje pro migraci databáze

1. Klikněte na **Posoudit a migrovat databáze.**
2. V **databázi**klepněte na tlačítko **Přidat nástroje**.
3. V části Přidat nástroj > **nástroj pro výběr migrace**vyberte nástroj, který chcete použít k migraci databáze.

## <a name="select-a-web-app-migration-tool"></a>Výběr nástroje pro migraci webových aplikací

1. Klikněte na **Posoudit a migrovat webové aplikace**.
2. Postupujte podle odkazu na nástroj migrace pro službu Azure App Service. Pomocí nástroje pro migraci můžete:

    - **Vyhodnoťte aplikace online**: Aplikace můžete vyhodnocovat a migrovat pomocí veřejné adresy URL online pomocí Pomocníka pro migraci služby Azure App Service.
    - **.NET/PHP**: Pro interní aplikace .NET a PHP si můžete stáhnout a spustit Pomocníka pro migraci.

## <a name="order-an-azure-data-box"></a>Objednání datové schránky Azure

Chcete-li migrovat velké množství dat do Azure, můžete si objednat Azure DAta Box pro offline přenos dat.

1. Klepněte **na položku Objednat datové pole**.
2. V **poli Vyberte azure data box**zadejte předplatné. 
3. Přenos bude import do Azure. Zadejte zdroj dat a cíl oblasti Azure pro data.

## <a name="next-steps"></a>Další kroky

Vyzkoušejte migraci pomocí migrace serveru Azure pro virtuální počítače [Hyper-V](tutorial-migrate-hyper-v.md) nebo [VMware.](tutorial-migrate-vmware.md)
