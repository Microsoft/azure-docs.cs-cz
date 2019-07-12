---
title: Přidání nástrojů pro migraci ve službě Azure Migrate
description: Popisuje postup přidání nástrojů pro migraci v centru Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b3c77f052ed92db235b363e63859b9beb9e4f5a2
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811866"
---
# <a name="add-migration-tools"></a>Přidání nástrojů pro migraci

Tento článek popisuje postup přidání nástrojů pro migraci v [Azure Migrate](migrate-overview.md).

Azure Migrate nabízí rozbočovač nástroje pro vyhodnocení a migraci do Azure. Zahrnuje nativní nástroje, nástroje poskytované dalšími službami Azure a nabídek softwaru třetích stran výrobce (ISV).

Pokud chcete přidat nástroj pro migraci a dosud nenastavili projekt Azure Migrate, proveďte to [článku](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Výběr nástroj pro nezávislé výrobce softwaru

Pokud se rozhodnete [ISV nástroj](migrate-services-overview.md#isv-integration) pro migraci, začněte získání licence nebo registrace pro bezplatnou zkušební verzi, v souladu se zásadami nezávislý výrobce softwaru. V jednotlivých nástrojích je možné se připojit k Azure Migrate. Nasazení nástroje a postupujte podle pokynů nástroje a dokumentaci pro připojení pracovního prostoru nástroje pomocí služby Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Vyberte scénář migrace

1. V projektu Azure Migrate klikněte na tlačítko **přehled**.
2. Vyberte scénář migrace, který chcete použít:

    - Pokud chcete migrovat počítače a úlohy do Azure, vyberte **posoudit a migrovat servery**.
    - Chcete-li migrovat místní SQL počítače, vyberte **posoudit a migrovat databáze s**.
    - Pokud chcete migrovat místní webové aplikace, vyberte **vyhodnocení a migrace webových aplikací**.
    - V režimu offline migraci velkého objemu dat v místním do Azure, vyberte **objednat zařízení Data Box**.

    ![Scénář hodnocení](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Vyberte nástroj pro migraci serveru

1. Klikněte na tlačítko **posoudit a migrovat servery**.
2. V **Azure Migrate – servery**, pokud ještě jste nepřidali nástrojů pro migraci, v části **nástrojů pro migraci**vyberte **kliknutím sem přidáte nástroj pro migraci**. Pokud jste už přidali nástroje pro migraci v **přidat další nástroje pro migraci**vyberte **změnu**.

    > [!NOTE]
    > Pokud budete muset přejít do jiného projektu, v **Azure Migrate – servery**vedle možnosti **získáte v podrobnostech o různých migrace projektu**, klikněte na tlačítko **kliknutím sem**.

3. V **Azure Migrate**, vyberte migrační nástroj, který chcete použít.
    - Pokud používáte Azure Migrate serveru migrace, můžete nastavit a spustit migrace přímo v projektu Azure Migrate.
    - Pokud používáte nástroj pro vyhodnocení třetích stran, přejděte na uvedený odkaz pro ISV a spuštění migrace v souladu s pokyny, které poskytují.

## <a name="select-a-database-migration-tool"></a>Vyberte nástroj pro migraci databáze

1. Klikněte na tlačítko **posoudit a migrovat databáze**
2. V **databází**, klikněte na tlačítko **přidat nástroje**.
3. V okně Přidat nástroj > **nástroj pro migraci vybrat**, vyberte nástroj, který chcete použít k migraci vaší databáze.

## <a name="select-a-web-app-migration-tool"></a>Vyberte nástroj pro migraci webové aplikace

1. Klikněte na tlačítko **posoudit a migrovat webových aplikací**.
2. Použijte odkaz na nástroj pro migraci pro službu Azure App Service. Použijte nástroj pro migraci:

    - **Posouzení aplikace online**: Můžete posoudit a migrovat aplikace s online, veřejnou adresu URL pomocí Azure App Service Pomocníka s migrací.
    - **.NET/PHP**: Interní aplikace .NET a PHP si můžete stáhnout a použít Pomocníka s migrací.

## <a name="order-an-azure-data-box"></a>Pořadí Azure Data Box

K migraci velkého objemu dat do Azure, můžete si objednat Azure DAta Box pro přenos dat offline.

1. Klikněte na tlačítko **objednat zařízení Data Box**.
2. V **vyberte váš Azure Data Box**, zadejte své předplatné. 
3. Převod bude import do Azure. Zadejte zdroj dat a cílové oblasti Azure pro data.

## <a name="next-steps"></a>Další postup

Vyzkoušejte si migraci pomocí Azure Migrate serveru migrace pro [Hyper-V](tutorial-migrate-hyper-v.md) nebo [VMware](tutorial-migrate-vmware.md) virtuálních počítačů.
