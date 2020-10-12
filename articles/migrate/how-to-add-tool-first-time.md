---
title: Přidání nástroje pro vyhodnocení/migraci do Azure Migrate
description: Popisuje, jak vytvořit projekt Azure Migrate a přidat nástroj pro vyhodnocení/migraci.
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: a94e3cc18f46c457d6ed54ef88c62adefb07c5b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86102527"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>První přidání nástroje pro vyhodnocení/migraci

Tento článek popisuje, jak poprvé přidat nástroj pro vyhodnocení nebo migraci do projektu [Azure Migrate](./migrate-services-overview.md) .  
Azure Migrate poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a virtuálních a veřejných cloudových virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci a také další [nabídky](migrate-services-overview.md#isv-integration) nástrojů a nezávislí výrobci softwaru (ISV). 

## <a name="check-permissions-to-create-project"></a>Ověřit oprávnění pro vytvoření projektu

Pokud jste ještě nevytvořili Azure Migrate projekt, ověřte, jestli máte správná oprávnění.

1. Na webu Azure Portal otevřete předplatné a vyberte **Řízení přístupu (IAM)** .
2. V části kontrolovat přístup Najděte příslušný účet a kliknutím na něj Zobrazte oprávnění. Měli byste mít oprávnění Přispěvatel nebo Owner.
    - Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem vašeho předplatného.
    - Pokud nejste vlastníkem předplatného, přidělte odpovídající roli ve spolupráci s vlastníkem.

## <a name="create-a-project-and-add-a-tool"></a>Vytvoření projektu a přidání nástroje

Nastavte nový projekt Azure Migrate v rámci předplatného Azure a přidejte nástroj.

- Azure Migrate projekt se používá k ukládání metadat zjišťování, hodnocení a migrace shromážděných z prostředí, které vyhodnotili nebo migrujete. 
- V projektu můžete sledovat zjištěné prostředky a orchestrovat posouzení a migraci.

1. Na webu Azure Portal v části **Všechny služby** vyhledejte **Azure Migrate**.
2. V části **Služby** vyberte **Azure Migrate**.

    ![Nastavit Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. V části **Přehled** klikněte na **Posoudit a migrovat servery**.
4. V části **zjišťování, vyhodnocení a migrace serverů**klikněte na možnost **zhodnotit a migrovat servery**.

    ![Zjištění a posouzení serverů](./media/how-to-add-tool-first-time/assess-migrate.png)

1. V části **Zjistit, posoudit a migrovat servery** klikněte na **Přidat nástroje**.
2. V části **Projekt migrace** vyberte své předplatné Azure a vytvořte skupinu prostředků, pokud ji ještě nemáte.
3. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt.  Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Vytvoření projektu Azure Migrate](./media/how-to-add-tool-first-time/migrate-project.png)

    - Zeměpisné umístění vybrané pro tento projekt slouží jen k uložení metadat získaných z místních virtuálních počítačů. Pro vlastní migraci můžete vybrat libovolnou cílovou oblast.
    - Pokud potřebujete nasadit projekt v rámci konkrétní oblasti v geografické oblasti, použijte následující rozhraní API k vytvoření projektu. Zadejte ID předplatného, název skupiny prostředků a název projektu společně s umístěním. Projděte si geografické oblasti a oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

        `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Klikněte na **Další**a přidejte Nástroj pro posouzení nebo migraci.

    > [!NOTE]
    > Při vytváření projektu je nutné přidat alespoň jeden nástroj pro vyhodnocení nebo migraci.

5. V **nástroji vybrat nástroj pro posouzení**přidejte Nástroj pro posouzení. Pokud nástroj pro posouzení nepotřebujete, vyberte **Přeskočit přidat nástroj pro posouzení na**  >  **Další**. 
2. V **nástroji vybrat nástroj pro migraci**přidejte Nástroj pro migraci podle potřeby. Pokud nepotřebujete Nástroj pro migraci hned teď, vyberte **Přeskočit přidat nástroj pro migraci pro teď**  >  **Další**.
3. V části **Revize + přidat nástroje**zkontrolujte nastavení a klikněte na **Přidat nástroje**.

Po vytvoření projektu můžete vybrat další nástroje pro posouzení a migraci serverů a úloh, databází a webových aplikací.

## <a name="create-additional-projects"></a>Vytvořit další projekty

V některých případech může být nutné vytvořit další Azure Migrate projekty. Například pokud máte datová centra v různých zeměpisných oblastech nebo potřebujete ukládat metadata v jiném geografickém umístění. Vytvořte další projekt následujícím způsobem:

1. V aktuálním projektu Azure Migrate klikněte na **servery** nebo **databáze**.
2. V pravém horním rohu klikněte na **změnit** vedle názvu aktuálního projektu.
3. V **Nastavení**vyberte **kliknutím sem vytvoříte nový projekt**.
4. Vytvořte nový projekt a přidejte nový nástroj, jak je popsáno v předchozím postupu.

## <a name="next-steps"></a>Další kroky

- Začínáme s [Azure Migrate: posouzení serveru](migrate-services-overview.md#azure-migrate-server-assessment-tool)nebo [Azure Migrate: Migrace serveru](migrate-services-overview.md#azure-migrate-server-migration-tool).
- Pokud jste přidali nástroj ISV nebo stěhovací, [Projděte si kroky](prepare-isv-movere.md) pro přípravu na propojení nástroje s Azure Migrate.
- Přečtěte si, jak přidat další nástroje pro [posouzení](how-to-assess.md) a [migraci](how-to-migrate.md) . 
