---
title: Architektura OPC Dvojčete – Azure | Dokumentace Microsoftu
description: Architektura Dvojčete OPC
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6ce9394f3d454bda5ead51f2c77a47db137a5136
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606186"
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
