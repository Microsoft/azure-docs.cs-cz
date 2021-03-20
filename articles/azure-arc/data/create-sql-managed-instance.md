---
title: Vytvoření spravované instance Azure SQL v Arc Azure
description: Vytvoření spravované instance Azure SQL v Arc Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0d1d6c7349adc65270e13d09d771963cb9cacea3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93280399"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Vytvoření spravované instance Azure SQL v Arc Azure

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="login-to-the-azure-arc-data-controller"></a>Přihlášení k řadiči dat ARC Azure

Než budete moct vytvořit instanci, přihlaste se k řadiči dat ARC Azure, pokud ještě nejste přihlášení.

```console
azdata login
```

Pak se zobrazí výzva k zadání uživatelského jména, hesla a oboru názvů System.  

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-sql-managed-instance"></a>Vytvoření spravované instance Azure SQL

Chcete-li zobrazit dostupné možnosti vytvoření forSQL spravované instance, použijte následující příkaz:
```console
azdata arc sql mi create --help
```

Chcete-li vytvořit spravovanou instanci SQL, použijte následující příkaz:

```console
azdata arc sql mi create -n <instanceName> --storage-class-data <storage class> --storage-class-logs <storage class>
```

Příklad:

```console
azdata arc sql mi create -n sqldemo --storage-class-data managed-premium --storage-class-logs managed-premium
```
> [!NOTE]
>  Délka názvu musí být kratší než 13 znaků a musí odpovídat [konvencím názvů DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names) .
>
>  Při zadání přidělení paměti a přidělení vCore použijte tento vzorec k zajištění úspěšného vytvoření – pro každé 1 vCore potřebujete alespoň 4 GB paměti RAM dostupné na uzlu Kubernetes, kde se spustí spravovaná instance SQL pod.
>
>  Pokud instanci SQL zřizujete v Azure, při jejím vytváření v jejím názvu nepoužívejte velká písmena.
>
>  Výpis dostupných tříd úložiště ve spuštění clusteru Kubernetes `kubectl get storageclass` 


> [!NOTE]
> Pokud chcete automatizovat vytváření instancí SQL a vyhnout se interaktivní výzvě pro heslo správce, můžete `AZDATA_USERNAME` `AZDATA_PASSWORD` před spuštěním příkazu nastavit proměnné prostředí a na požadované uživatelské jméno a heslo `azdata arc sql mi create` .
> 
>  Pokud jste vytvořili řadič dat pomocí AZDATA_USERNAME a AZDATA_PASSWORD ve stejné relaci terminálu, pak se hodnoty pro AZDATA_USERNAME a AZDATA_PASSWORD použijí k vytvoření spravované instance SQL.

> [!NOTE]
> Při vytváření spravované instance Azure SQL se prostředky v Azure neregistrují. Postup registrace prostředku najdete v následujících článcích: 
> - [Zobrazení protokolů a metrik pomocí Kibana a Grafana](monitor-grafana-kibana.md)
> - [Nahrání fakturačních dat do Azure a jejich zobrazení v Azure Portal](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Zobrazit instanci v Arc Azure

Chcete-li zobrazit instanci, použijte následující příkaz:

```console
azdata arc sql mi list
```

Výstup by měl vypadat takto:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Pokud používáte AKS nebo `kubeadm` nebo OpenShift atd., můžete zde zkopírovat externí IP adresu a číslo portu a připojit se k němu pomocí oblíbeného nástroje pro připojení k instanci SQL serveru nebo Azure SQL, jako je například Azure Data Studio nebo SQL Server Management Studio. Pokud ale používáte virtuální počítač pro rychlý Start, přečtěte si článek [připojení ke spravované instanci SQL s povoleným obloukem Azure](connect-managed-instance.md) , kde najdete speciální pokyny.


## <a name="next-steps"></a>Další kroky
- [Připojení ke spravované instanci SQL ARC s povoleným voláním Azure](connect-managed-instance.md)
- [Zaregistrujte svou instanci pomocí Azure a nahrajte metriky a protokoly o vaší instanci.](upload-metrics-and-logs-to-azure-monitor.md)
- [Nasazení spravované instance Azure SQL pomocí Azure Data Studio](create-sql-managed-instance-azure-data-studio.md)
