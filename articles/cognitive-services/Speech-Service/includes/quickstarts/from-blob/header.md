---
title: 'Rychlý Start: rozpoznávání řeči uložené ve službě BLOB Storage – Speech Service'
titleSuffix: Azure Cognitive Services
description: Bude doplněno
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 195c4c94f7ab00a8740c9dd14aad0d0fd0daa9b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503973"
---
V tomto rychlém startu použijete [dávku přepisu REST API](../../../batch-transcription.md) k rozpoznávání řeči uloženého v [objektu BLOB SAS](https://aka.ms/ignite2019/speech/placeholder). Po splnění několika předpokladů rozpozná rozpoznávání řeči pomocí REST API jenom několik kroků:
> [!div class="checklist"]
> * Odešlete požadavek JSON službě rozpoznávání řeči, aby začal zdlouhavého přepisování řeč.
> * Ověřte stav přepisu.
> * Po dokončení Stáhněte výsledky přepisu.