---
title: Řešení potíží se službou Azure IoT Edge | Microsoft Docs
description: Řešení běžných potíží a seznámení s dovednostmi souvisejícími s řešením potíží se službou Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 413b94c1f1845e0dcda54b04882e5d6664b81380
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815490"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Běžné potíže se službou Azure IoT Edge a jejich řešení

Pokud ve vašem prostředí dochází k potížím s provozem služby Azure IoT Edge, použijte tento článek jako vodítko k jejich řešení. 

## <a name="standard-diagnostic-steps"></a>Standardní postup diagnostiky 

Když narazíte na problém, získejte další informace o stavu vašeho zařízení IoT Edge tím, že zkontrolujete protokoly kontejneru a zprávy předávané do a ze zařízení. Ke shromáždění informací použijte příkazy a nástroje uvedené v této části. 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Zkontrolujte stav IoT Edge Security Manager a jeho protokoly:

V Linuxu:
- Chcete-li zobrazit stav správce zabezpečení Edge IoT:

   ```bash
   sudo systemctl status iotedge
   ```

- Chcete-li zobrazit protokoly správce zabezpečení Edge IoT:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- Zobrazení podrobnějších protokoly správce zabezpečení Edge IoT:

   - Upravte nastavení démon iotedge:

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Aktualizujte následující řádky:
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - Restartujte démona zabezpečení IoT Edge:
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

Ve Windows:
- Chcete-li zobrazit stav správce zabezpečení Edge IoT:

   ```powershell
   Get-Service iotedge
   ```

- Chcete-li zobrazit protokoly správce zabezpečení Edge IoT:

   ```powershell
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false} |
   format-table -autosize -wrap
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Pokud není spuštěn Správce zabezpečení IoT Edge, ověřte váš konfigurační soubor yaml

> [!WARNING]
> Soubory YAML nesmí obsahovat tabulátory jako identation. Místo toho použijte 2 mezery.

V Linuxu:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Ve Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Zkontrolujte protokoly kontejneru pro problémy

Po spuštění démona zabezpečení IoT Edge, prohlédněte si protokoly kontejnerů a detekujte problémy. Začněte nasazenými kontejnery a pak si prohlédněte kontejnery, ze kterých se skládá modul runtime IoT Edge: agent Edge a centrum Edge. Protokoly agenta Edge obvykle obsahují informace o životním cyklu jednotlivých kontejnerů. Protokoly centra Edge obsahují informace o zasílání zpráv a směrování. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-edge-hub"></a>Zobrazte zprávy procházející přes Centrum Edge

Zobrazte zprávy procházející přes Centrum Edge a získejte přehled o aktualizacích vlastností zařízení s využitím podrobných protokolů z kontejnerů modulu runtime edgeAgent a edgeHub. Chcete-li zapnout podrobné protokolování na těchto kontejnerů, nastavte `RuntimeLogLevel` v konfiguračním souboru yaml. Otevřít soubor:

V Linuxu:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Ve Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Ve výchozím nastavení `agent` element bude vypadat přibližně takto:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Nahraďte `env: {}` pomocí:

> [!WARNING]
> Soubory YAML nesmí obsahovat tabulátory jako identation. Místo toho použijte 2 mezery.

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

Uložte soubor a restartujte správce zabezpečení IoT Edge.

Můžete zkontrolovat také zprávy odesílané mezi službou IoT Hub a hraničními zařízeními IoT. Tyto zprávy zobrazíte pomocí rozšíření [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) pro Visual Studio Code. Další pokyny najdete v článku [Užitečný nástroj pro vývoj se službou Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Restartujte kontejnery
Po prozkoumání v protokolech a zprávách informace, můžete zkusit restartovat kontejnerů:

```
iotedge restart <container name>
```

Restartujte kontejnerů modulu runtime IoT Edge:

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Restartovat správce zabezpečení IoT Edge

Problém je stále uchování, můžete restartováním správce zabezpečení IoT Edge.

V Linuxu:

   ```cmd
   sudo systemctl restart iotedge
   ```

Ve Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>Agent Edge se přibližně po minutě zastaví

Agent Edge se spustí, přibližně minutu je spuštěný a pak se zastaví Z protokolů vyplývá, že se agent Edge pokouší připojit ke službě IoT Hub přes rozšířený protokol řízení front zpráv (AMQP) a pak se přibližně o 30 sekund později pokouší připojit pomocí protokolu AMQP přes WebSocket. V případě selhání se agent Edge ukončí. 

Příklad protokolů agenta Edge:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Původní příčina
Konfigurace sítě v hostitelské síti brání připojení agenta Edge k síti. Agent se nejprve pokusí připojit přes protokol AMQP (port 5671). V případě selhání zkusí WebSocket (port 443).

Modul runtime IoT Edge nastaví pro každý z modulů síť, na které budou komunikovat. V Linuxu je tato síť síťovým mostem. Ve Windows využívá překlad adres (NAT). K tomuto problému častěji dochází na zařízeních s Windows využívajících kontejnery Windows a síť s překladem adres (NAT). 

### <a name="resolution"></a>Řešení
Ujistěte se, že se IP adresy přiřazené k tomuto síťovému mostu nebo síti NAT směrují do internetu. Někdy konfigurace sítě VPN na hostiteli přepíše síť IoT Edge. 

## <a name="edge-hub-fails-to-start"></a>Centrum Edge se nedaří spustit

Centrum Edge se nedaří spustit a do protokolů se zapisuje následující zpráva: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Původní příčina
Nějaký jiný proces na hostitelském počítači používá port 443. Centrum Edge mapuje porty 5671 a 443 pro použití ve scénářích brány. Toto mapování portů selže, pokud daný port již používá jiný proces. 

### <a name="resolution"></a>Řešení
Vyhledejte a zastavte proces používající port 443. Tímto procesem je obvykle webový server.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>Agent Edge nemá přístup k imagi modulu (403)
Kontejner se nedaří spustit a v protokolech agenta Edge se zobrazí chyba 403. 

### <a name="root-cause"></a>Původní příčina
Agent Edge nemá oprávnění pro přístup k imagi modulu. 

### <a name="resolution"></a>Řešení
Ujistěte se, že jsou vaše přihlašovací údaje registru správně zadána v manifestu nasazení

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Démon zabezpečení IoT Edge se nezdaří s platný název hostitele

Příkaz `sudo journalctl -u iotedge` selže a zobrazí následující zprávu: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Původní příčina
Modul runtime IoT Edge podporuje pouze názvy hostitelů, které jsou kratší než 64 znaků. To obvykle není problém pro fyzické počítače, ale může dojít, když nastavíte modul runtime na virtuálním počítači. Automaticky generované názvy hostitelů pro virtuální počítače s Windows, které jsou hostované v Azure, zejména, jsou obvykle dlouhé. 

### <a name="resolution"></a>Řešení
Když se zobrazí tato chyba, ho mohli vyřešit tak, že konfigurace názvu DNS virtuálního počítače a pak nastavení název DNS jako název hostitele v příkazu pro nastavení.

1. Na webu Azure Portal přejděte na stránku přehled vašeho virtuálního počítače. 
2. Vyberte **konfigurace** pod názvem DNS. Pokud je váš virtuální počítač už nakonfigurovaný název DNS, není nutné konfigurovat nové. 

   ![Konfigurace názvu DNS](./media/troubleshoot/configure-dns.png)

3. Zadejte hodnotu pro **popisku názvu DNS** a vyberte **Uložit**.
4. Zkopírujte nový název DNS, které by měly být ve formátu  **\<DNSnamelabel\>.\< vmlocation\>. cloudapp.azure.com**.
5. Ve virtuálním počítači použijte následující příkaz pro nastavení modulu runtime IoT Edge s názvem DNS:

   - V Linuxu:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - Ve Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problémy se stabilitou u prostředku omezené zařízení 
Můžete setkat s problémy stabilitu na omezené zařízeními, jako je Raspberry Pi, zejména v případě, že se používá jako brána. Mezi příznaky patří nedostatek paměti výjimky v modulu Centrum edge, podřízené zařízení se nemohou připojit nebo zařízení zastaví odesílání telemetrických zpráv po několik hodin.

### <a name="root-cause"></a>Původní příčina
Centrum edge, která je součástí modulu runtime edge, je optimalizován pro výkon ve výchozím nastavení a pokusí se přidělit velké množství paměti. To není ideální pro omezené hraničními zařízeními a může způsobit problémy se stabilitou.

### <a name="resolution"></a>Řešení
Centrum pro na hraničních zařízeních nastavení proměnné prostředí **OptimizeForPerformance** k **false**. Chcete-li to provést dvěma způsoby:

V uživatelském rozhraní: 

Na portálu ze *podrobnosti o zařízení*->*nastavit moduly*->*konfigurovat rozšířená nastavení modulu Runtime Edge*, vytvořte proměnnou prostředí volá se, *OptimizeForPerformance* , která je nastavena na *false* pro *Centrum Edge*.

![optimizeforperformance][img-optimize-for-perf]

**OR**

V manifestu nasazení:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```
## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Nelze získat IoT Edge démon protokolování pro Windows
Pokud se zobrazí při použití EventLogException `Get-WinEvent` na Windows, zkontrolujte položky registru.

