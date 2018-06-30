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
ms.openlocfilehash: 9ec396e8a1ad36e85e1291995345ca1de24668d0
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128056"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Běžné potíže se službou Azure IoT Edge a jejich řešení

Pokud ve vašem prostředí dochází k potížím s provozem služby Azure IoT Edge, použijte tento článek jako vodítko k jejich řešení. 

## <a name="standard-diagnostic-steps"></a>Standardní postup diagnostiky 

Když narazíte na problém, získejte další informace o stavu vašeho zařízení IoT Edge tím, že zkontrolujete protokoly kontejneru a zprávy předávané do a ze zařízení. Ke shromáždění informací použijte příkazy a nástroje uvedené v této části. 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Zkontrolujte stav jeho protokoly a správce zabezpečení hraniční IoT:

V systému Linux:
- Chcete-li zobrazit stav správce zabezpečení hraniční IoT:

   ```bash
   sudo systemctl status iotedge
   ```

- Pokud chcete zobrazit protokoly správce zabezpečení hraniční IoT:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- Zobrazení podrobnějších protokoly správce zabezpečení hraniční IoT:

   - Upravte nastavení démon iotedge:

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Aktualizujte následující řádky:
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - Restartujte démon zabezpečení hraniční IoT:
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

Ve Windows:
- Chcete-li zobrazit stav správce zabezpečení hraniční IoT:

   ```powershell
   Get-Service iotedge
   ```

- Pokud chcete zobrazit protokoly správce zabezpečení hraniční IoT:

   ```powershell
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false}
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Pokud správce zabezpečení hraniční IoT neběží, zkontrolujte vaši yaml konfigurační soubor

> [!WARNING]
> Soubory YAML nemůže obsahovat karty jako identation. Místo toho použijte 2 mezery.

V systému Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Ve Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Zkontrolujte protokoly kontejneru problémů

Jakmile je spuštěn démon zabezpečení hraniční IoT, podívejte se na protokoly kontejnery rozpoznat problémy s. Začněte nasazenými kontejnery a pak si prohlédněte kontejnery, ze kterých se skládá modul runtime IoT Edge: agent Edge a centrum Edge. Protokoly agenta Edge obvykle obsahují informace o životním cyklu jednotlivých kontejnerů. Protokoly centra Edge obsahují informace o zasílání zpráv a směrování. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-edge-hub"></a>Zobrazení zpráv průchodu přes hraniční rozbočovače

Zobrazení zpráv průchodu přes hraniční rozbočovače a shromažďovat přehledy o aktualizace vlastností zařízení s podrobné protokoly z kontejnerů runtime edgeAgent a edgeHub. Chcete-li zapnout podrobné protokoly na těchto kontejnerů, nastavte `RuntimeLogLevel` proměnnou prostředí: 

V systému Linux:
    
   ```cmd
   export RuntimeLogLevel="debug"
   ```
    
Ve Windows:
    
   ```powershell
   [Environment]::SetEnvironmentVariable("RuntimeLogLevel", "debug")
   ```

Můžete zkontrolovat také zprávy odesílané mezi službou IoT Hub a hraničními zařízeními IoT. Tyto zprávy zobrazíte pomocí rozšíření [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) pro Visual Studio Code. Další pokyny najdete v článku [Užitečný nástroj pro vývoj se službou Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Restartujte kontejnery
Po prozkoumání protokoly a zprávy pro informace, pokuste se restartovat kontejnerů:

```
iotedge restart <container name>
```

Restartujte kontejnery runtime hraniční IoT:

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Restartovat správce zabezpečení hraniční IoT

Pokud je stále uložením problém, můžete zkusit restartování správce zabezpečení hraniční IoT.

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
Ujistěte se, že přihlašovací údaje registru jsou správně zadané v manifestu nasazení

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Démon zabezpečení hraniční IoT selže s neplatný název hostitele

Příkaz `sudo journalctl -u iotedge` se nezdaří a zobrazí se následující zpráva: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Původní příčina
Modul runtime IoT Edge podporuje pouze názvy hostitelů, které jsou kratší než 64 znaků. To obvykle není problém pro fyzické počítače, ale může dojít, když nastavíte modul runtime na virtuálním počítači. Automaticky generované názvy hostitelů pro virtuální počítače s Windows, které jsou hostované v Azure, zejména, jsou obvykle dlouhé. 

### <a name="resolution"></a>Řešení
Když se tato chyba, abyste ho mohli vyřešit konfiguraci název DNS virtuálního počítače, a jako název hostitele v možnosti instalačního příkazu nastavením názvu DNS.

1. Na portálu Azure přejděte na stránku přehled virtuálního počítače. 
2. Vyberte **konfigurace** pod názvem DNS. Pokud virtuální počítač už má název DNS nakonfigurovaný, nemusíte konfigurovat nový. 

   ![Konfigurace názvu DNS](./media/troubleshoot/configure-dns.png)

3. Zadejte hodnotu pro **Popisek názvu DNS** a vyberte **Uložit**.
4. Zkopírujte nový název DNS, které by měly být ve formátu  **\<DNSnamelabel\>.\< vmlocation\>. cloudapp.azure.com**.
5. Ve virtuálním počítači použijte následující příkaz Nastavit hraniční IoT modulu runtime s název DNS:

   - V systému Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - Ve Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="next-steps"></a>Další postup
Myslíte si, že jste v platformě IoT Edge našli chybu? [Odešlete problém](https://github.com/Azure/iotedge/issues), abychom mohli pokračovat ve zlepšování. 
