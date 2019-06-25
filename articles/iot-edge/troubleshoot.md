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
ms.custom: seodec18
ms.openlocfilehash: 00147002317f15345f01c88e81973837d16e6669
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65797614"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Běžné potíže se službou Azure IoT Edge a jejich řešení

Pokud ve vašem prostředí dochází k potížím s provozem služby Azure IoT Edge, použijte tento článek jako vodítko k jejich řešení.

## <a name="run-the-iotedge-check-command"></a>Spustit iotedge kontrola příkaz

Prvním krokem při řešení potíží s IoT Edge by měl být použít `check` příkaz, který provádí sběr konfigurací a připojením testů pro běžné problémy. `check` Příkaz je k dispozici v [release 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) a novější.

Můžete spustit `check` následující příkaz, nebo zahrnout `--help` příznak, který chcete zobrazit úplný seznam možností:

* V Linuxu:

  ```bash
  sudo iotedge check
  ```

* Ve Windows:

  ```powershell
  iotedge check
  ```

Typy kontrol, spusťte nástroj dají považovat za:

* Kontrola konfigurace: Zkontroluje informace, které by mohly bránit připojení ke cloudu, včetně problémů s hraniční zařízení *config.yaml* a modul kontejneru.
* Kontroluje se připojení: Ověřuje se modul runtime IoT Edge přístupné portů na hostitele zařízení a všechny součásti IoT Edge můžete připojit ke službě IoT Hub.
* Zkontroluje připravenost: Vyhledá produkční doporučené osvědčené postupy, jako je například stav zařízení certifikáty od certifikační autority (CA) a konfigurace souborů protokolu modulu.

