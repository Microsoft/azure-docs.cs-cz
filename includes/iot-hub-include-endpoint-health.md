---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a0d1de622eefad4ae5e55a427f8b0b1bf4360c0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84792081"
---
Ke zjištění stavu koncových bodů můžete použít REST API [získat stav koncových](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) bodů. Doporučujeme použít [IoT Hub metriky](../articles/iot-hub/iot-hub-metrics.md) týkající se latence zprávy směrování k identifikaci a ladění chyb, když je stav koncového bodu v pořádku nebo není v pořádku, protože při použití koncového bodu v jednom z těchto stavů očekáváme, že latence bude vyšší.


|Stav|Description|
|---|---|
|v pořádku|Koncový bod přijímá zprávy podle očekávání.|
|není v pořádku|Koncový bod nepřijímá zprávy a IoT Hub se pokouší odeslat zprávy do tohoto koncového bodu.|
|Neznámý|IoT Hub se nepokusilo doručovat zprávy do tohoto koncového bodu.|
|degradovaný|Koncový bod přijímá zprávy pomaleji, než se čekalo nebo obnovuje stav není v pořádku.|
|Zpráva|IoT Hub už nedoručuje zprávy do tohoto koncového bodu. Opakované pokusy o odeslání zpráv do tohoto koncového bodu se nezdařilo.|
