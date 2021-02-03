---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 6c8dbeea83cba306cfb788cf447236088045ffc9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99494004"
---
V této části se dozvíte, jak použít omezení kontextu zabezpečení (SCC). Pro verzi Preview tyto omezení zabezpečení neuvolní. 

1. Stáhněte si omezení vlastního kontextu zabezpečení (SCC). Použijte jednu z následujících možností: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [Žádný](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl`
   
      Následující příkaz stáhne oblouk-data-SCC. yaml:

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

   > [!NOTE]
   > RedHat OpenShift 4,5 nebo vyšší, mění způsob použití SCC na účet služby.
   > Použijte stejný obor názvů a v `azdata arc dc create` níže uvedeném příkazu. Příklad je `arc` . 
   > 
   > Pokud používáte RedHat OpenShift 4,5 nebo vyšší, spusťte příkaz: 
   >
   >```console
   >oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   >```
