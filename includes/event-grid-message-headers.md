---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c37e2c28eb6aee9edf4bdd97066ce5f15e7447c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005622"
---
## <a name="message-headers"></a>Záhlaví zpráv
Jedná se o vlastnosti, které obdržíte v hlavičkách zprávy:

| Název vlastnosti | Description |
| ------------- | ----------- | 
| AEG-Subscription-Name | Název odběru události |
| AEG – počet doručení | Počet pokusů o provedení události. |
| AEG-typ události | <p>Typ události</p><p>Může to být jedna z následujících hodnot:</p><ul><li>SubscriptionValidation</li><li>Notification (Oznámení)</li><li>SubscriptionDeletion</li></ul> | 
| AEG – metadata – verze | <p>Verze události v metadatech<p> Pro **Event Grid schéma událostí** Tato vlastnost představuje verzi metadat a pro **schéma cloudové události** představuje **verzi specifikace**. </p>|
| AEG-data-verze | <p>Verze dat události</p><p>Pro **Event Grid schéma událostí** Tato vlastnost představuje verzi dat a pro **schéma cloudových událostí** se nepoužije.</p> |
| AEG-Output-Event-ID | ID události Event Grid |


