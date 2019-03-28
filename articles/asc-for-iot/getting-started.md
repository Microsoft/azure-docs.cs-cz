---
title: Začněte používat Azure Security Center (ASC) pro verzi Preview IoT | Dokumentace Microsoftu
description: Začněte porozumět základním pracovním postupem ASC funkce IoT a služby.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: a78eeda814b21d489eb957d44202b17c61d836d5
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541884"
---
# <a name="get-started-with-azure-security-center-asc-for-iot"></a>Začínáme s Azure Security Center (ASC) pro IoT 

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek obsahuje vysvětlení různých stavební bloky ASC pro služby Azure IoT a vysvětluje, jak začít pracovat s [povolení služby](quickstart-onboard-iot-hub.md). 

ASC pro IoT můžete bez problémů integrovat do služby IoT Hub poskytnout zabezpečení analýzu konfigurace centra IoT, identitu zařízení a Centrum – zařízení komunikačních schémat.
Rozšířené možnosti zabezpečení poskytuje ASC pro IoT založené na agentovi shromažďování dat zabezpečení z zařízení IoT.

## <a name="asc-for-iot-seamless-iot-hub-integration"></a>ASC pro bezproblémovou integraci služby IoT Hub IoT

Při pokusu o ochraně jednotlivých zařízení IoT, schopnost shromažďovat data přímo ze zařízení nebo z jejich sítě je povinný. Pro podporu tohoto úsilí, nabízí ASC pro IoT arsenál zabezpečení s nízkými nároky agentů, aby zajišťoval sledování zařízení a posílení zabezpečení.

V ASC pro IoT ve verzi preview, referenční architektura pro systémy Linux a Windows security agenty v C# a jazyka C jsou k dispozici.
Agenti zpracování shromažďování nezpracovaných událostí z operačního systému zařízení agregace událostí a snižuje tak náklady a konfiguraci prostřednictvím dvojče modulu.
Zabezpečení zprávy odesílány prostřednictvím služby IoT Hub do služby IoT analytics ASC.

## <a name="asc-for-iot-basics"></a>ASC pro základy IoT

Zvolte scénář pracovního postupu, která nejlépe vyhovuje vašim požadavkům prostředí a zařízení IoT:

### <a name="get-started-with-asc-for-iot-seamless-iot-hub-integration"></a>Začínáme s ASC pro bezproblémovou integraci služby IoT Hub IoT 

>[!Note]
>Tento pracovní postup můžete použít službu bez použití ASC pro agenty zabezpečení IoT. 

Povolit monitorování zařízení správy identit, zařízení a cloud a cloud pro zařízení, schémata komunikace, použijte následující základní pracovní postup pro testování a spustit službu: 

1. [Povolení ASC pro službu IoT ve službě IoT Hub](quickstart-onboard-iot-hub.md)
1. Pokud nemá žádná zaregistrovaná zařízení služby IoT Hub [zaregistrovat nová zařízení](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Vytvoření modulu zabezpečení ascforiot](quickstart-create-security-twin.md) pro vaše zařízení. 
1. Definování běžné chování zařízení a systému prostřednictvím [vlastní upozornění](quickstart-create-custom-alerts.md). 
1. Proveďte systémové testování ověření stavu zařízení a služeb. 
1. Prozkoumejte [výstrahy](concept-security-alerts.md), [doporučení](concept-recommendations.md), a [podrobné informace o použití Log Analytics](how-to-security-data-access.md) pomocí služby IoT Hub. 


### <a name="get-started-with-asc-for-iot-security-agents"></a>Začínáme s ASC pro agenty zabezpečení IoT

Využijte ASC k IoT rozšířené možnosti zabezpečení, jako je například monitorování vzdáleného připojení, aktivní aplikace, události přihlášení a osvědčenými postupy konfigurace operačního systému pomocí následující základní pracovní postup pro testování a povolení služby: 

1. [Povolení ASC pro službu IoT ke službě IoT Hub](quickstart-onboard-iot-hub.md)
1. Pokud nemá žádná zaregistrovaná zařízení služby IoT Hub [zaregistrovat nová zařízení](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Vytvoření modulu zabezpečení azureiotsecurity](quickstart-create-security-twin.md) pro vaše zařízení.
1. Instalace agenta na Azure simulované zařízení se místo instalace na zařízení s skutečné [otočit nahoru nový Azure virtuální počítač (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) v zóně dostupná. 
1. [Nasazení ASC pro agenta zabezpečení IoT](tutorial-deploy-linux-cs.md) na zařízení IoT nebo nového virtuálního počítače.
1. Postupujte podle pokynů pro [trigger_events](https://aka.ms/iot-security-github-trigger-events) ke spuštění simulace neškodné útoku.
1. Ověřte ASC pro výstrahy IoT v reakci na simulované útoku v předchozím kroku. 
    - Proces ověření pět minut po spuštění skriptu.
1. Prozkoumejte [výstrahy](concept-security-alerts.md), [doporučení](concept-recommendations.md), a [podrobné informace o použití Log Analytics](how-to-security-data-access.md) pomocí služby IoT Hub. 

## <a name="next-steps"></a>Další postup

- Povolit [ASC pro IoT](quickstart-onboard-iot-hub.md)
- Konfigurace vašeho [řešení](quickstart-configure-your-solution.md)
- [Vytvoření modulů zabezpečení](quickstart-create-security-twin.md)
- Konfigurace [vlastní upozornění](quickstart-create-custom-alerts.md)
- [Nasazení agenta zabezpečení](select-deploy-agent.md)
