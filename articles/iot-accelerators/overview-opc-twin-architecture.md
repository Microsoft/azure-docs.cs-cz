---
title: Architektura OPC Dvojčete – Azure | Dokumentace Microsoftu
description: Architektura Dvojčete OPC
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 002e2e06201688638df98e16c45282187f593a3a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444302"
---
# <a name="opc-twin-architecture"></a>Architektura Dvojčete OPC

Následující obrázky znázorňují architektura Dvojčete OPC.

## <a name="discover-and-activate"></a>Zjišťování a aktivovat

1. Operátor, který umožňuje v modulu pro prohledávání sítě nebo provede jednorázovou zjišťování pomocí adresy URL zjišťování. Zjištěné koncové body a informací o aplikaci odesílají přes telemetrie k připojení agenta ke zpracování.  Agent připojení zařízení OPC UA zpracuje události zjišťování serveru OPC UA odesílaných modulu IoT Edge Dvojčete OPC v režimu kontroly nebo zjišťování. Výsledek události zjišťování v registrace aplikace a aktualizace v registru zařízení OPC UA.

   ![Jak funguje Dvojčete OPC](media/overview-opc-twin-architecture/opc-twin1.png)

1. Operátor, který zkontroluje certifikát nalezený koncový bod a aktivuje dvojčeti registrované koncový bod pro přístup. 

   ![Jak funguje Dvojčete OPC](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Procházení a monitorování

1. Po aktivaci, operátor, který můžete použít rozhraní REST API služby Dvojčete procházet nebo kontrolovat informační model serveru, čtení a zápis objektové proměnné a volání metod.  Uživatel používá zjednodušené OPC UA rozhraní API plně vyjádřené v protokolu HTTP a JSON.

   ![Jak funguje Dvojčete OPC](media/overview-opc-twin-architecture/opc-twin3.png)

1. Rozhraní REST služby dvojčete lze také vytvořit v vydavatele OPC monitorované položky a předplatných. Vydavatel OPC umožňuje telemetrie posílaly z serverovými systémy OPC UA do služby IoT Hub. Další informace o vydavateli OPC, naleznete v tématu [co je vydavatel OPC](overview-opc-publisher.md).

   ![Jak funguje Dvojčete OPC](media/overview-opc-twin-architecture/opc-twin4.png)
