---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 3d4c908e0caf1cf84159df49d98603fd13b75994
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821549"
---
Zpracování komprimovaného zvuku je implementováno pomocí [GStreamer](https://gstreamer.freedesktop.org). Z důvodů licencování nejsou binární soubory GStreamer kompilovány a propojeny se sadou Speech SDK. Vývojáři potřebují nainstalovat několik závislostí a modulů plug-in, viz [instalace ve Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) nebo [instalace v systému Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). GStreamer binární soubory musí být v systémové cestě, aby sada Speech SDK mohla načíst binární soubory za běhu. Například pokud je sada Speech SDK schopná během běhu najít v systému Windows `libgstreamer-1.0-0.dll` , znamená to, že binární soubory GStreamer jsou v systémové cestě.

