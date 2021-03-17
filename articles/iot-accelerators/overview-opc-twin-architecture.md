---
title: OPCá architektura – Azure | Microsoft Docs
description: Tento článek poskytuje přehled o architektuře s dvojitou OPCí. Popisuje zjišťování, aktivaci, procházení a monitorování serveru.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 602d2f77564709294d28b797ff3f03f12b3b32d8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91533379"
---
# <a name="opc-twin-architecture"></a>OPCá architektura

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

Následující diagramy znázorňují OPCou architekturu.

## <a name="discover-and-activate"></a>Vyhledat a aktivovat

1. Operátor povolí kontrolu sítě v modulu nebo vytvoří jednorázové zjišťování pomocí adresy URL zjišťování. Zjištěné koncové body a informace o aplikaci se odesílají prostřednictvím telemetrie do agenta připojování pro zpracování.  Agent registrace zařízení OPC UA zpracovává události zjišťování serveru OPC UA odeslané modulem OPC s dvojitou IoT Edge v režimu zjišťování nebo prohledávání. Události zjišťování mají za následek registraci a aktualizace aplikací v registru zařízení OPC UA.

   ![Diagram, který zobrazuje OPCou architekturu s modulem s dvojitým IoT Edgem OPC v režimu zjišťování nebo prohledávání.](media/overview-opc-twin-architecture/opc-twin1.png)

1. Operátor zkontroluje certifikát zjištěného koncového bodu a aktivuje pro přístup nevlákenný koncový bod registrovaného koncového bodu. 

   ![Diagram, který zobrazuje OPCou architekturu s IoT Edge "dopředná identita".](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Procházet a monitorovat

1. Po aktivaci může operátor použít dovolanou REST API služby k procházení nebo kontrole modelu informací o serveru, proměnných objektu pro čtení a zápis a metod volání.  Uživatel používá zjednodušené rozhraní OPC UA API, které je plně v HTTP a JSON.

   ![Diagram, který zobrazuje nastavení OPC s dvojitou architekturou pro procházení a kontrolu nad modelem informací o serveru.](media/overview-opc-twin-architecture/opc-twin3.png)

1. Rozhraní REST služby s dvojitou službou se dá použít také k vytvoření monitorovaných položek a odběrů v OPC vydavateli. Vydavatel OPC umožňuje, aby se telemetrie odesílala ze systémů serveru OPC UA do IoT Hub. Další informace o vydavateli OPC najdete v tématu [co je OPC Publisher](overview-opc-publisher.md).

   ![Jak funguje zdvojení OPC](media/overview-opc-twin-architecture/opc-twin4.png)
