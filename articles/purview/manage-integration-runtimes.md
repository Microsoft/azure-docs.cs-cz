---
title: Vytváření a Správa prostředí Integration runtime
description: Tento článek vysvětluje kroky pro vytváření a správu prostředí Integration runtime v Azure dosah.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 9276f845c95b5e736180159b282ddedc33523c17
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980741"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Vytvoření a Správa prostředí Integration runtime v místním prostředí

Tento článek popisuje, jak vytvořit a spravovat prostředí Integration runtime (SHIR) v místním prostředí, které umožňuje kontrolovat zdroje dat v Azure dosah.

> [!NOTE]
> Integration Runtime dosah nejde sdílet s Azure synapse Analytics nebo Azure Data Factory Integration Runtime na stejném počítači. Je nutné ji nainstalovat na oddělený počítač.

## <a name="create-a-self-hosted-integration-runtime"></a>Vytvoření místního prostředí Integration Runtime

1. Na domovské stránce dosah studia v levém navigačním podokně vyberte **Centrum pro správu** .

2. V části **zdroje a skenování** v levém podokně vyberte **modul runtime integrace** a pak vyberte **+ Nová**.

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Klikněte na IR.":::

3. Na stránce **instalace prostředí Integration runtime** vyberte v místním prostředí **, aby se** vytvořila Self-Hosted IR, a pak vyberte **pokračovat**.

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Vytvořte nový SHIR.":::

4. Zadejte název pro IR a vyberte vytvořit.

5. Na stránce **nastavení Integration runtime** postupujte podle pokynů v části **Ruční nastavení** . Prostředí Integration runtime si budete muset stáhnout z webu pro stažení do virtuálního počítače nebo počítače, kde ho máte v úmyslu spustit.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="získat klíč":::

   - Zkopírujte a vložte ověřovací klíč.

   - Stáhněte si místní prostředí Integration runtime z [Microsoft Integration runtime](https://www.microsoft.com/download/details.aspx?id=39717) na místním počítači s Windows. Spusťte instalační program.

   - Na stránce **Integration runtime (v místním prostředí) registrace** vložte jeden ze dvou klíčů, které jste předtím uložili, a vyberte **Registrovat**.

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="vstupní klíč.":::

   - Na stránce **nový uzel Integration runtime (v místním prostředí)** vyberte **Dokončit**.

6. Po úspěšné registraci místně hostovaného prostředí Integration runtime se zobrazí následující okno:

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="registrace byla úspěšná.":::

## <a name="manage-a-self-hosted-integration-runtime"></a>Správa prostředí pro místní hostování Integration runtime

Prostředí Integration runtime v místním prostředí můžete upravit tak, že přejdete na **modul runtime integrace** v **centru pro správu**, vyberete IR a potom kliknete na Upravit. Nyní můžete aktualizovat popis, zkopírovat klíč nebo znovu vygenerovat nové klíče.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="Upravit IR.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="Upravit podrobnosti IR":::

Prostředí Integration runtime v místním prostředí můžete odstranit tak, že přejdete na **modul runtime integrace** v centru pro správu, vyberete IR a pak kliknete na **Odstranit**. Po odstranění IR dojde k selhání všech probíhajících kontrol, které se na ni spoléhají.

## <a name="next-steps"></a>Další kroky

[Jak se při prohledávání zjišťují odstraněné prostředky](concept-detect-deleted-assets.md)
