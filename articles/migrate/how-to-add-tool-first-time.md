---
title: Přidání nástroje pro hodnocení/migraci v Azure Migrate
description: Popisuje, jak vytvořit projekt Migrace Azure a přidat nástroj pro hodnocení a migraci.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 319d97d96bd054aed90079777e2ff83d0e308e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185938"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>První přidání nástroje pro vyhodnocení/migraci

Tento článek popisuje, jak poprvé přidat nástroj pro hodnocení nebo migraci do projektu [Migrace Azure.](migrate-overview.md)  
Azure Migrate poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace místních aplikací a úloh a virtuálních počítačích privátního/veřejného cloudu do Azure. Centrum poskytuje nástroje pro migraci Azure pro hodnocení a migraci, stejně jako další nástroje a [nabídky nezávislých](migrate-services-overview.md#isv-integration) dodavatelů softwaru (ISV). 

## <a name="create-a-project-and-add-a-tool"></a>Vytvoření projektu a přidání nástroje

Nastavte nový projekt Azure Migrate v rámci předplatného Azure a přidejte nástroj.

- Projekt migrace Azure se používá k ukládání metadat zjišťování, hodnocení a migrace shromážděných z prostředí, které vyhodnocujete nebo migrujete. 
- V projektu můžete sledovat zjištěné datové zdroje a organizovat hodnocení a migraci.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.

    ![Nastavení migrace Azure](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. V části **Přehled** klikněte na **Posoudit a migrovat servery**.
4. V části **Zjistit, vyhodnoťte a migrujte servery**na **možnost Posoudit a migrovat servery**.

    ![Zjišťování a posuzování serverů](./media/how-to-add-tool-first-time/assess-migrate.png)

1. V části **Zjistit, posoudit a migrovat servery** klikněte na **Přidat nástroje**.
2. V části **Projekt migrace** vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ji ještě nemáte.
3. V **poli Podrobnosti projektu**zadejte název projektu a zeměpisnou polohu, ve které chcete projekt vytvořit. 

    ![Vytvoření projektu migrace Azure](./media/how-to-add-tool-first-time/migrate-project.png)

    Můžete vytvořit projekt Migrace Azure v některé z těchto zeměpisných oblastí.

   **Geografie** | **Oblast umístění úložiště**
    --- | ---
    Asie   | Jihovýchodní Asie nebo východní Asie
    Evropa | Severní Evropa nebo Západní Evropa
    Japonsko  | Japonsko – východ nebo Západ
    Spojené království | Spojené království – jih nebo Velká Británie – západ
    Spojené státy | Střední USA nebo západní USA 2
    Kanada | Střední Kanada
    Indie  | Indie Střední nebo Indie Jih
    Austrálie | Austrálie Jihovýchod

    Zeměpisné umístění vybrané pro tento projekt slouží jen k uložení metadat získaných z místních virtuálních počítačů. Můžete vybrat libovolnou cílovou oblast pro skutečnou migraci.

    Pokud chcete určit konkrétní oblast v rámci zeměpisné oblasti pro nasazení projektu migrace a jeho přidružené prostředky (omezení zásad ve vašem předplatném může povolit nasazení prostředků Azure pouze pro konkrétní oblast Azure), můžete použít níže uvedené rozhraní API k použití níže uvedeného rozhraní API vytvořit projekt migrace. Zadejte ID předplatného, název skupiny prostředků, migrovat název projektu spolu s umístěním (všechny oblasti Azure uvedené v tabulce, kde se nasadí Azure Migrate.)

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Klepněte na **tlačítko Další**a přidejte nástroj pro hodnocení nebo migraci.

    > [!NOTE]
    > Při vytváření projektu je třeba přidat alespoň jeden nástroj pro hodnocení nebo migraci.

5. V **nástroji pro výběr hodnocení**přidejte nástroj pro hodnocení. Pokud nástroj pro hodnocení nepotřebujete, vyberte **Přeskočit přidání nástroje pro hodnocení pro tuto chvíli** > **Další**. 
2. V **nástroji Pro výběr migrace**přidejte podle potřeby nástroj pro migraci. Pokud nástroj pro migraci právě teď nepotřebujete, vyberte **Přeskočit přidání nástroje pro migraci pro tuto chvíli** > **Další**.
3. V **části Revize + přidat nástroje**zkontrolujte nastavení a klepněte na tlačítko Přidat **nástroje**.

Po vytvoření projektu můžete vybrat další nástroje pro hodnocení a migraci serverů a úloh, databází a webových aplikací.

## <a name="create-additional-projects"></a>Vytvořit další projekty

V některých případech budete muset vytvořit další projekty Migrace Azure. Například pokud máte datová centra v různých zeměpisných oblastech nebo je třeba ukládat metadata v jiné zeměpisné oblasti. Vytvořte další projekt takto:

1. V aktuálním projektu Migrace Azure klikněte na **Servery** nebo **Databáze**.
2. V pravém horním rohu klikněte vedle názvu aktuálního projektu na **Změnit.**
3. V **nastavení**vyberte **Klepnutím sem vytvořte nový projekt**.
4. Vytvořte nový projekt a přidejte nový nástroj, jak je popsáno v předchozím postupu.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak přidat další nástroje pro [hodnocení](how-to-assess.md) a [migraci.](how-to-migrate.md) 
