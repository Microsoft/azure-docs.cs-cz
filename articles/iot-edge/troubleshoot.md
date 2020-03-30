---
title: Poradce při potížích – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Tento článek slouží k získání standardních diagnostických dovedností pro Azure IoT Edge, jako je načítání stavu komponent a protokolů, a řešení běžných problémů.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 13eab175356ed1ec20caa3263ba00d0563384f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064379"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Běžné potíže se službou Azure IoT Edge a jejich řešení

Pokud ve vašem prostředí dochází k potížím s provozem služby Azure IoT Edge, použijte tento článek jako vodítko k jejich řešení.

## <a name="run-the-iotedge-check-command"></a>Spuštění příkazu iotedge 'check'

Prvním krokem při řešení potíží s ioT `check` Edge by mělo být použití příkazu, který spouští kolekci testů konfigurace a připojení pro běžné problémy. Příkaz `check` je k dispozici ve [verzi 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) a novější.

`check` Příkaz můžete spustit následujícím způsobem nebo `--help` můžete zahrnout příznak, abyste viděli úplný seznam možností:

* Na Linuxu:

  ```bash
  sudo iotedge check
  ```

* Ve Windows:

  ```powershell
  iotedge check
  ```

Typy kontrol prováděné nástrojem lze klasifikovat jako:

* Kontroly konfigurace: Zkoumá podrobnosti, které by mohly zabránit zařízení Edge připojení ke cloudu, včetně problémů s *config.yaml* a modul kontejneru.
* Kontroly připojení: Ověří, že modul runtime IoT Edge může přistupovat k portům na hostitelském zařízení a všechny součásti IoT Edge se můžou připojit k centru IoT Hub.
* Kontroly připravenosti výroby: Vyhledá doporučené doporučené postupy výroby, jako jsou certifikáty certifikační autority (CA) stavu zařízení a konfigurace souboru protokolu modulu.

