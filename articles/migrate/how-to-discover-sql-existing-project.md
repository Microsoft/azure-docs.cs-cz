---
title: Zjištění instancí SQL Server v existujícím projektu Azure Migrate
description: Naučte se zjišťovat SQL Server instance v existujícím projektu Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: ca0052eebd8d3c8e80943ca8c0e0346216436800
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452760"
---
# <a name="discover-sql-server-instances-in-an-existing-project"></a>Zjištění instancí SQL Server v existujícím projektu 

Tento článek popisuje, jak zjistit SQL Server instance a databáze v projektu [Azure Migrate](./migrate-services-overview.md) , který byl vytvořen před náhledem funkce Azure SQL Assessment.

Zjišťování SQL Server instancí a databází, které běží na místních počítačích, pomáhá identifikovat a přizpůsobit cestu migrace do Azure SQL. Zařízení Azure Migrate provádí toto zjišťování pomocí přihlašovacích údajů domény nebo přihlašovacích údajů SQL Server ověřování, které mají přístup k instancím SQL Server a databázím spuštěným na cílových serverech. Tento proces zjišťování je bez agentů, tj. na cílových serverech není nic nainstalováno.

> [!Note]
> Zjišťování a hodnocení instancí SQL Server a databází spuštěných ve vašem prostředí VMware je teď ve verzi Preview. Pokud chcete tuto funkci vyzkoušet, použijte [**tento odkaz**](https://aka.ms/AzureMigrate/SQL) a vytvořte projekt v oblasti **Austrálie – východ**. Pokud projekt již máte v Austrálii – východ a chcete si vyzkoušet tuto funkci, ujistěte se, že jste dokončili [**požadavky**](how-to-discover-sql-existing-project.md) v tomto článku.

## <a name="before-you-start"></a>Než začnete

- Ujistěte se, že jste udělali toto: 
    - Vytvořili jste [projekt Azure Migrate](./create-manage-projects.md) před oznámením funkce vyhodnocení SQL pro vaši oblast.
    - Přidání nástroje [Azure Migrate: Discovery and Assessment](./how-to-assess.md) Tool do projektu
- Kontrola [požadavků a podpory zjišťování aplikací](./migrate-support-matrix-vmware.md#vmware-requirements)
-  Ujistěte se, že servery, na kterých spouštíte App-Discovery, mají nainstalované prostředí PowerShell verze 2,0 nebo novější a nástroje VMware (novější než 10.2.0) jsou nainstalované.
- Ověřte [požadavky](./migrate-appliance.md) na nasazení zařízení Azure Migrate.
- Ověřte, že v předplatném máte [požadované role](./create-manage-projects.md#verify-permissions) pro vytváření prostředků.
- Ujistěte se, že zařízení má přístup k Internetu.

## <a name="enable-discovery-of-sql-server-instances-and-databases"></a>Povolit zjišťování instancí SQL Server a databází

1. V Azure Migrate projektu buď
    - Vyberte **nepovolenou** na dlaždici centra nebo :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="dlaždici Azure Migrate centra s nepovoleným zjišťováním SQL"::: .
    - Vyberte možnost **Nepovoleno** u žádné položky na stránce zjišťování serveru v části instance SQL sloupce :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="Azure Migrate zjištěné servery s povoleným zjišťováním SQL"::: .
2. V části zjišťovat SQL Server instance a databáze postupujte podle následujících kroků:
    - Chcete-li vytvořit požadovaný prostředek, vyberte možnost **upgradovat**.
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Tlačítko pro upgrade zařízení Azure Migrate":::
    - Ověřte, že se služby běžící na zařízení aktualizují na nejnovější verze. Provedete to tak, že na serveru zařízení spustíte Správce konfigurace zařízení a na panelu nastavit požadované součásti vyberete Zobrazit služby zařízení.
        - Zařízení a jeho součásti se automaticky aktualizují. :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="Podívejte se na verzi zařízení"::: .
    - Na panelu Správa přihlašovacích údajů a zdrojů zjišťování ve Správci konfigurace zařízení přidejte přihlašovací údaje k doméně nebo SQL Server, které mají přístup sysadmin ke zjištění instance SQL Server a databází. 
    Můžete využít funkci automatického mapování přihlašovacích údajů zařízení nebo ručně namapovat přihlašovací údaje na příslušný server, jak je zvýrazněné [tady](/azure/migrate/tutorial-discover-vmware#start-continuous-discovery).
        
    Poznámka:
    - Ujistěte se prosím, že inventář softwaru je už povolený, nebo pokud chcete povolit stejné oprávnění, zadejte doménu nebo přihlašovací údaje k doméně nebo jiné doméně. Aby bylo možné zjišťovat SQL Server instance, je nutné provést inventář softwaru.
    - Zařízení se pokusí ověřit přihlašovací údaje domény pomocí služby AD, jak jsou přidány. Ujistěte se prosím, že server zařízení má na serveru AD přidruženého k přihlašovacím údajům síťovou linku. Přihlašovací údaje přidružené k ověřování SQL Server nejsou ověřené. 

3. Po přidání požadovaných přihlašovacích údajů vyberte spustit zjišťování a začněte kontrolu.

> [!Note] 
>Před vytvořením posouzení pro Azure SQL prosím počkejte, než se spustí zjišťování SQL pro nějakou dobu. Pokud není povoleno dokončení zjišťování instancí SQL Server a databází, jsou příslušné instance v sestavě posouzení označeny jako **neznámé** .

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak vytvořit [posouzení Azure SQL](./how-to-create-azure-sql-assessment.md) .
- Další informace o [hodnoceních Azure SQL](./concepts-azure-sql-assessment-calculation.md)