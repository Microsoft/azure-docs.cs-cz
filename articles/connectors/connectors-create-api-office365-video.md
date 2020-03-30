---
title: Připojení k Office 365 Video
description: Automatizace úloh a pracovních postupů, které spravují videa v Office 365 Video pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: 8ac6b7b411e7f42dd076c5b16e7b500a819c617f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665781"
---
# <a name="manage-videos-in-office365-video-by-using-azure-logic-apps"></a>Správa videí v Office365 Video pomocí Azure Logic Apps

Připojením k Office 365 Video získáte informace o videu Office 365, seznam videí a další informace. S Office 365 Video můžete:

* Sestavte si svůj obchodní tok na základě dat, která získáte z Office 365 Video. 

* Použijte akce, které kontrolují stav videoportálu, získají seznam všech videí v kanálu a další. Tyto akce získat odpověď a potom zpřístupnit výstup pro jiné akce. 

Pomocí konektoru Vyhledávání Bingu můžete například vyhledat videa Office 365 a pak pomocí konektoru videa Office 365 získat informace o tomto videu. Pokud video splňuje vaše požadavky, můžete toto video zveřejnit na Facebooku.

Můžete začít vytvořením aplikace logiky teď, najdete v [tématu Vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office365-video"></a>Připojení k Office365 Video

Když přidáte tento konektor do aplikací logiky, musíte se přihlásit ke svému účtu Office 365 Video a povolit aplikacím logiky připojení k vašemu účtu.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Po vytvoření připojení zadáte vlastnosti videa Office 365, jako je název klienta nebo ID kanálu. 

## <a name="connector-specific-details"></a>Podrobnosti specifické pro konektor

Zobrazit všechny aktivační události a akce definované v naparování a také zobrazit všechny limity v [podrobnostech konektoru](/connectors/office365videoconnector/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)