Úplný seznam diagnostických kontrol najdete v tématu [integrované řešení potíží s funkcí](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="standard-diagnostic-steps"></a>Standardní postup diagnostiky

Pokud narazíte na problém, můžete další informace o stavu zařízení IoT Edge kontrolou protokolů kontejneru a zprávy, předávané do a ze zařízení. Ke shromáždění informací použijte příkazy a nástroje uvedené v této části.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Zkontrolovat stav správce zabezpečení IoT Edge a protokolům

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
> Soubory YAML nesmí obsahovat tabulátory jako odsazení. Místo toho použijte 2 mezery.

V Linuxu:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Ve Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Zkontrolujte protokoly kontejneru pro problémy

Po spuštění démona zabezpečení IoT Edge, prohlédněte si protokoly kontejnerů a detekujte problémy. Začněte nasazenými kontejnery, pak si prohlédněte kontejnery, které tvoří modul runtime IoT Edge: edgeAgent a edgeHub. Protokoly agenta IoT Edge obvykle obsahují informace o životním cyklu jednotlivých kontejnerů. Protokoly centra IoT Edge obsahují informace o zasílání zpráv a směrování. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Zobrazte zprávy procházející přes Centrum IoT Edge

Můžete zobrazit zprávy procházející přes Centrum IoT Edge a shromažďování přehledů z podrobné protokoly z kontejnerů modulu runtime. Chcete-li zapnout podrobné protokolování na těchto kontejnerů, nastavte `RuntimeLogLevel` v konfiguračním souboru yaml. Otevřít soubor:

V Linuxu:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Ve Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Ve výchozím nastavení `agent` element bude vypadat jako v následujícím příkladu:

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

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > Soubory YAML nesmí obsahovat tabulátory jako identation. Místo toho použijte 2 mezery.

Uložte soubor a restartujte správce zabezpečení IoT Edge.

Můžete zkontrolovat také zprávy odesílané mezi službou IoT Hub a hraničními zařízeními IoT. Tyto zprávy zobrazíte pomocí [Azure IoT Hub Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) rozšíření (dříve rozšíření Azure IoT Toolkit) pro Visual Studio Code. Další informace najdete v tématu [užitečný nástroj pro vývoj se službou Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

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

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge agent přestane po přibližně minutu

Modul edgeAgent spustí a je spuštěný přibližně minutu a pak zastaví. Z protokolů vyplývá, že agenta IoT Edge se pokusí připojit ke službě IoT Hub přes protokol AMQP a poté se pokusí připojit pomocí protokolu AMQP přes WebSocket. V případě selhání agenta IoT Edge ukončí. 

Příklad edgeAgent protokoly:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Původní příčina
Konfigurace sítě v hostitelské síti brání dosažení síti agenta IoT Edge. Agent se nejprve pokusí připojit přes protokol AMQP (port 5671). Pokud se nepovede, zkusí Websocket (port 443).

Modul runtime IoT Edge nastaví pro každý z modulů síť, na které budou komunikovat. V Linuxu je tato síť síťovým mostem. Ve Windows využívá překlad adres (NAT). K tomuto problému častěji dochází na zařízeních s Windows využívajících kontejnery Windows a síť s překladem adres (NAT). 

### <a name="resolution"></a>Řešení
Ujistěte se, že se IP adresy přiřazené k tomuto síťovému mostu nebo síti NAT směrují do internetu. Někdy konfigurace sítě VPN na hostiteli přepíše síť IoT Edge. 

## <a name="iot-edge-hub-fails-to-start"></a>Centrum IoT Edge se nepodaří spustit

Modul edgeHub se nezdaří spuštění a zapisuje následující zpráva do protokolů: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Původní příčina
Nějaký jiný proces na hostitelském počítači používá port 443. Centrum IoT Edge mapuje porty 5671 a 443 pro použití ve scénářích brány. Toto mapování portů selže, pokud daný port již používá jiný proces. 

### <a name="resolution"></a>Řešení
Vyhledejte a zastavte proces používající port 443. Tímto procesem je obvykle webový server.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge agent nemá přístup k imagi modulu (403)
Kontejner se nedaří spustit a protokoly edgeAgent zobrazit Chyba 403. 

### <a name="root-cause"></a>Původní příčina
Iot Edge agent nemá oprávnění pro přístup k imagi modulu. 

### <a name="resolution"></a>Řešení
Ujistěte se, že jsou vaše přihlašovací údaje registru správně zadána v manifestu nasazení

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Démon zabezpečení IoT Edge se nezdaří s platný název hostitele

Příkaz `sudo journalctl -u iotedge` selže a zobrazí následující zprávu: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Původní příčina
Modul runtime IoT Edge podporuje pouze názvy hostitelů, které jsou kratší než 64 znaků. Fyzické počítače obvykle nemusí dlouhé názvy hostitelů, ale tento problém je běžnější na virtuálním počítači. Automaticky generované názvy hostitelů pro virtuální počítače s Windows, které jsou hostované v Azure, zejména, jsou obvykle dlouhé. 

### <a name="resolution"></a>Řešení
Když se zobrazí tato chyba, ho mohli vyřešit tak, že konfigurace názvu DNS virtuálního počítače a pak nastavení název DNS jako název hostitele v příkazu pro nastavení.

1. Na webu Azure Portal přejděte na stránku přehled vašeho virtuálního počítače. 
2. Vyberte **konfigurace** pod názvem DNS. Pokud je váš virtuální počítač už nakonfigurovaný název DNS, není nutné konfigurovat nové. 

   ![Nakonfigurujte název DNS virtuálního počítače](./media/troubleshoot/configure-dns.png)

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
Centrum IoT Edge, která je součástí modulu runtime IoT Edge, je optimalizován pro výkon ve výchozím nastavení a pokusí se přidělit velké množství paměti. Tato optimalizace není ideální pro omezené hraničními zařízeními a může způsobit problémy se stabilitou.

### <a name="resolution"></a>Řešení
Pro Centrum IoT Edge, nastavte proměnnou prostředí **OptimizeForPerformance** k **false**. Chcete-li to provést dvěma způsoby:

V uživatelském rozhraní: 

Na portálu přejděte na **podrobnosti o zařízení** > **nastavit moduly** > **konfigurovat rozšířená nastavení modulu Runtime Edge**. Vytvořte proměnnou prostředí pro Centrum Edge modul s názvem *OptimizeForPerformance* , která je nastavena na *false*.

![OptimizeForPerformance nastavena na hodnotu false](./media/troubleshoot/optimizeforperformance-false.png)

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
`Get-WinEvent` Příkaz Powershellu využívá vyskytovat najít protokoly určitou položku registru `ProviderName`.

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
Proces démon IoT Edge vynutí proces identifikace pro všechny moduly propojíte edgeHub z bezpečnostních důvodů. Ověřuje, že všechny zprávy modulem pocházejí z hlavní proces ID modulu. Pokud zpráva je odesíláno modul z ID jiného procesu než původně vytvořeno, odmítne zprávy s 404 chybovou zprávu.

### <a name="resolution"></a>Řešení
Ujistěte se, že stejné ID procesu je vždy používá vlastní modul IoT Edge pro odesílání zpráv edgeHub. Například se ujistěte, že `ENTRYPOINT` místo `CMD` příkaz v souboru Docker, protože `CMD` povede k jeden proces ID modulu a jiné ID procesu pro příkaz prostředí bash s hlavní program, že `ENTRYPOINT` povede k ID jednoho procesu.


## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Konfigurace pravidla brány firewall a portů pro nasazení IoT Edge
Azure IoT Edge umožňuje komunikaci z místního serveru do cloudu Azure pomocí podporovaných protokolů služby IoT Hub, najdete v článku [výběr komunikační protokol](../iot-hub/iot-hub-devguide-protocols.md). Pro zvýšení zabezpečení komunikačních kanálů mezi Azure IoT Edge a Centrum IoT Azure jsou vždy nakonfigurované jako odchozí. Tato konfigurace je založená na [komunikace s asistencí služby](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), což minimalizuje napadení škodlivým entity prozkoumat. Příchozí komunikace je pouze požadovaná u konkrétních scénářů, kde Azure IoT Hub potřebuje k odesílání zpráv zařízení Azure IoT Edge. Zprávy typu cloud zařízení jsou chráněny pomocí zabezpečené kanály TLS a můžete dál zabezpečit pomocí certifikátů X.509 a modulů zařízení TPM. Správce zabezpečení Azure IoT Edge se řídí, jak může být tato komunikace naváže, naleznete v tématu [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

I když IoT Edge poskytuje rozšířené konfigurace pro zabezpečení modulu runtime Azure IoT Edge a nasadit moduly, je stále závislá na základní konfiguraci počítače a sítě. Proto je nezbytné k zajištění řádné síťové a pravidla brány firewall jsou nastavené pro zabezpečené hraničními zařízeními a cloudovým komunikace. V následující tabulce slouží jako vodítko při konfiguraci brány firewall pravidla pro základní servery je hostitelem modulu runtime Azure IoT Edge:

|Protocol|Port|příchozí|Odchozí|Doprovodné materiály|
|--|--|--|--|--|
|MQTT|8883|BLOKOVANÉ (výchozí)|BLOKOVANÉ (výchozí)|<ul> <li>Nakonfigurujte odchozí (odchozí) být otevřít, když jako protokol pro komunikaci pomocí protokolu MQTT.<li>IoT Edge není podporován. 1883 pro MQTT. <li>Příchozí připojení (příchozí) by se zablokovat.</ul>|
|AMQP|5671|BLOKOVANÉ (výchozí)|Otevřít (výchozí)|<ul> <li>Výchozí komunikační protokol pro IoT Edge. <li> Musí být nakonfigurován Open Azure IoT Edge není nakonfigurovaný pro jiné podporované protokoly nebo je požadovaný komunikační protokol AMQP.<li>5672 pro AMQP nepodporuje IoT Edge.<li>Blokovat tento port při použití Azure IoT Edge různé IoT Hub podporovaný protokol.<li>Příchozí připojení (příchozí) by se zablokovat.</ul></ul>|
|HTTPS|443|BLOKOVANÉ (výchozí)|Otevřít (výchozí)|<ul> <li>Nakonfigurujte odchozí (odchozí) bude otevřít na 443 pro IoT Edge zřizování. Tato konfigurace je nutná, pokud používáte ruční skripty nebo Azure IoT zařízení zřizování služby (DPS). <li>Příchozí připojení (příchozí) by měl být otevřené pouze u konkrétních scénářů: <ul> <li>  Pokud máte transparentní brány pomocí zařízení typu list, které může odesílat požadavky metod. Port 443 v takovém případě nemusí být otevřený, aby externí sítě připojit k IOT hub nebo poskytuje služby IOT hub pomocí Azure IoT Edge. Proto může být příchozí pravidlo s omezeným přístupem můžete pouze otevřít příchozí (příchozí) z interní sítě. <li> Klient zařízení (C2D) scénáře.</ul><li>80 pro protokol HTTP není podporován IoT Edge.<li>Pokud se v podnikové síti; nedá nakonfigurovat jiných protokolů než HTTP (například připojení přes AMQP nebo MQTT) zprávy odesílat přes WebSockets. Port 443 se v takovém případě používat pro komunikaci pomocí protokolu WebSocket.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Modul agenta Edge průběžně sestavy "prázdný konfigurační soubor" a žádné moduly start na zařízení

Zařízení má problémy se spuštěním moduly, které jsou definovány v nasazení. Pouze edgeAgent je spuštěná, ale neustále reporting "prázdný konfigurační soubor …".

### <a name="potential-root-cause"></a>Hlavní příčinu
IoT Edge ve výchozím nastavení spustí moduly ve vlastní síti izolovaného kontejneru. Zařízení může mít potíže s překladem názvů DNS v rámci této privátní sítě.

### <a name="resolution"></a>Řešení

**Option 1: Nastavení serveru DNS v kontejneru modulu**

Určení serveru DNS pro vaše prostředí v nastavení modulu container, které bude platit pro všechny moduly kontejneru tím, že modul. Vytvořte soubor s názvem `daemon.json` určení serveru DNS pro použití. Příklad:

```
{
    "dns": ["1.1.1.1"]
}
```

Výše uvedený příklad nastaví na veřejně dostupná služba DNS DNS server. Hraniční zařízení nemůže získat přístup k této IP ze své prostředí, nahraďte ho adresu serveru DNS, který je přístupný.

Místo `daemon.json` na správném místě pro vaši platformu: 

| Platforma | Location |
| --------- | -------- |
| Linux | `/etc/docker` |
| Hostitel Windows s kontejnery Windows | `C:\ProgramData\iotedge-moby\config` |

Pokud umístění již obsahuje `daemon.json` přidejte **dns** klíče k němu a soubor uložte.

*Restartujte modul kontejneru aktualizace se projeví*

| Platforma | Příkaz |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Powershellu pro správce) | `Restart-Service iotedge-moby -Force` |

**Option 2: Nastavení serveru DNS v nasazení IoT Edge na modul**

Můžete nastavit server DNS pro každý modul *CreateOptions field* v nasazení IoT Edge. Příklad:

```
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Nezapomeňte nastavit pro *edgeAgent* a *edgeHub* i moduly. 

## <a name="next-steps"></a>Další postup
Myslíte si, že jste v platformě IoT Edge našli chybu? [Odešlete problém](https://github.com/Azure/iotedge/issues) tak, aby Pokračujeme ke zlepšení. 

Pokud máte další dotazy, vytvořit [žádost o podporu](https://portal.azure.com/#create/Microsoft.Support) nápovědu. 

