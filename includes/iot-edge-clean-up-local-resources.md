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
ms.openlocfilehash: 2b61cc8c5c448c28e96b06afa3556688a82567ed
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202699"
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