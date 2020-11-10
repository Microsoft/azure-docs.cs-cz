---
title: Běžné chyby – Azure IoT Edge | Microsoft Docs
description: Pomocí tohoto článku můžete vyřešit běžné problémy zjištěné při nasazení řešení IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 98ee865a3ddf6c26ffe9cb77767f3872b42018d8
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442357"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Běžné potíže se službou Azure IoT Edge a jejich řešení

V tomto článku najdete postup řešení běžných problémů, ke kterým může docházet při nasazení IoT Edge řešení. Pokud se potřebujete dozvědět, jak najít protokoly a chyby ze zařízení IoT Edge, přečtěte si téma [řešení potíží se zařízením IoT Edge](troubleshoot.md).

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>Agent IoT Edge se zastaví po přibližně minutě.

**Pozorované chování:**

Modul edgeAgent se spustí a úspěšně se spustí přibližně minutu a pak se zastaví. Protokoly signalizují, že se agent IoT Edge pokusí připojit k IoT Hub přes AMQP a pak se pokusí připojit pomocí AMQP přes WebSocket. Pokud se to nepovede, agent IoT Edge se ukončí.

Příklady protokolů edgeAgent:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Hlavní příčina:**

Konfigurace sítě v hostitelské síti brání tomu, aby agent IoT Edge dosáhnout sítě. Agent se nejprve pokusí připojit přes protokol AMQP (port 5671). Pokud se připojení nepovede, zkusí WebSocket (port 443).

Modul runtime IoT Edge nastaví pro každý z modulů síť, na které budou komunikovat. V Linuxu je tato síť síťovým mostem. Ve Windows využívá překlad adres (NAT). K tomuto problému častěji dochází na zařízeních s Windows využívajících kontejnery Windows a síť s překladem adres (NAT).

**Rozhodnutí**

Ujistěte se, že se IP adresy přiřazené k tomuto síťovému mostu nebo síti NAT směrují do internetu. Někdy konfigurace sítě VPN na hostiteli přepíše síť IoT Edge.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>Agent IoT Edge nemá přístup k imagi modulu (403).

**Pozorované chování:**

Nepodařilo se spustit kontejner a v protokolech edgeAgent se zobrazí chyba 403.

**Hlavní příčina:**

Agent IoT Edge nemá oprávnění pro přístup k imagi modulu.

**Rozhodnutí**

Ujistěte se, že přihlašovací údaje registru jsou správně zadané v manifestu nasazení.

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Modul hraničního agenta hlásí prázdný konfigurační soubor a v zařízení nezačíná žádné moduly.

**Pozorované chování:**

Zařízení má potíže se spouštěním modulů definovaných v nasazení. Běží jenom edgeAgent, ale průběžně hlásí prázdný konfigurační soubor...

**Hlavní příčina:**

Ve výchozím nastavení IoT Edge spouští moduly ve vlastní izolované síti kontejneru. Zařízení může mít potíže s překladem názvů DNS v rámci této privátní sítě.

**Rozhodnutí**

**Možnost 1: nastavení serveru DNS v nastavení modulu pro vytvoření kontejneru**

Zadejte server DNS pro vaše prostředí v nastavení modulu container Engine, který bude platit pro všechny moduly kontejneru spouštěné modulem. Vytvořte soubor s názvem `daemon.json` Určení serveru DNS, který chcete použít. Příklad:

```json
{
    "dns": ["1.1.1.1"]
}
```

Výše uvedený příklad nastaví server DNS na veřejně dostupnou službu DNS. Pokud hraniční zařízení nemá k této IP adrese přístup z jeho prostředí, nahraďte ho adresou serveru DNS, která je přístupná.

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

## <a name="iot-edge-hub-fails-to-start"></a>Nepodařilo se spustit centrum IoT Edge.

**Pozorované chování:**

Spuštění modulu edgeHub se nezdařilo. V protokolech se může zobrazit zpráva podobná jedné z následujících chyb:

```output
One or more errors occurred.
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80):
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

Nebo

```output
info: edgelet_docker::runtime -- Starting module edgeHub...
warn: edgelet_utils::logging -- Could not start module edgeHub
warn: edgelet_utils::logging --     caused by: failed to create endpoint edgeHub on network nat: hnsCall failed in Win32:  
        The process cannot access the file because it is being used by another process. (0x20)
