---
title: Řešení potíží – Azure IoT Edge | Dokumentace Microsoftu
description: Další standardní diagnostiky dovednosti pro Azure IoT Edge, třeba načítání protokoly a stavu komponenty použijte tento článek a při řešení běžných problémů
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cc05d820a409500e66e2525654954e4dddf48e4b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457260"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Běžné potíže se službou Azure IoT Edge a jejich řešení

Pokud ve vašem prostředí dochází k potížím s provozem služby Azure IoT Edge, použijte tento článek jako vodítko k jejich řešení.

## <a name="run-the-iotedge-check-command"></a>Spusťte příkaz ' check ' iotedge

Prvním krokem při řešení potíží IoT Edge by měl být použití příkazu `check`, který provádí shromažďování testů konfigurace a připojení pro běžné problémy. Příkaz `check` je k dispozici ve [verzi 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) a novější.

Příkaz `check` můžete spustit následujícím způsobem nebo můžete použít příznak `--help` a zobrazit úplný seznam možností:

* V Linuxu:

  ```bash
  sudo iotedge check
  ```

* Ve Windows:

  ```powershell
  iotedge check
  ```

Typy kontrol spouštěných pomocí nástroje lze klasifikovat jako:

* Kontroly konfigurace: kontroluje podrobnosti, které by mohly bránit hraničním zařízením v připojení ke cloudu, včetně problémů s *config. yaml* a modulem kontejnerů.
* Kontroly připojení: ověří, že modul runtime IoT Edge má přístup k portům na hostitelském zařízení a všechny IoT Edge součásti se mohou připojit k IoT Hub.
* Kontroly připravenosti výroby: vyhledá Doporučené provozní postupy, jako je například stav certifikátů certifikační autorita zařízení (CA) a konfigurace souboru protokolu modulu.

