---
title: Přidání nástroje pro posouzení/migraci poprvé v Azure Migrate | Microsoft Docs
description: Popisuje, jak vytvořit projekt Azure Migrate a přidat nástroj pro vyhodnocení/migraci.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: a4e66175b20552e632702cb2ba46d0ae6c0956d4
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720242"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>První přidání nástroje pro vyhodnocení/migraci

Tento článek popisuje, jak poprvé přidat nástroj pro vyhodnocení nebo migraci do projektu [Azure Migrate](migrate-overview.md) .  
Azure Migrate poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a virtuálních a veřejných cloudových virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci a také další [nabídky](migrate-services-overview.md#isv-integration) nástrojů a nezávislí výrobci softwaru (ISV). 

## <a name="create-a-project-and-add-a-tool"></a>Vytvoření projektu a přidání nástroje

Nastavte nový projekt Azure Migrate v rámci předplatného Azure a přidejte nástroj.

- Azure Migrate projekt se používá k ukládání metadat zjišťování, hodnocení a migrace shromážděných z prostředí, které vyhodnotili nebo migrujete. 
- V projektu můžete sledovat zjištěné prostředky a orchestrovat posuzování a migraci.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.

    ![Nastavit Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. V části **Přehled** klikněte na **Posoudit a migrovat servery**.
4. V části **zjišťování, vyhodnocení a migrace serverů**klikněte na možnost **zhodnotit a migrovat servery**.

    ![Zjišťování a vyhodnocení serverů](./media/how-to-add-tool-first-time/assess-migrate.png)

1. V části **Zjistit, posoudit a migrovat servery** klikněte na **Přidat nástroje**.
2. V části **Projekt migrace** vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ji ještě nemáte.
3. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt. 

    ![Vytvoření projektu Azure Migrate](./media/how-to-add-tool-first-time/migrate-project.png)

    Azure Migrate projekt můžete vytvořit v některém z těchto geografických oblastí.

   **Zeměpisné oblasti** | **Oblast umístění úložiště**
    --- | ---
    Asie   | Jihovýchodní Asie nebo Východní Asie
    Evropa | Severní Evropa nebo Západní Evropa
    Japonsko  | Japonsko – východ nebo Japonsko – západ
    Spojené království | Velká Británie – jih nebo Velká Británie – západ
    Spojené státy | Střed USA nebo Západní USA 2
    Kanada | Střední Kanada
    Indie  | Indie – střed nebo Indie – jih
    Austrálie | Austrálie – jihovýchod

    Zeměpisné umístění vybrané pro tento projekt slouží jen k uložení metadat získaných z místních virtuálních počítačů. Pro vlastní migraci můžete vybrat libovolnou cílovou oblast.

    Pokud chcete určit konkrétní oblast v rámci geografické oblasti pro nasazení projektu migrace a jeho přidružených prostředků (omezení zásad ve vašem předplatném může umožňovat nasazení prostředků Azure jenom do konkrétní oblasti Azure), můžete k tomu použít rozhraní API uvedené níže. Vytvořte projekt migrace. Zadejte ID předplatného, název skupiny prostředků, migrujte název projektu společně s umístěním (libovolné oblasti Azure uvedené v tabulce, ve které je nasazen Azure Migrate.)

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Klikněte na **Další**a přidejte Nástroj pro posouzení nebo migraci.

    > [!NOTE]
    > Při vytváření projektu je nutné přidat alespoň jeden nástroj pro vyhodnocení nebo migraci.

5. V **nástroji vybrat nástroj pro posouzení**přidejte Nástroj pro posouzení. Pokud nástroj pro posouzení nepotřebujete, vyberte **Přeskočit přidat nástroj pro posouzení** > **Další**. 
2. V **nástroji vybrat nástroj pro migraci**přidejte Nástroj pro migraci podle potřeby. Pokud teď Nástroj pro migraci nepotřebujete, vyberte pro teď > **Další** **Přeskočit přidat nástroj pro migraci** .
3. V části **Revize + přidat nástroje**zkontrolujte nastavení a klikněte na **Přidat nástroje**.

Po vytvoření projektu můžete vybrat další nástroje pro posouzení a migraci serverů a úloh, databází a webových aplikací.

## <a name="create-additional-projects"></a>Vytvořit další projekty

V některých případech může být nutné vytvořit další Azure Migrate projekty. Například pokud máte datová centra v různých zeměpisných oblastech nebo potřebujete ukládat metadata v jiném geografickém umístění. Vytvořte další projekt následujícím způsobem:

1. V aktuálním projektu Azure Migrate klikněte na **servery** nebo **databáze**.
2. V pravém horním rohu klikněte na **změnit** vedle názvu aktuálního projektu.
3. V **Nastavení**vyberte **kliknutím sem vytvoříte nový projekt**.
4. Vytvořte nový projekt a přidejte nový nástroj, jak je popsáno v předchozím postupu.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak přidat další nástroje pro [posouzení](how-to-assess.md) a [migraci](how-to-migrate.md) . 
