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
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612032"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Data je možné zapsat do úložiště objektů BLOB ve formátu [Apache Avro](https://avro.apache.org/) , který je výchozí nebo JSON (Preview). 
>    
> Možnost kódovat formát JSON je ve verzi Preview ve všech oblastech, ve kterých je dostupná IoT Hub, s výjimkou Východní USA, Západní USA a Západní Evropa. Formát kódování lze nastavit pouze v době, kdy je nakonfigurován koncový bod služby Blob Storage. Formát nelze změnit pro koncový bod, který již byl nastaven. Při použití kódování JSON musíte nastavit contentType na JSON a contentEncoding na UTF-8 ve vlastnostech systému zpráv. 
>
> Podrobnější informace o použití koncového bodu služby Blob Storage najdete v tématu [pokyny k směrování do úložiště](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage).
>