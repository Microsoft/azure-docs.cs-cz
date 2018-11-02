---
title: Konfigurace zařízení Azure IoT Edge pro proxy servery sítě | Dokumentace Microsoftu
description: Postup konfigurace modulu runtime Azure IoT Edge a všech modulů IoT Edge přístupem k Internetu pro komunikaci přes proxy server.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 72855058c5e8294eece55f8dbcdc501025c9aabf
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913219"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurace zařízení IoT Edge pro komunikaci přes proxy server

Zařízení IoT Edge odesílají požadavky HTTPS pro komunikaci se službou IoT Hub. Pokud vaše zařízení je připojené k síti, která používá proxy server, budete muset nakonfigurovat modul runtime IoT Edge pro komunikaci přes server. Proxy servery může také ovlivnit jednotlivých modulů IoT Edge, pokud provádění požadavků protokolu HTTP nebo HTTPS, které nejsou směrovány přes Centrum Edge. 

Konfigurace zařízení IoT Edge pro práci s proxy serverem zahrnuje následující základní kroky: 

1. Nainstalujte modul runtime IoT Edge na zařízení. 
2. Konfigurace démona Dockeru a proces daemon IoT Edge na zařízení pro použití proxy serveru.
3. Konfigurace vlastností edgeAgent v souboru config.yaml na vašem zařízení.
4. Nastavení proměnných prostředí pro modul runtime IoT Edge a dalších IoT Edge moduly v manifestu nasazení. 

## <a name="know-your-proxy-url"></a>Znát adresu URL vašeho proxy serveru

Ke konfiguraci démon Dockeru i IoT Edge na zařízení, musíte znát adresu URL vašeho proxy serveru. 

Adresy URL proxy serveru provést následující formát: **protokol**://**proxy_host**:**proxy_port**. 

* **Protokol** HTTP nebo HTTPS. Démon Dockeru můžete nakonfigurovat pomocí obou protokolů, v závislosti na nastavení registru kontejneru, ale kontejnerů démon a modul runtime IoT Edge by měl vždycky používají protokol HTTPS.

* **Proxy_host** je adresa proxy serveru. Pokud váš proxy server vyžaduje ověření, můžete zadat své přihlašovací údaje jako součást proxy_host ve formátu **uživatele**:**heslo**@**proxy_host**. 

* **Proxy_port** je síťového portu, na kterém proxy serveru reaguje na síťový provoz. 

## <a name="install-the-runtime"></a>Nainstalovat modul runtime

