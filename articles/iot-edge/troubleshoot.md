---
title: Řešení potíží – Azure IoT Edge | Microsoft Docs
description: V tomto článku se naučíte používat standardní diagnostické dovednosti pro Azure IoT Edge, jako je načítání stavu komponent a protokolů.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: daae45c9eca45022225ea47aa048815d5eff70c4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964503"
---
# <a name="troubleshoot-your-iot-edge-device"></a>Řešení potíží s IoT Edgem zařízením

Pokud máte problémy se spouštěním Azure IoT Edge ve vašem prostředí, použijte tento článek jako vodítko pro řešení potíží a diagnostiku.

## <a name="run-the-check-command"></a>Spuštění příkazu check

Prvním krokem při řešení potíží IoT Edge by měl být použití `check` příkazu, který spouští kolekci konfigurací a testů připojení pro běžné problémy. `check`Příkaz je k dispozici ve [verzi 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) a novější.

>[!NOTE]
>Nástroj pro řešení potíží nemůže spustit kontroly připojení, pokud je zařízení IoT Edge za proxy server.

Tento příkaz můžete spustit `check` následujícím způsobem, nebo pokud `--help` chcete zobrazit úplný seznam možností, přidejte příznak.

V systému Linux:

```bash
sudo iotedge check
```

Ve Windows:

```powershell
iotedge check
```

Nástroj pro řešení potíží spustí mnoho kontrol, které jsou seřazené do těchto tří kategorií:

* *Kontroly konfigurace* prozkoumají podrobnosti, které by mohly bránit IoT Edge zařízením v připojení ke cloudu, včetně problémů s *config. yaml* a modulem kontejnerů.
* *Kontroly připojení* ověřují, zda IoT Edge runtime má přístup k portům na hostitelském zařízení a že se všechny součásti IoT Edge mohou připojit k IoT Hub. Tato sada kontrol vrátí chyby, pokud je zařízení IoT Edge za proxy serverem.
* *Kontroly připravenosti na produkci* hledají Doporučené provozní postupy, jako je například stav certifikátů certifikační autorita zařízení (CA) a konfigurace souboru protokolu modulu.

