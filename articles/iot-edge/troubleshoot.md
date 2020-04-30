---
title: Řešení potíží – Azure IoT Edge | Microsoft Docs
description: V tomto článku se naučíte používat standardní diagnostické dovednosti pro Azure IoT Edge, jako je načítání stavu komponent a protokolů, a řešení běžných problémů.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 2e15dffac73b4a50b1ef9288feaeb6073dea91e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086517"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Běžné potíže se službou Azure IoT Edge a jejich řešení

Pokud ve vašem prostředí dochází k potížím s provozem služby Azure IoT Edge, použijte tento článek jako vodítko k jejich řešení.

## <a name="run-the-iotedge-check-command"></a>Spusťte příkaz ' check ' iotedge

Prvním krokem při řešení potíží IoT Edge by měl být použití `check` příkazu, který spouští kolekci konfigurací a testů připojení pro běžné problémy. `check` Příkaz je k dispozici ve [verzi 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) a novější.

Tento `check` příkaz můžete spustit následujícím způsobem, nebo pokud chcete zobrazit `--help` úplný seznam možností, přidejte příznak.

* V systému Linux:

  ```bash
  sudo iotedge check
  ```

* Ve Windows:

  ```powershell
  iotedge check
  ```

Nástroj pro řešení potíží spustí mnoho kontrol, které jsou seřazené do těchto tří kategorií:

* Kontroly konfigurace: kontroluje podrobnosti, které by mohly bránit hraničním zařízením v připojení ke cloudu, včetně problémů s *config. yaml* a modulem kontejnerů.
* Kontroly připojení: ověří, že modul runtime IoT Edge má přístup k portům na hostitelském zařízení a všechny IoT Edge součásti se mohou připojit k IoT Hub.
* Kontroly připravenosti výroby: vyhledá Doporučené provozní postupy, jako je například stav certifikátů certifikační autorita zařízení (CA) a konfigurace souboru protokolu modulu.

