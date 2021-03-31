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
ms.openlocfilehash: b20c97bf919073b01f564b59b3a55eccb7614005
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89042957"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Data je možné zapsat do úložiště objektů BLOB ve formátu [Apache Avro](https://avro.apache.org/) , který je výchozí nebo JSON (Preview). 
>    
> Možnost kódovat formát JSON je ve verzi Preview ve všech oblastech, ve kterých je dostupná IoT Hub, s výjimkou Východní USA, Západní USA a Západní Evropa. Formát kódování lze nastavit pouze v době, kdy je nakonfigurován koncový bod služby Blob Storage. Formát nelze změnit pro koncový bod, který již byl nastaven. Při použití kódování JSON musíte nastavit contentType na JSON a contentEncoding na UTF-8 ve vlastnostech systému zpráv. 
>
> Podrobnější informace o použití koncového bodu služby Blob Storage najdete v tématu [pokyny k směrování do úložiště](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).
>