Nástroj pro kontrolu IoT Edge používá ke spuštění diagnostiky kontejner. Image kontejneru `mcr.microsoft.com/azureiotedge-diagnostics:latest` je dostupná prostřednictvím [Microsoft Container Registry](https://github.com/microsoft/containerregistry). Pokud potřebujete spustit kontrolu zařízení bez přímého přístupu k Internetu, zařízení budou potřebovat přístup k imagi kontejneru.

Informace o všech diagnostických kontrolách, které tento nástroj spouští, včetně toho, co dělat, pokud se zobrazí chyba nebo upozornění, najdete v tématu [IoT Edge řešení potíží](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-support-bundle-command"></a>Shromažďování informací o ladění pomocí příkazu support-komplet

Když potřebujete shromažďovat protokoly ze zařízení IoT Edge, nejpohodlnější způsob je použít `support-bundle` příkaz. Ve výchozím nastavení tento příkaz shromažďuje modul, IoT Edge správce zabezpečení a protokoly kontejnerů, `iotedge check` výstup JSON a další užitečné ladicí informace. Komprimuje je do jednoho souboru pro snadné sdílení. `support-bundle`Příkaz je k dispozici ve [verzi 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) a novější.

Spusťte `support-bundle` příkaz s `--since` příznakem a určete tak, jak dlouho od minulosti chcete získat protokoly. Například načte `6h` protokoly za posledních šest hodin, za posledních šest `6d` dní, za `6m` posledních šest minut a tak dále. Pokud `--help` chcete zobrazit úplný seznam možností, přidejte příznak.

V systému Linux:

```bash
sudo iotedge support-bundle --since 6h
```

Ve Windows:

```powershell
iotedge support-bundle --since 6h
```

Můžete také použít přímé volání [metody](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics) do vašeho zařízení a nahrát výstup příkazu support-rekomplet do Azure Blob Storage.

> [!WARNING]
> Výstupem `support-bundle` příkazu může být název hostitele, zařízení a modulu, informace zaznamenané moduly atd. Uvědomte si prosím tuto akci, pokud sdílíte výstup ve veřejném fóru.

## <a name="check-your-iot-edge-version"></a>Ověřit verzi IoT Edge

Pokud používáte starší verzi IoT Edge, je možné, že se váš problém vyřeší upgradem. `iotedge check`Nástroj kontroluje, zda je démon zabezpečení IoT Edge nejnovější verze, ale nekontroluje verze IoT Edge centra a agentů. Chcete-li zjistit verzi modulů runtime na vašem zařízení, použijte příkazy `iotedge logs edgeAgent` a `iotedge logs edgeHub` . Číslo verze se zobrazí v protokolech po spuštění modulu.

Pokyny k aktualizaci zařízení najdete v tématu [aktualizace démona zabezpečení IoT Edge a modulu runtime](how-to-update-iot-edge.md).

## <a name="verify-the-installation-of-iot-edge-on-your-devices"></a>Ověření instalace IoT Edge na zařízeních

Instalaci IoT Edge můžete ověřit na svých zařízeních tím, že [monitoruje nevlákenný modul edgeAgent](https://docs.microsoft.com/azure/iot-edge/how-to-monitor-module-twins).

Chcete-li získat nejnovější edgeAgent modul, spusťte následující příkaz z [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub module-twin show --device-id <edge_device_id> --module-id $edgeAgent --hub-name <iot_hub_name>
   ```

Tento příkaz zobrazí výstup všech [hlášených vlastností](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub)edgeAgent. Tady je několik užitečných sledování stavu zařízení:

* Běhový stav
* čas spuštění za běhu
* čas posledního ukončení modulu runtime
* počet restartování za běhu

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Kontrolovat stav IoT Edge Security Manageru a jeho protokolů

[IoT Edge Security Manager](iot-edge-security-manager.md) zodpovídá za operace, jako je inicializace IoT Edge systému při spuštění a zřizování zařízení. Pokud IoT Edge nezačnete, můžou vám poskytnout užitečné informace protokoly správce zabezpečení.

V systému Linux:

* Zobrazit stav správce zabezpečení IoT Edge:

   ```bash
   sudo systemctl status iotedge
   ```

* Zobrazit protokoly IoT Edge Security Manageru:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Zobrazit podrobnější protokoly IoT Edge Security Manageru:

  * Upravte nastavení IoT Edge démona:

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

* Zobrazit stav správce zabezpečení IoT Edge:

   ```powershell
   Get-Service iotedge
   ```

* Zobrazit protokoly IoT Edge Security Manageru:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* Zobrazit pouze posledních 5 minut protokolu IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* Zobrazit podrobnější protokoly IoT Edge Security Manageru:

  * Přidat proměnnou prostředí na úrovni systému:

      ```powershell
      [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
      ```

  * Restartujte proces démona zabezpečení IoT Edge:

      ```powershell
      Restart-Service iotedge
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

## <a name="check-container-logs-for-issues"></a>Kontrolovat problémy v protokolech kontejnerů

Jakmile je spuštěn démon zabezpečení IoT Edge, podívejte se do protokolů kontejnerů a zjistěte problémy. Začněte s nasazenými kontejnery a pak se podívejte do kontejnerů, které tvoří modul runtime IoT Edge: edgeAgent a edgeHub. Protokoly agenta IoT Edge obvykle poskytují informace o životním cyklu každého kontejneru. Protokoly centra IoT Edge poskytují informace o zasílání zpráv a směrování.

```cmd
iotedge logs <container name>
```

Můžete také použít přímé volání [metody](how-to-retrieve-iot-edge-logs.md#upload-module-logs) do modulu v zařízení k nahrání protokolů tohoto modulu do Azure Blob Storage.

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Zobrazení zpráv, které procházejí centrem centra IoT Edge

Zprávy můžete zobrazit prostřednictvím centra IoT Edge a shromažďovat přehledy z podrobných protokolů z kontejnerů modulu runtime. Pokud chcete zapnout podrobné protokoly těchto kontejnerů, nastavte `RuntimeLogLevel` v konfiguračním souboru YAML. Otevření souboru:

V systému Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Ve Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Ve výchozím nastavení `agent` bude element vypadat jako v následujícím příkladu:

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

## <a name="restart-containers"></a>Restartovat kontejnery

Po prozkoumání protokolů a zpráv pro informace můžete zkusit restartovat kontejnery:

```cmd
iotedge restart <container name>
```

Restartujte IoT Edge kontejnery modulu runtime:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>Ověřte bránu firewall a pravidla konfigurace portů.

Azure IoT Edge umožňuje komunikaci z místního serveru do cloudu Azure pomocí podporovaných protokolů IoT Hub najdete v tématu [Volba komunikačního protokolu](../iot-hub/iot-hub-devguide-protocols.md). Pro lepší zabezpečení jsou komunikační kanály mezi Azure IoT Edge a Azure IoT Hub vždycky nakonfigurované jako odchozí. Tato konfigurace je založená na [vzoru komunikace s asistencí služeb](/archive/blogs/clemensv/service-assisted-communication-for-connected-devices), který minimalizuje plochu pro útok na škodlivou entitu k prozkoumávání. Příchozí komunikace se vyžaduje jenom pro konkrétní scénáře, kdy Azure IoT Hub potřebuje odeslat zprávy do Azure IoT Edge zařízení. Zprávy z cloudu na zařízení jsou chráněné pomocí zabezpečených kanálů TLS a je možné je dál zabezpečit pomocí certifikátů X. 509 a modulů zařízení TPM. Azure IoT Edge Security Manager určuje, jak může být tato komunikace navázána, viz [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

I když IoT Edge poskytuje rozšířenou konfiguraci pro zabezpečení Azure IoT Edge runtime a nasazených modulů, je stále závislý na základní konfiguraci počítače a sítě. Proto je nutné zajistit, aby byla pro zabezpečená komunikace s cloudovou komunikací nastavena správná pravidla sítě a brány firewall. Následující tabulku lze použít jako vodítko při konfiguraci pravidel brány firewall pro základní servery, kde je hostovaný Azure IoT Edge Runtime:

|Protokol|Port|Příchozí|Odesílaná|Pokyny|
|--|--|--|--|--|
|MQTT|8883|BLOKOVÁNo (výchozí)|BLOKOVÁNo (výchozí)|<ul> <li>Konfigurace odchozího (odchozího), aby se otevírala při používání MQTT jako komunikačního protokolu<li>1883 pro MQTT není podporován IoT Edge. <li>Příchozí (příchozí) připojení by měla být blokovaná.</ul>|
|AMQP|5671|BLOKOVÁNo (výchozí)|OTEVŘÍT (výchozí)|<ul> <li>Výchozí komunikační protokol pro IoT Edge. <li> Musí být nastavené tak, aby byly otevřené, pokud není Azure IoT Edge nakonfigurovaný pro jiné podporované protokoly nebo že AMQP je požadovaný komunikační protokol.<li>5672 pro AMQP není podporován IoT Edge.<li>Zablokovat tento port, když Azure IoT Edge používá jiný podporovaný protokol IoT Hub.<li>Příchozí (příchozí) připojení by měla být blokovaná.</ul></ul>|
|HTTPS|443|BLOKOVÁNo (výchozí)|OTEVŘÍT (výchozí)|<ul> <li>Konfigurace odchozího (odchozího), která se má otevřít v 443 pro IoT Edge zřizování Tato konfigurace se vyžaduje při použití ručních skriptů nebo služby Azure IoT Device Provisioning (DPS). <li>Příchozí (příchozí) připojení by se mělo otevřít jenom pro konkrétní scénáře: <ul> <li>  Máte transparentní bránu s koncovými zařízeními, která mohou odesílat požadavky na metody. V takovém případě nemusí být port 443 otevřený pro externí sítě pro připojení k IoTHub nebo poskytování služeb IoTHub prostřednictvím Azure IoT Edge. Příchozí pravidlo tedy může být omezeno pouze na otevřené příchozí (příchozí) z interní sítě. <li> Pro scénáře klienta k zařízení (C2D).</ul><li>80 pro protokol HTTP není IoT Edge podporován.<li>Pokud v podniku nemůžete nakonfigurovat protokoly bez protokolu HTTP (například AMQP nebo MQTT), zprávy lze odesílat přes objekty WebSockets. V takovém případě bude v takovém případě použit port 443 pro komunikaci protokolu WebSocket.</ul>|

## <a name="next-steps"></a>Další kroky

Myslíte si, že jste v platformě IoT Edge našli chybu? [Odešlete problém](https://github.com/Azure/iotedge/issues) , abychom mohli pokračovat v vylepšování.

Pokud máte více otázek, vytvořte [support Request](https://portal.azure.com/#create/Microsoft.Support) pro nápovědu.