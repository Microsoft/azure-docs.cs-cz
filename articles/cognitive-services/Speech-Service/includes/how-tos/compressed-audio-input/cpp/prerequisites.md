---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 590e5494a8c8f9d4e06b69af0708e83d53be72b5
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943870"
---
Zpracování komprimovaného zvuku je implementováno pomocí [GStreamer](https://gstreamer.freedesktop.org). Z důvodů licencování nejsou binární soubory GStreamer kompilovány a propojeny se sadou Speech SDK. Vývojáři potřebují nainstalovat několik závislostí a modulů plug-in.

# <a name="ubuntu-1604-1804-or-debian-9"></a>[Ubuntu, 16,04, 18,04 nebo Debian 9](#tab/debian)

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```

# <a name="rehl--centos"></a>[REHL/CentOS](#tab/centos)

```sh
sudo yum install gstreamer1 \
gstreamer1-plugins-base \
gstreamer1-plugins-good \
gstreamer1-plugins-bad-free \
gstreamer1-plugins-ugly-free
```

> [!NOTE]
> V RHEL/CentOS postupujte podle pokynů, [jak nakonfigurovat OpenSSL pro Linux](../../../../how-to-configure-openssl-linux.md).

---