---
title: Migrace aplikací do nejnovějšího schématu
description: Jak migrovat definice JSON pracovního postupu aplikace logiky na nejnovější verzi schématu jazyka definice pracovního postupu
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: 114b8b32d4abb1fd9b7e641625cd1b132470bafd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87281442"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migrace aplikací logiky na nejnovější verzi schématu

Pokud chcete stávající aplikace logiky přesunout do nejnovějšího schématu, postupujte takto: 

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

2. V nabídce aplikace logiky klikněte na **Přehled**. Na panelu nástrojů vyberte **Aktualizovat schéma**.

   > [!NOTE]
   > Když zvolíte možnost **Aktualizovat schéma**, Azure Logic Apps automaticky spustí kroky migrace a poskytne vám výstup kódu. Tento výstup můžete použít pro aktualizaci definice aplikace logiky. Ujistěte se však, že dodržujete osvědčené postupy, jak je popsáno v následující části věnované **osvědčeným postupům** .

   ![Aktualizovat schéma](./media/connectors-schema-migration/update-schema.png)

   Zobrazí se stránka aktualizovat schéma a zobrazí odkaz na dokument, který popisuje vylepšení v novém schématu.

## <a name="best-practices"></a>Osvědčené postupy

Tady jsou některé osvědčené postupy pro migraci aplikací logiky na nejnovější verzi schématu:

* Zkopírujte migrovaný skript do nové aplikace logiky. Nepřepisujte starou verzi, dokud nedokončíte testování a ověříte, že migrovaná aplikace funguje podle očekávání.

* **Před** vložením do produkčního prostředí otestujte aplikaci logiky.

* Po dokončení migrace začněte aktualizovat aplikace logiky, aby používaly [spravovaná rozhraní API](../connectors/apis-list.md) tam, kde je to možné. Můžete například začít používat Dropbox v2 všude, kde používáte DropBox v1.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [ručně migrovat aplikace logiky](../logic-apps/logic-apps-schema-2016-04-01.md)

