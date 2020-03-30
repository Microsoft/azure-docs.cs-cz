---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 055951a7b6b21e24af9dbfcc273e1503708c0080
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593934"
---
Chcete-li tajné klíče odstranit, spusťte následující příkaz:

```bash
kubectl get secret --all-namespaces -o json | jq '.items[].metadata | ["kubectl delete secret -n", .namespace, .name] | join(" ")' -r | fgrep "istio." | xargs -t0 bash -c
```