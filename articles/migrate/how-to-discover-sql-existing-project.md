---
title: Zjištění instancí SQL Server v existujícím projektu Azure Migrate
description: Naučte se zjišťovat SQL Server instance v existujícím projektu Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 2de60880b511e43ffb2949a15fec2cf2a94f62fa
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567148"
---
# <a name="discover-sql-server-instances-in-an-existing-project"></a>Zjištění instancí SQL Server v existujícím projektu 

Tento článek popisuje, jak zjistit SQL Server instance a databáze v projektu [Azure Migrate](./migrate-services-overview.md) , který byl vytvořen před náhledem funkce Azure SQL Assessment.

Zjišťování SQL Server instancí a databází, které běží na místních počítačích, pomáhá identifikovat a přizpůsobit cestu migrace do Azure SQL. Zařízení Azure Migrate provádí toto zjišťování pomocí přihlašovacích údajů domény nebo přihlašovacích údajů SQL Server ověřování, které mají přístup k instancím SQL Server a databázím spuštěným na cílových serverech. Tento proces zjišťování je bez agentů, což znamená, že na cílových serverech není nic nainstalované.

## <a name="before-you-start"></a>Než začnete

- Ujistěte se, že jste udělali toto:
    - Vytvořili jste [projekt Azure Migrate](./create-manage-projects.md) před oznámením funkce vyhodnocení SQL pro vaši oblast.
    - Přidání nástroje [Azure Migrate: Discovery and Assessment](./how-to-assess.md) Tool do projektu
- Kontrola [požadavků a podpory zjišťování aplikací](./migrate-support-matrix-vmware.md#vmware-requirements)
-  Ujistěte se, že servery, na kterých spouštíte App-Discovery, mají nainstalované prostředí PowerShell verze 2,0 nebo novější a nástroje VMware (novější než 10.2.0) jsou nainstalované.
- Ověřte [požadavky](./migrate-appliance.md) na nasazení zařízení Azure Migrate.
- Ověřte, že v předplatném máte [požadované role](./create-manage-projects.md#verify-permissions) pro vytváření prostředků.
- Ujistěte se, že vaše zařízení má přístup k Internetu.

## <a name="enable-discovery-of-sql-server-instances-and-databases"></a>Povolit zjišťování instancí SQL Server a databází

1. V Azure Migrate projektu buď
    - Vyberte **nepovolenou** na dlaždici centra nebo :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="dlaždici Azure Migrate centra s nepovoleným zjišťováním SQL"::: .
    - Vyberte možnost **Nepovoleno** u žádné položky na stránce zjišťování serveru v části instance SQL sloupce :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="Azure Migrate zjištěné servery s povoleným zjišťováním SQL"::: .
2. V části zjišťovat SQL Server instance a databáze postupujte podle následujících kroků:
    - Chcete-li vytvořit požadovaný prostředek, vyberte možnost **upgradovat**.
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Tlačítko pro upgrade zařízení Azure Migrate":::
    - Ověřte, že se služby běžící na zařízení aktualizují na nejnovější verze. Provedete to tak, že z vašeho serveru zařízení spustíte Správce konfigurace zařízení a v panelu nastavit požadované součásti vyberete Zobrazit služby zařízení.
        - Zařízení a jeho součásti se automaticky aktualizují. :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="Podívejte se na verzi zařízení"::: .
    - Na panelu Správa přihlašovacích údajů a zdrojů zjišťování ve Správci konfigurace zařízení přidejte přihlašovací údaje k doméně nebo SQL Server, které mají přístup sysadmin ke zjištění instance SQL Server a databází.
    Můžete využít funkci automatického mapování přihlašovacích údajů zařízení nebo ručně namapovat přihlašovací údaje na příslušný server, jak je zvýrazněné [tady](./tutorial-discover-vmware.md#start-continuous-discovery).

    Poznámka:
    - Zajistěte, aby inventář softwaru byl už povolený, nebo pokud chcete povolit stejné oprávnění, zadejte doménu nebo přihlašovací údaje k doméně nebo jiné doméně. Aby bylo možné zjišťovat SQL Server instance, je nutné provést inventář softwaru.
    - Zařízení se pokusí ověřit přihlašovací údaje domény pomocí služby AD, jak jsou přidány. Ujistěte se, že server zařízení má na serveru služby AD přidruženého k přihlašovacím údajům síťovou řadu pozorování. Přihlašovací údaje přidružené k ověřování SQL Server nejsou ověřené.

3. Po přidání požadovaných přihlašovacích údajů vyberte spustit zjišťování a začněte kontrolu.

> [!Note]
>Před vytvořením posouzení pro Azure SQL prosím počkejte, než se spustí zjišťování SQL pro nějakou dobu. Pokud není povoleno dokončení zjišťování instancí SQL Server a databází, jsou příslušné instance v sestavě posouzení označeny jako **neznámé** .

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak vytvořit [posouzení Azure SQL](./how-to-create-azure-sql-assessment.md) .
- Další informace o [hodnoceních Azure SQL](./concepts-azure-sql-assessment-calculation.md)