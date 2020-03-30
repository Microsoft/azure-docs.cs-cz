---
title: Konfigurace zařízení pro síťové proxy servery – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Jak nakonfigurovat modul runtime Azure IoT Edge a všechny moduly IoT Edge směřující k internetu pro komunikaci prostřednictvím proxy serveru.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0600568ace5384cfb13688d14d1cf79e473f3208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133219"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurace zařízení IoT Edge tak, aby komunikovalo přes proxy server

Zařízení IoT Edge odesílají požadavky HTTPS pro komunikaci s IoT Hubem. Pokud je vaše zařízení připojeno k síti, která používá proxy server, je třeba nakonfigurovat runtime IoT Edge pro komunikaci prostřednictvím serveru. Proxy servery mohou také ovlivnit jednotlivé moduly IoT Edge, pokud dělají požadavky HTTP nebo HTTPS, které nejsou směrovány přes centrum IoT Edge.

Tento článek vás provede následujícími čtyřmi kroky konfigurace a následné správy zařízení IoT Edge za proxy serverem:

1. **Nainstalujte runtime IoT Edge do zařízení.**

   Instalační skripty IoT Edge vytahují balíčky a soubory z Internetu, takže vaše zařízení musí komunikovat prostřednictvím proxy serveru, aby tyto požadavky. Podrobné kroky naleznete v [části Instalace běhu prostřednictvím proxy serveru](#install-the-runtime-through-a-proxy) tohoto článku. Pro zařízení se systémem Windows poskytuje instalační skript také možnost [instalace offline.](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

   Tento krok je jednorázový proces prováděný na zařízení IoT Edge při prvním nastavení. Stejná připojení jsou také vyžadována při aktualizaci runtime IoT Edge.

2. **Nakonfigurujte daemon dockeru a demon IoT Edge na vašem zařízení.**

   IoT Edge používá dva daemons na zařízení, které je třeba provést webové požadavky prostřednictvím proxy serveru. Daemon IoT Edge je zodpovědný za komunikaci se službě IoT Hub. Moby daemon je zodpovědný za správu kontejnerů, takže komunikuje s registry kontejnerů. Podrobné kroky naleznete v části [Konfigurace daemonů v](#configure-the-daemons) tomto článku.

   Tento krok je jednorázový proces prováděný na zařízení IoT Edge při prvním nastavení.

3. **Nakonfigurujte vlastnosti agenta IoT Edge v souboru config.yaml v zařízení.**

   Daemon IoT Edge spustí modul edgeAgent zpočátku, ale modul edgeAgent je zodpovědný za načtení manifestu nasazení z ioT hubu a spuštění všech ostatních modulů. Pro agenta IoT Edge k nastolu počáteční připojení k ioT hubu, nakonfigurujte proměnné prostředí modulu edgeAgent ručně na samotném zařízení. Po počátečním připojení můžete modul edgeAgent konfigurovat vzdáleně. Podrobné kroky najdete v [části Konfigurace agenta IoT Edge](#configure-the-iot-edge-agent) v tomto článku.

   Tento krok je jednorázový proces prováděný na zařízení IoT Edge při prvním nastavení.

4. **Pro všechna budoucí nasazení modulů nastavte proměnné prostředí pro libovolný modul komunikující prostřednictvím proxy serveru.**

   Po nastavení a připojení zařízení IoT Edge k ioT hubu přes proxy server je potřeba udržovat připojení ve všech budoucích nasazeních modulů. Podrobné kroky naleznete v části [Konfigurace manifestů nasazení](#configure-deployment-manifests) v tomto článku.

   Tento krok je probíhající proces prováděný vzdáleně tak, aby každá nová aktualizace modulu nebo nasazení udržovala schopnost zařízení komunikovat prostřednictvím proxy serveru.

## <a name="know-your-proxy-url"></a>Poznejte adresu URL proxy

Než začnete některý z kroků v tomto článku, musíte znát adresu URL proxy.

Proxy adresy URL mají následující formát: **protokol**://**proxy_host**:**proxy_port**.

* **Protokol** je http nebo https. Daemon Dockeru může používat buď protokol, v závislosti na nastavení registru kontejnerů, ale kontejnery demonu IoT Edge a runtime by měly vždy používat protokol HTTP pro připojení k proxy serveru.

* **proxy_host** je adresa proxy serveru. Pokud váš proxy server vyžaduje ověření, můžete zadat pověření jako součást hostitele proxy s následujícím formátem: **uživatel**:**heslo**\@**proxy_host**.

* **Proxy_port** je síťový port, na kterém proxy server reaguje na síťový provoz.

## <a name="install-the-runtime-through-a-proxy"></a>Instalace běhu přes proxy server

Ať už vaše zařízení IoT Edge běží ve Windows nebo Linuxu, musíte získat přístup k instalačním balíčkům prostřednictvím proxy serveru. V závislosti na operačním systému postupujte podle pokynů k instalaci runtime IoT Edge přes proxy server.

### <a name="linux-devices"></a>Linuxová zařízení

Pokud instalujete runtime IoT Edge na zařízení s Linuxem, nakonfigurujte správce balíčků tak, aby prošel proxy serverem pro přístup k instalačnímu balíčku. Například [Nastavit apt-get používat http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Po konfiguraci správce balíčků postupujte podle pokynů v [části Instalace runtime Azure IoT Edge na Linuxu](how-to-install-iot-edge-linux.md) jako obvykle.

### <a name="windows-devices"></a>Zařízení s Windows

Pokud instalujete runtime IoT Edge na zařízení se systémem Windows, musíte dvakrát projít proxy server. První připojení stáhne soubor instalačního skriptu a druhé připojení je během instalace ke stažení potřebných součástí. Informace o serveru proxy můžete konfigurovat v nastavení systému Windows nebo zahrnout informace o serveru proxy přímo do příkazů prostředí PowerShell.

Následující kroky ukazují příklad instalace systému `-proxy` Windows pomocí argumentu:

1. Příkaz Invoke-WebRequest potřebuje informace o serveru proxy pro přístup ke skriptu instalačního programu. Pak deploy-IoTEdge příkaz potřebuje proxy informace ke stažení instalačních souborů.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Příkaz Initialize-IoTEdge nemusí procházet proxy serverem, takže druhý krok vyžaduje pouze informace o serveru proxy pro Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Pokud máte složité přihlašovací údaje pro proxy server, který nelze zahrnout `-ProxyCredential` do `-InvokeWebRequestParameters`adresy URL, použijte parametr v rámci . Například:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Další informace o parametrech proxy naleznete v tématu [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Další informace o možnostech instalace systému Windows, včetně offline instalace, najdete [v tématu Instalace prostředí Azure IoT Edge v systému Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Konfigurace daemonů

IoT Edge spoléhá na dva daemony běžící na zařízení IoT Edge. Moby daemon provádí webové požadavky na vyžádat image kontejneru z registrů kontejnerů. Daemon IoT Edge provádí webové požadavky na komunikaci s ioT hubem.

Moby a IoT Edge daemons musí být nakonfigurován tak, aby používat proxy server pro průběžné funkce zařízení. Tento krok probíhá na zařízení IoT Edge během počátečního nastavení zařízení.

### <a name="moby-daemon"></a>Mobyho daemon

Vzhledem k tomu, že Moby je postaven na Dockeru, podívejte se do dokumentace Dockeru a nakonfigurujte daemon Moby s proměnnými prostředí. Většina registrů kontejnerů (včetně DockerHubu a Azure Container Registryies) podporuje požadavky HTTPS, takže parametr, který byste měli nastavit, je **HTTPS_PROXY**. Pokud stahujete obrázky z registru, který nepodporuje zabezpečení transportní vrstvy (TLS), měli byste nastavit **parametr HTTP_PROXY.**

Vyberte článek, který se vztahuje na váš operační systém zařízení IoT Edge:

* [Konfigurace daemonu Dockeru na Linuxu](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) Daemon Moby na linuxových zařízeních zachová název Docker.
* [Konfigurace daemonu Dockeru v systému Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) Moby ho na zařízeních se systémem Windows nazývá iotedge-moby. Názvy se liší, protože je možné spustit Docker Desktop a Moby paralelně na zařízení se systémem Windows.

### <a name="iot-edge-daemon"></a>Déon IoT Edge

Daemon IoT Edge je konfigurován podobným způsobem jako daemon Moby. Pomocí následujících kroků nastavte proměnnou prostředí pro službu na základě operačního systému.

Daemon IoT Edge vždy používá protokol HTTPS k odesílání požadavků do služby IoT Hub.

#### <a name="linux"></a>Linux

Otevřete editor v terminálu a nakonfigurujte daemon IoT Edge.

```bash
sudo systemctl edit iotedge
```

Zadejte následující text ** \<** a nahraďte adresu URL proxy>adresou a portem proxy serveru. Potom uložte a ukončete.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Aktualizujte správce služeb a vyzvedněte novou konfiguraci pro IoT Edge.

```bash
sudo systemctl daemon-reload
```

Restartujte IoT Edge, aby se změny projevily.

```bash
sudo systemctl restart iotedge
```

Ověřte, zda byla vytvořena proměnná prostředí a zda byla načtena nová konfigurace.

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Otevřete okno Prostředí PowerShell jako správce a spusťte následující příkaz pro úpravu registru s novou proměnnou prostředí. Nahraďte ** \<adresu URL proxy>** adresou proxy serveru a portem.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Restartujte IoT Edge, aby se změny projevily.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Konfigurace agenta IoT Edge

Agent IoT Edge je první modul, který se spustí na jakémkoli zařízení IoT Edge. Poprvé se spustila na základě informací v souboru Config.yaml ioT Edge. Agent IoT Edge se pak připojí k ioT hubu k načtení manifestů nasazení, které deklarují, jaké další moduly by měly být nasazeny na zařízení.

Tento krok se uskuteční jednou na zařízení IoT Edge během počátečního nastavení zařízení.

1. Otevřete soubor config.yaml na zařízení IoT Edge. Na systémech Linux, tento soubor je umístěn na **/etc/iotedge/config.yaml**. V systémech Windows je tento soubor umístěn na adrese **C:\ProgramData\iotedge\config.yaml**. Konfigurační soubor je chráněn, takže k přístupu k němu potřebujete oprávnění správce. V systémech Linux `sudo` použijte příkaz před otevřením souboru v upřednostňovaném textovém editoru. Ve Windows otevřete textový editor, jako je Poznámkový blok, jako správce a potom otevřete soubor.

2. V souboru config.yaml vyhledejte část **specifikace modulu Edge Agent.** Definice agenta IoT Edge obsahuje **env parametr,** kde můžete přidat proměnné prostředí.

3. Odeberte složené závorky, které jsou zástupnými symboly pro env parametr a přidejte novou proměnnou na nový řádek. Nezapomeňte, že odsazení v YAML jsou dvě mezery.

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. Runtime IoT Edge používá AMQP ve výchozím nastavení k mluvení do ioT hubu. Některé proxy servery blokují porty AMQP. Pokud tomu tak je, pak je také nutné nakonfigurovat edgeAgent pro použití AMQP přes WebSocket. Přidejte druhou proměnnou prostředí.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![edgeAgent definice s proměnnými prostředí](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Uložte změny na config.yaml a zavřete editor. Restartujte IoT Edge, aby se změny projevily.

   * Linux:

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Konfigurace manifestů nasazení  

Jakmile je vaše zařízení IoT Edge nakonfigurováno pro práci s proxy serverem, musíte pokračovat v deklarování proměnné HTTPS_PROXY prostředí v budoucích manifestech nasazení. Manifesty nasazení můžete upravit buď pomocí průvodce portálu Azure, nebo úpravou souboru JSON manifestu nasazení.

Vždy nakonfigurujte dva moduly runtime, edgeAgent a edgeHub, pro komunikaci prostřednictvím proxy serveru, aby mohli udržovat připojení k IoT Hubu. Pokud odeberete informace o serveru proxy z modulu edgeAgent, jediným způsobem, jak obnovit připojení, je úprava souboru config.yaml v zařízení, jak je popsáno v předchozí části.

Kromě modulů edgeAgent a edgeHub mohou ostatní moduly potřebovat konfiguraci proxy serveru. Jedná se o moduly, které potřebují přístup k prostředkům Azure kromě služby IoT Hub, jako je například úložiště objektů blob, a musí mít HTTPS_PROXY proměnnou určenou pro daný modul v souboru manifestu nasazení.

Následující postup je použitelný po celou dobu životnosti zařízení IoT Edge.

### <a name="azure-portal"></a>portál Azure

Při použití průvodce **nastavit moduly** k vytvoření nasazení pro zařízení IoT Edge, každý modul má proměnné **prostředí** sekce, které můžete použít ke konfiguraci připojení proxy serveru.

Chcete-li nakonfigurovat moduly agenta IoT Edge a moduly rozbočovače IoT Edge, vyberte v prvním kroku průvodce **nastavení modulu Runtime.**

![Konfigurace upřesňujících nastavení edge runtime](./media/how-to-configure-proxy-support/configure-runtime.png)

Přidejte proměnnou **prostředí https_proxy** do definice modulu agenta IoT Edge i modulu centra IoT Edge. Pokud jste do souboru config.yaml na zařízení IoT Edge zahrnuli proměnnou prostředí **UpstreamProtocol,** přidejte ji také do definice modulu agenta IoT Edge.

![Nastavit https_proxy proměnné prostředí](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Všechny ostatní moduly, které přidáte do manifestu nasazení postupujte podle stejného vzoru. Na stránce, kde nastavíte název modulu a obrázek, je sekce proměnných prostředí.

### <a name="json-deployment-manifest-files"></a>Soubory manifestu nasazení JSON

Pokud vytvoříte nasazení pro zařízení IoT Edge pomocí šablon v kódu sady Visual Studio nebo ručním vytvářením souborů JSON, můžete proměnné prostředí přidat přímo do každé definice modulu.

Použijte následující formát JSON:

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

S proměnnými prostředí zahrnuty, definice modulu by měl vypadat jako následující příklad edgeHub:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Pokud jste do souboru confige.yaml na zařízení IoT Edge zahrnuli proměnnou prostředí **UpstreamProtocol,** přidejte ji také do definice modulu agenta IoT Edge.

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

## <a name="next-steps"></a>Další kroky

Další informace o rolích [runtime IoT Edge](iot-edge-runtime.md).

Poradce při potížích s instalací a konfigurací [běžných problémů a řešení azure iot edge](troubleshoot.md)
