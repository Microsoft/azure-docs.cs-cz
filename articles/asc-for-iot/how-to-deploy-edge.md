---
title: Nasazení ASC pro modul IoT Edge | Dokumentace Microsoftu
description: Další informace o nasazení ASC pro agenta zabezpečení IoT na hraničních zařízeních IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 87094b265a0c30c0349d64e4b956224525c04f74
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580490"
---
# <a name="deploy-security-module-on-your-iot-edge-device"></a>Nasazení modulu zabezpečení na vašem zařízení IoT Edge

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC pro IoT **azureiotsecurity** modul poskytuje komplexní řešení zabezpečení pro vaše zařízení IoT Edge.
Modul zabezpečení shromažďuje, agreguje a analyzuje data nezpracovaná zabezpečení z operačního systému a kontejneru systému do užitečná doporučení zabezpečení a upozornění.
Další informace najdete v tématu [zabezpečení modul IoT Edge](security-edge-architecture.md).

V této příručce se dozvíte, jak nasadit modul zabezpečení na vašem zařízení IoT Edge.

## <a name="deploy-security-module"></a>Nasazení modulu zabezpečení

Použijte následující postup ASC pro modul IoT zabezpečení nasazení IoT Edge.

### <a name="prerequisites"></a>Požadavky

1. Ujistěte se, že vaše zařízení je [zaregistrované jako zařízení IoT Edge](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-register-device-portal).

1. Vyžaduje ASC pro modul IoT Edge [démona AuditD framework](https://linux.die.net/man/8/auditd) nainstalovat na hraniční zařízení.

   Nainstalujte rozhraní framework spuštěním následujícího příkazu na hraniční zařízení:
   
   `apt-get install auditd audispd-plugins`

### <a name="deployment-using-azure-portal"></a>Nasazení pomocí webu Azure portal

1. Otevřít **Marketplace** na webu Azure portal.

1. Vyhledejte **zabezpečení azure iot** a klikněte na **zabezpečení Azure IoT**.

   ![](media/howto/edge_onboarding_7.png)

1. Klikněte na možnost **Vytvořit**.

1. Zvolte vaši **předplatné**, **služby IoT Hub** a **název zařízení IoT Edge**, pak klikněte na tlačítko **vytvořit**.

1. Klikněte na tlačítko **Další** dvakrát na **nasazení zkontrolujte**.

1. Ujistěte se, že **edgeHub.settings.createOptions** nakonfigurován takto:

   `"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"`

1. Klikněte na tlačítko **odeslat** k dokončení nasazení.


## <a name="next-steps"></a>Další postup

Další informace o možnostech konfigurace, pokračujte v Průvodci s postupy pro konfiguraci modulu. 
> [!div class="nextstepaction"]
> [Konfigurace modulu postup Průvodce](./how-to-agent-configuration.md)