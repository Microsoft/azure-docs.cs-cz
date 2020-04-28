---
title: Připojení k Office 365 video
description: Automatizace úloh a pracovních postupů, které spravují videa v Office 365 pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: 8ac6b7b411e7f42dd076c5b16e7b500a819c617f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75665781"
---
# <a name="manage-videos-in-office365-video-by-using-azure-logic-apps"></a>Správa videí v Office 365 videu pomocí Azure Logic Apps

Připojte se k videu Office 365 a získejte informace o videu Office 365, získáte seznam videí a další. Video s Office 365 vám umožní:

* Sestavte svůj obchodní tok na základě dat, která získáte v Office 365 video. 

* Použijte akce, které zkontrolují stav portálu video Portal, získejte seznam všech videí v kanálu a další. Tyto akce obdrží odpověď a následně zpřístupní výstup pro jiné akce. 

Pomocí konektoru Vyhledávání Bingu můžete například vyhledat videa pro Office 365 a potom pomocí konektoru Office 365 Video Connector získat informace o tomto videu. Pokud video splňuje vaše požadavky, můžete toto video publikovat na Facebooku.

Můžete začít vytvořením aplikace logiky hned teď v tématu [Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office365-video"></a>Připojení k Office 365 videu

Když přidáte tento konektor do aplikace logiky, musíte se přihlásit ke svému účtu videa Office 365 a nechat aplikace logiky, aby se připojili k vašemu účtu.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Po vytvoření připojení zadáte vlastnosti videa Office 365, jako je název tenanta nebo ID kanálu. 

## <a name="connector-specific-details"></a>Podrobnosti specifické pro spojnici

Zobrazit všechny triggery a akce definované v Swagger a také zobrazit omezení v [podrobnostech konektoru](/connectors/office365videoconnector/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)