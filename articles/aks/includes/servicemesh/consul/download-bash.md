---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 4e35f8cbf6c32bdfd100b651ab10e3248f8b8b2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86244123"
---
V prostředí založeném na bash systému Linux, [podsystém Windows pro Linux][install-wsl] nebo MacOS, použijte `curl` ke stažení verze grafu Consul Helm následujícím způsobem:

```bash
# Specify the Consul Helm chart version that will be leveraged throughout these instructions
CONSUL_HELM_VERSION=0.10.0

curl -sL "https://github.com/hashicorp/consul-helm/archive/v$CONSUL_HELM_VERSION.tar.gz" | tar xz
mv consul-helm-$CONSUL_HELM_VERSION consul-helm
```

<!-- LINKS - external -->
[install-wsl]: /windows/wsl/install-win10
