---
title: Migrace aplikací do nejnovějšího schématu
description: Jak migrovat definice JSON pracovního postupu aplikace logiky do nejnovější verze schématu definice pracovního postupu
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: cef0fcb990cd2c5c6583822d4dc4c6993c52eac2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666784"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migrace aplikací logiky na nejnovější verzi schématu

Pokud chcete přesunout existující aplikace logiky do nejnovějšího schématu, postupujte takto: 

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři aplikací logiky.

2. V nabídce aplikace logiky zvolte **Přehled**. Na panelu nástrojů zvolte **Aktualizovat schéma**.

   > [!NOTE]
   > Když zvolíte **Aktualizovat schéma**, Azure Logic Apps automaticky spustí kroky migrace a poskytne výstup kódu pro vás. Tento výstup můžete použít k aktualizaci definice aplikace logiky. Ujistěte se však, že dodržujete osvědčené postupy, jak je popsáno v následující části **Doporučené postupy.**

   ![Aktualizovat schéma](./media/connectors-schema-migration/update-schema.png)

   Zobrazí se stránka Aktualizovat schéma a zobrazí odkaz na dokument, který popisuje vylepšení nového schématu.

## <a name="best-practices"></a>Osvědčené postupy

Tady je několik doporučených postupů pro migraci aplikací logiky na nejnovější verzi schématu:

* Zkopírujte migrovaný skript do nové aplikace logiky. Nepřepisujte starou verzi, dokud nedokončíte testování a nepotvrďte, že migrovaná aplikace funguje podle očekávání.

* Otestujte aplikaci logiky **před** uvedením do produkčního prostředí.

* Po dokončení migrace začněte aktualizovat aplikace logiky tak, aby tam, kde je to možné, [používaly spravovaná rozhraní API.](../connectors/apis-list.md) Můžete například začít používat Dropbox v2 všude, kde používáte DropBox v1.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [ručně migrovat aplikace Logiky.](../logic-apps/logic-apps-schema-2015-08-01.md)
