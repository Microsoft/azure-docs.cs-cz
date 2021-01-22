---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 17a8c9580a8e69213c7f34e8ec889f7e46c6d17d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696056"
---
V této části se dozvíte, jak použít omezení kontextu zabezpečení (SCC). Pro verzi Preview tyto omezení zabezpečení neuvolní. 

1. Stáhněte si omezení vlastního kontextu zabezpečení (SCC). Použijte jednu z následujících možností: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([Raw](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` Následující příkaz stáhne oblouk-data-SCC. yaml:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Vytvořte SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Použijte SCC pro účet služby.

   > [!NOTE]
   > Použijte stejný obor názvů a v `azdata arc dc create` níže uvedeném příkazu. Příklad je `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```