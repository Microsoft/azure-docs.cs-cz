---
title: Připojit k disku Google
description: Automatizujte pracovní postupy, které vytvářejí a spravují soubory pro disk Google pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 155a579438747ca7de67eba6449b8a0191e86e2c
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666835"
---
# <a name="create-and-manage-files-for-google-drive-by-using-azure-logic-apps"></a>Vytváření a Správa souborů pro disk Google pomocí Azure Logic Apps

Připojte se k disku Google, abyste mohli vytvářet soubory, získávat řádky a další. S diskem Google můžete: 

* Sestavte svůj obchodní tok na základě dat, která získáte z hledání. 
* Použijte akce pro hledání obrázků, hledání zpráv a další. Tyto akce obdrží odpověď a následně zpřístupní výstup pro jiné akce. Můžete například vyhledat video a pak pomocí služby Twitter odeslat toto video do kanálu Twitteru.

Můžete začít vytvořením aplikace logiky hned teď v tématu [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-connection-to-google-drive"></a>Vytvoření připojení k disku Google

Když přidáte tento konektor do vašich aplikací logiky, musíte autorizovat aplikace logiky pro připojení k vašemu disku Google.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]

Po vytvoření připojení zadáte vlastnosti disku Google, jako je třeba cesta ke složce nebo název souboru. 

## <a name="connector-specific-details"></a>Podrobnosti specifické pro spojnici

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu Swagger konektoru, najdete na [referenční stránce konektoru](/connectors/googledrive/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](apis-list.md)
