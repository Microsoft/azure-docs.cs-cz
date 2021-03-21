---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 8c77efe9e3e301573b032d1dc1dd32bb4a5ab1a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417699"
---
Zpracování komprimovaného zvuku je implementováno pomocí [GStreamer](https://gstreamer.freedesktop.org). Z důvodů licencování nejsou binární soubory GStreamer kompilovány a propojeny se sadou Speech SDK. Vývojáři potřebují nainstalovat několik závislostí a modulů plug-in, viz [instalace ve Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) nebo [instalace v systému Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). GStreamer binární soubory musí být v systémové cestě, aby sada Speech SDK mohla načíst binární soubory za běhu. Například v systému Windows, pokud sada Speech SDK dokáže najít `libgstreamer-1.0-0.dll` nebo `gstreamer-1.0-0.dll` (pro nejnovější GStreamer) během běhu, znamená to, že binární soubory GStreamer jsou v systémové cestě.

