---
title: Vytváření a správa projektů Azure Migrate
description: Hledání, vytváření, správa a odstraňování projektů v Azure Migrate.
ms.topic: how-to
ms.date: 04/19/2020
ms.openlocfilehash: f5079ed979d98f2c6f0c654c860c6f176f366497
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676396"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Vytváření a správa projektů Azure Migrate

Tento článek popisuje, jak vytvořit, spravovat a odstranit projekty [Migrace Azure.](migrate-services-overview.md)


## <a name="create-a-project-for-the-first-time"></a>První vytvoření projektu

Při prvním nastavení Migrace Azure vytvoříte projekt a přidáte nástroj pro hodnocení nebo migraci. [Postupujte podle těchto pokynů](how-to-add-tool-first-time.md) a nastavte je poprvé.

## <a name="create-additional-projects"></a>Vytvořit další projekty

Pokud už máte projekt Migrace Azure a chcete vytvořit další projekt, postupujte takto:  

1. Na [veřejném portálu Azure](https://portal.azure.com) nebo [Azure Government](https://portal.azure.us)vyhledejte Azure **Migrate**.
2. Na řídicím panelu Azure Migrate > **servery**vyberte **změnu** v pravém horním rohu.

   ![Změna projektu migrace Azure](./media/create-manage-projects/switch-project.png)

3. Chcete-li vytvořit nový projekt, vyberte **klepnutím sem**.

   ![Vytvoření druhého projektu migrace Azure](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Najít projekt

Najděte projekt takto:

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte **Azure Migrate**.
2. Na řídicím panelu Azure Migrate > **servery**vyberte **změnu** v pravém horním rohu.

    ![Přepnutí na existující projekt Migrace Azure](./media/create-manage-projects/switch-project.png)

3. Vyberte příslušné předplatné a projekt Migrace Azure.


Pokud jste projekt vytvořili v [předchozí verzi](migrate-services-overview.md#azure-migrate-versions) Azure Migrate, najděte ho takto:

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte **Azure Migrate**.
2. Na řídicím panelu Migrace Azure, pokud jste vytvořili projekt v předchozí verzi, zobrazí se banner odkazující na starší projekty. Vyberte banner.

    ![Přístup k existujícím projektům](./media/create-manage-projects/access-existing-projects.png)

3. Prohlédněte si seznam starých projektů.


## <a name="delete-a-project"></a>Odstranění projektu

Odstraňte následujícím způsobem:

1. Otevřete skupinu prostředků Azure, ve které byl projekt vytvořen.
2. Na stránce skupiny prostředků vyberte **Zobrazit skryté typy**.
3. Vyberte migrovat projekt, který chcete odstranit, a jeho přidružené zdroje.
    - Typ prostředku je **Microsoft.Migrate/migrateprojects**.
    - Pokud skupinu prostředků používá výhradně projekt Migrace Azure, můžete odstranit celou skupinu prostředků.


Poznámky:

- Při odstranění, projekt a metadata o zjištěných počítačích jsou odstraněny.
- Pokud používáte starší verzi Azure Migrate, otevřete skupinu prostředků Azure, ve které byl projekt vytvořen. Vyberte migrovat projekt, který chcete odstranit (typ zdroje je **Projekt migrace**).
- Pokud používáte analýzu závislostí s pracovním prostorem Azure Log Analytics:
    - Pokud jste k nástroji Pro hodnocení serveru připojili pracovní prostor Analýzy protokolů, pracovní prostor se automaticky neodstraní. Stejný pracovní prostor Log Analytics lze použít pro více scénářů.
    - Pokud chcete odstranit pracovní prostor Analýzy protokolů, udělejte to ručně.

### <a name="delete-a-workspace-manually"></a>Ruční odstranění pracovního prostoru

1. Přejděte do pracovního prostoru Log Analytics připojeného k projektu.

    - Pokud jste projekt Migrace Azure neodstranili, najdete odkaz na pracovní prostor v **aplikaci Essentials** > **Server Assessment**.
       ![Pracovní prostor](./media/create-manage-projects/loganalytics-workspace.png)LA .
       
    - Pokud jste už projekt Migrace Azure odstranili, vyberte **skupiny prostředků** v levém podokně portálu Azure a najděte pracovní prostor.
       
2. [Podle pokynů](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) pracovní prostor odstraňte.

## <a name="next-steps"></a>Další kroky

Přidejte nástroje [pro hodnocení](how-to-assess.md) nebo [migraci](how-to-migrate.md) do projektů Migrace Azure.
