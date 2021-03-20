---
title: zahrnout soubor
description: zahrnout soubor
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2b7881e7fa1ccbcec1325b2af0a570c86b0585a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017471"
---
Když později v tomto kurzu spustíte ukázkovou aplikaci zařízení, budete potřebovat následující konfigurační hodnoty:

* Obor ID: ve vaší aplikaci IoT Central přejděte na **správa > připojení zařízení**. Poznamenejte si hodnotu **Rozsah ID** .
* Skupinový primární klíč: ve vaší aplikaci IoT Central přejděte na **správa > připojení zařízení > SAS-IoT-Devices**. Poznamenejte si hodnotu **primárního klíče** sdíleného přístupového podpisu.

Pomocí Cloud Shell vygenerujte klíč zařízení z klíče SAS skupiny, který jste právě načetli:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key  --device-id sample-device-01 --pk <the group SAS primary key value>
```

Poznamenejte si generovaný klíč zařízení a použijete ho později v tomto kurzu.