Úplný seznam diagnostických kontrol naleznete [v tématu Předdefinované funkce řešení potíží](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-iotedge-support-bundle-command"></a>Shromažďování informací o ladění pomocí příkazu iotedge 'support-bundle'

Když potřebujete shromáždit protokoly ze zařízení IoT Edge, nejpohodlnější `support-bundle` maješ-li se použít příkaz. Ve výchozím nastavení tento příkaz shromažďuje modul, Správce zabezpečení IoT Edge a protokoly modulu kontejneru, výstup JSON "iotedge Check" a další užitečné informace o ladění. Komprimuje je do jednoho souboru pro snadné sdílení. Příkaz `support-bundle` je k dispozici ve [verzi 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) a novější.

Spusťte `support-bundle` příkaz `--since` s příznakem a určete, jak dlouho od minulosti chcete získat protokoly. Například `6h` získá protokoly od posledních `6d` 6 hodin, od `6m` posledních 6 dnů, od posledních 6 minut a tak dále. Zahrňte `--help` příznak, chcete-li zobrazit úplný seznam možností.


* Na Linuxu:

  ```bash
  sudo iotedge support-bundle --since 6h
  ```

* Ve Windows:

  ```powershell
  iotedge support-bundle --since 6h
  ```

> [!WARNING]
> Výstup z `support-bundle` příkazu může obsahovat názvy hostitelů, zařízení a modulů, informace zaznamenané vašimi moduly atd. Vezměte prosím na vědomí, pokud sdílení výstupu ve veřejném fóru.

## <a name="standard-diagnostic-steps"></a>Standardní postup diagnostiky

Pokud narazíte na problém, můžete získat další informace o stavu zařízení IoT Edge kontrolou protokoly kontejnerů a zprávy, které přecházejí do a ze zařízení. Ke shromáždění informací použijte příkazy a nástroje uvedené v této části.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Kontrola stavu Správce zabezpečení Okraje IoT a jeho protokolů

Na Linuxu:

* Chcete-li zobrazit stav Správce zabezpečení IoT Edge:

   ```bash
   sudo systemctl status iotedge
   ```

* Zobrazení protokolů Správce zabezpečení IoT Edge:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Chcete-li zobrazit podrobnější protokoly Správce zabezpečení IoT Edge:

  * Upravte nastavení iotedge daemon:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Aktualizujte následující řádky:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Restartujte daemon zabezpečení IoT Edge:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

Ve Windows:

* Chcete-li zobrazit stav Správce zabezpečení IoT Edge:

   ```powershell
   Get-Service iotedge
   ```

* Zobrazení protokolů Správce zabezpečení IoT Edge:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Pokud správce zabezpečení IoT Edge není spuštěn, ověřte konfigurační soubor yaml

> [!WARNING]
> Soubory YAML nemohou obsahovat tabulátory jako odsazení. Místo toho použijte 2 mezery. Prvky nejvyšší úrovně by neměly mít žádné úvodní mezery.

Na Linuxu:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Ve Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Zkontrolujte, zda protokoly kontejnerů neobsahuje problémy

Jakmile je spuštěn daemon zabezpečení IoT Edge, podívejte se na protokoly kontejnerů ke zjištění problémů. Začněte s nasazenými kontejnery a pak se podívejte na kontejnery, které tvoří runtime IoT Edge: edgeAgent a edgeHub. Protokoly agenta IoT Edge obvykle poskytují informace o životním cyklu každého kontejneru. Protokoly centra IoT Edge poskytují informace o zasílání zpráv a směrování.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Zobrazení zpráv procházejících centrem IoT Edge

Můžete zobrazit zprávy procházející centrem IoT Edge a shromažďovat přehledy z podrobných protokolů z kontejnerů runtime. Chcete-li zapnout podrobné protokoly na `RuntimeLogLevel` těchto kontejnerech, nastavte v konfiguračním souboru yaml. Otevření souboru:

Na Linuxu:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Ve Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Ve výchozím `agent` nastavení bude prvek vypadat jako v následujícím příkladu:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Nahradit: `env: {}`

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > Soubory YAML nemohou obsahovat karty jako identifikaci. Místo toho použijte 2 mezery. Položky nejvyšší úrovně nemohou mít úvodní prázdné znaky.

Uložte soubor a restartujte správce zabezpečení IoT Edge.

Můžete zkontrolovat také zprávy odesílané mezi službou IoT Hub a hraničními zařízeními IoT. Zobrazení těchto zpráv pomocí [rozšíření Azure IoT Hub pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Další informace najdete v [tématu Handy nástroj při vývoji s Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Restartovat kontejnery

Po prozkoumání protokoly a zprávy pro informace, můžete zkusit restartování kontejnerů:

```cmd
iotedge restart <container name>
```

Restartujte kontejnery runtime IoT Edge:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Restartování správce zabezpečení IoT Edge

Pokud problém přetrvává, můžete zkusit restartovat správce zabezpečení IoT Edge.

Na Linuxu:

   ```cmd
   sudo systemctl restart iotedge
   ```

Ve Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>Agent IoT Edge se zastaví asi po minutě

Modul edgeAgent se spustí a úspěšně spustí asi minutu, poté se zastaví. Protokoly označují, že agent IoT Edge se pokusí připojit k ioT hubpřes AMQP a pak se pokusí o připojení pomocí AMQP přes WebSocket. Když se to nezdaří, agent IoT Edge ukončí.

Příklad protokolů edgeAgent:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Příčinou**

Konfigurace sítě v hostitelské síti brání agentovi IoT Edge v přístupu k síti. Agent se nejprve pokusí připojit přes protokol AMQP (port 5671). Pokud se připojení nezdaří, pokusí se o websockets (port 443).

Modul runtime IoT Edge nastaví pro každý z modulů síť, na které budou komunikovat. V Linuxu je tato síť síťovým mostem. Ve Windows využívá překlad adres (NAT). K tomuto problému častěji dochází na zařízeních s Windows využívajících kontejnery Windows a síť s překladem adres (NAT).

**Rozlišení**

Ujistěte se, že se IP adresy přiřazené k tomuto síťovému mostu nebo síti NAT směrují do internetu. Někdy konfigurace sítě VPN na hostiteli přepíše síť IoT Edge.

## <a name="iot-edge-hub-fails-to-start"></a>Spuštění rozbočovače IoT Edge se nespustí

EdgeHub modul se nezdaří spustit a vytiskne následující zprávu do protokolů:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Příčinou**

Nějaký jiný proces na hostitelském počítači používá port 443. Rozbočovač IoT Edge mapuje porty 5671 a 443 pro použití ve scénářích brány. Toto mapování portů selže, pokud daný port již používá jiný proces.

**Rozlišení**

Vyhledejte a zastavte proces používající port 443. Tímto procesem je obvykle webový server.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>Agent IoT Edge nemá přístup k obrázku modulu (403)

Kontejner se nepodaří spustit a edgeAgent protokoly zobrazit chybu 403.

**Příčinou**

Agent IoT Edge nemá oprávnění k přístupu k image modulu.

**Rozlišení**

Zkontrolujte, zda jsou pověření registru správně zadána v manifestu nasazení.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Konana zabezpečení IoT Edge se nezdaří s neplatným názvem hostitele

Příkaz `sudo journalctl -u iotedge` selže a vytiskne následující zprávu:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Příčinou**

Runtime IoT Edge může podporovat pouze názvy hostitelů, které jsou kratší než 64 znaků. Fyzické počítače obvykle nemají dlouhé názvy hostitelů, ale problém je častější na virtuálním počítači. Automaticky generované názvy hostitelů pro virtuální počítače s Windows hostované v Azure, zejména mají tendenci být dlouhé.

**Rozlišení**

Když se zobrazí tato chyba, můžete ji vyřešit konfigurací názvu DNS virtuálního počítače a nastavením názvu DNS jako názvu hostitele v příkazu nastavení.

1. Na webu Azure Portal přejděte na stránku s přehledem vašeho virtuálního počítače.
2. V části Název DNS vyberte **konfigurovat.** Pokud váš virtuální počítač už má nakonfigurovaný název DNS, nemusíte nakonfigurovat nový.

   ![Konfigurace názvu DNS virtuálního počítače](./media/troubleshoot/configure-dns.png)

3. Zadejte hodnotu **popisku názvu DNS** a vyberte **Uložit**.
4. Zkopírujte nový název DNS, který by měl být ve formátu ** \<\>DNSnamelabel\< . vmlocation\>.cloudapp.azure.com**.
5. Uvnitř virtuálního počítače nastavte runtime IoT Edge pomocí následujícího příkazu s vaším názvem DNS:

   * Na Linuxu:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * Ve Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problémy se stabilitou u zařízení s omezenými prostředky

Můžete se setkat s problémy se stabilitou na omezených zařízeních, jako je Raspberry Pi, zejména při použití jako brána. Příznaky zahrnují výjimky z paměti v modulu hraničního rozbočovače, zařízení pro příjem dat se nemohou připojit nebo zařízení přestane odesílat telemetrické zprávy po několika hodinách.

**Příčinou**

Rozbočovač IoT Edge, který je součástí runtime IoT Edge, je optimalizovaný pro výkon ve výchozím nastavení a pokusí se přidělit velké bloky paměti. Tato optimalizace není ideální pro zařízení s omezeným okrajem a může způsobit problémy se stabilitou.

**Rozlišení**

Pro centrum IoT Edge nastavte proměnnou prostředí **OptimizeForPerformance** na **false**. Existují dva způsoby, jak nastavit proměnné prostředí:

Na webu Azure Portal:

Ve svém IoT Hubu vyberte zařízení IoT Edge a na stránce podrobností o zařízení a vyberte **Nastavit nastavení modulů** > **runtime**. Vytvořte proměnnou prostředí pro modul Edge Hub s názvem *OptimizeForPerformance,* která je nastavena na *hodnotu false*.

![OptimizeForPerformance nastaveno na hodnotu false](./media/troubleshoot/optimizeforperformance-false.png)

**Nebo**

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

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Nelze získat protokoly demonu IoT Edge v systému Windows

Pokud se při použití v `Get-WinEvent` systému Windows zobrazí výjimka EventLogException, zkontrolujte položky registru.

**Příčinou**

Příkaz `Get-WinEvent` Prostředí PowerShell spoléhá na položku registru, která má `ProviderName`být k dispozici, aby vyhledala protokoly podle konkrétního .

**Rozlišení**

Nastavte položku registru pro demon IoT Edge. Vytvořte soubor **iotedge.reg** s následujícím obsahem a importujte do registru systému Windows poklepáním nebo pomocí příkazu: `reg import iotedge.reg`

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>Modul IoT Edge neodešle zprávu na edgeHub s chybou 404

Vlastní modul IoT Edge se nezdaří odeslat zprávu edgeHub s chybou 404. `Module not found` Daemon IoT Edge vytiskne do protokolů následující zprávu:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Příčinou**

Daemon IoT Edge vynucuje identifikaci procesu pro všechny moduly, které se připojují k edgeHubu z bezpečnostních důvodů. Ověří, že všechny zprávy odesílané modulem pocházejí z ID hlavního procesu modulu. Pokud je zpráva odesílána modulem z jiného ID procesu, než bylo původně vytvořeno, odmítne zprávu s chybovou zprávou 404.

**Rozlišení**

Od verze 1.0.7 jsou všechny modulové procesy povoleny pro připojení. Pokud upgrade na 1.0.7 není možný, proveďte následující kroky. Další informace naleznete v [meze řidítku verzí verze 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Ujistěte se, že stejné ID procesu se vždy používá vlastní modul IoT Edge k odesílání zpráv na edgeHub. Například, ujistěte `ENTRYPOINT` se, že místo příkazu `CMD` v souboru Dockeru, protože `CMD` povede k jednomu ID procesu `ENTRYPOINT` pro modul a další ID procesu pro příkaz bash spuštěnhlavní program, zatímco povede k jednomu ID procesu.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Pravidla konfigurace brány firewall a portu pro nasazení IoT Edge

Azure IoT Edge umožňuje komunikaci z místního serveru do cloudu Azure pomocí podporovaných protokolů IoT Hub, viz [výběr komunikačního protokolu](../iot-hub/iot-hub-devguide-protocols.md). Pro rozšířené zabezpečení jsou komunikační kanály mezi Azure IoT Edge a Azure IoT Hub vždy nakonfigurované tak, aby byly odchozí. Tato konfigurace je založena na [vzoru služby asistované komunikace](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), který minimalizuje prostor pro útok pro škodlivou entitu k prozkoumání. Příchozí komunikace je vyžadována jenom pro konkrétní scénáře, kde Azure IoT Hub potřebuje k nabízení zpráv na zařízení Azure IoT Edge. Zprávy typu Cloud-to-device jsou chráněny pomocí zabezpečených kanálů TLS a lze je dále zabezpečit pomocí certifikátů X.509 a modulů zařízení TPM. Azure IoT Edge Security Manager určuje, jak lze tuto komunikaci navázat, viz [Správce zabezpečení IoT Edge](../iot-edge/iot-edge-security-manager.md).

Zatímco IoT Edge poskytuje vylepšenou konfiguraci pro zabezpečení modulu runtime Azure IoT Edge a nasazených modulů, je stále závislý na základní konfiguraci počítače a sítě. Proto je nezbytné zajistit, aby byla nastavena správná pravidla sítě a brány firewall pro bezpečnou komunikaci mezi hranami a cloudem. Následující tabulku lze použít jako vodítko při konfiguračním nastavení brány firewall pro podkladové servery, kde je hostován runtime Azure IoT Edge:

|Protocol (Protokol)|Port|Příchozí|Odchozí|Doprovodné materiály|
|--|--|--|--|--|
|MQTT|8883|BLOKOVÁNO (výchozí)|BLOKOVÁNO (výchozí)|<ul> <li>Konfigurace odchozí (odchozí) být otevřena při použití MQTT jako komunikační protokol.<li>1883 pro MQTT není podporován ioT Edge. <li>Příchozí (Příchozí) připojení by měla být blokována.</ul>|
|AMQP|5671|BLOKOVÁNO (výchozí)|OTEVŘÍT (výchozí)|<ul> <li>Výchozí komunikační protokol pro IoT Edge. <li> Musí být nakonfigurován tak, aby byl otevřený, pokud Azure IoT Edge není nakonfigurován pro jiné podporované protokoly nebo AMQP je požadovaný komunikační protokol.<li>5672 pro AMQP není podporován ioT Edge.<li>Blokujte tento port, když Azure IoT Edge používá jiný protokol podporovaný službou IoT Hub.<li>Příchozí (Příchozí) připojení by měla být blokována.</ul></ul>|
|HTTPS|443|BLOKOVÁNO (výchozí)|OTEVŘÍT (výchozí)|<ul> <li>Konfigurace odchozí (odchozí) být otevřena na 443 pro zřizování IoT Edge. Tato konfigurace je vyžadována při použití ruční skripty nebo Azure IoT device Provisioning Service (DPS). <li>Příchozí (Příchozí) připojení by mělo být otevřeno pouze pro určité scénáře: <ul> <li>  Pokud máte transparentní bránu s listovými zařízeními, která mohou odesílat požadavky na metody. V takovém případě port 443 nemusí být otevřený pro externí sítě pro připojení k IoTHub nebo poskytování služeb IoTHub prostřednictvím Azure IoT Edge. Příchozí pravidlo tedy může být omezeno pouze na otevření příchozí (příchozí) z interní sítě. <li> Pro scénáře Klient k zařízení (C2D).</ul><li>80 pro HTTP není podporováno IoT Edge.<li>Pokud nelze v rozlehlé síti konfigurovat protokoly než HTTP (například AMQP nebo MQTT). zprávy mohou být odesílány přes WebSockets. Port 443 bude použit pro komunikaci WebSocket v tomto případě.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Modul Edge Agent průběžně hlásí "prázdný konfigurační soubor" a v zařízení se nespustí žádné moduly

Zařízení má potíže se spuštěním modulů definovaných v nasazení. Pouze edgeAgent běží, ale neustále hlásí "prázdný konfigurační soubor...".

**Příčinou**

Ve výchozím nastavení ioT Edge spouští moduly ve vlastní izolované kontejnerové síti. Zařízení může mít potíže s překladem názvů DNS v této privátní síti.

**Rozlišení**

**Možnost 1: Nastavení serveru DNS v nastavení modulu kontejneru**

Zadejte server DNS pro vaše prostředí v nastavení modulu kontejneru, který se bude vztahovat na všechny moduly kontejneru spuštěné modulem. Vytvořte soubor `daemon.json` s názvem určující server DNS, který má být používán. Například:

```json
{
    "dns": ["1.1.1.1"]
}
```

Výše uvedený příklad nastaví server DNS na veřejně přístupnou službu DNS. Pokud hraniční zařízení nemá přístup k této adrese IP ze svého prostředí, nahraďte ji adresou serveru DNS, která je přístupná.

Umístěte `daemon.json` se na správné místo pro vaši platformu:

| Platforma | Umístění |
| --------- | -------- |
| Linux | `/etc/docker` |
| Hostitel systému Windows s kontejnery systému Windows | `C:\ProgramData\iotedge-moby\config` |

Pokud umístění již `daemon.json` soubor obsahuje, přidejte do něj klíč **DNS** a soubor uložte.

Restartujte modul kontejneru, aby se aktualizace projevily.

| Platforma | Příkaz |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Admin Powershell) | `Restart-Service iotedge-moby -Force` |

**Možnost 2: Nastavení serveru DNS v nasazení IoT Edge na modul**

Můžete nastavit DNS server pro každý modul *createOptions* v nasazení IoT Edge. Například:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Ujistěte se, že tuto konfiguraci nastavíte také pro moduly *edgeAgent* a *edgeHub.*

## <a name="next-steps"></a>Další kroky

Myslíte si, že jste v platformě IoT Edge našli chybu? [Odešlete problém,](https://github.com/Azure/iotedge/issues) abychom se mohli nadále zlepšovat.

Pokud máte další dotazy, vytvořte [žádost o pomoc.](https://portal.azure.com/#create/Microsoft.Support)
