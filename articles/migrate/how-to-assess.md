---
title: Přidat nástroje pro posouzení ve službě Azure Migrate | Dokumentace Microsoftu
description: Popisuje postup přidání nástroje pro vyhodnocení v centru Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: d176e6276d69cd3465aa4943efa86ea1e6b0736d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811645"
---
# <a name="add-assessment-tools"></a>Přidání nástroje pro vyhodnocení

Tento článek popisuje postup přidání nástroje pro vyhodnocení v [Azure Migrate](migrate-overview.md).

Azure Migrate nabízí rozbočovač nástroje pro vyhodnocení a migraci do Azure. Zahrnuje nativní nástroje, nástroje poskytované dalšími službami Azure a nabídek softwaru třetích stran výrobce (ISV).

Pokud chcete přidat nástroj pro vyhodnocení a ještě nemáte projekt Azure Migrate, použít tento [článku](how-to-add-tool-first-time.md).

## <a name="selecting-an-isv-tool"></a>Výběr nástroj pro nezávislé výrobce softwaru

Pokud se rozhodnete [ISV nástroj](migrate-services-overview.md#isv-integration) pro posouzení, můžete začít získání licence nebo registrace pro bezplatnou zkušební verzi, v souladu se zásadami nezávislý výrobce softwaru. V jednotlivých nástrojích je možné se připojit k Azure Migrate. Postupujte podle pokynů nástroje a dokumentaci pro připojení pracovního prostoru nástroje pomocí služby Azure Migrate. 


## <a name="select-an-assessment-scenario"></a>Vyberte scénáři posouzení

1. V projektu Azure Migrate klikněte na tlačítko **přehled**.
2. Vyberte hodnocení scénáře, který chcete použít:

    - Chcete-li zjistit a posoudit počítače a úlohy pro migraci do Azure, vyberte **posoudit a migrovat servery**.
    - Chcete-li posoudit místní počítače SQL, vyberte **posoudit a migrovat databáze s**.
    - Chcete-li posoudit místní webové aplikace, vyberte **vyhodnocení a migrace webových aplikací**.

    ![Scénář hodnocení](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Vyberte nástroj pro vyhodnocení serveru 

1. Klikněte na tlačítko **posoudit a migrovat servery**.
2. V **Azure Migrate – servery**, pokud jste nepřidali nástroj pro vyhodnocení, v části **posuzovací nástroje**vyberte **kliknutím sem přidáte nástroj pro vyhodnocení**. Pokud jste už přidali nástroje pro vyhodnocení, v **přidat další nástroje pro vyhodnocení**vyberte **změnu**.

    > [!NOTE]
    > Pokud budete muset přejít do jiného projektu, v **Azure Migrate – servery**vedle možnosti **získáte v podrobnostech o různých migrace projektu**, klikněte na tlačítko **kliknutím sem**.

3. V **Azure Migrate**, vyberte nástroj posouzení, které chcete použít.

    
    ![Nástroje pro vyhodnocení](./media/how-to-assess/assess-tool.png)

    - Pokud používáte Azure Migrate Server Assessment, nastavení a spuštění posouzení zobrazit přímo v projektu Azure Migrate.
    - Pokud používáte nástroj pro vyhodnocení třetích stran, přejděte na uvedený odkaz pro svůj web a spustit vyhodnocení v souladu s pokyny, které poskytují.


## <a name="select-a-database-assessment-tool"></a>Vyberte nástroj pro vyhodnocení databáze

1. Klikněte na tlačítko **posoudit a migrovat databáze**
2. V **databází**, klikněte na tlačítko **přidat nástroje**.
3. V okně Přidat nástroj > **nástroj pro vyhodnocení vyberte**, vyberte nástroj, který chcete použít k vyhodnocení vaší databáze.

## <a name="select-a-web-app-assessment-tool"></a>Vyberte nástroj pro vyhodnocení webové aplikace

1. Klikněte na tlačítko **posoudit a migrovat webových aplikací**.
2. Použijte odkaz na nástroj pro migraci pro službu Azure App Service. Použijte nástroj pro migraci:

    - **Posouzení aplikace online**: Aplikace s online, veřejnou adresu URL můžete vyhodnotit pomocí Azure App Service Pomocníka s migrací.
    - **.NET/PHP**: Interní aplikace .NET a PHP si můžete stáhnout a použít Pomocníka s migrací.



## <a name="next-steps"></a>Další kroky

Vyzkoušejte si posouzení pomocí Azure Migrate Server Assessment pro [Hyper-V](tutorial-prepare-hyper-v.md) nebo [VMware](tutorial-prepare-vmware.md) virtuálních počítačů.
