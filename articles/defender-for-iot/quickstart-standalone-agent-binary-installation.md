---
title: 'Rychlý Start: instalace programu Defender pro IoT Micro Agent (Preview)'
description: V tomto rychlém startu se dozvíte, jak nainstalovat a ověřit agenta Defender Micro.
ms.date: 3/9/2021
ms.topic: quickstart
ms.openlocfilehash: a153b640a1d1e86f9b761817d05fda7d3e47da98
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384404"
---
# <a name="quickstart-install-defender-for-iot-micro-agent-preview"></a>Rychlý Start: instalace programu Defender pro IoT Micro Agent (Preview)

Tento článek poskytuje vysvětlení, jak nainstalovat a ověřit agenta Defender Micro.

## <a name="prerequisites"></a>Požadavky

Před instalací modulu Defender for IoT je nutné vytvořit identitu modulu v IoT Hub. Další informace o tom, jak vytvořit identitu modulu, najdete v tématu [Vytvoření aplikace Defender IoT Micro agent Module (Preview)](quickstart-create-micro-agent-module-twin.md).

## <a name="install-the-package"></a>Instalace balíčku

Nainstalujte a nakonfigurujte úložiště balíčků Microsoft pomocí následujících [pokynů](/windows-server/administration/linux-package-repository-for-microsoft-software). 

V případě Debian 9 pokyny neobsahují úložiště, které je třeba přidat. k přidání úložiště použijte následující příkazy: 

```azurecli
curl -sSL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add - 

sudo apt-get install software-properties-common

sudo apt-add-repository https://packages.microsoft.com/debian/9/multiarch/prod

sudo apt-get update
```

Chcete-li nainstalovat balíček programu Defender Micro Agent na Debian a distribuce systému Linux založené na Ubuntu, použijte následující příkaz:

```azurecli
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>Metody ověřování mikroagentů 

K ověření programu Defender pro IoT Micro agent jsou k disdobu tyto dvě možnosti: 

- Připojovací řetězec identity modulu 

- Certifikát.

### <a name="authenticate-using-a-module-identity-connection-string"></a>Ověřování pomocí připojovacího řetězce identity modulu

Zajistěte splnění [požadavků](#prerequisites) pro tento článek a vytvoření identity modulu před zahájením tohoto postupu. 

#### <a name="get-the-module-identity-connection-string"></a>Získání připojovacího řetězce identity modulu

Získání připojovacího řetězce identity modulu z IoT Hub: 

1. Přejděte do IoT Hub a vyberte své centrum.

1. V nabídce na levé straně v části **průzkumníky** vyberte **zařízení IoT**.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/iot-devices.png" alt-text="V nabídce na levé straně vyberte zařízení IoT.":::

1. Vyberte zařízení ze seznamu ID zařízení a zobrazte tak stránku s **podrobnostmi o zařízení** .

1. Vyberte kartu **identity modulu**   a potom v ****   seznamu identit modulu přidružených k zařízení vyberte modul DefenderIotMicroAgent.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/module-identities.png" alt-text="Vyberte kartu identity modulu.":::

1. Na stránce **Podrobnosti o identitě modulu** zkopírujte primární klíč tak, že vyberete tlačítko **Kopírovat** .

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/copy-button.png" alt-text="Kliknutím na tlačítko Kopírovat zkopírujte primární klíč.":::

#### <a name="configure-authentication-using-a-module-identity-connection-string"></a>Konfigurace ověřování pomocí připojovacího řetězce identity modulu

Konfigurace agenta pro ověřování pomocí připojovacího řetězce identity modulu:

1. `connection_string.txt`Zadáním následujícího příkazu umístěte soubor s názvem, který obsahuje připojovací řetězec kódovaný v kódování UTF-8, do cesty adresáře agenta programu Defender `/var/defender_iot_micro_agent` :

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    `connection_string.txt`By měl být umístěn v následujícím umístění cesty `/var/defender_iot_micro_agent/connection_string.txt` .

1. Restartujte službu pomocí tohoto příkazu:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>Ověřování pomocí certifikátu

Ověření pomocí certifikátu:

1. Pomocí [těchto pokynů](../iot-hub/iot-hub-security-x509-get-started.md)doopatřete certifikát.

1. Umístěte veřejnou část certifikátu zakódovanou do PEM a privátní klíč do adresáře agentů Defenderu do souboru s názvem `certificate_public.pem` a `certificate_private.pem` . 

1. Do souboru umístěte příslušný připojovací řetězec `connection_string.txt` . připojovací řetězec by měl vypadat takto: 

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true` 

    Tento řetězec upozorní agenta programu Defender, aby očekával, že se pro ověřování zadal certifikát. 

1. Restartujte službu pomocí následujícího příkazu:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>Ověření instalace

Ověření instalace:

1. Ujistěte se, že Micro agent funguje správně pomocí následujícího příkazu:  

    ```azurecli
    systemctl status defender-iot-micro-agent.service
    ```
1. Ujistěte se, že je služba stabilní, tím, že je `active` a že je doba provozu procesu vhodná.

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="Ověřte, že je vaše služba stabilní a aktivní.":::
 
## <a name="testing-the-system-end-to-end"></a>Kompletní testování systému 

Systém můžete otestovat z konce na konec tím, že na zařízení vytvoříte spouštěcí soubor. Spouštěcí soubor způsobí, že kontrola standardních hodnot v agentovi detekuje soubor jako porušení úrovně. 

Pomocí následujícího příkazu vytvořte soubor v systému souborů:

```azurecli
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```
V centru se zobrazí doporučení k selhání ověření směrného plánu s `CceId` CI-Debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0,0: 

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="Základní doporučení při selhání ověření směrného plánu, ke kterému dochází v centru." lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

Umožní vám to až jednu hodinu, než se doporučení objeví v centru. 

## <a name="micro-agent-versioning"></a>Správa verzí Micro agentů 

Pokud chcete nainstalovat určitou verzi agenta IoT IoT Micro, spusťte následující příkaz: 

```azurecli
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Sestavení agenta Defender Micro ze zdrojového kódu](quickstart-building-the-defender-micro-agent-from-source.md)
