---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421811"
---
Zpracování komprimovaného zvuku je implementováno pomocí [GStreamer](https://gstreamer.freedesktop.org). Z důvodů licencování nejsou binární soubory GStreamer kompilovány a propojeny se sadou Speech SDK. Vývojáři potřebují nainstalovat několik závislostí a modulů plug-in, viz [instalace ve Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). GStreamer binární soubory musí být v systémové cestě, aby sada Speech SDK mohla během běhu načíst binární soubory GStreamer. Pokud sada Speech SDK dokáže najít libgstreamer-1.0 -0. dll během běhu, znamená to, že binární soubory GStreamer jsou v systémové cestě.

