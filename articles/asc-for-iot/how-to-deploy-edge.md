---
title: Nasazení Azure Security Center pro modul IoT Edge (Preview) | Microsoft Docs
description: Přečtěte si, jak nasadit Azure Security Center pro agenta zabezpečení IoT v IoT Edge.
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 7171923e4badb3355a64b63515d40e73fadca6b0
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596357"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Nasazení modulu zabezpečení na zařízení IoT Edge

> [!IMPORTANT]
> Podpora zařízení Azure Security Center pro IoT IoT Edge je v současnosti ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Azure Security Center pro modul IoT** poskytuje komplexní řešení zabezpečení pro vaše IoT Edgeá zařízení.
Modul zabezpečení shromažďuje, agreguje a analyzuje nezpracovaná data zabezpečení z operačního systému a systému kontejnerů do užitečných doporučení zabezpečení a výstrah.
Další informace najdete v tématu [modul zabezpečení pro IoT Edge](security-edge-architecture.md).

V tomto článku se dozvíte, jak nasadit modul zabezpečení na zařízení IoT Edge.

## <a name="deploy-security-module"></a>Nasadit modul zabezpečení

Pomocí následujících kroků nasaďte Azure Security Center pro modul zabezpečení IoT pro IoT Edge.

### <a name="prerequisites"></a>Požadavky

- V IoT Hub se ujistěte, že je zařízení [registrované jako IoT Edge zařízení](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

- Azure Security Center pro modul IoT Edge vyžaduje, aby na zařízení IoT Edge bylo nainstalováno [auditované rozhraní](https://linux.die.net/man/8/auditd) .

    - Nainstalujte rozhraní spuštěním následujícího příkazu na zařízení IoT Edge:
   
      `sudo apt-get install auditd audispd-plugins`
   
    - Ověřte, že je audit aktivní, spuštěním následujícího příkazu:
   
      `sudo systemctl status auditd`
      
        Očekávaná odpověď je `active (running)`. 

### <a name="deployment-using-azure-portal"></a>Nasazení pomocí Azure Portal

1. Z Azure Portal otevřete **Marketplace**.

1. Vyberte **Internet věcí**, vyhledejte **Azure Security Center pro IoT** a vyberte ji.

   ![Vyberte Azure Security Center pro IoT](media/howto/edge-onboarding-8.png)

1. Kliknutím na **vytvořit** nakonfigurujete nasazení. 

1. Zvolte **předplatné** Azure vašeho IoT Hub a pak vyberte svoji **IoT Hub**.<br>Vyberte **nasadit do zařízení** pro zaměření na jedno zařízení, nebo vyberte **nasadit ve** velkém měřítku, abyste mohli cílit na více zařízení, a klikněte na **vytvořit**. Další informace o nasazení ve velkém měřítku najdete v tématu [nasazení](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Pokud jste vybrali možnost **nasadit ve škálování**, před pokračováním na kartu **přidat moduly** v následujících pokynech přidejte název a podrobnosti o zařízení.     

Existují tři kroky pro vytvoření nasazení IoT Edge pro Azure Security Center pro IoT. V následujících částech se provedou v rámci každé z nich. 

#### <a name="step-1-add-modules"></a>Krok 1: Přidat moduly

1. Na kartě **přidat moduly** v oblasti **moduly nasazení** klikněte na **AzureSecurityCenterforIoT**. 
   
1. Změňte **název** na **azureiotsecurity**.
1. Změňte **identifikátor URI image** na **MCR.Microsoft.com/ascforiot/azureiotsecurity:0.0.3**.
1. Ověřte, že hodnota **možnosti vytvořit kontejner** je nastavená na:      
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
1. Ověřte, zda je vybrána možnost **nastavit požadované vlastnosti vlákna** a změnit objekt konfigurace na:
      
    ``` json
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. Klikněte na **Uložit**.
1. Posuňte se do dolní části karty a vyberte **konfigurovat pokročilá nastavení modulu runtime Edge**.
   
   
1. Změňte **Obrázek** v oblasti **Edge hub** na **MCR.Microsoft.com/ascforiot/edgehub:1.0.9-Preview**.

   >[!Note]
   > Modul Azure Security Center for IoT vyžaduje rozvětvenou verzi centra IoT Edge na základě sady SDK verze 1,20.
   > Změnou obrázku IoT Edge centra dáte pokyn, aby zařízení IoT Edge nahradilo nejnovější stabilní verzi pomocí rozvětvené verze IoT Edge centra, která není oficiálně podporována službou IoT Edge.

1. Ověřte, že **Možnosti vytváření** jsou nastavené na: 
         
    ``` json
    {
      "HostConfig": {
        "PortBindings": {
          "8883/tcp": [{"HostPort": "8883"}],
          "443/tcp": [{"HostPort": "443"}],
          "5671/tcp": [{"HostPort": "5671"}]
        }
      }
    }
    ```
      
1. Klikněte na **Uložit**.
   
1. Klikněte na **Další**.

#### <a name="step-2-specify-routes"></a>Krok 2: Zadejte trasy 

1. Na kartě **zadat trasy** se ujistěte, že máte trasu (explicitní nebo implicitní), která bude předávat zprávy z modulu **azureiotsecurity** do **$upstream**. 
1. Klikněte na **Další**.

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream
    ~~~

#### <a name="step-3-review-deployment"></a>Krok 3: Zkontrolovat nasazení

- Na kartě **zkontrolovat nasazení** zkontrolujte informace o nasazení a pak vyberte **Odeslat** . tím se nasazení dokončí.

## <a name="diagnostic-steps"></a>Kroky diagnostiky

Pokud narazíte na problém, je nejlepším způsobem, jak se dozvědět o stavu zařízení modulu zabezpečení IoT Edge, jsou protokoly kontejnerů. Ke shromáždění informací použijte příkazy a nástroje uvedené v této části.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Ověřte, že požadované kontejnery jsou nainstalované a fungují podle očekávání.

1. Na zařízení IoT Edge spusťte následující příkaz:
    
     `sudo docker ps`
   
1. Ověřte, zda jsou spuštěny následující kontejnery:
   
   | Name | OBRAZU |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3 |
   | edgeHub | mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   Pokud nejsou k dispozici minimální požadované kontejnery, ověřte, zda je váš manifest nasazení IoT Edge zarovnán s doporučeným nastavením. Další informace najdete v tématu [nasazení modulu IoT Edge](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Kontrola chyb v protokolech modulů
   
1. Na zařízení IoT Edge spusťte následující příkaz:

   `sudo docker logs azureiotsecurity`
   
1. Pro podrobnější protokoly přidejte následující proměnnou prostředí do nasazení modulu **azureiotsecurity** : `logLevel=Debug`.

## <a name="next-steps"></a>Další postup

Pokud chcete získat další informace o možnostech konfigurace, přejděte k příručce Průvodce konfigurací modulu. 
> [!div class="nextstepaction"]
> [Průvodce konfigurací modulů](./how-to-agent-configuration.md)
