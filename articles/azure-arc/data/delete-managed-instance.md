---
title: Odstranit spravovanou instanci SQL s podporou ARC Azure
description: Odstranit spravovanou instanci SQL s podporou ARC Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e531349e8f404380d9f0601caa3b66557c297062
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90936829"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>Odstranit spravovanou instanci SQL s podporou ARC Azure
Tento článek popisuje, jak můžete odstranit spravovanou instanci SQL s podporou ARC Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>Zobrazit existující spravované instance SQL s podporou ARC Azure
Pokud chcete zobrazit spravované instance SQL, spusťte následující příkaz:

```console
azdata arc sql mi list
```

Výstup by měl vypadat přibližně takto:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>Odstraní spravovanou instanci SQL s povoleným obloukem Azure.
Pokud chcete odstranit spravovanou instanci SQL, spusťte následující příkaz:

```console
azdata arc sql mi delete -n <NAME_OF_INSTANCE>
```

Výstup by měl vypadat přibližně takto:

```console
# azdata arc sql mi delete -n demo-mi
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Uvolnit deklarace identity trvalého svazku Kubernetes (PVC)

Odstraněním spravované instance SQL se neodstraní přidružené [virtuální okruhy](https://kubernetes.io/docs/concepts/storage/persistent-volumes/). Toto chování je úmyslné. Záměrem je pomoct uživateli získat přístup k souborům databáze v případě, že odstranění instance bylo nechtěné. Odstraňování deklarací identity trvalých svazků není povinné. Doporučuje se to ale. Pokud tyto virtuální okruhy neuvolníte, nakonec se ukončí s chybami, protože cluster Kubernetes nemá dostatek místa na disku. Pokud chcete získat zpět deklarace identity trvalých svazků, proveďte následující kroky:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. vypíšete okruhy PVC pro skupinu serverů, kterou jste odstranili.
Pokud chcete zobrazit seznam virtuálních okruhů, spusťte následující příkaz:
```console
kubectl get pvc
```

V následujícím příkladu si všimněte, že se jedná o trvalé virtuální okruhy pro spravované instance SQL, které jste odstranili.
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. Odstraňte jednotlivé virtuální okruhy.
Odstraňte data a protokol PVC pro všechny spravované instance SQL, které jste odstranili.
Obecný formát tohoto příkazu je: 
```console
kubectl delete pvc <name of pvc>
```

Například:
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

Každý z těchto příkazů kubectl ověří úspěšné odstranění trvalého virtuálního okruhu. Například:
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> Jak je uvedeno, virtuální okruhy (PVC) můžou nakonec získat cluster Kubernetes v situaci, kdy dojde k chybám. Některé z těchto chyb můžou zahrnovat neúspěšné přihlášení k vašemu Kubernetes clusteru pomocí azdata, protože z něj z důvodu tohoto problému úložiště (normální chování Kubernetes) se z něj odkládají.
>
> V protokolech se například můžou zobrazovat zprávy podobné:  
> - Poznámky: microsoft.com/ignore-pod-health: true  
> - Stav: neúspěšné  
> - Důvod: vyřazeno  
> - Zpráva: uzel měl nedostatek prostředků: dočasné úložiště. Řadič kontejneru používal 16372Ki, což překračuje jeho požadavek na 0.

## <a name="next-steps"></a>Další kroky

Další informace o [funkcích a možnostech spravované instance SQL ARC s podporou Azure](managed-instance-features.md)

[Začněte vytvořením kontroleru dat](create-data-controller.md)

Už jste vytvořili řadič dat? [Vytvoření spravované instance SQL s povoleným ARC Azure](create-sql-managed-instance.md)