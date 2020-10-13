---
title: Nasadit modul zabezpečení IoT Edge
description: Přečtěte si, jak nasadit program Defender pro agenta zabezpečení IoT na IoT Edge.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 06836673f1a567f64217d49cf8e0ca82e132111f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90935586"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Nasazení modulu zabezpečení na zařízení IoT Edge

Modul **Defender for IoT** poskytuje komplexní řešení zabezpečení pro vaše IoT Edge zařízení.
Modul zabezpečení shromažďuje, agreguje a analyzuje nezpracovaná data zabezpečení z operačního systému a systému kontejnerů do užitečných doporučení zabezpečení a výstrah.
Další informace najdete v tématu [modul zabezpečení pro IoT Edge](security-edge-architecture.md).

V tomto článku se dozvíte, jak nasadit modul zabezpečení na zařízení IoT Edge.

## <a name="deploy-security-module"></a>Nasadit modul zabezpečení

Pomocí následujících kroků nasaďte modul zabezpečení Defender for IoT pro IoT Edge.

### <a name="prerequisites"></a>Požadavky

1. V IoT Hub se ujistěte, že je zařízení [registrované jako IoT Edge zařízení](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

1. Defender pro IoT Edge modul vyžaduje, aby se na zařízení IoT Edge nainstalovala [auditovaná architektura](https://linux.die.net/man/8/auditd) .

    - Nainstalujte rozhraní spuštěním následujícího příkazu na zařízení IoT Edge:

    `sudo apt-get install auditd audispd-plugins`

    - Ověřte, že je audit aktivní, spuštěním následujícího příkazu:

    `sudo systemctl status auditd`<br>
    - Očekávaná odpověď: `active (running)`

### <a name="deployment-using-azure-portal"></a>Nasazení pomocí Azure Portal

1. Z Azure Portal otevřete **Marketplace**.

1. Vyberte **Internet věcí**, vyhledejte **Defender pro IoT** a vyberte ho.

   ![Vyberte Defender pro IoT](media/howto/edge-onboarding-8.png)

1. Kliknutím na **vytvořit** nakonfigurujete nasazení.

1. Zvolte **předplatné** Azure vašeho IoT Hub a pak vyberte svoji **IoT Hub**.<br>Vyberte **nasadit do zařízení** pro zaměření na jedno zařízení, nebo vyberte **nasadit ve velkém měřítku** , abyste mohli cílit na více zařízení, a klikněte na **vytvořit**. Další informace o nasazení ve velkém měřítku najdete v tématu [nasazení](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor).

    >[!Note]
    >Pokud jste vybrali možnost **nasadit ve škálování**, před pokračováním na kartu **přidat moduly** v následujících pokynech přidejte název a podrobnosti o zařízení.

Dokončete každý krok, abyste dokončili nasazení IoT Edge pro Defender pro IoT.

#### <a name="step-1-modules"></a>Krok 1: moduly

1. Vyberte modul **AzureSecurityCenterforIoT** .
1. Na kartě **nastavení modulu** změňte **název** na **azureiotsecurity**.
1. Na kartě **proměnné vyhledáte** přidejte proměnnou v případě potřeby (například úroveň ladění).
1. Na kartě **možnosti vytvoření kontejneru** přidejte následující konfiguraci:

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. Na kartě **nastavení s dvojitým modulem** přidejte následující konfiguraci:

   Vlastnost vlákna modulu:
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   Obsah s dvojitou vlastností modulu: 

   ```json
     {

     }
   ```
    
   Další informace o konfiguraci agenta najdete v tématu [Konfigurace agentů zabezpečení](https://docs.microsoft.com/azure/defender-for-iot/how-to-agent-configuration).

1. Vyberte **Aktualizovat**.

#### <a name="step-2-runtime-settings"></a>Krok 2: nastavení modulu runtime

1. Vyberte **nastavení modulu runtime**.
1. V části **hraniční centrum**změňte **Obrázek** na **MCR.Microsoft.com/azureiotedge-hub:1.0.8.3**.
1. Ověřte, že **Možnosti vytváření** jsou nastavené na následující konfiguraci:

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

1. Vyberte **Uložit**.

1. Vyberte **Další**.

#### <a name="step-3-specify-routes"></a>Krok 3: určení tras

1. Na kartě **zadat trasy** se ujistěte, že máte trasu (explicitní nebo implicitní), která bude předávat zprávy z modulu **azureiotsecurity** , aby se **$upstream** podle následujících příkladů. Pouze v případě, že je trasa zavedena, vyberte možnost **Další**.

   Příklady tras:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. Vyberte **Další**.

#### <a name="step-4-review-deployment"></a>Krok 4: Kontrola nasazení

- Na kartě **zkontrolovat nasazení** zkontrolujte informace o vašem nasazení a pak výběrem **vytvořit** dokončete nasazení.

## <a name="diagnostic-steps"></a>Kroky diagnostiky

Pokud narazíte na problém, je nejlepším způsobem, jak se dozvědět o stavu zařízení modulu zabezpečení IoT Edge, jsou protokoly kontejnerů. Ke shromáždění informací použijte příkazy a nástroje uvedené v této části.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Ověřte, že požadované kontejnery jsou nainstalované a fungují podle očekávání.

1. Na zařízení IoT Edge spusťte následující příkaz:

    `sudo docker ps`

1. Ověřte, zda jsou spuštěny následující kontejnery:

   | Name | OBRÁZEK |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   Pokud nejsou k dispozici minimální požadované kontejnery, ověřte, zda je váš manifest nasazení IoT Edge zarovnán s doporučeným nastavením. Další informace najdete v tématu [nasazení modulu IoT Edge](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Kontrola chyb v protokolech modulů

1. Na zařízení IoT Edge spusťte následující příkaz:

   `sudo docker logs azureiotsecurity`

1. Pro podrobnější protokoly přidejte následující proměnnou prostředí do nasazení modulu **azureiotsecurity** : `logLevel=Debug` .

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o možnostech konfigurace, přejděte k příručce Průvodce konfigurací modulu.
> [!div class="nextstepaction"]
> [Průvodce konfigurací modulů](./how-to-agent-configuration.md)
