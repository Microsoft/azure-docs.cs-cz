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
ms.openlocfilehash: e4f9fd537d7743a5bbb9d129b21c4bf0a529d32d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491058"
---
Když později v tomto kurzu spustíte ukázkovou aplikaci zařízení, budete potřebovat následující konfigurační hodnoty:

* Obor ID: ve vaší aplikaci IoT Central přejděte na **správa > připojení zařízení**. Poznamenejte si hodnotu **Rozsah ID** .
* Skupinový primární klíč: ve vaší aplikaci IoT Central přejděte na **správa > připojení zařízení > SAS-IoT-Devices**. Poznamenejte si hodnotu **primárního klíče** sdíleného přístupového podpisu.

Pomocí Cloud Shell vygenerujte klíč zařízení z primárního klíče skupiny, který jste načetli:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key --device-id sample-device-01 --pk <the group primary key value>
```

Poznamenejte si generovaný klíč zařízení a použijete ho později v tomto kurzu.
