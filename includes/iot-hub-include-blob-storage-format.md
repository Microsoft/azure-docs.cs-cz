---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 27a254b664e2035cc66109f7a473a3e9ef4d730e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73612032"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Data lze zapsat do úložiště objektů blob ve formátu [Apache Avro,](https://avro.apache.org/) což je výchozí, nebo JSON (náhled). 
>    
> Možnost kódování formátu JSON je ve verzi Preview ve všech oblastech, ve kterých je k dispozici služba IoT Hub, s výjimkou východní CHV USA, Západní USA a Západní Evropa. Formát kódování lze nastavit pouze v době, kdy je nakonfigurován koncový bod úložiště objektů blob. Formát nelze změnit pro koncový bod, který již byl nastaven. Při použití kódování JSON, musíte nastavit contentType json a contentEncoding na UTF-8 ve vlastnostech systému zpráv. 
>
> Podrobnější informace o použití koncového bodu úložiště objektů blob najdete v [návodu k směrování do úložiště](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage).
>