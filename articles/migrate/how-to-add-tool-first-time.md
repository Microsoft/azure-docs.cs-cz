---
title: Přidat nástroj pro vyhodnocení a migrace poprvé ve službě Azure Migrate | Dokumentace Microsoftu
description: Popisuje, jak vytvořit projekt Azure Migrate a přidat nástroj pro vyhodnocení a migrace.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b226f7c5879673b573133cde45db78d8d1f2fffa
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812022"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Přidat nástroj pro vyhodnocení a migrace poprvé

Tento článek popisuje, jak přidat posouzení nebo migrace nástroje pro [Azure Migrate](migrate-overview.md) projektu poprvé.  
Azure Migrate nabízí centrální rozbočovač pro sledování zjišťování, vyhodnocení a migraci místních aplikací a úloh a soukromého a veřejného cloudu virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro vyhodnocení a migrace, jakož i softwaru třetích stran, nezávislé výrobce (ISV) [nabídek](migrate-services-overview.md#isv-integration) . 

## <a name="create-a-project-and-add-a-tool"></a>Vytvořte projekt a přidat nástroj

Nastavte nový projekt Azure Migrate v rámci předplatného Azure a přidat nástroj.

- Projekt Azure Migrate se používá k ukládání zjišťování, vyhodnocení a migrace metadata shromážděná z prostředí už posouzení nebo migrace. 
- V projektu můžete sledovat zjištěných prostředků a orchestrovat vyhodnocení a migraci.

1. Na webu Azure Portal > **všechny služby**, vyhledejte **Azure Migrate**.
2. V části **služby**vyberte **Azure Migrate**.

    ![Nastavte si Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. V **přehled**, klikněte na tlačítko **posoudit a migrovat servery**.
4. V části **zjišťování, vyhodnocení a migrace serverů**, klikněte na tlačítko **posouzení a migraci serverů**.

    ![Zkoumání a posouzení servery](./media/how-to-add-tool-first-time/assess-migrate.png)

1. V **zjišťování, vyhodnocení a migrace serverů**, klikněte na tlačítko **přidat nástroje**.
2. V **migrace projektu**, vyberte své předplatné Azure a pokud ho nemáte, vytvořte skupinu prostředků.
3. V **Project Details**, zadejte název projektu a zeměpisné oblasti, ve kterém chcete vytvořit projekt. 

    ![Vytvoření projektu Azure Migrate](./media/how-to-add-tool-first-time/migrate-project.png)

    Projekt Azure Migrate můžete vytvořit v některém z těchto zeměpisných oblastech.

    **Zeměpisné oblasti** | **Oblast umístění úložiště**
    --- | ---
    Asie | Jihovýchodní Asie a jihovýchodní Asie
    Evropa | Jižní Evropa a západní Evropa
    Spojené království | Velká Británie – jih nebo Velká Británie – západ
    Spojené státy | USA (střed) nebo USA – západ 2

    Zeměpisné umístění vybrané pro tento projekt slouží jen k uložení metadat získaných z místních virtuálních počítačů. Můžete vybrat libovolnou oblast cílového skutečné migrace.

4. Klikněte na tlačítko **Další**a přidat hodnocení nebo migrací nástroj.

    > [!NOTE]
    > Při vytváření projektu musíte přidat aspoň jeden nástroj pro posouzení nebo migrace.

5. V **nástroj pro vyhodnocení vyberte**, přidat nástroj pro vyhodnocení. Pokud nepotřebujete nástroj pro vyhodnocení, vyberte **přeskočit přidávání nástroj pro vyhodnocení nyní** > **Další**. 
2. V **nástroj pro migraci vybrat**, podle potřeby přidat nástroj pro migraci. Pokud nástroj pro migraci momentálně nepotřebujete, vyberte **přidat nástroj pro migraci prozatím přeskočit** > **Další**.
3. V **zkontrolujte + přidat nástroje**, zkontrolujte nastavení a klikněte na tlačítko **přidat nástroje**.

Po vytvoření projektu můžete vybrat další nástroje pro vyhodnocení a migraci serverů a úloh, databáze a webových aplikací.

## <a name="create-additional-projects"></a>Vytvořte další projekty

V některých případech můžete potřebovat vytvořit další projekty Azure Migrate. Například pokud máte v různých zeměpisných oblastech datových center nebo potřebujete k ukládání metadat v jiném datovém typu geography. Vytvořte další projekt následujícím způsobem:

1. V aktuálním projektu Azure Migrate klikněte na tlačítko **servery** nebo **databází**.
2. V pravém horním rohu klikněte na tlačítko **změnu** vedle názvu aktuálního projektu.
3. V **nastavení**vyberte **kliknutím sem vytvoříte nový projekt**.
4. Vytvořte nový projekt a přidejte nový nástroj, jak je popsáno v předchozím postupu.

## <a name="next-steps"></a>Další postup

Zjistěte, jak přidat další [posouzení](how-to-assess.md) a [migrace](how-to-migrate.md) nástroje. 
