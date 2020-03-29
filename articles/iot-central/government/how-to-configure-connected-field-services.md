---
title: Propojení aplikace Azure IoT Central se službami Dynamics 365 Field Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit komplexní řešení pomocí služby Azure IoT Central a Dynamics 365 Field Service
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7c708268eaeade1cfb625c73ff8758a1941ff5b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157445"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Vytváření komplexního řešení pomocí služby Azure IoT Central a Dynamics 365 Field Service 



Jako tvůrce můžete povolit integraci vaší aplikace Azure IoT Central do jiných podnikových systémů. 


Například v propojeném řešení nakládání s odpady můžete optimalizovat dispečink sběrných vozíků pro sběr odpadků. Optimalizaci lze provést na základě dat snímačů IoT z připojených odpadkových košů. V [aplikaci pro nakládání s odpady připojenou službou IoT Central](./tutorial-connected-waste-management.md) můžete nakonfigurovat pravidla a akce a nastavit je tak, aby se aktivovalo vytváření výstrah ve službě Dynamics Field Service. Tento scénář se provádí pomocí Microsoft Flow, který bude nakonfigurován přímo v IoT Central pro automatizaci pracovních postupů napříč aplikacemi a službami. Kromě toho na základě aktivit služeb v Field Service, informace lze odeslat zpět do Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Jak propojit aplikaci Azure IoT Central se službami Dynamics 365 Field Services 

Níže uvedené integrační procesy lze snadno implementovat na základě čistě konfiguračnízkušenosti:
* Azure IoT Central můžete odesílat informace o anomáliích zařízení do služby Connected Field Service (jako výstraha IoT) pro diagnostiku.
* Connected Field Service může vytvářet případy nebo pracovní příkazy aktivované z anomálií zařízení.
* Connected Field Service může naplánovat techniky pro kontrolu, aby se zabránilo prostojům.
* Řídicí panel zařízení Azure IoT Central lze aktualizovat o relevantní informace o službách a plánování.


## <a name="next-steps"></a>Další kroky
* Další informace o [šablonách centrálních vládních ioT](./overview-iot-central-government.md)
* Další informace o [IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* Další informace o [terénních službách Dynamics 365](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)
