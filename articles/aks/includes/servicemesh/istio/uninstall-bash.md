---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: b1f3ea33694440a9366a64dd8d778b934a1d25ff
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530573"
---
Chcete-li odstranit CRDs, spusťte následující příkaz:

```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

K odstranění tajných kódů spusťte následující příkaz:

```bash
kubectl get secret --all-namespaces -o json | jq '.items[].metadata | ["kubectl delete secret -n", .namespace, .name] | join(" ")' -r | fgrep "istio." | xargs -t0 bash -c
```