---
title: 'Rychlý Start: rozpoznávání řeči uložené ve službě BLOB Storage – Speech Service'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: ef61139056ad194d89e2a67cb4ac1b1414a3380b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828868"
---
V tomto rychlém startu použijete [dávku přepisu REST API](../../../batch-transcription.md) k rozpoznávání řeči uloženého v [objektu BLOB SAS](https://aka.ms/ignite2019/speech/placeholder). Po splnění několika předpokladů rozpozná rozpoznávání řeči pomocí REST API jenom několik kroků:
> [!div class="checklist"]
> * Odešlete požadavek JSON službě rozpoznávání řeči, aby začal zdlouhavého přepisování řeč.
> * Ověřte stav přepisu.
> * Po dokončení Stáhněte výsledky přepisu.