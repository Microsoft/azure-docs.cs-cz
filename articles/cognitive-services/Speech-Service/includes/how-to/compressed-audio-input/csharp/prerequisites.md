---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 07405ad0da9c9ba280810402a638395a7feb8554
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637298"
---
Manipulace s komprimovaným zvukem je realizována pomocí [GStreameru](https://gstreamer.freedesktop.org). Z licenčních důvodů gstreamer binární soubory nejsou kompilovány a propojeny s sadou Speech SDK. Vývojáři potřebují nainstalovat několik závislostí a pluginů, viz [Instalace v systému Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). Binární soubory Gstreamer musí být v systémové cestě, aby sada SDK řeči mohla za běhu načíst binární soubory gstreamer. Pokud řeč SDK je schopen najít libgstreamer-1.0-0.dll během běhu znamená, že gstreamer binární soubory jsou v systémové cestě.

