---
title: Konfigurace zařízení pro síťové proxy-Azure IoT Edge | Microsoft Docs
description: Jak nakonfigurovat Azure IoT Edge runtime a všechny internetové IoT Edge moduly, které budou komunikovat prostřednictvím proxy server.
author: kgremban
ms.author: kgremban
ms.date: 09/03/2020
ms.topic: how-to
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- contperfq1
ms.openlocfilehash: c39ce2bed63b6efb6224e0e27fdb1104ef7a5ec8
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862390"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurace zařízení IoT Edge tak, aby komunikovalo přes proxy server

IoT Edge zařízení odesílají požadavky HTTPS ke komunikaci s IoT Hub. Pokud je zařízení připojené k síti, která používá proxy server, je nutné nakonfigurovat modul runtime IoT Edge, aby komunikoval prostřednictvím serveru. Proxy servery mohou také ovlivnit jednotlivé moduly IoT Edge, pokud provádějí požadavky HTTP nebo HTTPS, které nejsou směrovány prostřednictvím centra IoT Edge.

Tento článek vás provede následujícími čtyřmi kroky ke konfiguraci a správě IoT Edge zařízení za proxy server:

1. [**Instalace modulu runtime IoT Edge na zařízení**](#install-the-runtime-through-a-proxy)

   Instalační skripty pro IoT Edge najdou balíčky a soubory z Internetu, takže vaše zařízení musí komunikovat přes proxy server, aby tyto požadavky učinily. Instalační skript pro zařízení s Windows také nabízí možnost offline instalace.

   Tento krok je jednorázový proces konfigurace zařízení IoT Edge při jeho prvním nastavení. Při aktualizaci modulu runtime IoT Edge se vyžadují také stejná připojení.

2. [**Konfigurace démona Docker a procesu IoT Edge démona v zařízení**](#configure-the-daemons)

   IoT Edge používá na zařízení dva procesy démonů, které potřebují vytvářet webové požadavky prostřednictvím proxy server. Proces démona IoT Edge zodpovídá za komunikaci s IoT Hub. Démon Moby je zodpovědný za správu kontejnerů, takže komunikuje s registry kontejnerů.

   Tento krok je jednorázový proces konfigurace zařízení IoT Edge při jeho prvním nastavení.

3. [**Konfigurace vlastností IoT Edge agenta v souboru config. yaml na vašem zařízení**](#configure-the-iot-edge-agent)

   Proces IoT Edge démon zpočátku spustí modul edgeAgent. Pak modul edgeAgent načte manifest nasazení z IoT Hub a spustí všechny ostatní moduly. Aby mohl agent IoT Edge vytvořit počáteční připojení k IoT Hub, nakonfigurujte proměnné prostředí modulu edgeAgent ručně na samotném zařízení. Po počátečním připojení můžete modul edgeAgent nakonfigurovat vzdáleně.

   Tento krok je jednorázový proces konfigurace zařízení IoT Edge při jeho prvním nastavení.

4. [**Pro všechna nasazení v budoucnu nastavte proměnné prostředí pro libovolný modul, který komunikuje prostřednictvím proxy serveru.**](#configure-deployment-manifests)

   Po nastavení zařízení IoT Edge a připojení k IoT Hub prostřednictvím proxy server je potřeba zachovat připojení ve všech budoucích nasazeních modulů.

   Tento krok se provádí vzdáleně, takže každý nový modul nebo aktualizace nasazení udržuje schopnost zařízení komunikovat prostřednictvím proxy server.

## <a name="know-your-proxy-url"></a>Znát adresu URL proxy serveru

Než začnete s některým z kroků v tomto článku, musíte znát adresu URL vašeho proxy serveru.

Adresy URL proxy serveru mají následující formát: **protokol**://**proxy_host**:**proxy_port**.

* **Protokol** je buď http, nebo HTTPS. Démon Docker může použít buď protokol, v závislosti na nastavení registru kontejneru, ale IoT Edge démon a kontejnery modulu runtime vždy používají protokol HTTP pro připojení k proxy serveru.

* **Proxy_host** je adresa proxy server. Pokud vaše proxy server vyžaduje ověření, můžete přihlašovací údaje zadat v rámci hostitele proxy v následujícím formátu: **uživatel**:**heslo** \@ **proxy_host**.

* **Proxy_port** je síťový port, na kterém proxy server reaguje na síťový provoz.

## <a name="install-the-runtime-through-a-proxy"></a>Instalace modulu runtime prostřednictvím proxy serveru

Bez ohledu na to, jestli je zařízení IoT Edge spuštěné v systému Windows nebo Linux, budete potřebovat přístup k instalačním balíčkům přes proxy server. V závislosti na vašem operačním systému postupujte podle pokynů k instalaci modulu runtime IoT Edge pomocí proxy server.

### <a name="linux-devices"></a>Zařízení se systémem Linux

Pokud instalujete modul runtime IoT Edge na zařízení se systémem Linux, nakonfigurujte Správce balíčků tak, aby procházel proxy server pro přístup k instalačnímu balíčku. [Nastavte například apt-get pro použití HTTP-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Po nakonfigurování správce balíčků postupujte podle pokynů v části [Instalace modulu runtime Azure IoT Edge](how-to-install-iot-edge.md) jako obvykle.

### <a name="windows-devices"></a>Zařízení s Windows

Pokud instalujete modul runtime IoT Edge na zařízení s Windows, budete muset projít proxy server dvakrát. První připojení stáhne soubor skriptu instalačního programu a druhé připojení probíhá během instalace ke stažení potřebných součástí. V nastavení systému Windows můžete nakonfigurovat informace o proxy serveru nebo informace o proxy serveru zahrnout přímo do příkazů PowerShellu.

Následující kroky ukazují příklad instalace Windows pomocí `-proxy` argumentu:

1. Příkaz Invoke-WebRequest potřebuje pro přístup ke skriptu instalačního programu informace o proxy serveru. Příkaz Deploy-IoTEdge pak potřebuje informace o proxy serveru ke stažení instalačních souborů.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Příkaz Initialize-IoTEdge nepotřebuje projít proxy server, takže druhý krok vyžaduje jenom informace o proxy serveru pro Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Máte-li složitá pověření pro proxy server, která nelze zahrnout do adresy URL, použijte `-ProxyCredential` parametr v rámci `-InvokeWebRequestParameters` . Příklad:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Další informace o parametrech proxy najdete v tématu [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest). Další informace o parametrech instalace systému Windows najdete v tématu [skripty PowerShellu pro IoT Edge ve Windows](reference-windows-scripts.md).

## <a name="configure-the-daemons"></a>Konfigurace démonů

IoT Edge spoléhá na dva démony spuštěné v zařízení IoT Edge. Démon Moby vydává webové požadavky na vyžádání imagí kontejneru z registrů kontejnerů. Démon IoT Edge provádí webové požadavky na komunikaci s IoT Hub.

Moby i démony IoT Edge musí být nakonfigurované tak, aby používaly proxy server pro průběžné funkce zařízení. Tento krok probíhá na zařízení IoT Edge při počátečním nastavení zařízení.

### <a name="moby-daemon"></a>Démon Moby

Vzhledem k tomu, že Moby je postaven na Docker, použijte dokumentaci k Docker ke konfiguraci démona Moby s proměnnými prostředí. Většina registrů kontejnerů (včetně Dockerhubu a kontejnerů Azure Container Registry) podporuje požadavky HTTPS, takže parametr, který byste měli nastavit, je **HTTPS_PROXY**. Pokud nahráváte image z registru, který nepodporuje protokol TLS (Transport Layer Security), měli byste nastavit parametr **http_proxy** .

Vyberte článek, který se vztahuje na operační systém IoT Edge zařízení:

* [Konfigurace démona Docker v systému Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) Rozhraní Moby démona v zařízeních se systémem Linux udržuje název Docker.
* [Konfigurace démona Docker ve Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) Démon Moby na zařízeních s Windows se nazývá iotedge-Moby. Názvy se liší, protože je možné spustit na zařízení s Windows paralelní plochu i Moby.

### <a name="iot-edge-daemon"></a>Démon IoT Edge

Démon IoT Edge je nakonfigurován podobným způsobem jako démon Moby. Pomocí následujících kroků nastavte proměnnou prostředí pro službu na základě vašeho operačního systému.

Démon IoT Edge vždy používá protokol HTTPS k posílání požadavků do IoT Hub.

#### <a name="linux"></a>Linux

Otevřete Editor v terminálu a nakonfigurujte IoT Edge démon.

```bash
sudo systemctl edit iotedge
```

Zadejte následující text a nahraďte **\<proxy URL>** ho adresou proxy server a portem. Pak operaci uložte a ukončete.

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

Ověřte, zda byla proměnná prostředí vytvořena a byla načtena nová konfigurace.

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Otevřete okno PowerShellu jako správce a spusťte následující příkaz pro úpravu registru s novou proměnnou prostředí. Nahraďte **\<proxy url>** adresou proxy server a portem.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Restartujte IoT Edge, aby se změny projevily.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Konfigurace agenta IoT Edge

Agent IoT Edge je prvním modulem, který se má spustit na jakémkoli IoT Edgem zařízení. Spouští se poprvé na základě informací v souboru config. yaml IoT Edge. Agent IoT Edge se pak připojí k IoT Hub a načte manifesty nasazení, které určují, jaké další moduly by měly být na zařízení nasazeny.

Tento krok probíhá na zařízení IoT Edge při počátečním nastavení zařízení.

1. Otevřete soubor config. yaml na zařízení IoT Edge. V systémech Linux se tento soubor nachází na adrese **/etc/iotedge/config.yaml**. V systémech Windows se tento soubor nachází na adrese **C:\ProgramData\iotedge\config.yaml**. Konfigurační soubor je chráněný, takže pro přístup k němu potřebujete oprávnění správce. V systémech Linux použijte `sudo` příkaz před otevřením souboru v upřednostňovaném textovém editoru. Ve Windows otevřete textový editor, jako je Poznámkový blok jako správce, a pak otevřete soubor.

2. V souboru config. yaml Najděte oddíl **Specifikace modulu Edge agent** . Definice agenta IoT Edge zahrnuje parametr **ENV** , kde můžete přidat proměnné prostředí.

3. Odeberte složené závorky, které jsou zástupné symboly pro parametr ENV, a přidejte novou proměnnou na nový řádek. Nezapomeňte, že odsazení v YAML jsou dvě mezery.

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. Modul runtime IoT Edge ve výchozím nastavení používá ke komunikaci s IoT Hub AMQP. Některé proxy servery blokují porty AMQP. Pokud je to tento případ, musíte také nakonfigurovat edgeAgent tak, aby používal AMQP přes WebSocket. Přidejte druhou proměnnou prostředí.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![definice edgeAgent s proměnnými prostředí](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Uložte změny do souboru config. yaml a zavřete Editor. Restartujte IoT Edge, aby se změny projevily.

   * Linux:

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Konfigurace manifestů nasazení  

Jakmile je vaše zařízení IoT Edge nakonfigurované pro práci s proxy server, musíte v budoucích manifestech nasazení pokračovat v deklaraci HTTPS_PROXY proměnné prostředí. Manifesty nasazení můžete upravit buď pomocí Průvodce Azure Portal, nebo úpravou souboru JSON manifestu nasazení.

Vždy nakonfigurujte dva běhové moduly, edgeAgent a edgeHub, aby komunikovaly prostřednictvím proxy server, aby se mohly udržovat připojení IoT Hub. Pokud odeberete informace o proxy serveru z modulu edgeAgent, jediný způsob, jak obnovit připojení, je úpravou souboru config. yaml v zařízení, jak je popsáno v předchozí části.

Kromě modulů edgeAgent a edgeHub mohou další moduly vyžadovat konfiguraci proxy serveru. Moduly, které potřebují přístup k prostředkům Azure, kromě IoT Hub, jako je BLOB Storage, musí mít HTTPS_PROXY proměnnou určenou v souboru manifestu nasazení.

Následující postup se vztahuje po celou dobu životnosti IoT Edgeho zařízení.

### <a name="azure-portal"></a>portál Azure

Když použijete průvodce **nastavením modulů** k vytvoření nasazení pro IoT Edge zařízení, každý modul obsahuje oddíl **proměnné prostředí** , ve kterém můžete nakonfigurovat proxy server připojení.

Chcete-li nakonfigurovat IoT Edge agenta a moduly IoT Edge centra, vyberte v prvním kroku průvodce možnost **nastavení modulu runtime** .

![Konfigurace pokročilých nastavení modulu runtime Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Přidejte proměnnou prostředí **https_proxy** do definice modulu IoT Edge agenta a IoT Edge. Pokud jste do souboru config. yaml v IoT Edge zařízení zahrnuli proměnnou prostředí **UpstreamProtocol** , přidejte ji taky do definice modulu IoT Edge agenta.

![Nastavit https_proxy proměnnou prostředí](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Všechny ostatní moduly, které přidáte do manifestu nasazení, budou následovat po stejném vzoru.

### <a name="json-deployment-manifest-files"></a>Soubory manifestu nasazení JSON

Pokud vytváříte nasazení pro IoT Edge zařízení pomocí šablon v Visual Studio Code nebo ručním vytvářením souborů JSON, můžete proměnné prostředí přidat přímo do každé definice modulu.

Použijte následující formát JSON:

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

V případě obsažených proměnných prostředí by definice modulu měla vypadat jako v následujícím příkladu edgeHub:

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

## <a name="working-with-traffic-inspecting-proxies"></a>Práce s prohlížením provozu – kontroly proxy serverů

Pokud má proxy server, který se pokoušíte použít, provádět kontrolu provozu na připojeních zabezpečených protokolem TLS, je důležité si uvědomit, že ověřování pomocí X. 509 certifikáty nefunguje. IoT Edge vytvoří kanál TLS zašifrovaný na konci se zadaným certifikátem a klíčem. Pokud je tento kanál pro kontrolu provozu porušený, proxy server nemůže kanál znovu vytvořit se správnými přihlašovacími údaji a IoT Hub a služba IoT Hub Device Provisioning vrátí `Unauthorized` chybu.

Pokud chcete použít proxy server, který provádí kontrolu provozu, musíte použít buď ověřování sdíleného přístupového podpisu, nebo IoT Hub a službu IoT Hub Device Provisioning, kterou jste přidali do povolených, aby se předešlo kontrole.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o rolích [modulu runtime IoT Edge](iot-edge-runtime.md).

Řešení potíží s instalací a konfigurací pomocí [běžných problémů a řešení pro Azure IoT Edge](troubleshoot.md)