```

**Hlavní příčina:**

Některé jiné procesy na hostitelském počítači mají vazbu na port, který se pokouší vytvořit modul edgeHub. Centrum IoT Edge mapuje porty 443, 5671 a 8883 pro použití ve scénářích bran. Modul se nepovede spustit, pokud už je jeden z těchto portů vázaný na jiný proces.

**Rozhodnutí**

Tento problém můžete vyřešit dvěma způsoby:

Pokud zařízení IoT Edge funguje jako zařízení brány, je nutné najít a zastavit proces, který používá port 443, 5671 nebo 8883. Chyba pro port 443 obvykle znamená, že druhý proces je webový server.

Pokud nepotřebujete zařízení IoT Edge používat jako bránu, můžete odebrat vazby portů z možností vytvoření modulu edgeHub. Můžete změnit možnosti vytvoření v Azure Portal nebo přímo v deployment.jsv souboru.

Na webu Azure Portal:

1. Přejděte do svého centra IoT a vyberte **IoT Edge**.

2. Vyberte zařízení IoT Edge, které chcete aktualizovat.

3. Vyberte možnost **nastavit moduly**.

4. Vyberte **nastavení modulu runtime**.

5. V nastavení modulu **Edge hub** odstraňte vše z textového pole **vytvořit možnosti** .

6. Uložte změny a vytvořte nasazení.

V deployment.jssouboru:

1. Otevřete deployment.jspro soubor, který jste použili pro IoT Edge zařízení.

2. `edgeHub`V části požadované vlastnosti edgeAgent vyhledejte nastavení:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
           "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

3. Odstraňte `createOptions` řádek a koncovou čárku na konci `image` řádku před ním:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.0"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

4. Uložte soubor a znovu ho nainstalujte do zařízení IoT Edge.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge démon zabezpečení se nezdařil s neplatným názvem hostitele.

**Pozorované chování:**

Při pokusu o [kontrolu IoT Edge protokolů Security Manageru](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs) dojde k chybě a je vytištěna následující zpráva:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Hlavní příčina:**

Modul runtime IoT Edge může podporovat pouze názvy hostitelů, které jsou kratší než 64 znaků. Fyzické počítače většinou nemají dlouhé názvy hostitelů, ale tento problém je častější na virtuálním počítači. Automaticky vygenerované názvy hostitelů pro virtuální počítače s Windows hostované v Azure, zejména obvykle dlouhé.

**Rozhodnutí**

Když se zobrazí tato chyba, můžete ji vyřešit tak, že nakonfigurujete název DNS virtuálního počítače a pak nastavíte název DNS jako název hostitele v příkazu pro instalaci.

1. V Azure Portal přejděte na stránku Přehled vašeho virtuálního počítače.
2. V části název DNS vyberte **Konfigurovat** . Pokud má váš virtuální počítač už nakonfigurovaný název DNS, nemusíte konfigurovat nový.

   ![Konfigurace názvu DNS virtuálního počítače](./media/troubleshoot/configure-dns.png)

3. Zadejte hodnotu **jmenovky názvu DNS** a vyberte **Uložit**.
4. Zkopírujte nový název DNS, který by měl být ve formátu **\<DNSnamelabel\> . \<vmlocation\> . cloudapp.azure.com**.
5. Ve virtuálním počítači pomocí následujícího příkazu nastavte modul runtime IoT Edge s vaším názvem DNS:

   * V systému Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * Ve Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Nelze získat protokoly IoT Edge démona v systému Windows.

**Pozorované chování:**

Při použití ve Windows se zobrazí EventLogException `Get-WinEvent` .

**Hlavní příčina:**

`Get-WinEvent`Příkaz prostředí PowerShell spoléhá na přítomnost položky registru, aby bylo možné najít protokoly podle konkrétního `ProviderName` .

**Rozhodnutí**

Nastavte položku registru pro démona IoT Edge. Vytvořte soubor **iotedge. reg** s následujícím obsahem a naimportujte ho do registru Windows tak, že na něj dvakrát kliknete nebo použijete `reg import iotedge.reg` příkaz:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="stability-issues-on-smaller-devices"></a>Problémy s stabilitou na menších zařízeních

**Pozorované chování:**

Můžete se setkat s problémy se stabilitou na zařízeních s omezeným využitím prostředků, jako je například Malina Pi, zejména při použití jako brány. Mezi příznaky patří výjimky z paměti v modulu IoT Edge hub, neúspěšná připojení zařízení, která se nedaří připojit, nebo zařízení, které se po několika hodinách nedaří poslat zprávy telemetrie.

**Hlavní příčina:**

Rozbočovač IoT Edge, který je součástí modulu runtime IoT Edge, je ve výchozím nastavení optimalizován pro výkon a pokusí se přidělit velké bloky paměti. Tato optimalizace není ideální pro omezení hraničních zařízení a může způsobit problémy s stabilitou.

**Rozhodnutí**

Pro Centrum IoT Edge nastavte proměnnou prostředí **OptimizeForPerformance** na **false**. Existují dva způsoby, jak nastavit proměnné prostředí:

Na webu Azure Portal:

V IoT Hub vyberte své zařízení IoT Edge a na stránce Podrobnosti o zařízení a vyberte **nastavit moduly**  >  **nastavení modulu runtime**. Vytvořte proměnnou prostředí pro modul IoT Edge hub s názvem *OptimizeForPerformance* , který je nastaven na *hodnotu false*.

![OptimizeForPerformance nastaveno na hodnotu false](./media/troubleshoot/optimizeforperformance-false.png)

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

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>IoT Edge modulu se nepovedlo odeslat zprávu do edgeHub s chybou 404.

**Pozorované chování:**

Vlastní modul IoT Edge nedokáže odeslat zprávu do centra IoT Edge s `Module not found` chybou 404. Démon IoT Edge v protokolech vytiskne následující zprávu:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Hlavní příčina:**

Démon IoT Edge vynutil identifikaci procesu pro všechny moduly, které se připojují k edgeHub z bezpečnostních důvodů. Ověřuje, že všechny zprávy odesílané modulem přicházejí z hlavního ID procesu modulu. Pokud je zpráva odesílána modulem z jiného ID procesu, než je původně navázána, bude zpráva odmítnuta chybovou zprávou 404.

**Rozhodnutí**

Od verze 1.0.7 jsou všechny procesy modulů autorizované pro připojení. Další informace najdete v tématu protokol [změn verze 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Pokud upgrade na 1.0.7 není možný, proveďte následující kroky. Ujistěte se, že vlastní modul IoT Edge vždy používá stejné ID procesu k posílání zpráv do edgeHub. Například se ujistěte, že `ENTRYPOINT` místo `CMD` příkazu v souboru Docker. `CMD`Příkaz vede k jednomu ID procesu pro modul a jiné ID procesu pro příkaz bash, který spouští hlavní program, ale `ENTRYPOINT` vede k jednomu ID procesu.

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>Nasazení modulu IoT Edge se úspěšně nasazuje ze zařízení.

**Pozorované chování:**

Po nastavení modulů pro zařízení IoT Edge se moduly nasazují úspěšně, ale po několika minutách zmizí ze zařízení a z podrobností o zařízení v Azure Portal. V zařízení se taky můžou zobrazit jiné moduly, než jsou definované.

**Hlavní příčina:**

Pokud se automatické nasazení zaměřuje na zařízení, má přednost před ručním nastavením modulů pro jedno zařízení. Funkce **set modules** v Azure Portal nebo **vytvoření nasazení pro funkce na jednom zařízení** v Visual Studio Code se projeví v průběhu chvilky. Na zařízení se zobrazí moduly, které jste definovali. Priorita automatického nasazení se pak vyplní a přepíše požadované vlastnosti zařízení.

**Rozhodnutí**

Pro každé zařízení můžete použít jenom jeden typ mechanismu nasazení, buď pro automatické nasazení, nebo pro jednotlivá nasazení zařízení. Pokud máte více automatických nasazení cílících na zařízení, můžete změnit prioritu nebo popisy cíle, abyste se ujistili, že se u daného zařízení vztahuje správná možnost. Můžete také aktualizovat dvojitou cílovou možnost zařízení, aby se už neshodovala s popisem automatického nasazení.

Další informace najdete v tématu [vysvětlení IoT Edge automatického nasazení pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

## <a name="iot-edge-behind-a-gateway-cannot-perform-http-requests-and-start-edgeagent-module"></a>IoT Edge za bránou nemůže provádět požadavky HTTP a spustit modul edgeAgent.

**Pozorované chování:**

Démon IoT Edge je aktivní s platným konfiguračním souborem, ale nemůže spustit modul edgeAgent. Příkaz `iotedge list` vrátí prázdný seznam. Sestava protokolů démona IoT Edge `Could not perform HTTP request` .

**Hlavní příčina:**

Zařízení IoT Edge za bránou získají své image modulu z nadřazeného IoT Edge zařízení zadaného v `parent_hostname` poli config. yaml. Tato `Could not perform HTTP request` chyba znamená, že podřízené zařízení není schopné získat přístup k nadřazenému zařízení přes HTTP.

**Rozhodnutí**

Ujistěte se, že nadřazený IoT Edge zařízení může přijímat příchozí žádosti z podřízeného IoT Edge zařízení. Pro požadavky přicházející z podřízeného zařízení otevřete síťový provoz na portech 443 a 6617.

:::moniker-end

## <a name="next-steps"></a>Další kroky

Myslíte si, že jste v platformě IoT Edge našli chybu? [Odešlete problém](https://github.com/Azure/iotedge/issues) , abychom mohli pokračovat v vylepšování.

Pokud máte více otázek, vytvořte [support Request](https://portal.azure.com/#create/Microsoft.Support) pro nápovědu.