Informace o všech diagnostických kontrolách, které tento nástroj spouští, včetně toho, co dělat, pokud se zobrazí chyba nebo upozornění, najdete v tématu [IoT Edge řešení potíží](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-iotedge-support-bundle-command"></a>Shromažďování informací o ladění pomocí příkazu iotedge ' support-komplet '

Když potřebujete shromažďovat protokoly ze zařízení IoT Edge, nejpohodlnější způsob je použít `support-bundle` příkaz. Ve výchozím nastavení tento příkaz shromažďuje modul, IoT Edge správce zabezpečení a protokoly kontejnerů, "výstup JSON" iotedge check "a další užitečné ladicí informace. Komprimuje je do jednoho souboru pro snadné sdílení. `support-bundle` Příkaz je k dispozici ve [verzi 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) a novější.

Spusťte `support-bundle` příkaz s `--since` příznakem a určete tak, jak dlouho od minulosti chcete získat protokoly. Například `6h` načte protokoly za posledních 6 hodin, `6d` za posledních 6 dní, `6m` od posledních 6 minut a tak dále. Pokud chcete `--help` Zobrazit úplný seznam možností, přidejte příznak.

* V systému Linux:

  ```bash
  sudo iotedge support-bundle --since 6h
  ```

* Ve Windows:

  ```powershell
  iotedge support-bundle --since 6h
  ```

> [!WARNING]
> Výstupem `support-bundle` příkazu může být název hostitele, zařízení a modulu, informace zaznamenané moduly atd. Uvědomte si prosím tuto akci, pokud sdílíte výstup ve veřejném fóru.

## <a name="standard-diagnostic-steps"></a>Standardní postup diagnostiky

Pokud narazíte na problém, můžete získat další informace o stavu zařízení IoT Edge. Projděte si protokoly kontejnerů a zprávy, které předávají a ze zařízení. Ke shromáždění informací použijte příkazy a nástroje uvedené v této části.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Kontrolovat stav IoT Edge Security Manageru a jeho protokolů

V systému Linux:

* Zobrazení stavu IoT Edge Security Manageru:

   ```bash
   sudo systemctl status iotedge
   ```

* Postup zobrazení protokolů IoT Edge Security Manager:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Zobrazení podrobnějších protokolů IoT Edge Security Manageru:

  * Upravit nastavení démona iotedge:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Aktualizujte následující řádky:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Restartujte proces démona zabezpečení IoT Edge:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

Ve Windows:

* Zobrazení stavu IoT Edge Security Manageru:

   ```powershell
   Get-Service iotedge
   ```

* Postup zobrazení protokolů IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Pokud IoT Edge Security Manager není spuštěný, ověřte konfigurační soubor YAML.

> [!WARNING]
> Soubory YAML nemůžou jako odsazení obsahovat tabulátory. Místo toho použijte 2 mezery. Elementy na nejvyšší úrovni by neměly mít žádné úvodní mezery.

V systému Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Ve Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Kontrolovat problémy v protokolech kontejnerů

Jakmile je spuštěn démon zabezpečení IoT Edge, podívejte se do protokolů kontejnerů a zjistěte problémy. Začněte s nasazenými kontejnery a pak se podívejte do kontejnerů, které tvoří modul runtime IoT Edge: edgeAgent a edgeHub. Protokoly agenta IoT Edge obvykle poskytují informace o životním cyklu každého kontejneru. Protokoly centra IoT Edge poskytují informace o zasílání zpráv a směrování.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Zobrazení zpráv, které procházejí centrem centra IoT Edge

Zprávy můžete zobrazit prostřednictvím centra IoT Edge a shromažďovat přehledy z podrobných protokolů z kontejnerů modulu runtime. Pokud chcete zapnout podrobné protokoly těchto kontejnerů, nastavte `RuntimeLogLevel` v konfiguračním souboru YAML. Otevření souboru:

V systému Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Ve Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Ve výchozím nastavení bude `agent` element vypadat jako v následujícím příkladu:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Nahradit `env: {}` :

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > Soubory YAML nemůžou obsahovat tabulátory jako identation. Místo toho použijte 2 mezery. Položky nejvyšší úrovně nemohou mít počáteční prázdné znaky.

Uložte soubor a restartujte správce zabezpečení IoT Edge.

Můžete zkontrolovat také zprávy odesílané mezi službou IoT Hub a hraničními zařízeními IoT. Zobrazte tyto zprávy pomocí [rozšíření Azure IoT Hub pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Další informace najdete v tématu [praktický nástroj při vývoji s využitím Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Restartovat kontejnery

Po prozkoumání protokolů a zpráv pro informace můžete zkusit restartovat kontejnery:

```cmd
iotedge restart <container name>
```

Restartujte IoT Edge kontejnery modulu runtime:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Restartujte správce zabezpečení IoT Edge

Pokud je problém stále přetrvává, můžete zkusit restartovat správce zabezpečení IoT Edge.

V systému Linux:

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

**Hlavní příčina**

Konfigurace sítě v hostitelské síti brání tomu, aby agent IoT Edge dosáhnout sítě. Agent se nejprve pokusí připojit přes protokol AMQP (port 5671). Pokud se připojení nepovede, zkusí WebSocket (port 443).

Modul runtime IoT Edge nastaví pro každý z modulů síť, na které budou komunikovat. V Linuxu je tato síť síťovým mostem. Ve Windows využívá překlad adres (NAT). K tomuto problému častěji dochází na zařízeních s Windows využívajících kontejnery Windows a síť s překladem adres (NAT).

**Rozlišení**

Ujistěte se, že se IP adresy přiřazené k tomuto síťovému mostu nebo síti NAT směrují do internetu. Někdy konfigurace sítě VPN na hostiteli přepíše síť IoT Edge.

## <a name="iot-edge-hub-fails-to-start"></a>Nepodařilo se spustit centrum IoT Edge.

Nepodařilo se spustit modul edgeHub a vytiskne následující zprávu do protokolů:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Hlavní příčina**

Nějaký jiný proces na hostitelském počítači používá port 443. Rozbočovač IoT Edge mapuje porty 5671 a 443 pro použití ve scénářích bran. Toto mapování portů selže, pokud daný port již používá jiný proces.

**Rozlišení**

Vyhledejte a zastavte proces používající port 443. Tímto procesem je obvykle webový server.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>Agent IoT Edge nemá přístup k imagi modulu (403).

Nepodařilo se spustit kontejner a v protokolech edgeAgent se zobrazí chyba 403.

**Hlavní příčina**

Agent IoT Edge nemá oprávnění pro přístup k imagi modulu.

**Rozlišení**

Ujistěte se, že přihlašovací údaje registru jsou správně zadané v manifestu nasazení.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge démon zabezpečení se nezdařil s neplatným názvem hostitele.

Příkaz `sudo journalctl -u iotedge` se nezdařil a vytiskne následující zprávu:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Hlavní příčina**

Modul runtime IoT Edge může podporovat pouze názvy hostitelů, které jsou kratší než 64 znaků. Fyzické počítače většinou nemají dlouhé názvy hostitelů, ale tento problém je častější na virtuálním počítači. Automaticky vygenerované názvy hostitelů pro virtuální počítače s Windows hostované v Azure, zejména obvykle dlouhé.

**Rozlišení**

Když se zobrazí tato chyba, můžete ji vyřešit tak, že nakonfigurujete název DNS virtuálního počítače a pak nastavíte název DNS jako název hostitele v příkazu pro instalaci.

1. V Azure Portal přejděte na stránku Přehled vašeho virtuálního počítače.
2. V části název DNS vyberte **Konfigurovat** . Pokud má váš virtuální počítač už nakonfigurovaný název DNS, nemusíte konfigurovat nový.

   ![Konfigurace názvu DNS virtuálního počítače](./media/troubleshoot/configure-dns.png)

3. Zadejte hodnotu **jmenovky názvu DNS** a vyberte **Uložit**.
4. Zkopírujte nový název DNS, který by měl být ve formátu ** \<DNSnamelabel\>.\< vmlocation\>. cloudapp.Azure.com**.
5. Ve virtuálním počítači pomocí následujícího příkazu nastavte modul runtime IoT Edge s vaším názvem DNS:

   * V systému Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * Ve Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problémy stability na omezených zařízeních prostředků

U omezených zařízení, jako je například PI, se můžete setkat s problémy se stabilitou, zejména pokud se používá jako brána. Mezi příznaky patří výjimky z paměti v modulu Edge hub. po několika hodinách se zařízení nemohou připojit nebo zařízení přestane odesílat zprávy telemetrie.

**Hlavní příčina**

Rozbočovač IoT Edge, který je součástí modulu runtime IoT Edge, je ve výchozím nastavení optimalizován pro výkon a pokusí se přidělit velké bloky paměti. Tato optimalizace není ideální pro omezení hraničních zařízení a může způsobit problémy s stabilitou.

**Rozlišení**

Pro Centrum IoT Edge nastavte proměnnou prostředí **OptimizeForPerformance** na **false**. Existují dva způsoby, jak nastavit proměnné prostředí:

Na webu Azure Portal:

V IoT Hub vyberte své zařízení IoT Edge a na stránce Podrobnosti o zařízení a vyberte **nastavit moduly** > **nastavení modulu runtime**. Vytvořte proměnnou prostředí pro modul hraničního centra s názvem *OptimizeForPerformance* , který je nastaven na *hodnotu false*.

![OptimizeForPerformance nastaveno na hodnotu false](./media/troubleshoot/optimizeforperformance-false.png)

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

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Nelze získat protokoly IoT Edge démona v systému Windows.

Pokud se při použití `Get-WinEvent` v systému Windows zobrazí EventLogException, podívejte se na položky registru.

**Hlavní příčina**

Příkaz `Get-WinEvent` prostředí PowerShell spoléhá na přítomnost položky registru, aby bylo možné najít protokoly podle konkrétního `ProviderName`.

**Rozlišení**

Nastavte položku registru pro démona IoT Edge. Vytvořte soubor **iotedge. reg** s následujícím obsahem a naimportujte ho do registru Windows tak, že na něj dvakrát kliknete nebo použijete `reg import iotedge.reg` příkaz:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>IoT Edge modulu se nepovedlo odeslat zprávu do edgeHub s chybou 404.

Vlastní modul IoT Edge nedokáže odeslat zprávu do edgeHub s chybou 404 `Module not found` . Démon IoT Edge v protokolech vytiskne následující zprávu:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Hlavní příčina**

Démon IoT Edge vynutil identifikaci procesu pro všechny moduly, které se připojují k edgeHub z bezpečnostních důvodů. Ověřuje, že všechny zprávy odesílané modulem přicházejí z hlavního ID procesu modulu. Pokud je zpráva odesílána modulem z jiného ID procesu, než je původně navázána, bude zpráva odmítnuta chybovou zprávou 404.

**Rozlišení**

Od verze 1.0.7 jsou všechny procesy modulů autorizované pro připojení. Pokud upgrade na 1.0.7 není možný, proveďte následující kroky. Další informace najdete v tématu protokol [změn verze 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Ujistěte se, že vlastní modul IoT Edge vždy používá stejné ID procesu k posílání zpráv do edgeHub. Například se ujistěte, `ENTRYPOINT` že místo `CMD` příkazu v souboru Docker, protože `CMD` povede k jednomu ID procesu pro modul a jiné ID procesu pro příkaz bash, který spouští hlavní program, zatímco `ENTRYPOINT` povede k jednomu ID procesu.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Pravidla konfigurace brány firewall a portů pro nasazení IoT Edge

Azure IoT Edge umožňuje komunikaci z místního serveru do cloudu Azure pomocí podporovaných protokolů IoT Hub najdete v tématu [Volba komunikačního protokolu](../iot-hub/iot-hub-devguide-protocols.md). Pro lepší zabezpečení jsou komunikační kanály mezi Azure IoT Edge a Azure IoT Hub vždycky nakonfigurované jako odchozí. Tato konfigurace je založená na [vzoru komunikace s asistencí služeb](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), který minimalizuje plochu pro útok na škodlivou entitu k prozkoumávání. Příchozí komunikace se vyžaduje jenom pro konkrétní scénáře, kdy Azure IoT Hub potřebuje odeslat zprávy do Azure IoT Edge zařízení. Zprávy z cloudu na zařízení jsou chráněné pomocí zabezpečených kanálů TLS a je možné je dál zabezpečit pomocí certifikátů X. 509 a modulů zařízení TPM. Azure IoT Edge Security Manager určuje, jak může být tato komunikace navázána, viz [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

I když IoT Edge poskytuje rozšířenou konfiguraci pro zabezpečení Azure IoT Edge runtime a nasazených modulů, je stále závislý na základní konfiguraci počítače a sítě. Proto je nutné zajistit, aby byla pro zabezpečená komunikace s cloudovou komunikací nastavena správná pravidla sítě a brány firewall. Následující tabulku lze použít jako vodítko při konfiguraci pravidel brány firewall pro základní servery, kde je hostovaný Azure IoT Edge Runtime:

|Protocol (Protokol)|Port|Příchozí|Odesílaná|Doprovodné materiály|
|--|--|--|--|--|
|MQTT|8883|BLOKOVÁNo (výchozí)|BLOKOVÁNo (výchozí)|<ul> <li>Konfigurace odchozího (odchozího), aby se otevírala při používání MQTT jako komunikačního protokolu<li>1883 pro MQTT není podporován IoT Edge. <li>Příchozí (příchozí) připojení by měla být blokovaná.</ul>|
|AMQP|5671|BLOKOVÁNo (výchozí)|OTEVŘÍT (výchozí)|<ul> <li>Výchozí komunikační protokol pro IoT Edge. <li> Musí být nastavené tak, aby byly otevřené, pokud není Azure IoT Edge nakonfigurovaný pro jiné podporované protokoly nebo že AMQP je požadovaný komunikační protokol.<li>5672 pro AMQP není podporován IoT Edge.<li>Zablokovat tento port, když Azure IoT Edge používá jiný podporovaný protokol IoT Hub.<li>Příchozí (příchozí) připojení by měla být blokovaná.</ul></ul>|
|HTTPS|443|BLOKOVÁNo (výchozí)|OTEVŘÍT (výchozí)|<ul> <li>Konfigurace odchozího (odchozího), která se má otevřít v 443 pro IoT Edge zřizování Tato konfigurace se vyžaduje při použití ručních skriptů nebo služby Azure IoT Device Provisioning (DPS). <li>Příchozí (příchozí) připojení by se mělo otevřít jenom pro konkrétní scénáře: <ul> <li>  Máte transparentní bránu s koncovými zařízeními, která mohou odesílat požadavky na metody. V takovém případě nemusí být port 443 otevřený pro externí sítě pro připojení k IoTHub nebo poskytování služeb IoTHub prostřednictvím Azure IoT Edge. Příchozí pravidlo tedy může být omezeno pouze na otevřené příchozí (příchozí) z interní sítě. <li> Pro scénáře klienta k zařízení (C2D).</ul><li>80 pro protokol HTTP není IoT Edge podporován.<li>Pokud v podniku nemůžete nakonfigurovat protokoly bez protokolu HTTP (například AMQP nebo MQTT), zprávy lze odesílat přes objekty WebSockets. V takovém případě bude v takovém případě použit port 443 pro komunikaci protokolu WebSocket.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Modul Edge agent průběžně hlásí prázdný konfigurační soubor a žádné moduly na zařízení nezačínají.

Zařízení má potíže se spouštěním modulů definovaných v nasazení. Běží jenom edgeAgent, ale průběžně hlásí prázdný konfigurační soubor...

**Hlavní příčina**

Ve výchozím nastavení IoT Edge spouští moduly ve vlastní izolované síti kontejneru. Zařízení může mít potíže s překladem názvů DNS v rámci této privátní sítě.

**Rozlišení**

**Možnost 1: nastavení serveru DNS v nastavení modulu pro vytvoření kontejneru**

Zadejte server DNS pro vaše prostředí v nastavení modulu container Engine, který bude platit pro všechny moduly kontejneru spouštěné modulem. Vytvořte soubor s názvem `daemon.json` určení serveru DNS, který chcete použít. Příklad:

```json
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

Restartujte modul kontejnerů, aby se aktualizace projevily.

| Platforma | Příkaz |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (PowerShell pro správu) | `Restart-Service iotedge-moby -Force` |

**Možnost 2: nastavení serveru DNS v nasazení IoT Edge na modul**

Můžete nastavit server DNS pro *createOptions* modulu v nasazení IoT Edge. Příklad:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Ujistěte se, že jste tuto konfiguraci nastavili také pro moduly *edgeAgent* a *edgeHub* .

## <a name="next-steps"></a>Další kroky

Myslíte si, že jste v platformě IoT Edge našli chybu? [Odešlete problém](https://github.com/Azure/iotedge/issues) , abychom mohli pokračovat v vylepšování.

Pokud máte více otázek, vytvořte [support Request](https://portal.azure.com/#create/Microsoft.Support) pro nápovědu.