Pokud instalujete modul runtime IoT Edge na zařízení s Linuxem, konfigurace Správce balíčků na serveru proxy pro přístup k instalačnímu balíčku. Například [nastavení apt-get pro používání proxy serveru http](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Jakmile správce balíčku je nakonfigurovaná, postupujte podle pokynů v [modul runtime nainstalovat Azure IoT Edge v Linuxu (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) nebo [nainstalovat modul runtime Azure IoT Edge v Linuxu (x64)](how-to-install-iot-edge-linux.md) jako obvykle. 

Pokud instalujete modul runtime IoT Edge na zařízení s Windows, budete muset přejít přes proxy server pro přístup k instalačnímu balíčku. Můžete nakonfigurovat informace o proxy serveru v nastavení Windows, nebo zahrnout informace o serveru proxy přímo do instalačního skriptu. Následující příkaz powershellu je příkladem systému windows pomocí instalace `-proxy` argument:

```powershell
. {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows
```

Další informace a možnostech instalace, najdete v části [modul runtime nainstalovat Azure IoT Edge ve Windows pro použití s kontejnery Windows](how-to-install-iot-edge-windows-with-windows.md) nebo [modul runtime nainstalovat Azure IoT Edge ve Windows pro použití s kontejnery Linuxu](how-to-install-iot-edge-windows-with-linux.md).

Po instalaci modulu runtime IoT Edge, použijte v následující části ji nakonfigurovat s informacemi o proxy serveru. 

## <a name="configure-the-daemons"></a>Konfigurace procesy daemon

Docker a IoT Edge procesy démon běžícího ve vašem zařízení IoT Edge je potřeba nakonfigurovat pro použití proxy serveru. Démona Dockeru provede webových žádostí o přijetí změn imagí kontejneru z registrů kontejnerů. Proces démon IoT Edge umožňuje webové požadavky na komunikaci se službou IoT Hub.

### <a name="docker-daemon"></a>Démon dockeru

V dokumentaci Docker ke konfiguraci démona Dockeru s proměnnými prostředí. Většina registrů kontejnerů (včetně Dockerhubu a Azure Container Registry) podporují požadavky HTTPS, takže je parametr, který byste měli nastavit **HTTPS_PROXY**. Pokud jste přetahování imagí z registru, který nepodporuje zabezpečení transportní vrstvy (TLS), pak byste měli nastavit **HTTP_PROXY** parametru. 

Vyberte článek, který se vztahuje na vaší verzi Dockeru: 

* [Docker](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Docker pro Windows](https://docs.docker.com/docker-for-windows/#proxies)

### <a name="iot-edge-daemon"></a>Démon IoT Edge

Proces démon IoT Edge je nakonfigurovaný v podobným způsobem jako démona Dockeru. Všechny požadavky, které IoT Edge odesílá do služby IoT Hub pomocí protokolu HTTPS. Použijte následující postup k nastavení proměnné prostředí pro službu založený na operačním systému. 

#### <a name="linux"></a>Linux

Otevřete editor, v terminálu konfigurace démona IoT Edge. 

```bash
sudo systemctl edit iotedge
```

Zadejte následující text nahrazení  **\<adresu URL proxy serveru >** adresa proxy serveru a port. Potom uložte a zavřete. 

```text
[Service]
Environment="https_proxy=<proxy URL>"
```

Aktualizujte stránku portálu service manager, aby se získaly nové konfigurace pro iotedge.

```bash
sudo systemctl daemon-reload
```

Restartujte IoT Edge, aby se změny projevily.

```bash
sudo systemctl restart iotedge
```

Ověřte, zda byl vytvořen vaší proměnné prostředí a byla načtena nová konfigurace. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Otevřete okno Powershellu jako správce a spusťte následující příkaz, který úprava registru pomocí nové proměnné prostředí. Nahraďte  **\<adresu url proxy serveru >** adresa proxy serveru a port. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Restartujte IoT Edge, aby se změny projevily.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-edge-agent"></a>Konfigurace agenta Edge

Edge agent je první modul pro spuštění na libovolném zařízení IoT Edge. Je spuštěn poprvé na základě informací v souboru config.yaml IoT Edge. Edge agent se pak připojí ke službě IoT Hub se načíst manifest nasazení, které deklarovat, co by měl být ostatní moduly nasazené na zařízení.

Otevřete soubor config.yaml na zařízení IoT Edge. V systémech Linux, tento soubor nachází ve **/etc/iotedge/config.yaml**. V systémech Windows, tento soubor nachází ve **C:\ProgramData\iotedge\config.yaml**. Konfigurační soubor je chráněný, takže je třeba oprávnění správce pro přístup k ní. V systémech Linux, to znamená, že pomocí `sudo` příkaz před otevřením souboru v upřednostňovaném textovém editoru. Na Windows, to znamená otevření textového editoru, například Poznámkový blok spustit jako správce a pak otevřete soubor. 

V souboru config.yaml vyhledejte **agenta Edge module specifikace** oddílu. Obsahuje definice agenta Edge **env** parametr, kam můžete zadat proměnné prostředí. 

<!--
![edgeAgent definition](./media/how-to-configure-proxy-support/edgeagent-unedited.png)
-->

Odeberte složených závorek, které jsou zástupné symboly pro parametr env a přidejte novou proměnnou na nový řádek. Mějte na paměti, že odsazení v YAML byly mezery dvě. 

```yaml
https_proxy: "<proxy URL>"
```

Modul runtime IoT Edge ve výchozím nastavení používá AMQP mluvit do služby IoT Hub. Některé proxy servery blokovat porty protokolu AMQP. Pokud je to tento případ, také musíte nakonfigurovat edgeAgent pomocí protokolu AMQP přes WebSocket. Přidejte druhé proměnné prostředí.

```yaml
UpstreamProtocol: "AmqpWs"
```

![definice edgeAgent s proměnnými prostředí](./media/how-to-configure-proxy-support/edgeagent-edited.png)

Uložte změny do config.yaml a ukončete editor. Restartujte IoT Edge, aby se změny projevily. 

* Linux: 

   ```bash
   sudo systemctl restart iotedge
   ```

* Windows:

   ```powershell
   Restart-Service iotedge
   ```

## <a name="configure-deployment-manifests"></a>Manifesty nasazení konfigurace  

Jakmile se zařízení IoT Edge je nakonfigurováno pro práci s proxy serverem, budete muset také deklarovat proměnné prostředí ve všech budoucích manifestů. Dva moduly runtime, edgeAgent a edgeHub, by měl mít vždy proxy server nakonfigurovaný tak, aby udržování komunikace se službou IoT Hub. Můžete vytvořit jakýkoli modul IoT Edge pro komunikaci přes proxy server, ale není nutné pro moduly, které směrují jejich zpráv prostřednictvím edgeHub nebo, které komunikují jenom s ostatními moduly v zařízení. 

Můžete vytvořit manifesty nasazení pomocí webu Azure portal nebo ručně pomocí úpravy souboru JSON. 

### <a name="azure-portal"></a>portál Azure

Při použití **nastavit moduly** průvodce k vytvoření nasazení IoT Edge zařízení, každý modul **proměnné prostředí** oddíl, který můžete použít ke konfiguraci připojení k proxy serveru. 

Pokud chcete nakonfigurovat agenta Edge a moduly Centrum Edge, vyberte **konfigurovat rozšířená nastavení modulu Runtime Edge** v prvním kroku v průvodci. 

![Konfigurovat upřesňující nastavení modulu runtime Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Přidat **https_proxy** proměnnou prostředí, aby Edge agent a definice modulu Edge hub. Pokud jste zahrnuli **UpstreamProtocol** proměnnou prostředí v souboru config.yaml na zařízení IoT Edge, přidejte ho do definice modulu agenta Edge příliš. 

![Nastavení proměnných prostředí](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Všechny moduly, které přidáte do manifestu nasazení postupují stejným způsobem. Na stránce kde nastavit název modulu a bitovou kopii je oddíl proměnné prostředí.

### <a name="json-deployment-manifest-files"></a>Soubory manifestu nasazení JSON

Pokud vytvoříte nasazení IoT Edge zařízení pomocí šablony v aplikaci Visual Studio Code nebo ručně vytvořit soubory JSON, můžete přidat proměnné prostředí přímo na každá definice modulu. 

Pomocí následujícího formátu JSON: 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

S proměnnými prostředí zahrnuty vaše definice modulu by měl vypadat jako v následujícím příkladu edgeHub:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "https://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Pokud jste zahrnuli **UpstreamProtocol** proměnnou prostředí v souboru confige.yaml na zařízení IoT Edge, přidejte ho do definice modulu agenta Edge příliš. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Další postup

Další informace o rolích z [modul runtime IoT Edge](iot-edge-runtime.md).

Řešení potíží s chybami instalace a konfigurace s [běžné problémy a řešení Azure IoT Edge](troubleshoot.md)

