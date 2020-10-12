---
title: Vytváření a správa projektů Azure Migrate
description: Umožňuje najít, vytvořit, spravovat a odstranit projekty v Azure Migrate.
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: d60868f9d0d4c60291cfd92a9e8d11fd3f9a42b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87071796"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Vytváření a správa projektů Azure Migrate

Tento článek popisuje, jak vytvářet, spravovat a odstraňovat [Azure Migrate](migrate-services-overview.md) projekty.


## <a name="create-a-project-for-the-first-time"></a>První vytvoření projektu

Při prvním nastavení Azure Migrate vytvoříte projekt a přidáte Nástroj pro posouzení nebo migraci. Při prvním nastavení [postupujte podle těchto pokynů](how-to-add-tool-first-time.md) .

## <a name="create-additional-projects"></a>Vytvořit další projekty

Pokud již máte projekt Azure Migrate a chcete vytvořit další projekt, udělejte toto:  

1. Na [veřejném portálu Azure](https://portal.azure.com) nebo [Azure Government](https://portal.azure.us)vyhledejte **Azure Migrate**.
2. Na Azure Migrate řídicím panelu > **servery**vyberte v pravém horním rohu možnost **změnit** .

   ![Změnit Azure Migrate projekt](./media/create-manage-projects/switch-project.png)

3. Chcete-li vytvořit nový projekt, vyberte **možnost klikněte sem**.

   ![Vytvořit druhý Azure Migrate projekt](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Najít projekt

Vyhledejte projekt následujícím způsobem:

1. V [Azure Portal](https://portal.azure.com)vyhledejte **Azure Migrate**.
2. V řídicím panelu Azure Migrate > **servery**vyberte v pravém horním rohu možnost **změnit** .

    ![Přepnout na existující Azure Migrate projekt](./media/create-manage-projects/switch-project.png)

3. Vyberte odpovídající předplatné a Azure Migrate projekt.


Pokud jste projekt vytvořili v [předchozí verzi](migrate-services-overview.md#azure-migrate-versions) Azure Migrate, najděte ho následujícím způsobem:

1. V [Azure Portal](https://portal.azure.com)vyhledejte **Azure Migrate**.
2. Pokud jste v řídicím panelu Azure Migrate vytvořili projekt v předchozí verzi, zobrazí se banner odkazující na starší projekty. Vyberte banner.

    ![Přístup ke stávajícím projektům](./media/create-manage-projects/access-existing-projects.png)

3. Zkontrolujte seznam starých projektů.


## <a name="delete-a-project"></a>Odstranit projekt

Odstraňte následujícím způsobem:

1. Otevřete skupinu prostředků Azure, ve které se projekt vytvořil.
2. Na stránce skupina prostředků vyberte **Zobrazit skryté typy**.
3. Vyberte projekt migrace, který chcete odstranit, a jeho přidružené prostředky.
    - Typ prostředku je **Microsoft. migruje/migrateprojects**.
    - Pokud se skupina prostředků používá výhradně v projektu Azure Migrate, můžete odstranit celou skupinu prostředků.

Poznámky:

- Při odstranění se odstraní projekt i metadata zjištěných počítačů.
- Pokud používáte starší verzi Azure Migrate, otevřete skupinu prostředků Azure, ve které se projekt vytvořil. Vyberte projekt migrace, který chcete odstranit (typ prostředku je **projekt migrace**).
- Pokud používáte analýzu závislostí s pracovním prostorem Azure Log Analytics:
    - Pokud jste připojili Log Analytics pracovní prostor k nástroji pro vyhodnocení serveru, pracovní prostor se automaticky neodstraní. Stejný pracovní prostor Log Analytics lze použít pro více scénářů.
    - Pokud chcete pracovní prostor Log Analytics odstranit, udělejte to ručně.
- Odstranění projektu je nevratné. Odstraněné objekty se nedají obnovit.

### <a name="delete-a-workspace-manually"></a>Ruční odstranění pracovního prostoru

1. Přejděte do pracovního prostoru Log Analytics připojeného k projektu.

    - Pokud jste neodstranili Azure Migrate projekt, můžete najít odkaz na pracovní prostor v rámci **Essentials**  >  **posouzení serveru**Essentials.
       ![LA pracovní prostor ](./media/create-manage-projects/loganalytics-workspace.png) .
       
    - Pokud jste již Azure Migrate projekt odstranili, vyberte **skupiny prostředků** v levém podokně Azure Portal a vyhledejte pracovní prostor.
       
2. [Podle pokynů](../azure-monitor/platform/delete-workspace.md) odstraňte pracovní prostor.

## <a name="next-steps"></a>Další kroky

Přidejte nástroje pro [posouzení](how-to-assess.md) nebo [migraci](how-to-migrate.md) do Azure Migrate projektů.
