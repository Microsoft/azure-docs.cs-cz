---
title: Odstranění skupiny serverů s povoleným PostgreSQLým rozšířením Azure ARC
description: Odstranění skupiny serverů s povoleným Postgresým rozšířením Azure ARC
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: dcabe4b1520c66b8d5bfa398dc1248972587cd32
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936805"
---
# <a name="delete-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Odstranění skupiny serverů s povoleným PostgreSQLým rozšířením Azure ARC

Tento dokument popisuje kroky, jak odstranit skupinu serverů z instalace Azure ARC.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-the-server-group"></a>Odstranit skupinu serverů

Předpokládejme například, že chceme odstranit instanci _postgres01_ z následujícího nastavení:

```console
azdata arc postgres server list
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Obecný formát příkazu Delete je:
```console
azdata arc postgres server delete -n <server group name>
```
Další podrobnosti o příkazu Delete získáte spuštěním příkazu:
```console
azdata arc postgres server delete --help
```

### <a name="lets-delete-the-server-group-used-in-this-example"></a>Pojďme odstranit skupinu serverů použitou v tomto příkladu:
```console
azdata arc postgres server delete -n postgres01
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Uvolnit deklarace identity trvalého svazku Kubernetes (PVC)

Odstraněním skupiny serverů se neodstraní přidružené [virtuální okruhy](https://kubernetes.io/docs/concepts/storage/persistent-volumes/). Toto chování je úmyslné. Záměrem je pomáhat uživateli získat přístup k souborům databáze v případě, že odstranění instance bylo nechtěné. Odstraňování trvalých virtuálních okruhů není povinné. Doporučuje se to ale. Pokud tyto virtuální okruhy neuvolníte, budete nakonec mít chyby, protože cluster Kubernetes bude považovat za nedostatek místa na disku. Pokud chcete znovu získat trvalé virtuální okruhy, proveďte následující kroky:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. vypíšete okruhy PVC pro skupinu serverů, kterou jste odstranili.
Pokud chcete zobrazit seznam virtuálních okruhů, spusťte tento příkaz:
```console
kubectl get pvc [-n <namespace name>]
```

Vrátí seznam PVSs, zejména okruh PVC pro skupinu serverů, kterou jste odstranili. Příklad:
```console
kubectl get pvc
NAME                STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-postgres01-0   Bound    pvc-72ccc225-dad0-4dee-8eae-ed352be847aa   5Gi        RWO            default        2d18h
data-postgres01-1   Bound    pvc-ce6f0c51-faed-45ae-9472-8cdf390deb0d   5Gi        RWO            default        2d18h
data-postgres01-2   Bound    pvc-5a863ab9-522a-45f3-889b-8084c48c32f8   5Gi        RWO            default        2d18h
data-postgres01-3   Bound    pvc-00e1ace3-1452-434f-8445-767ec39c23f2   5Gi        RWO            default        2d15h
logs-postgres01-0   Bound    pvc-8b810f4c-d72a-474a-a5d7-64ec26fa32de   5Gi        RWO            default        2d18h
logs-postgres01-1   Bound    pvc-51d1e91b-08a9-4b6b-858d-38e8e06e60f9   5Gi        RWO            default        2d18h
logs-postgres01-2   Bound    pvc-8e5ad55e-300d-4353-92d8-2e383b3fe96e   5Gi        RWO            default        2d18h
logs-postgres01-3   Bound    pvc-f9e4cb98-c943-45b0-aa07-dd5cff7ea585   5Gi        RWO            default        2d15h
```
Pro tuto skupinu serverů je k dispozici 8 trvalých virtuálních okruhů.

### <a name="2-delete-each-of-the-pvcs"></a>2. Odstraňte jednotlivé virtuální okruhy.
Odstraňte data a Zaprotokolujte virtuální okruhy pro každý PostgreSQL uzel (koordinátor a pracovní procesy) skupiny serverů, kterou jste odstranili.
Obecný formát tohoto příkazu je: 
```console
kubectl delete pvc <name of pvc>  [-n <namespace name>]
```

Příklad:
```console
kubectl delete pvc data-postgres01-0
kubectl delete pvc data-postgres01-1 
kubectl delete pvc data-postgres01-2
kubectl delete pvc data-postgres01-3
kubectl delete pvc logs-postgres01-0
kubectl delete pvc logs-postgres01-1
kubectl delete pvc logs-postgres01-2
kubectl delete pvc logs-postgres01-3
```

Každý z těchto příkazů kubectl ověří úspěšné odstranění trvalého virtuálního okruhu. Příklad:
```console
persistentvolumeclaim "data-postgres01-0" deleted
```
  

>**Poznámka:** Jak je uvedeno, virtuální okruhy (PVC) můžou nakonec získat cluster Kubernetes v situaci, kdy dojde k chybám. Některé z těchto chyb můžou zahrnovat neúspěšné přihlášení k vašemu Kubernetes clusteru pomocí azdata, protože z něj z důvodu tohoto problému úložiště (normální chování Kubernetes) se z něj odkládají.
>
> V protokolech se například můžou zobrazovat zprávy podobné:  
    > Poznámky: microsoft.com/ignore-pod-health: true  
    > Stav: neúspěšné  
    > Důvod: vyřazeno  
    > Zpráva: uzel měl nedostatek prostředků: dočasné úložiště. Řadič kontejneru používal 16372Ki, což překračuje jeho požadavek na 0.
    
## <a name="next-step"></a>Další krok
Vytvoření [PostgreSQL s povoleným rozšířením Azure ARC](create-postgresql-hyperscale-server-group.md)
