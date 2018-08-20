---
title: zahrnout soubor
description: zahrnout soubor
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: cdb60ffb3ba3c31c336c8b74c46621792c707f74
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2018
ms.locfileid: "40046915"
---
### <a name="delete-local-resources"></a>Odstranění místních prostředků

Pokud ze svého zařízení chcete odebrat modul runtime IoT Edge a související prostředky, použijte odpovídající příkazy pro operační systém vašeho zařízení. 

#### <a name="windows"></a>Windows

Odinstalujte modul runtime IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

Při odebrání modulu runtime IoT Edge se zastaví kontejnery, které vytvořil, ale na zařízení se zachovají. Zobrazte všechny kontejnery.

   ```powershell
   docker ps -a
   ```

Odstraňte kontejnery modulu runtime, které se vytvořily ve vašem zařízení.

   ```powershell
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

S použitím názvů kontejnerů odstraňte všechny další kontejnery, které se zobrazily ve výstupu příkazu `docker ps`. 

#### <a name="linux"></a>Linux

Odeberte modul runtime IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Při odebrání modulu runtime IoT Edge se zastaví kontejnery, které vytvořil, ale na zařízení se zachovají. Zobrazte všechny kontejnery.

   ```bash
   sudo docker ps -a
   ```

Odstraňte kontejnery modulu runtime, které se vytvořily ve vašem zařízení.

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

S použitím názvů kontejnerů odstraňte všechny další kontejnery, které se zobrazily ve výstupu příkazu `docker ps`. 

Odeberte kontejnerový modul runtime.

   ```bash
   sudo apt-get remove --purge moby
   ```