### <a name="root-cause"></a>Původní příčina
`Get-WinEvent` Příkazu Powershellu spoléhá na položku registru s vyskytovat najít protokoly určitou `ProviderName`.

### <a name="resolution"></a>Řešení
Nastavte položku registru pro démona IoT Edge. Vytvoření **iotedge.reg** soubor s následujícím obsahem a importu v registru Windows na ni poklikáte nebo pomocí `reg import iotedge.reg` příkaz:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>Modul IoT Edge se nepodařilo odeslat zprávu do edgeHub kvůli chybě 404

Vlastní modul IoT Edge se nepodařilo odeslat zprávu do edgeHub s 404 `Module not found` chyby. Proces démon IoT Edge zobrazí následující zprávu do protokolů: 

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 ) 
```

### <a name="root-cause"></a>Původní příčina
Proces démon IoT Edge vynutí proces identifikace pro všechny moduly propojíte edgeHub z bezpečnostních důvodů. Ověřuje, že všechny zprávy modulem pocházejí z hlavní proces id modulu. Pokud zpráva je odesíláno modul z id jiného procesu než původně vytvořeno, odmítne zprávy s 404 chybovou zprávu.

### <a name="resolution"></a>Řešení
Ujistěte se, že stejné id procesu je vždy používá vlastní modul IoT Edge pro odesílání zpráv edgeHub. Například se ujistěte, že `ENTRYPOINT` místo `CMD` příkaz v souboru Docker, protože `CMD` povede k jeden proces id modulu a jiné id procesu pro příkaz prostředí bash s hlavní program, že `ENTRYPOINT` povede k id jednoho procesu.


## <a name="next-steps"></a>Další postup
Myslíte si, že jste v platformě IoT Edge našli chybu? [Odešlete problém](https://github.com/Azure/iotedge/issues), abychom mohli pokračovat ve zlepšování. 

<!-- Images -->
[img-optimize-for-perf]: ./media/troubleshoot/OptimizeForPerformanceFalse.png
