---
title: Konfigurace zařízení pro síť – proxy služby Azure IoT Edge | Dokumentace Microsoftu
description: Postup konfigurace modulu runtime Azure IoT Edge a všech modulů IoT Edge přístupem k Internetu pro komunikaci přes proxy server.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1c0da1a768b894f543b9089643622c31d6a8758d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730149"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurace zařízení IoT Edge pro komunikaci přes proxy server

Zařízení IoT Edge odesílají požadavky HTTPS pro komunikaci se službou IoT Hub. Pokud vaše zařízení je připojené k síti, která používá proxy server, budete muset nakonfigurovat modul runtime IoT Edge pro komunikaci přes server. Proxy servery může také ovlivnit jednotlivých modulů IoT Edge, pokud provádění požadavků protokolu HTTP nebo HTTPS, které nejsou směrovány přes Centrum IoT Edge. 

Tento článek vás provede následující čtyři kroky ke konfiguraci a potom spravovat zařízení IoT Edge za proxy serverem: 

1. **Nainstalujte modul runtime IoT Edge na zařízení.**

   IoT Edge instalační skripty o přijetí změn balíčky a soubory z Internetu, aby vaše zařízení potřebuje komunikovat prostřednictvím proxy serveru, aby tyto požadavky. Podrobné pokyny najdete v článku [nainstalovat modul runtime prostřednictvím proxy serveru](#install-the-runtime-through-a-proxy) části tohoto článku. Pro zařízení s Windows, instalační skript také poskytuje [Offline instalaci](how-to-install-iot-edge-windows.md#offline-installation) možnost. 

   Tento krok je jednorázový proces provést na zařízení IoT Edge, když poprvé nastavujete ji. Když aktualizujete modul runtime IoT Edge se rovněž vyžadují stejné připojení. 

2. **Konfigurace démona Dockeru a proces daemon IoT Edge na zařízení.**

   IoT Edge používá dva procesy démon na zařízení, které potřebujete k vytvoření webových požadavků prostřednictvím proxy serveru. Proces démon IoT Edge je zodpovědná za komunikaci se službou IoT Hub. Démon Moby zodpovídá za správu kontejnerů, takže komunikuje se službou container Registry. Podrobné pokyny najdete v článku [nakonfigurovat procesy démon](#configure-the-daemons) části tohoto článku. 

   Tento krok je jednorázový proces provést na zařízení IoT Edge, když poprvé nastavujete ji.

3. **Konfigurace vlastností agenta IoT Edge v souboru config.yaml na vašem zařízení.**

   Proces démon IoT Edge spustí modul edgeAgent zpočátku, ale pak modulu edgeAgent zodpovídá za načítání manifestu nasazení ze služby IoT Hub a spouští všechny ostatní moduly. Pro agenta IoT Edge k vytvoření počátečního připojení ke službě IoT Hub nakonfigurujte proměnné prostředí modulu edgeAgent ručně na samotném zařízení. Po počátečním připojení můžete nakonfigurovat modul edgeAgent vzdáleně. Podrobné pokyny najdete v článku [konfigurace agenta IoT Edge](#configure-the-iot-edge-agent) části tohoto článku.

   Tento krok je jednorázový proces provést na zařízení IoT Edge, když poprvé nastavujete ji.

4. **Pro všechna budoucí modul nasazení nastavte proměnné prostředí pro jakýkoli modul komunikují prostřednictvím proxy serveru.**

   Jakmile zařízení IoT Edge je nastavená a připojená ke službě IoT Hub přes proxy server, budete muset udržovat připojení ve všech nasazeních budoucí modul. Podrobné pokyny najdete v článku [konfigurace manifesty nasazení](#configure-deployment-manifests) části tohoto článku. 

   Tento krok je soustavný proces provést vzdáleně, tak, aby všechny nové aktualizace modulu nebo nasazení udržuje zařízení možnosti komunikace prostřednictvím proxy serveru. 

## <a name="know-your-proxy-url"></a>Znát adresu URL vašeho proxy serveru

Před zahájením kroků v tomto článku, musíte znát adresu URL vašeho proxy serveru.

Adresy URL proxy serveru provést následující formát: **protokol**://**proxy_host**:**proxy_port**.

* **Protokol** HTTP nebo HTTPS. Démon Dockeru můžete použít buď protokol, v závislosti na nastavení registru kontejneru, ale kontejnerů démon a modul runtime IoT Edge by měl vždycky používají protokol HTTPS.

* **Proxy_host** je adresa proxy serveru. Pokud váš proxy server vyžaduje ověření, můžete zadat své přihlašovací údaje jako součást proxy hostitele v následujícím formátu: **uživatele**:**heslo**\@**proxy_host** .

* **Proxy_port** je síťového portu, na kterém proxy serveru reaguje na síťový provoz.

## <a name="install-the-runtime-through-a-proxy"></a>Nainstalovat modul runtime prostřednictvím proxy serveru

Zda zařízení IoT Edge běží na Windows nebo Linuxem, potřebujete přístup k instalaci balíčků prostřednictvím proxy serveru. V závislosti na vašem operačním systému a postupujte podle pokynů a nainstalujte modul runtime IoT Edge prostřednictvím proxy serveru. 

### <a name="linux"></a>Linux

Pokud instalujete modul runtime IoT Edge na zařízení s Linuxem, konfigurace Správce balíčků na serveru proxy pro přístup k instalačnímu balíčku. Například [nastavení apt-get pro používání proxy serveru http](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Jakmile správce balíčku je nakonfigurovaná, postupujte podle pokynů v [modul runtime nainstalovat Azure IoT Edge v Linuxu (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) nebo [nainstalovat modul runtime Azure IoT Edge v Linuxu (x64)](how-to-install-iot-edge-linux.md) jako obvykle.

### <a name="windows"></a>Windows

Pokud instalujete modul runtime IoT Edge na zařízení s Windows, budete muset přejít přes proxy server dvakrát. První připojení stáhne instalační soubor skriptu a druhé připojení je během instalace nezbytné součásti ke stažení. Můžete nakonfigurovat informace o proxy serveru v nastavení Windows, nebo zahrnout informace o serveru proxy přímo v příkazech prostředí PowerShell. 

Následující kroky ukazují, příklad použití instalace systému windows `-proxy` argument:

1. Příkaz Invoke-WebRequest vyžaduje informace o proxy serveru pro přístup k instalační skript. Potom příkaz IoTEdge nasazení vyžaduje informace o proxy serveru ke stažení instalačních souborů. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Příkaz inicializace IoTEdge nemusí přejít přes proxy server, aby druhý krok vyžaduje jenom informace o proxy serveru pro Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge

If you have complicated credentials for the proxy server that can't be included in the URL, use the `-ProxyCredential` parameter within `-InvokeWebRequestParameters`. For example,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Další informace o parametrech serveru proxy, naleznete v tématu [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Další informace o možnosti instalace Windows, včetně offline instalaci, naleznete v tématu [modul runtime nainstalovat Azure IoT Edge ve Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Konfigurace procesy daemon

IoT Edge se spoléhá na dva procesy démon spuštěného v příslušném zařízení IoT Edge. Proces démon Moby provede webové požadavky přetáhnout Image kontejneru z registrů kontejnerů. Proces démon IoT Edge umožňuje webové požadavky na komunikaci se službou IoT Hub.

Moby a procesy daemon IoT Edge je potřeba nakonfigurovat pro použití proxy serveru pro probíhající zařízení funkce. Tento krok se provádí na hraničních zařízeních IoT zařízení během počátečního nastavování zařízení. 

### <a name="moby-daemon"></a>Démon Moby

Protože Moby je postavená na Docker, naleznete v dokumentaci Docker démona Moby nakonfigurovat proměnné prostředí. Většina registrů kontejnerů (včetně Dockerhubu a Azure Container Registry) podporují požadavky HTTPS, takže je parametr, který byste měli nastavit **HTTPS_PROXY**. Pokud jste přetahování imagí z registru, který nepodporuje zabezpečení transportní vrstvy (TLS), pak byste měli nastavit **HTTP_PROXY** parametru. 

Vyberte článek, který se vztahuje na operační systém zařízení IoT Edge: 

* [Konfigurace démona Dockeru v Linuxu](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * Moby démon v Linuxu zařízení zůstane název Dockeru.
* [Konfigurace démona Dockeru na Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * Démon Moby na zařízeních s Windows se nazývá iotedge moby. Názvy se liší, protože je možné spustit Docker Desktop i Moby souběžně na zařízení s Windows. 

### <a name="iot-edge-daemon"></a>Démon IoT Edge

Proces démon IoT Edge je nakonfigurovaný v podobným způsobem jako Moby démona. Použijte následující postup k nastavení proměnné prostředí pro službu založený na operačním systému. 

Proces démon IoT Edge vždy používá protokol HTTPS k odeslání žádosti do služby IoT Hub.

#### <a name="linux"></a>Linux

Otevřete editor, v terminálu konfigurace démona IoT Edge. 

```bash
sudo systemctl edit iotedge
```

Zadejte následující text nahrazení  **\<adresu URL proxy serveru >** adresa proxy serveru a port. Potom uložte a zavřete. 

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Aktualizujte stránku portálu service manager, aby se získaly nové konfigurace pro IoT Edge.

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

## <a name="configure-the-iot-edge-agent"></a>Konfigurace agenta IoT Edge

Agenta IoT Edge je první modul pro spuštění na libovolném zařízení IoT Edge. Je spuštěn poprvé na základě informací v souboru config.yaml IoT Edge. Agenta IoT Edge se pak připojí ke službě IoT Hub se načíst manifest nasazení, které deklarovat, co by měl být ostatní moduly nasazené na zařízení.

Tento krok se provádí jednou na hraničních zařízeních IoT zařízení během počátečního nastavování zařízení. 

1. Otevřete soubor config.yaml na zařízení IoT Edge. V systémech Linux, tento soubor nachází ve **/etc/iotedge/config.yaml**. V systémech Windows, tento soubor nachází ve **C:\ProgramData\iotedge\config.yaml**. Konfigurační soubor je chráněný, takže je třeba oprávnění správce pro přístup k ní. V systémech Linux použít `sudo` příkaz před otevřením souboru v upřednostňovaném textovém editoru. Na Windows otevřete textový editor třeba do poznámkového bloku jako správce a pak otevřete soubor. 

2. V souboru config.yaml vyhledejte **agenta Edge module specifikace** oddílu. Obsahuje definice agenta IoT Edge **env** parametr, kam můžete zadat proměnné prostředí. 

3. Odeberte složených závorek, které jsou zástupné symboly pro parametr env a přidejte novou proměnnou na nový řádek. Mějte na paměti, že odsazení v YAML byly mezery dvě. 

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. Modul runtime IoT Edge ve výchozím nastavení používá AMQP mluvit do služby IoT Hub. Některé proxy servery blokovat porty protokolu AMQP. Pokud je to tento případ, také musíte nakonfigurovat edgeAgent pomocí protokolu AMQP přes WebSocket. Přidejte druhé proměnné prostředí.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![definice edgeAgent s proměnnými prostředí](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Uložte změny do config.yaml a ukončete editor. Restartujte IoT Edge, aby se změny projevily. 

   * Linux: 

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Manifesty nasazení konfigurace  

Jakmile se zařízení IoT Edge je nakonfigurováno pro práci s proxy serverem, budete muset pokračovat k deklarování proměnných prostředí v budoucnu manifesty nasazení. Můžete upravit manifesty nasazení pomocí Průvodce Azure portal nebo pomocí úpravy nasazení soubor JSON manifestu. 

Vždy konfigurujte dva moduly runtime, edgeAgent a edgeHub komunikace přes proxy server, můžete udržovat připojení pomocí služby IoT Hub. Pokud odeberete informace o proxy serveru z modulu edgeAgent, je jediný způsob, jak obnovit připojení tak, že upravíte soubor config.yaml na zařízení, jak je popsáno v předchozí části. 

Další moduly IoT Edge, které se připojují k Internetu by měl být nakonfigurovaný pro komunikaci přes proxy server, příliš. Moduly, které směrují jejich zpráv prostřednictvím edgeHub nebo, které komunikují jenom s ostatními moduly v zařízení, ale nepotřebujete podrobnosti proxy serveru. 

Tento krok probíhá po celou dobu životnosti zařízení IoT Edge. 

### <a name="azure-portal"></a>portál Azure

Při použití **nastavit moduly** průvodce k vytvoření nasazení IoT Edge zařízení, každý modul **proměnné prostředí** oddíl, který můžete použít ke konfiguraci připojení k proxy serveru. 

Pokud chcete nakonfigurovat agenta IoT Edge a Centrum moduly IoT Edge, vyberte **konfigurovat rozšířená nastavení modulu Runtime Edge** v prvním kroku v průvodci. 

![Konfigurovat upřesňující nastavení modulu runtime Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Přidat **https_proxy** proměnné prostředí na hraničních zařízeních IoT agenta i na definice modulů IoT Edge hub. Pokud jste zahrnuli **UpstreamProtocol** proměnnou prostředí v souboru config.yaml na zařízení IoT Edge, přidejte ho do definice modulu IoT Edge agenta příliš. 

![Nastavte proměnnou prostředí https_proxy](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

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

Pokud jste zahrnuli **UpstreamProtocol** proměnnou prostředí v souboru confige.yaml na zařízení IoT Edge, přidejte ho do definice modulu IoT Edge agenta příliš. 

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

