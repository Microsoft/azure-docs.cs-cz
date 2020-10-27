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
ms.openlocfilehash: 36c05badb3b2292a29b8227c7f03b841474c97ad
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548228"
---
Ke zjištění stavu koncových bodů můžete použít REST API [získat stav koncových](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) bodů. Doporučujeme použít [metriky směrování IoT Hub](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics) týkající se latence zpráv směrování a identifikovat a ladit chyby, když je stav koncového bodu v pořádku nebo není v pořádku, protože očekáváme, že latence bude vyšší, pokud je koncový bod v jednom z těchto stavů. Další informace o použití metrik IoT Hub najdete v tématu [monitorování IoT Hub](../articles/iot-hub/monitor-iot-hub.md).

|Stav|Popis|
|---|---|
|v pořádku|Koncový bod přijímá zprávy podle očekávání.|
|není v pořádku|Koncový bod nepřijímá zprávy a IoT Hub se pokouší odeslat zprávy do tohoto koncového bodu.|
|Neznámý|IoT Hub se nepokusilo doručovat zprávy do tohoto koncového bodu.|
|degradovaný|Koncový bod přijímá zprávy pomaleji, než se čekalo nebo obnovuje stav není v pořádku.|
|Zpráva|IoT Hub už nedoručuje zprávy do tohoto koncového bodu. Opakované pokusy o odeslání zpráv do tohoto koncového bodu se nezdařilo.|
