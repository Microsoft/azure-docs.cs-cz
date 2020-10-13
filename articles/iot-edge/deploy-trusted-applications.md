---
title: Důvěryhodné aplikace jako Azure IoT Edge moduly
description: Pomocí Open enklávy SDK a rozhraní API můžete psát důvěryhodné aplikace a nasazovat je jako IoT Edge moduly pro důvěrný výpočetní výkon.
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: kgremban
ms.openlocfilehash: d81cc6c94c04c683362fd12cd6777c304a4b0a84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361972"
---
# <a name="confidential-computing-at-the-edge"></a>Důvěrné výpočetní prostředí na hraničních zařízeních

Azure IoT Edge podporuje důvěryhodné aplikace, které běží v zabezpečeném enclaves na zařízení. Šifrování poskytuje zabezpečení pro data během přenosu nebo v klidovém režimu, ale enclaves poskytuje zabezpečení pro data a zatížení, které se používají. IoT Edge podporuje Open enklávy jako standard pro vývoj důvěryhodných aplikací.

Zabezpečení bylo vždycky důležité soustředit se na Internet věcí (IoT), protože často jsou zařízení IoT v celém světě často mimo zabezpečená v rámci soukromého zařízení. Tato expozice znamená, že zařízení hrozí v ohrožení proti falšování a padělání, protože jsou fyzicky přístupná pro chybné aktéry. IoT Edge zařízení ještě více potřebují pro důvěryhodnost a integritu, protože umožňují běžet citlivá zatížení na hraničních zařízeních. Na rozdíl od běžných senzorů a poháněcí zařízení můžou tato inteligentní hraniční zařízení vystavovat citlivé úlohy, které se dřív spouštěly jenom v chráněném cloudu nebo v místních prostředích.

[IoT Edge Security Manager](iot-edge-security-manager.md) řeší jednu část výzvy důvěrného zpracování. Správce zabezpečení používá modul hardwarového zabezpečení (HSM) k ochraně zatížení identity a probíhajících procesů IoT Edge zařízení.

Dalším aspektem důvěrného výpočetního prostředí je chránit data, která se používají na hraničních zařízeních. *Důvěryhodné prostředí pro spuštění* (TEE) je zabezpečené a izolované prostředí v procesoru, které se někdy označuje jako *enklávy*. *Důvěryhodná aplikace* je aplikace, která běží v enklávy. Z důvodu povahy enclaves jsou důvěryhodné aplikace chráněny před jinými aplikacemi spuštěnými v hlavním procesoru nebo v TEE.

## <a name="trusted-applications-on-iot-edge"></a>Důvěryhodné aplikace v IoT Edge

Důvěryhodné aplikace jsou šifrovány při přenosu a v klidovém stavu a jsou dešifrovány pouze pro spuštění v prostředí důvěryhodných spuštění. Tento standard má hodnotu true pro důvěryhodné aplikace nasazené jako IoT Edge moduly.

Vývojář vytvoří důvěryhodnou aplikaci a zabalí ji jako modul IoT Edge. Aplikace je před odesláním do registru kontejneru zašifrována. Aplikace zůstane v rámci procesu nasazení IoT Edge šifrovaná, dokud se modul nespustí v IoT Edgem zařízení. Jakmile je důvěryhodná aplikace v TEE zařízení, bude dešifrována a může ji začít spouštět.

![Diagram – důvěryhodné aplikace se šifrují v IoT Edge modulech, dokud nejsou nasazené do zabezpečeného enklávyu.](./media/deploy-trusted-applications/trusted-applications-encrypted.png)

Důvěryhodné aplikace v IoT Edge jsou logické rozšíření [důvěrné výpočetní služby Azure](../confidential-computing/overview.md). Úlohy, které běží v rámci zabezpečeného enclavesu v cloudu, je možné nasadit taky tak, aby se spouštěly v rámci zabezpečené enclaves na hraničních zařízeních.

## <a name="open-enclave"></a>Otevřít enklávy

[Open ENKLÁVY SDK](https://openenclave.io/sdk/) je open source projekt, který vývojářům pomáhá vytvářet důvěryhodné aplikace pro více platforem a prostředí. Otevřená sada enklávy SDK funguje v rámci důvěryhodného prováděcího prostředí zařízení, zatímco otevřené rozhraní enklávy API funguje jako rozhraní mezi TEE a prostředím pro zpracování bez TEE.

Open enklávy podporuje několik hardwarových platforem. IoT Edge podpora pro enclaves v současnosti vyžaduje otevřený TEE operační systém (OP – TEE OS). Další informace najdete v tématu [Open ENKLÁVY SDK for op-Tee OS](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md).

Open enklávy úložiště obsahuje také ukázky, které vývojářům pomůžou začít. Další informace získáte výběrem některého z úvodních článků:

* [Sestavování otevřených ukázek sady enklávy SDK v systému Linux](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [Sestavování otevřených ukázek sady enklávy SDK ve Windows](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>Hardware

V současné době je [TrustBox podle Scalys](https://scalys.com/trustbox-industrial/) jediným zařízením podporovaným smlouvami o poskytování služeb od výrobců pro nasazení důvěryhodných aplikací jako IoT Edgech modulů. TrustBox je postavená na TrustBox hraničních zařízeních a TrustBoxch EdgeXL zařízení, která jsou předem načtená pomocí sady Open enklávy SDK a Azure IoT Edge.

Další informace najdete v tématu [Začínáme s Open enklávy pro Scalys TrustBox](https://aka.ms/scalys-trustbox-edge-get-started).

## <a name="develop-and-deploy"></a>Vývoj a nasazení

Až budete připraveni vyvíjet a nasazovat důvěryhodnou aplikaci, může vám Visual Studio Code rozšíření pro [Microsoft Open enklávy](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) pro. K vývoji modulů pro TrustBox můžete použít systém Linux nebo Windows jako vývojový počítač.

## <a name="next-steps"></a>Další kroky

Naučte se, jak začít vyvíjet důvěryhodné aplikace jako IoT Edge moduly s [rozšířením Open enklávy pro Visual Studio Code](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)
