---
title: Přidání nástrojů pro hodnocení v Azure Migrate
description: Přečtěte si, jak přidat nástroje pro hodnocení v Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185912"
---
# <a name="add-assessment-tools"></a>Přidání nástrojů pro vyhodnocení

Tento článek popisuje, jak přidat nástroje pro hodnocení v [Azure Migrate](migrate-overview.md).

Azure Migrate poskytuje centrum nástrojů pro hodnocení a migraci do Azure. Obsahuje nástroje pro migraci Azure, stejně jako další nástroje a nabídky nezávislých dodavatelů softwaru (ISV).

Pokud chcete přidat nástroj pro hodnocení a ještě nemáte projekt Migrace Azure, postupujte podle tohoto [článku](how-to-add-tool-first-time.md).

## <a name="select-a-tool"></a>Výběr nástroje

Pokud pro hodnocení zvolíte nástroj pro migraci mimo Azure, začněte získáním licence nebo registrací bezplatné zkušební verze v souladu se zásadami nástroje. Nástroje mají možnost se připojit k Migraci Azure. Podle pokynů a dokumentace připojte nástroj k Migraci Azure. [Přečtěte si další informace](migrate-services-overview.md) o nástrojích.


## <a name="select-an-assessment-scenario"></a>Výběr scénáře hodnocení

1. V projektu Migrace Azure klikněte na **Přehled**.
2. Vyberte scénář hodnocení, který chcete použít:

    - Pokud chcete zjistit a vyhodnocovat počítače a úlohy pro migraci do Azure, vyberte **Posoudit a migrovat servery**.
    - Chcete-li posoudit místní počítače SQL, vyberte **vyhodnotit a migrovat databáze**.
    - Pokud chcete posoudit místní webové aplikace, vyberte **Posoudit a migrovat webové aplikace**.

    ![Hodnotící scénář](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Výběr nástroje pro hodnocení serveru 

1. Klepněte na **tlačítko Posoudit a migrovat servery**.
2. Pokud jste v **Azure Migrate – Servers**nepřidali nástroj pro hodnocení, v části Nástroje pro **hodnocení**vyberte **Kliknutím sem přidáte nástroj pro hodnocení**. Pokud jste už přidali nástroje pro hodnocení, vyberte v **přidejte další nástroje pro hodnocení**vmožnost **Změnit**.

    > [!NOTE]
    > Pokud potřebujete přejít na jiný projekt, v **Azure Migrate – Servery**, vedle **Zobrazit podrobnosti pro jiný projekt migrace**, klikněte na Tlačítko **Klikněte zde**.

3. V **Azure Migrate**vyberte nástroj pro hodnocení, který chcete použít.

    - Pokud používáte Azure Migrate Server Assessment, můžete nastavit, spustit a zobrazit hodnocení přímo v projektu Migrace Azure.
    - Pokud používáte jiný nástroj pro hodnocení, přejděte na odkaz pro jejich web a spusťte hodnocení v souladu s pokyny, které poskytují.


## <a name="select-a-database-assessment-tool"></a>Výběr nástroje pro hodnocení databáze

1. Klikněte na **Posoudit a migrovat databáze.**
2. V **databázi**klepněte na tlačítko **Přidat nástroje**.
3. V části Přidat nástroj > **nástroj pro hodnocení výběrvyberte**vyberte nástroj, který chcete použít k posouzení databáze.

## <a name="select-a-web-app-assessment-tool"></a>Výběr nástroje pro hodnocení webové aplikace

1. Klikněte na **Posoudit a migrovat webové aplikace**.
2. Postupujte podle odkazu na nástroj migrace pro službu Azure App Service. Pomocí nástroje pro migraci můžete:

    - **Vyhodnoťte aplikace online**: Aplikace můžete vyhodnocovat pomocí veřejné adresy URL online pomocí Pomocníka pro migraci služby Azure App Service.
    - **.NET/PHP**: Pro interní aplikace .NET a PHP si můžete stáhnout a spustit Pomocníka pro migraci.



## <a name="next-steps"></a>Další kroky

Vyzkoušejte vyhodnocení pomocí azure migrate server assessment pro virtuální počítače [VMware,](tutorial-prepare-vmware.md) [hyper-V](tutorial-prepare-hyper-v.md)nebo [fyzické servery](tutorial-prepare-physical.md)
