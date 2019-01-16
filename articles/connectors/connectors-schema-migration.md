---
title: Migrace aplikací do nejnovější schéma – Azure Logic Apps | Dokumentace Microsoftu
description: Jak migrovat na nejnovější verzi schématu logic apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: bf27739bd42106550c18e3bbc27a1ff8b3770747
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321122"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migrace aplikací logiky na nejnovější verzi schématu

Chcete-li přesunout vaše stávající aplikace logiky na nejnovější schéma, postupujte takto: 

1. V [webu Azure portal](https://portal.azure.com), otevřete v návrháři aplikace logiky aplikace logiky.

2. V nabídce aplikace logiky zvolte **přehled**. Na panelu nástrojů zvolte **aktualizovat schéma**.

   > [!NOTE]
   > Pokud zvolíte **aktualizovat schéma**, automaticky spustí kroky migrace Azure Logic Apps a poskytuje výstup kódu. Tento výstup můžete použít k aktualizaci definici aplikace logiky. Nicméně, ujistěte se, že dodržujete osvědčené postupy, jak je popsáno v následujícím **osvědčené postupy** oddílu.

   ![Aktualizovat schéma](./media/connectors-schema-migration/update-schema.png)

   Na stránce aktualizovat schéma se zobrazí a zobrazí odkaz na dokument, který popisuje vylepšení v novém schématu.

## <a name="best-practices"></a>Osvědčené postupy

Tady jsou některé osvědčené postupy pro migraci vašich aplikací logiky na nejnovější verzi schématu:

* Migrovaný skript zkopírujte do nové aplikace logiky. Nepřepisujte předchozí verzi aplikace, až po dokončení testování a potvrďte, že migrované aplikace funguje podle očekávání.

* Otestujte aplikaci logiky **před** ji předáte do produkce.

* Po dokončení migrace spusťte aktualizaci logic apps používat [spravovaná rozhraní API](../connectors/apis-list.md) tam, kde je to možné. Například začněte používat Dropbox v2 všude, kde používat DropBox v1.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [vaší aplikace logiky přenášet ručně](../logic-apps/logic-apps-schema-2015-08-01.md)
