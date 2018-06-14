---
title: Migrace aplikací logiky na schéma verze 2015-08-01-preview | Dokumentace Microsoftu
description: Aplikace logiky můžete snadno migrovat na schéma nejnovější verze. Postupujte podle těchto kroků.
services: logic-apps
documentationcenter: ''
author: MSFTMAN
manager: erikre
editor: ''
tags: connectors
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2016
ms.author: deonhe
ms.openlocfilehash: a5a73a9f124e5339b61dbc49021444a208a471f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "22707120"
---
# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>Migrace aplikací logiky na schéma verze 2015-08-01-preview
Pokud chcete stávající aplikaci logiky migrovat na nové schéma, postupujte následovně:  

1. Otevřete aplikaci logiky na portálu Azure  
2. Klikněte na tlačítko Aktualizovat schéma:
   
   ![Ikona rozhraní API][step1]   
   Na stránce Aktualizovat schéma uvidíte a získáte odkaz na dokument obsahující informace o vylepšeních v novém schématu: ![ikona rozhraní API][step2]

> [!NOTE]
> Když vyberete položku **Update Schema** (Aktualizovat schéma), můžeme automaticky spustit kroky migrace a poskytnout vám výstupní kód. S jeho pomocí můžete aktualizovat svou definici. Dbejte ale na to, abyste se drželi osvědčených kódovacích postupů, které jsou uvedeny níže v části **Osvědčené postupy**.
> 
> 

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>Osvědčené postupy při migraci vašich aplikací logiky na nejnovější verzi schématu:
* Migrovaný skript zkopírujte do nové aplikace logiky. Tu původní ale nepředělávejte, dokud migrovanou aplikaci řádně neotestujete a neujistíte se, že správně funguje.
* Svou aplikaci logiky si otestujte ještě **předtím**, než ji předáte do produkce.
* Po dokončení migrace spusťte aktualizaci Logic Apps, abyste všude, kde je to možné, využili [spravované rozhraní API](apis-list.md). Například můžete začít používat Dropbox v2 všude, kde jste používali DropBox v1.

## <a name="whats-next"></a>Kam dál
* [Naučte se své aplikace logiky přenášet ručně](../logic-apps/logic-apps-schema-2015-08-01.md)

<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png






