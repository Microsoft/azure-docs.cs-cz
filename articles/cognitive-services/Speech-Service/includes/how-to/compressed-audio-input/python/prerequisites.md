---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: b8dda0347e5713ef35705425b54f29a110803488
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97821515"
---
Zpracování komprimovaného zvuku je implementováno pomocí [GStreamer](https://gstreamer.freedesktop.org). Z důvodů licencování nejsou binární soubory GStreamer kompilovány a propojeny se sadou Speech SDK. Vývojáři potřebují nainstalovat několik závislostí a modulů plug-in, viz [instalace ve Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) nebo [instalace v systému Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). GStreamer binární soubory musí být v systémové cestě, aby sada Speech SDK mohla načíst binární soubory za běhu. Například pokud je sada Speech SDK schopná během běhu najít v systému Windows `libgstreamer-1.0-0.dll` , znamená to, že binární soubory GStreamer jsou v systémové cestě.

