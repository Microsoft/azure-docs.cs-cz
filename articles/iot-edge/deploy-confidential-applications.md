---
title: Důvěrné aplikace jako Azure IoT Edge moduly
description: Pomocí Open enklávy SDK a rozhraní API zapište důvěrné aplikace a nasaďte je jako IoT Edge moduly pro důvěrný výpočetní výkon.
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kgremban
ms.openlocfilehash: f9dff1b4c6b2489edd3cd685e3546618961d9757
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487713"
---
# <a name="confidential-computing-at-the-edge"></a>Důvěrné výpočetní prostředí na hraničních zařízeních

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge podporuje důvěrné aplikace, které běží v zabezpečeném enclaves na zařízení. Šifrování poskytuje zabezpečení pro data během přenosu nebo v klidovém režimu, ale enclaves poskytuje zabezpečení pro data a zatížení, které se používají. IoT Edge podporuje Open enklávy jako standard pro vývoj důvěrných aplikací.

Zabezpečení bylo vždycky důležité soustředit se na Internet věcí (IoT), protože často jsou zařízení IoT v celém světě často mimo zabezpečená v rámci soukromého zařízení. Tato expozice znamená, že zařízení hrozí v ohrožení proti falšování a padělání, protože jsou fyzicky přístupná pro chybné aktéry. IoT Edge zařízení ještě více potřebují pro důvěryhodnost a integritu, protože umožňují běžet citlivá zatížení na hraničních zařízeních. Na rozdíl od běžných senzorů a poháněcí zařízení můžou tato inteligentní hraniční zařízení vystavovat citlivé úlohy, které se dřív spouštěly jenom v chráněném cloudu nebo v místních prostředích.

[IoT Edge Security Manager](iot-edge-security-manager.md) řeší jednu část výzvy důvěrného zpracování. Správce zabezpečení používá modul hardwarového zabezpečení (HSM) k ochraně zatížení identity a probíhajících procesů IoT Edge zařízení.

Dalším aspektem důvěrného výpočetního prostředí je chránit data, která se používají na hraničních zařízeních. *Důvěryhodné prostředí pro spuštění* (TEE) je zabezpečené a izolované prostředí v procesoru, které se někdy označuje jako *enklávy*. *Důvěrná aplikace* je aplikace, která běží v enklávy. Z důvodu povahy enclaves jsou důvěrné aplikace chráněny před jinými aplikacemi spuštěnými v hlavním procesoru nebo v TEE.

## <a name="confidential-applications-on-iot-edge"></a>Důvěrné aplikace v IoT Edge

Důvěrné aplikace jsou šifrované při přenosu a v klidovém stavu a jsou dešifrovány, aby je bylo možné spouštět v prostředí důvěryhodných spuštění. Tento standard má hodnotu true pro důvěrné aplikace nasazené jako IoT Edge moduly.

Vývojář vytvoří tajnou aplikaci a zabalí ji jako modul IoT Edge. Aplikace je před odesláním do registru kontejneru zašifrována. Aplikace zůstane v rámci procesu nasazení IoT Edge šifrovaná, dokud se modul nespustí v IoT Edgem zařízení. Jakmile se důvěrné aplikace nachází v TEE zařízení, dešifruje se a může se začít spouštět.

![Diagram – důvěrné aplikace se šifrují v IoT Edge modulech, dokud nejsou nasazené do zabezpečeného enklávyu.](./media/deploy-confidential-applications/confidential-applications-encrypted.png)

Důvěrné aplikace v IoT Edge jsou logické rozšíření [důvěrné výpočetní služby Azure](../confidential-computing/overview.md). Úlohy, které běží v rámci zabezpečeného enclavesu v cloudu, je možné nasadit taky tak, aby se spouštěly v rámci zabezpečené enclaves na hraničních zařízeních.

## <a name="open-enclave"></a>Otevřít enklávy

[Open ENKLÁVY SDK](https://openenclave.io/sdk/) je open source projekt, který vývojářům pomáhá vytvářet důvěrné aplikace pro více platforem a prostředí. Otevřená sada enklávy SDK funguje v rámci důvěryhodného prováděcího prostředí zařízení, zatímco otevřené rozhraní enklávy API funguje jako rozhraní mezi TEE a prostředím pro zpracování bez TEE.

Open enklávy podporuje několik hardwarových platforem. IoT Edge podpora pro enclaves v současnosti vyžaduje otevřený TEE operační systém (OP – TEE OS). Další informace najdete v tématu [Open ENKLÁVY SDK for op-Tee OS](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md).

Open enklávy úložiště obsahuje také ukázky, které vývojářům pomůžou začít. Další informace získáte výběrem některého z úvodních článků:

* [Sestavování otevřených ukázek sady enklávy SDK v systému Linux](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [Sestavování otevřených ukázek sady enklávy SDK ve Windows](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>Hardware

V současné době je [TrustBox podle Scalys](https://scalys.com/trustbox-industrial/) jediným zařízením podporovaným smlouvami o poskytování služeb od výrobců pro nasazování důvěrných aplikací jako IoT Edgech modulů. TrustBox je postavená na TrustBox hraničních zařízeních a TrustBoxch EdgeXL zařízení, která jsou předem načtená pomocí sady Open enklávy SDK a Azure IoT Edge.

Další informace najdete v tématu [Začínáme s Open enklávy pro Scalys TrustBox](https://aka.ms/scalys-trustbox-edge-get-started).

## <a name="develop-and-deploy"></a>Vývoj a nasazení

Až budete připraveni vyvíjet a nasazovat důvěrné aplikace, může vám enklávy rozšíření [Microsoft Open](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) pro Visual Studio Code. K vývoji modulů pro TrustBox můžete použít systém Linux nebo Windows jako vývojový počítač.

## <a name="next-steps"></a>Další kroky

Naučte se, jak začít vyvíjet důvěrné aplikace jako IoT Edge moduly s [rozšířením Open enklávy pro Visual Studio Code](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)