Úplný seznam diagnostických kontrol najdete v tématu [integrovaná funkce řešení potíží](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="standard-diagnostic-steps"></a>Standardní postup diagnostiky

Pokud narazíte na problém, můžete získat další informace o stavu zařízení IoT Edge. Projděte si protokoly kontejnerů a zprávy, které předávají a ze zařízení. Ke shromáždění informací použijte příkazy a nástroje uvedené v této části.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Kontrolovat stav IoT Edge Security Manageru a jeho protokolů

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
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Pokud není spuštěn Správce zabezpečení IoT Edge, ověřte váš konfigurační soubor yaml

> [!WARNING]
> Soubory YAML nemůžou jako odsazení obsahovat tabulátory. Místo toho použijte 2 mezery.

V Linuxu:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Ve Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Zkontrolujte protokoly kontejneru pro problémy

Po spuštění démona zabezpečení IoT Edge, prohlédněte si protokoly kontejnerů a detekujte problémy. Začněte s nasazenými kontejnery a pak se podívejte do kontejnerů, které tvoří modul runtime IoT Edge: edgeAgent a edgeHub. Protokoly agenta IoT Edge obvykle poskytují informace o životním cyklu každého kontejneru. Protokoly centra IoT Edge poskytují informace o zasílání zpráv a směrování. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Zobrazení zpráv, které procházejí centrem centra IoT Edge

Zprávy můžete zobrazit prostřednictvím centra IoT Edge a shromažďovat přehledy z podrobných protokolů z kontejnerů modulu runtime. Pokud chcete zapnout podrobné protokoly těchto kontejnerů, nastavte `RuntimeLogLevel` v konfiguračním souboru YAML. Otevřít soubor:

V Linuxu:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Ve Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Ve výchozím nastavení bude element `agent` vypadat jako v následujícím příkladu:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Nahradit `env: {}`:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > Soubory YAML nesmí obsahovat tabulátory jako identation. Místo toho použijte 2 mezery.

Uložte soubor a restartujte správce zabezpečení IoT Edge.

Můžete zkontrolovat také zprávy odesílané mezi službou IoT Hub a hraničními zařízeními IoT. Zobrazte tyto zprávy pomocí rozšíření [azure IoT Hub Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dříve rozšíření Azure IoT Toolkit) pro Visual Studio Code. Další informace najdete v tématu [praktický nástroj při vývoji s využitím Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

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

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>Agent IoT Edge se zastaví po přibližně minutě.

Modul edgeAgent se spustí a úspěšně se spustí přibližně minutu a pak se zastaví. Protokoly signalizují, že se agent IoT Edge pokusí připojit k IoT Hub přes AMQP a pak se pokusí připojit pomocí AMQP přes WebSocket. Pokud se to nepovede, agent IoT Edge se ukončí. 

Příklady protokolů edgeAgent:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

**Původní příčina**

Konfigurace sítě v hostitelské síti brání tomu, aby agent IoT Edge dosáhnout sítě. Agent se nejprve pokusí připojit přes protokol AMQP (port 5671). Pokud se nepovede, zkusí Websocket (port 443).

Modul runtime IoT Edge nastaví pro každý z modulů síť, na které budou komunikovat. V Linuxu je tato síť síťovým mostem. Ve Windows využívá překlad adres (NAT). K tomuto problému častěji dochází na zařízeních s Windows využívajících kontejnery Windows a síť s překladem adres (NAT). 

**Rozhodnutí**

Ujistěte se, že se IP adresy přiřazené k tomuto síťovému mostu nebo síti NAT směrují do internetu. Někdy konfigurace sítě VPN na hostiteli přepíše síť IoT Edge. 

## <a name="iot-edge-hub-fails-to-start"></a>Nepodařilo se spustit centrum IoT Edge.

Nepodařilo se spustit modul edgeHub a vytiskne následující zprávu do protokolů: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

**Původní příčina**

Nějaký jiný proces na hostitelském počítači používá port 443. Rozbočovač IoT Edge mapuje porty 5671 a 443 pro použití ve scénářích bran. Toto mapování portů selže, pokud daný port již používá jiný proces. 

**Rozhodnutí**

Vyhledejte a zastavte proces používající port 443. Tímto procesem je obvykle webový server.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>Agent IoT Edge nemá přístup k imagi modulu (403).
Nepodařilo se spustit kontejner a v protokolech edgeAgent se zobrazí chyba 403. 

**Původní příčina**

Agent IoT Edge nemá oprávnění pro přístup k imagi modulu. 

**Rozhodnutí**

Ujistěte se, že jsou vaše přihlašovací údaje registru správně zadána v manifestu nasazení

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Démon zabezpečení IoT Edge se nezdaří s platný název hostitele

Příkaz `sudo journalctl -u iotedge` se nezdařil a vytiskne následující zprávu: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Původní příčina**

Modul runtime IoT Edge podporuje pouze názvy hostitelů, které jsou kratší než 64 znaků. Fyzické počítače obvykle nemusí dlouhé názvy hostitelů, ale tento problém je běžnější na virtuálním počítači. Automaticky generované názvy hostitelů pro virtuální počítače s Windows, které jsou hostované v Azure, zejména, jsou obvykle dlouhé. 

**Rozhodnutí**

Když se zobrazí tato chyba, ho mohli vyřešit tak, že konfigurace názvu DNS virtuálního počítače a pak nastavení název DNS jako název hostitele v příkazu pro nastavení.

1. Na webu Azure Portal přejděte na stránku přehled vašeho virtuálního počítače. 
2. V části název DNS vyberte **Konfigurovat** . Pokud je váš virtuální počítač už nakonfigurovaný název DNS, není nutné konfigurovat nové. 

   ![Nakonfigurujte název DNS virtuálního počítače](./media/troubleshoot/configure-dns.png)

3. Zadejte hodnotu **jmenovky názvu DNS** a vyberte **Uložit**.
4. Zkopírujte nový název DNS, který by měl být ve formátu **\<DNSnamelabel\>.\<vmlocation\>. cloudapp.Azure.com**.
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

**Původní příčina**

Rozbočovač IoT Edge, který je součástí modulu runtime IoT Edge, je ve výchozím nastavení optimalizován pro výkon a pokusí se přidělit velké bloky paměti. Tato optimalizace není ideální pro omezené hraničními zařízeními a může způsobit problémy se stabilitou.

**Rozhodnutí**

Pro Centrum IoT Edge nastavte proměnnou prostředí **OptimizeForPerformance** na **false**. Chcete-li to provést dvěma způsoby:

V uživatelském rozhraní: 

Na portálu přejděte na **Podrobnosti o zařízení** > **nastavit moduly** > **nakonfigurovat Pokročilá nastavení modulu runtime Edge**. Vytvořte proměnnou prostředí pro modul hraničního centra s názvem *OptimizeForPerformance* , který je nastaven na *hodnotu false*.

![OptimizeForPerformance nastavena na hodnotu false](./media/troubleshoot/optimizeforperformance-false.png)

**ANI**

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
Pokud při použití `Get-WinEvent` ve Windows dojde k EventLogException, podívejte se na položky registru.

**Původní příčina**

Příkaz `Get-WinEvent` PowerShellu spoléhá na přítomnost položky registru pro vyhledání protokolů konkrétní `ProviderName`.

**Rozhodnutí**

Nastavte položku registru pro démona IoT Edge. Vytvořte soubor **iotedge. reg** s následujícím obsahem a naimportujte ho do registru Windows dvojitým kliknutím na něj nebo pomocí příkazu `reg import iotedge.reg`:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>Modul IoT Edge se nepodařilo odeslat zprávu do edgeHub kvůli chybě 404

Vlastní modul IoT Edge nedokáže odeslat zprávu do edgeHub s chybou 404 `Module not found`. Proces démon IoT Edge zobrazí následující zprávu do protokolů: 

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 ) 
```

**Původní příčina**

Proces démon IoT Edge vynutí proces identifikace pro všechny moduly propojíte edgeHub z bezpečnostních důvodů. Ověřuje, že všechny zprávy modulem pocházejí z hlavní proces ID modulu. Pokud zpráva je odesíláno modul z ID jiného procesu než původně vytvořeno, odmítne zprávy s 404 chybovou zprávu.

**Rozhodnutí**

Od verze 1.0.7 jsou všechny procesy modulů autorizované pro připojení. Pokud upgrade na 1.0.7 není možný, proveďte následující kroky. Další informace najdete v tématu protokol [změn verze 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Ujistěte se, že stejné ID procesu je vždy používá vlastní modul IoT Edge pro odesílání zpráv edgeHub. Ujistěte se například, že `ENTRYPOINT` místo příkazu `CMD` v souboru Docker, protože `CMD` povede k jednomu ID procesu pro modul a další ID procesu pro příkaz bash, který spouští hlavní program, zatímco `ENTRYPOINT` vede k jednomu ID procesu.


## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Konfigurace pravidla brány firewall a portů pro nasazení IoT Edge
Azure IoT Edge umožňuje komunikaci z místního serveru do cloudu Azure pomocí podporovaných protokolů IoT Hub najdete v tématu [Volba komunikačního protokolu](../iot-hub/iot-hub-devguide-protocols.md). Pro zvýšení zabezpečení komunikačních kanálů mezi Azure IoT Edge a Centrum IoT Azure jsou vždy nakonfigurované jako odchozí. Tato konfigurace je založená na [vzoru komunikace s asistencí služeb](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), který minimalizuje plochu pro útok na škodlivou entitu k prozkoumávání. Příchozí komunikace je pouze požadovaná u konkrétních scénářů, kde Azure IoT Hub potřebuje k odesílání zpráv zařízení Azure IoT Edge. Zprávy typu cloud zařízení jsou chráněny pomocí zabezpečené kanály TLS a můžete dál zabezpečit pomocí certifikátů X.509 a modulů zařízení TPM. Azure IoT Edge Security Manager určuje, jak může být tato komunikace navázána, viz [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

I když IoT Edge poskytuje rozšířené konfigurace pro zabezpečení modulu runtime Azure IoT Edge a nasadit moduly, je stále závislá na základní konfiguraci počítače a sítě. Proto je nutné zajistit, aby byla pro zabezpečená komunikace s cloudovou komunikací nastavena správná pravidla sítě a brány firewall. Následující tabulku lze použít jako vodítko při konfiguraci pravidel brány firewall pro základní servery, kde je hostovaný Azure IoT Edge Runtime:

|Protocol (Protokol)|Port|příchozí|Odchozí|Doprovodné materiály|
|--|--|--|--|--|
|MQTT|8883|BLOKOVANÉ (výchozí)|BLOKOVANÉ (výchozí)|<ul> <li>Nakonfigurujte odchozí (odchozí) být otevřít, když jako protokol pro komunikaci pomocí protokolu MQTT.<li>IoT Edge není podporován. 1883 pro MQTT. <li>Příchozí připojení (příchozí) by se zablokovat.</ul>|
|AMQP|5671|BLOKOVANÉ (výchozí)|Otevřít (výchozí)|<ul> <li>Výchozí komunikační protokol pro IoT Edge. <li> Musí být nakonfigurován Open Azure IoT Edge není nakonfigurovaný pro jiné podporované protokoly nebo je požadovaný komunikační protokol AMQP.<li>5672 pro AMQP nepodporuje IoT Edge.<li>Blokovat tento port při použití Azure IoT Edge různé IoT Hub podporovaný protokol.<li>Příchozí připojení (příchozí) by se zablokovat.</ul></ul>|
|HTTPS|443|BLOKOVANÉ (výchozí)|Otevřít (výchozí)|<ul> <li>Nakonfigurujte odchozí (odchozí) bude otevřít na 443 pro IoT Edge zřizování. Tato konfigurace je nutná, pokud používáte ruční skripty nebo Azure IoT zařízení zřizování služby (DPS). <li>Příchozí připojení (příchozí) by měl být otevřené pouze u konkrétních scénářů: <ul> <li>  Pokud máte transparentní brány pomocí zařízení typu list, které může odesílat požadavky metod. Port 443 v takovém případě nemusí být otevřený, aby externí sítě připojit k IOT hub nebo poskytuje služby IOT hub pomocí Azure IoT Edge. Proto může být příchozí pravidlo s omezeným přístupem můžete pouze otevřít příchozí (příchozí) z interní sítě. <li> Klient zařízení (C2D) scénáře.</ul><li>80 pro protokol HTTP není podporován IoT Edge.<li>Pokud se v podnikové síti; nedá nakonfigurovat jiných protokolů než HTTP (například připojení přes AMQP nebo MQTT) zprávy odesílat přes WebSockets. Port 443 se v takovém případě používat pro komunikaci pomocí protokolu WebSocket.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Modul Edge agent průběžně hlásí prázdný konfigurační soubor a žádné moduly na zařízení nezačínají.

Zařízení má potíže se spouštěním modulů definovaných v nasazení. Běží jenom edgeAgent, ale průběžně hlásí prázdný konfigurační soubor...

**Původní příčina**

Ve výchozím nastavení IoT Edge spouští moduly ve vlastní izolované síti kontejneru. Zařízení může mít potíže s překladem názvů DNS v rámci této privátní sítě.

**Rozhodnutí**


**Možnost 1: nastavení serveru DNS v nastavení modulu pro vytvoření kontejneru**

Zadejte server DNS pro vaše prostředí v nastavení kontejnerového modulu, který bude platit pro všechny moduly kontejneru spouštěné modulem. Vytvořte soubor s názvem `daemon.json` určení serveru DNS, který chcete použít. Příklad:

```
{
    "dns": ["1.1.1.1"]
}
```

Výše uvedený příklad nastaví server DNS na veřejně dostupnou službu DNS. Pokud hraniční zařízení nemůže získat přístup k této IP adrese z jeho prostředí, nahraďte ho adresou serveru DNS, která je přístupná.

Umístit `daemon.json` do správného umístění pro vaši platformu: 

| Platforma | Umístění |
| --------- | -------- |
| Linux | `/etc/docker` |
| Hostitel s Windows s kontejnery Windows | `C:\ProgramData\iotedge-moby\config` |

Pokud umístění již obsahuje `daemon.json` soubor, přidejte do něj klíč **DNS** a soubor uložte.

*Restartujte modul kontejnerů, aby se aktualizace projevily.*

| Platforma | Příkaz |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (PowerShell pro správu) | `Restart-Service iotedge-moby -Force` |

**Možnost 2: nastavení serveru DNS v nasazení IoT Edge na modul**

Můžete nastavit server DNS pro *createOptions* modulu v nasazení IoT Edge. Příklad:

```
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Nezapomeňte tuto hodnotu nastavit i pro moduly *edgeAgent* a *edgeHub* . 

## <a name="next-steps"></a>Další kroky
Myslíte si, že jste v platformě IoT Edge našli chybu? [Odešlete problém](https://github.com/Azure/iotedge/issues) , abychom mohli pokračovat v vylepšování. 

Pokud máte více otázek, vytvořte [support Request](https://portal.azure.com/#create/Microsoft.Support) pro nápovědu. 

