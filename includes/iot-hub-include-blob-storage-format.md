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
ms.openlocfilehash: c779147e464a592d45da8a9a2d8e812320dc23e8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162587"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Data se dají zapisovat do úložiště objektů blob v buď [Apache Avro](https://avro.apache.org/) formátu, což je výchozí nebo formátu JSON (preview). 
>    
> Možnost kódování formátu JSON je ve verzi preview ve všech oblastech, ve které je dostupné služby IoT Hub, s výjimkou východní USA, západní USA a západní Evropa. Formát kódování lze nastavit pouze v době, kdy je nakonfigurovaný koncový bod služby blob storage. Formát nelze změnit pro koncový bod, který je již nastaven. Při použití JSON kódování, je nutné nastavit hlavičku contentType do formátu JSON a contentEncoding na UTF-8 v systémových vlastnostech zprávy. 
>
> Podrobné informace o používání koncový bod služby blob storage, najdete [doprovodné materiály k směrování do úložiště objektů blob](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-blob-storage).
>