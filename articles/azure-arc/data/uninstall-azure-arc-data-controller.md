---
title: Odstranit řadič dat ARC Azure
description: Odstranit řadič dat ARC Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a040200c5746defcaee84a951521d5919c0c4d28
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91660672"
---
# <a name="delete-azure-arc-data-controller"></a>Odstranit řadič dat ARC Azure

Následující článek popisuje, jak odstranit řadič dat ARC Azure.

Než budete pokračovat, ujistěte se, že všechny datové služby, které byly vytvořeny na řadiči dat, jsou odebrány následujícím způsobem:

## <a name="log-in-to-the-data-controller"></a>Přihlášení k řadiči dat

Přihlaste se k řadiči dat, který chcete odstranit:

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>Vypsat & odstranit existující datové služby

Spusťte následující příkaz a ověřte, zda jsou vytvořeny nějaké spravované instance SQL:

```
azdata arc sql mi list
```

Pro každou spravovanou instanci SQL ze seznamu výše spusťte příkaz DELETE následujícím způsobem:

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

Podobně pro kontrolu PostgreSQL instancí s škálovatelným škálováním spusťte:

```
azdata arc postgres server list
```

V případě každé PostgreSQL instance s škálovatelným škálováním, spusťte příkaz DELETE následujícím způsobem:
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>Odstranit kontroler

Po odebrání všech spravovaných instancí SQL a PostgreSQL instancí v rámci škálování se dá řadič dat odstranit následujícím způsobem:

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>Odebrat SCCs (jenom Red Hat OpenShift)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>Odstranit objekty na úrovni clusteru

Kromě oboru názvů objektů, pokud chcete také odstranit objekty na úrovni clusteru, jako je CRDs, `clusterroles` a `clusterrolebindings` Spustit následující příkazy:

```console
# Cleanup azure arc data service artifacts
#Delete CRDs
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
#Delete clusterrole
kubectl delete clusterrole <namespace>:cr-arc-metricsdc-reader
#For example: kubectl delete clusterrole arc:cr-arc-metricsdc-reader
#Delete rolebinding
kubectl delete clusterrolebinding <namespace>:crb-arc-metricsdc-reader
#For example: kubectl delete clusterrolebinding arc:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Volitelně můžete odstranit obor názvů řadiče dat ARC Azure.


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example: kubectl delete ns arc
```

## <a name="next-steps"></a>Další kroky

[Co jsou datové služby s podporou ARC Azure?](overview.md)
