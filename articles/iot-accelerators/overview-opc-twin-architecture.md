---
title: Architektura OPC Twin – Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled architektury OPC Twin. Popisuje zjišťování, aktivaci, procházení a monitorování serveru.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b8d4424c92ff24c36650e34a5d050bdc5f0f9091
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73819860"
---
# <a name="opc-twin-architecture"></a>Architektura OPC Twin

Následující diagramy znázorňují architekturu OPC Twin.

## <a name="discover-and-activate"></a>Objevte a aktivujte

1. Operátor povolí prohledávání sítě v modulu nebo provede jednorázové zjišťování pomocí adresy URL zjišťování. Zjištěné koncové body a informace o aplikaci jsou odesílány prostřednictvím telemetrie agentovi registrace ke zpracování.  Agent onboardingu zařízení OPC UA zpracovává události zjišťování serveru OPC UA odeslané modulem OPC Twin IoT Edge v režimu zjišťování nebo skenování. Výsledkem událostí zjišťování je registrace a aktualizace aplikace v registru zařízení OPC UA.

   ![Jak OPC Twin funguje](media/overview-opc-twin-architecture/opc-twin1.png)

1. Operátor zkontroluje certifikát zjištěného koncového bodu a aktivuje registrované dvojče koncového bodu pro přístup. 

   ![Jak OPC Twin funguje](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Procházení a sledování

1. Po aktivaci může operátor pomocí rozhraní REST API služby Twin procházet nebo kontrolovat informační model serveru, proměnné objektu pro čtení a zápis a metody volání.  Uživatel používá zjednodušené OPC UA API plně vyjádřené v HTTP a JSON.

   ![Jak OPC Twin funguje](media/overview-opc-twin-architecture/opc-twin3.png)

1. Rozhraní REST služby dvojčete lze také použít k vytvoření monitorovaných položek a odběrů v Vydavatel OPC. Vydavatel OPC umožňuje telemetrii odesílat ze serverových systémů OPC UA do služby IoT Hub. Další informace o vydavateli OPC naleznete v tématu [Co je Vydavatel OPC](overview-opc-publisher.md).

   ![Jak OPC Twin funguje](media/overview-opc-twin-architecture/opc-twin4.png)
