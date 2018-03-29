---
title: Řešení potíží se službou Azure IoT Edge | Microsoft Docs
description: Řešení běžných potíží a seznámení s dovednostmi souvisejícími s řešením potíží se službou Azure IoT Edge
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/23/2018
ms.topic: article
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: b03ece52c4ff77c9e0abbc794325cd7e9a20c915
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Běžné potíže se službou Azure IoT Edge a jejich řešení

Pokud ve vašem prostředí dochází k potížím s provozem služby Azure IoT Edge, použijte tento článek jako vodítko k jejich řešení. 

## <a name="standard-diagnostic-steps"></a>Standardní postup diagnostiky 

Když narazíte na problém, získejte další informace o stavu vašeho zařízení IoT Edge tím, že zkontrolujete protokoly kontejneru a zprávy předávané do a ze zařízení. Ke shromáždění informací použijte příkazy a nástroje uvedené v této části. 

* Prohlédněte si protokoly kontejnerů Dockeru a detekujte problémy. Začněte nasazenými kontejnery a pak si prohlédněte kontejnery, ze kterých se skládá modul runtime IoT Edge: agent Edge a centrum Edge. Protokoly agenta Edge obvykle obsahují informace o životním cyklu jednotlivých kontejnerů. Protokoly centra Edge obsahují informace o zasílání zpráv a směrování. 

   ```cmd
   docker logs <container name>
   ```

* Zobrazte zprávy procházející přes centrum Edge a získejte přehled o aktualizacích vlastností zařízení s využitím podrobných protokolů z kontejnerů modulu runtime.

   ```cmd
   iotedgectl setup --connection-string "{device connection string}" --runtime-log-level debug
   ```
   
* Zobrazte podrobné protokoly z příkazů iotedgectl:

   ```cmd
   iotedgectl --verbose DEBUG <command>
   ```

* Pokud dochází k problémům s připojením, zkontrolujte proměnné prostředí vašeho hraničního zařízení, například připojovací řetězec zařízení:

   ```cmd
   docker exec edgeAgent printenv
   ```

Můžete zkontrolovat také zprávy odesílané mezi službou IoT Hub a hraničními zařízeními IoT. Tyto zprávy zobrazíte pomocí rozšíření [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) pro Visual Studio Code. Další pokyny najdete v článku [Užitečný nástroj pro vývoj se službou Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

Po prozkoumání informací v protokolech a zprávách můžete také zkusit restartovat modul runtime Azure IoT Edge:

   ```cmd
   iotedgectl restart
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
Zkuste znovu spustit příkaz `iotedgectl login`.

## <a name="iotedgectl-cant-find-docker"></a>Příkaz iotedgectl nemůže najít Docker

Příkazy `iotedgectl setup` nebo `iotedgectl start` služeb při selhání a tisk do protokolů se následující zpráva:
```output
File "/usr/local/lib/python2.7/dist-packages/edgectl/host/dockerclient.py", line 98, in get_os_type
  info = self._client.info()
File "/usr/local/lib/python2.7/dist-packages/docker/client.py", line 174, in info
  return self.api.info(*args, **kwargs)
File "/usr/local/lib/python2.7/dist-packages/docker/api/daemon.py", line 88, in info
  return self._result(self._get(self._url("/info")), True)
```

### <a name="root-cause"></a>Původní příčina
Příkaz iotedgectl nemůže najít Docker, což je nezbytný předpoklad.

### <a name="resolution"></a>Řešení
Nainstalujte Docker, ujistěte se, že je spuštěný, a zkuste to znovu.

## <a name="iotedgectl-setup-fails-with-an-invalid-hostname"></a>Instalační program iotedgectl selže s neplatný název hostitele

Příkaz `iotedgectl setup` se nezdaří a zobrazí se následující zpráva: 

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

   ```input
   iotedgectl setup --connection-string "<connection string>" --nopass --edge-hostname "<DNS name>"
   ```

## <a name="next-steps"></a>Další postup
Myslíte si, že jste v platformě IoT Edge našli chybu? [Odešlete problém](https://github.com/Azure/iot-edge/issues), abychom mohli pokračovat ve zlepšování. 
