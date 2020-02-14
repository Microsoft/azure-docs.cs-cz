---
title: Konfigurace zařízení pro síť – proxy služby Azure IoT Edge | Dokumentace Microsoftu
description: Postup konfigurace modulu runtime Azure IoT Edge a všech modulů IoT Edge přístupem k Internetu pro komunikaci přes proxy server.
author: kgremban
ms.author: kgremban
ms.date: 11/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a8ee1e07dafac46467aa26f89b609cd499346974
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186597"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurace zařízení IoT Edge pro komunikaci přes proxy server

Zařízení IoT Edge odesílají požadavky HTTPS pro komunikaci se službou IoT Hub. Pokud vaše zařízení je připojené k síti, která používá proxy server, budete muset nakonfigurovat modul runtime IoT Edge pro komunikaci přes server. Proxy servery mohou také ovlivnit jednotlivé moduly IoT Edge, pokud provádějí požadavky HTTP nebo HTTPS, které nejsou směrovány prostřednictvím centra IoT Edge.

Tento článek vás provede následujícími čtyřmi kroky ke konfiguraci a správě IoT Edge zařízení za proxy server:

1. **Nainstalujte do svého zařízení modul runtime IoT Edge.**

   Instalační skripty pro IoT Edge najdou balíčky a soubory z Internetu, takže vaše zařízení musí komunikovat přes proxy server, aby tyto požadavky učinily. Podrobný postup najdete v části [Instalace modulu runtime prostřednictvím serveru proxy](#install-the-runtime-through-a-proxy) v tomto článku. Instalační skript pro zařízení s Windows také nabízí možnost [offline instalace](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation) .

   Tento krok je jednorázový proces, který se v zařízení IoT Edge prováděl při prvním nastavení. Při aktualizaci modulu runtime IoT Edge se vyžadují také stejná připojení.

2. **Nakonfigurujte démona Docker a IoT Edge démona na vašem zařízení.**

   IoT Edge používá na zařízení dva procesy démonů, které potřebují vytvářet webové požadavky prostřednictvím proxy server. Proces démona IoT Edge zodpovídá za komunikaci s IoT Hub. Démon Moby je zodpovědný za správu kontejnerů, takže komunikuje s registry kontejnerů. Podrobný postup najdete v části [Konfigurace démonů](#configure-the-daemons) v tomto článku.

   Tento krok je jednorázový proces, který se v zařízení IoT Edge prováděl při prvním nastavení.

3. **Nakonfigurujte vlastnosti agenta IoT Edge v souboru config. yaml na vašem zařízení.**

   IoT Edge démon na začátku spustí modul edgeAgent, ale modul edgeAgent zodpovídá za načtení manifestu nasazení z IoT Hub a spouštění všech ostatních modulů. Aby mohl agent IoT Edge vytvořit počáteční připojení k IoT Hub, nakonfigurujte proměnné prostředí modulu edgeAgent ručně na samotném zařízení. Po počátečním připojení můžete modul edgeAgent nakonfigurovat vzdáleně. Podrobný postup najdete v části [Konfigurace agenta IoT Edge](#configure-the-iot-edge-agent) v tomto článku.

   Tento krok je jednorázový proces, který se v zařízení IoT Edge prováděl při prvním nastavení.

4. **Pro všechna nasazení v budoucích modulech nastavte proměnné prostředí pro libovolný modul, který komunikuje prostřednictvím proxy serveru.**

   Po nastavení zařízení IoT Edge a připojení k IoT Hub prostřednictvím proxy server je potřeba zachovat připojení ve všech budoucích nasazeních modulů. Podrobný postup najdete v části [Konfigurace manifestů nasazení](#configure-deployment-manifests) v tomto článku.

   Tento krok je nepřetržitě prováděný proces, takže každý nový modul nebo aktualizace nasazení udržuje schopnost zařízení komunikovat prostřednictvím proxy server.

## <a name="know-your-proxy-url"></a>Znát adresu URL vašeho proxy serveru

Než začnete s některým z kroků v tomto článku, musíte znát adresu URL vašeho proxy serveru.

Adresy URL proxy serveru mají následující formát: **protokol**://**proxy_host**:**proxy_port**.

* **Protokol** je buď http, nebo HTTPS. Démon Docker může použít buď protokol, v závislosti na nastavení registru kontejneru, ale IoT Edge démon a kontejnery modulu runtime vždy používají protokol HTTP pro připojení k proxy serveru.

* **Proxy_host** je adresa proxy server. Pokud vaše proxy server vyžaduje ověření, můžete přihlašovací údaje zadat v rámci hostitele proxy v následujícím formátu: **uživatel**:**heslo**\@**proxy_host**.

* **Proxy_port** je síťový port, na kterém proxy server reaguje na síťový provoz.

## <a name="install-the-runtime-through-a-proxy"></a>Instalace modulu runtime prostřednictvím proxy serveru

Bez ohledu na to, jestli je zařízení IoT Edge spuštěné v systému Windows nebo Linux, budete potřebovat přístup k instalačním balíčkům přes proxy server. V závislosti na vašem operačním systému postupujte podle pokynů k instalaci modulu runtime IoT Edge pomocí proxy server.

### <a name="linux-devices"></a>Linux zařízení

Pokud instalujete modul runtime IoT Edge na zařízení s Linuxem, konfigurace Správce balíčků na serveru proxy pro přístup k instalačnímu balíčku. [Nastavte například apt-get pro použití HTTP-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Po nakonfigurování správce balíčků postupujte podle pokynů v části [Instalace modulu runtime Azure IoT Edge v systému Linux](how-to-install-iot-edge-linux.md) obvyklým způsobem.

### <a name="windows-devices"></a>Zařízení Windows

Pokud instalujete modul runtime IoT Edge na zařízení s Windows, budete muset projít proxy server dvakrát. První připojení stáhne soubor skriptu instalačního programu a druhé připojení probíhá během instalace ke stažení potřebných součástí. V nastavení systému Windows můžete nakonfigurovat informace o proxy serveru nebo informace o proxy serveru zahrnout přímo do příkazů PowerShellu.

Následující kroky ukazují příklad instalace Windows pomocí argumentu `-proxy`:

1. Příkaz Invoke-WebRequest potřebuje pro přístup ke skriptu instalačního programu informace o proxy serveru. Pak příkaz Deploy-IoTEdge potřebuje pro stažení instalačních souborů informace o proxy serveru.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Příkaz Initialize-IoTEdge není potřeba projít proxy server, takže druhý krok vyžaduje jenom informace o proxy serveru pro Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Pokud máte složitá pověření pro proxy server, která nelze zahrnout do adresy URL, použijte parametr `-ProxyCredential` v rámci `-InvokeWebRequestParameters`. Například:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Další informace o parametrech proxy najdete v tématu [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Další informace o možnostech instalace systému Windows, včetně offline instalace, najdete v tématu [Install Azure IoT Edge runtime v systému Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Konfigurace procesy daemon

IoT Edge spoléhá na dva démony spuštěné v zařízení IoT Edge. Démon Moby vydává webové požadavky na vyžádání imagí kontejneru z registrů kontejnerů. Proces démon IoT Edge umožňuje webové požadavky na komunikaci se službou IoT Hub.

Moby i démony IoT Edge musí být nakonfigurované tak, aby používaly proxy server pro průběžné funkce zařízení. Tento krok probíhá na zařízení IoT Edge při počátečním nastavení zařízení.

### <a name="moby-daemon"></a>Démon Moby

Vzhledem k tomu, že Moby je postaven na Docker, použijte dokumentaci k Docker ke konfiguraci démona Moby s proměnnými prostředí. Většina registrů kontejnerů (včetně Dockerhubu a kontejnerů Azure Container Registry) podporuje požadavky HTTPS, takže parametr, který byste měli nastavit, je **HTTPS_PROXY**. Pokud nahráváte image z registru, který nepodporuje protokol TLS (Transport Layer Security), měli byste nastavit parametr **http_proxy** .

Vyberte článek, který se vztahuje na operační systém IoT Edge zařízení:

* [Konfigurace démona Docker v systému Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) Rozhraní Moby démona v zařízeních se systémem Linux udržuje název Docker.
* [Konfigurace démona Docker ve Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) Démon Moby na zařízeních s Windows se nazývá iotedge-Moby. Názvy se liší, protože je možné spustit na zařízení s Windows paralelní plochu i Moby.

### <a name="iot-edge-daemon"></a>Démon IoT Edge

Démon IoT Edge je nakonfigurován podobným způsobem jako démon Moby. Použijte následující postup k nastavení proměnné prostředí pro službu založený na operačním systému.

Démon IoT Edge vždy používá protokol HTTPS k posílání požadavků do IoT Hub.

#### <a name="linux"></a>Linux

Otevřete editor, v terminálu konfigurace démona IoT Edge.

```bash
sudo systemctl edit iotedge
```

Zadejte následující text a nahraďte **\<adresu URL proxy serveru >** adresou a portem proxy server. Potom uložte a zavřete.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Aktualizujte Service Manager, aby se vybrala nová konfigurace pro IoT Edge.

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

Otevřete okno Powershellu jako správce a spusťte následující příkaz, který úprava registru pomocí nové proměnné prostředí. Adresu **url\<proxy serveru** nahraďte > adresou a portem proxy server.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Restartujte IoT Edge, aby se změny projevily.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Konfigurace agenta IoT Edge

Agent IoT Edge je prvním modulem, který se má spustit na jakémkoli IoT Edgem zařízení. Je spuštěn poprvé na základě informací v souboru config.yaml IoT Edge. Agent IoT Edge se pak připojí k IoT Hub a načte manifesty nasazení, které určují, jaké další moduly by měly být na zařízení nasazeny.

Tento krok probíhá na zařízení IoT Edge při počátečním nastavení zařízení.

1. Otevřete soubor config.yaml na zařízení IoT Edge. V systémech Linux se tento soubor nachází na adrese **/etc/iotedge/config.yaml**. V systémech Windows se tento soubor nachází na adrese **C:\ProgramData\iotedge\config.yaml**. Konfigurační soubor je chráněný, takže je třeba oprávnění správce pro přístup k ní. V systémech Linux použijte příkaz `sudo` před otevřením souboru v upřednostňovaném textovém editoru. Ve Windows otevřete textový editor, jako je Poznámkový blok jako správce, a pak otevřete soubor.

2. V souboru config. yaml Najděte oddíl **Specifikace modulu Edge agent** . Definice agenta IoT Edge zahrnuje parametr **ENV** , kde můžete přidat proměnné prostředí.

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

Jakmile je vaše zařízení IoT Edge nakonfigurované pro práci s proxy server, musíte v budoucích manifestech nasazení pokračovat v deklaraci proměnných prostředí. Manifesty nasazení můžete upravit buď pomocí Průvodce Azure Portal, nebo úpravou souboru JSON manifestu nasazení.

Vždy nakonfigurujte dva běhové moduly, edgeAgent a edgeHub, aby komunikovaly prostřednictvím proxy server, aby se mohly udržovat připojení IoT Hub. Pokud odeberete informace o proxy serveru z modulu edgeAgent, jediný způsob, jak obnovit připojení, je úpravou souboru config. yaml v zařízení, jak je popsáno v předchozí části.

Další IoT Edge moduly, které se připojují k Internetu, by měly být nakonfigurované tak, aby komunikovaly prostřednictvím proxy server. Moduly, které směrují své zprávy přes edgeHub nebo které komunikují pouze s jinými moduly v zařízení, ale nepotřebují podrobnosti proxy server.

Tento krok probíhá po celou dobu životnosti IoT Edgeho zařízení.

### <a name="azure-portal"></a>Azure Portal

Použijete-li průvodce **nastavením modulů** k vytvoření nasazení pro IoT Edge zařízení, každý modul obsahuje oddíl **proměnné prostředí** , které můžete použít ke konfiguraci proxy server připojení.

Chcete-li nakonfigurovat IoT Edge agenta a moduly IoT Edge centra, vyberte v prvním kroku průvodce možnost **nastavení modulu runtime** .

![Konfigurovat upřesňující nastavení modulu runtime Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Přidejte proměnnou prostředí **https_proxy** do definice modulu IoT Edge agenta a IoT Edge. Pokud jste do souboru config. yaml v IoT Edge zařízení zahrnuli proměnnou prostředí **UpstreamProtocol** , přidejte ji taky do definice modulu IoT Edge agenta.

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
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Pokud jste zahrnuli proměnnou prostředí **UpstreamProtocol** do souboru config. yaml na zařízení IoT Edge, přidejte ji také do definice modulu IoT Edge agenta.

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

Přečtěte si další informace o rolích [modulu runtime IoT Edge](iot-edge-runtime.md).

Řešení potíží s instalací a konfigurací pomocí [běžných problémů a řešení pro Azure IoT Edge](troubleshoot.md)
