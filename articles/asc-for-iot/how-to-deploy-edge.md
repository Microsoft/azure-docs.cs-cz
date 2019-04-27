---
title: Nasazení Azure Security Center pro modul IoT Edge | Dokumentace Microsoftu
description: Další informace o tom, jak nasadit Azure Security Center pro agenta zabezpečení IoT na hraničních zařízeních IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/1/2019
ms.author: mlottner
ms.openlocfilehash: d72980d6e27600cb844d5477d3b9a61d9e1573e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61358600"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>Nasazení modulu zabezpečení na vašem zařízení IoT Edge

> [!IMPORTANT]
> Azure Security Center pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Azure Security Center (ASC) pro IoT** modul poskytuje komplexní řešení zabezpečení pro vaše zařízení IoT Edge.
Modul zabezpečení shromažďuje, agreguje a analyzuje data nezpracovaná zabezpečení z operačního systému a kontejneru systému do užitečná doporučení zabezpečení a upozornění.
Další informace najdete v tématu [zabezpečení modul IoT Edge](security-edge-architecture.md).

V této příručce se dozvíte, jak nasadit modul zabezpečení na vašem zařízení IoT Edge.

## <a name="deploy-security-module"></a>Nasazení modulu zabezpečení

Použijte následující postup ASC pro modul IoT zabezpečení nasazení IoT Edge.

### <a name="prerequisites"></a>Požadavky

- Ve službě IoT Hub, ujistěte se, že vaše zařízení je [zaregistrované jako zařízení IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).

- Vyžaduje ASC pro modul IoT Edge [démona AuditD framework](https://linux.die.net/man/8/auditd) je nainstalovaný na zařízení IoT Edge.

    - Nainstalujte rozhraní framework spuštěním následujícího příkazu na zařízení IoT Edge:
   
      `sudo apt-get install auditd audispd-plugins`
   
    - Ověřte, zda že démona AuditD je aktivní spuštěním následujícího příkazu:
   
      `sudo systemctl status auditd`
      
        Očekávaná odezva je `active (running)`. 

### <a name="deployment-using-azure-portal"></a>Nasazení pomocí webu Azure portal

1. Na webu Azure portal otevřete **Marketplace**.

1. Vyberte **Internet of Things**, vyhledejte **Azure Security Center pro IoT** a vyberte ji.

   ![Vyberte Azure Security Center pro IoT](media/howto/edge-onboarding-8.png)

1. Klikněte na tlačítko **vytvořit** ke konfiguraci nasazení. 

1. Zvolte Azure **předplatné** služby IoT Hub, pak vyberte vaši **služby IoT Hub**.<br>Vyberte **nasadit do zařízení** cílit na jedno zařízení, nebo vyberte **nasazení ve velkém měřítku** cílit na více zařízení, a klikněte na **vytvořit**. Další informace o nasazení ve velkém měřítku, naleznete v tématu [nasazení](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor). 

    >[!Note] 
    >Pokud jste vybrali **nasazení ve velkém měřítku**, přidejte název zařízení a podrobnosti až potom **přidat moduly** kartu v následujících pokynech.     

Existují tři kroky pro vytvoření nasazení IoT Edge pro Azure Security Center pro IoT. V následujících částech se provedou v rámci každé z nich. 

#### <a name="step-1-add-modules"></a>Krok 1: Přidat moduly

1. Z **přidat moduly** kartě **moduly nasazení** oblast, klikněte na tlačítko **AzureSecurityCenterforIoT**. 
   
1. Změnit **název** k **azureiotsecurity**.
1. Změnit **identifikátor URI Image** k **mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3**.
1. Ověřte, **možnosti vytvoření kontejneru** je hodnota nastavena na:      
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
1. Ověřte, že **požadované vlastnosti dvojčete modulu sady** je vybraná a změnit objekt konfigurace pro:
      
    ``` json
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. Klikněte na **Uložit**.
1. Přejděte do dolní části karty a vyberte **konfigurovat rozšířená nastavení modulu Runtime Edge**.
   
   >[!Note]
   > Proveďte **není** zakázat AMQP komunikaci pro Centrum IoT Edge.
   > Azure Security Center pro modul IoT vyžaduje AMQP komunikaci s centrem IoT Edge.
   
1. Změnit **Image** pod **hraniční centra** k **mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview**.

   >[!Note]
   > Azure Security Center pro modul IoT vyžaduje rozvětveného verzi Centrum IoT Edge, založené na sadě SDK verze 1,20.
   > Změnou bitové kopie Centrum IoT Edge jsou instruující zařízení IoT Edge na nejnovější stabilní verze nahradit rozvětveného verzi Centrum IoT Edge, který není oficiálně podporován službou IoT Edge.

1. Ověřte **možnosti vytvoření** je nastavena na: 
         
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

1. V **určení tras** kartu, nastavte **ASCForIoTToIoTHub** směrovat **"z/zpráv/moduly/azureiotsecurity/\* do $upstream"** a klikněte na tlačítko  **Další**.

   ![Zadejte trasy](media/howto/edge-onboarding-9.png)

#### <a name="step-3-review-deployment"></a>Krok 3: Zkontrolovat nasazení

1. V **zkontrolujte nasazení** kartu, zkontrolujte informace o nasazení a pak vyberte **odeslat** k dokončení nasazení.

## <a name="diagnostic-steps"></a>Postup diagnostiky

Pokud narazíte na problém, protokoly kontejneru jsou nejlepší způsob, jak získat informace o stavu zařízení IoT Edge zabezpečení modulu. Ke shromáždění informací použijte příkazy a nástroje uvedené v této části.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>Ověřte, že požadované kontejnery jsou nainstalovaná a funkční podle očekávání

1. Spusťte následující příkaz na vašem zařízení IoT Edge:
    
     `sudo docker ps`
   
1. Ověřte, zda jsou spuštěny následujících kontejnerech:
   
   | Název | BITOVÉ KOPIE |
   | --- | --- |
   | azureIoTSecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3 |
   | edgeHub | mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   Pokud minimální požadovaná kontejnery nejsou k dispozici, zkontrolujte, pokud manifestu nasazení IoT Edge je v souladu s doporučeným nastavením. Další informace najdete v tématu [nasazení IoT Edge module](#deployment-using-azure-portal).

### <a name="inspect-the-module-logs-for-errors"></a>Zkontrolujte chyby v protokolech modulu
   
1. Spusťte následující příkaz na vašem zařízení IoT Edge:

   `sudo docker logs azureiotsecurity`
   
1. Podrobnější protokoly, přidejte následující proměnnou prostředí k **azureiotsecurity** nasazení modulu: `logLevel=Debug`.

## <a name="next-steps"></a>Další postup

Další informace o možnostech konfigurace, pokračujte v Průvodci s postupy pro konfiguraci modulu. 
> [!div class="nextstepaction"]
> [Postupy: Průvodce konfigurací modulu](./how-to-agent-configuration.md)
