---
title: Nasazení centra zabezpečení Azure pro modul IoT Edge| Dokumenty společnosti Microsoft
description: Přečtěte si, jak nasadit agenta zabezpečení Azure Security Center pro IoT na IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: b2af392dc4dc848a099b8297bb58e7d4a7104fa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964035"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Nasazení modulu zabezpečení na zařízení IoT Edge


**Modul Azure Security Center for IoT** poskytuje komplexní řešení zabezpečení pro vaše zařízení IoT Edge.
Modul zabezpečení shromažďuje, agreguje a analyzuje nezpracovaná data zabezpečení z operačního systému a kontejnerového systému na doporučení a výstrahy s použitelným zabezpečením.
Další informace najdete [v tématu Modul zabezpečení pro IoT Edge](security-edge-architecture.md).

V tomto článku se dozvíte, jak nasadit modul zabezpečení na zařízení IoT Edge.

## <a name="deploy-security-module"></a>Nasazení modulu zabezpečení

Pomocí následujících kroků nasadit modul zabezpečení Azure Security Center pro IoT pro IoT Edge.

### <a name="prerequisites"></a>Požadavky

1. Ve svém IoT Hubu se ujistěte, že je vaše zařízení [registrované jako zařízení IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

1. Azure Security Center pro IoT Edge modul vyžaduje [auditované architektury](https://linux.die.net/man/8/auditd) je nainstalován na zařízení IoT Edge.

    - Nainstalujte architekturu spuštěním následujícího příkazu na zařízení IoT Edge:
   
    `sudo apt-get install auditd audispd-plugins`

    - Ověření, zda je auditováno aktivní, spuštěním následujícího příkazu: 
   
    `sudo systemctl status auditd`<br>
    - Očekávaná odpověď je:`active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Nasazení pomocí portálu Azure

1. Na webu Azure Portal otevřete **Marketplace**.

1. Vyberte **Internet věcí**, vyhledejte **Azure Security Center pro IoT** a vyberte ho.

   ![Vyberte Azure Security Center pro IoT](media/howto/edge-onboarding-8.png)

1. Chcete-li nakonfigurovat nasazení, klepněte na tlačítko **Vytvořit.** 

1. Zvolte **předplatné** Azure ve vašem IoT Hubu a vyberte **ioT hub**.<br>Vyberte **Nasadit do zařízení,** chcete-li cílit na jedno zařízení, nebo vyberte **Nasadit ve velkém měřítku** tak, aby cílila na více zařízení, a klikněte na **Vytvořit**. Další informace o nasazení ve velkém měřítku naleznete v tématu [Jak nasadit](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Pokud jste vybrali **možnost Nasadit ve velkém měřítku**, přidejte název zařízení a podrobnosti před pokračováním na kartu **Přidat moduly** v následujících pokynech.     

Dokončete každý krok a dokončete nasazení IoT Edge pro Azure Security Center for IoT. 

#### <a name="step-1-modules"></a>Krok 1: Moduly

1. Vyberte modul **AzureSecurityCenterforIoT.**
1. Na kartě **Nastavení modulu** změňte **název** na **azureiotsecurity**.
1. Na kartě **Proměnné prostředí** přidejte proměnnou v případě potřeby (například úroveň ladění).
1. Na kartě **Možnosti vytvoření kontejneru** přidejte následující konfiguraci:

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
    
1. Na kartě **Nastavení dvojčete modulu** přidejte následující konfiguraci:
      
    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration":{}
    ```

1. Vyberte **Aktualizovat**.

#### <a name="step-2-runtime-settings"></a>Krok 2: Nastavení runtime

1. Vyberte **možnost Nastavení běhu**.
1. V části **Edge Hub**změňte **image** na **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**.
1. Ověřit, **že možnosti vytvoření** jsou nastaveny na následující konfiguraci: 
         
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
   
1. Vyberte **další**.

#### <a name="step-3-specify-routes"></a>Krok 3: Určení tras 

1. Na **kartě Zadejte trasy** se ujistěte, že máte trasu (explicitní nebo implicitní), která bude předávat zprávy z modulu **azureiotsecurity,** aby **$upstream** podle následujících příkladů. Pouze v případě, že je trasa na místě, vyberte **další**.

   Příklady tras:

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

1. Vyberte **další**.

#### <a name="step-4-review-deployment"></a>Krok 4: Kontrola nasazení

- Na kartě **Zkontrolovat nasazení** zkontrolujte informace o nasazení a pak kliknutím **na Vytvořit** nasazení dokončete nasazení.

## <a name="diagnostic-steps"></a>Diagnostické kroky

Pokud narazíte na problém, protokoly kontejnerů jsou nejlepší způsob, jak zjistit o stavu zařízení zabezpečení IoT Edge. Ke shromáždění informací použijte příkazy a nástroje uvedené v této části.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Ověřte, zda jsou požadované kontejnery nainstalovány a fungují podle očekávání.

1. Na zařízení IoT Edge spusťte následující příkaz:
    
    `sudo docker ps`
   
1. Ověřte, zda jsou spuštěny následující kontejnery:
   
   | Name (Název) | OBRÁZEK |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |
   
   Pokud nejsou k dispozici minimální požadované kontejnery, zkontrolujte, jestli je manifest nasazení IoT Edge zarovnán s doporučeným nastavením. Další informace naleznete [v tématu Deploy IoT Edge module .](#deployment-using-azure-portal)

### <a name="inspect-the-module-logs-for-errors"></a>Zkontrolujte, zda protokoly modulu neobsahuje chyby
   
1. Na zařízení IoT Edge spusťte následující příkaz:

   `sudo docker logs azureiotsecurity`
   
1. Pro podrobnější protokoly přidejte následující proměnnou prostředí do nasazení modulu `logLevel=Debug` **azureiotsecurity:** .

## <a name="next-steps"></a>Další kroky

Další informace o možnostech konfigurace najdete v návodu pro konfiguraci modulu. 
> [!div class="nextstepaction"]
> [Návod pro konfiguraci modulu](./how-to-agent-configuration.md)
