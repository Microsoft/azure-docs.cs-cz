---
title: Principy dvojčat Azure Security Center pro modul IoT zabezpečení ve verzi Preview | Dokumentace Microsoftu
description: Další informace o konceptu dvojčaty modulů zabezpečení a jak se používají ve službě Azure Security Center pro IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 56bc93973bd2c080b13b97f1344cc165f742e757
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758407"
---
# <a name="security-module"></a>Modul zabezpečení

> [!IMPORTANT]
> Azure Security Center pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek vysvětluje, jak Azure Security Center (ASC) for IoT používá dvojčata zařízení a moduly. 

## <a name="device-twins"></a>Dvojčata zařízení

Dvojčata zařízení pro řešení IoT v Azure, hrát klíčovou roli při správě zařízení a automatizace procesů.  

ASC pro IoT nabízí plnou integraci s vaší stávající IoT platformy pro správu zařízení, můžete ke správě stavu zabezpečení zařízení a ujistěte se také použít existující řízení funkcí zařízení. Integrace se dosahuje využitím služby IoT Hub dvojčete mechanismus.  

Další informace o konceptu [dvojčata zařízení](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) ve službě Azure IoT Hub. 

## <a name="security-module-twins"></a>Zabezpečení dvojčaty modulů

ASC pro IoT udržuje dvojčete modulu zabezpečení pro každé zařízení ve službě.
Dvojče modulu zabezpečení obsahuje všechny informace týkající se zabezpečení pro každé zařízení specifické zařízení ve vašem řešení.
Vlastnosti zabezpečení zařízení jsou zachována ve dvojčete modulu vyhrazené zabezpečení pro bezpečnější komunikaci a povolení aktualizací a údržby, které vyžadují méně prostředků.  

V tématu [dvojče zařízení zabezpečení vytvořit](quickstart-create-security-twin.md) a [konfigurace zabezpečení agentů](how-to-agent-configuration.md) se naučíte vytvářet, upravovat a konfigurovat dvojčeti. Zobrazit [Principy dvojčat modulů](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) získat další informace o konceptu dvojčaty modulů ve službě IoT Hub. 
 

## <a name="see-also"></a>Další informace najdete v tématech
- [ASC pro IoT ve verzi Preview](overview.md)
- [Nasazení agentů zabezpečení](how-to-deploy-agent.md)
- [Metody ověřování zabezpečení agenta](concept-security-agent-authentication-methods.md)