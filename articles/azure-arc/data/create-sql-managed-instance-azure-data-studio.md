---
title: Vytvoření spravované instance Azure SQL pomocí Azure Data Studio
description: Vytvoření spravované instance Azure SQL pomocí Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 651811384a8e831c41c48ca8c4849e5ee3852054
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280480"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>Vytvoření spravované instance SQL – ARC Azure pomocí Azure Data Studio

Tento dokument vás provede kroky pro instalaci spravované instance Azure SQL – ARC Azure pomocí Azure Data Studio

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="log-in-to-the-azure-arc-data-controller"></a>Přihlášení k řadiči dat ARC Azure

Než budete moct vytvořit instanci, přihlaste se k řadiči dat ARC Azure, pokud ještě nejste přihlášení.

```console
azdata login
```

Pak budete vyzváni k zadání oboru názvů, ve kterém je vytvořen řadič dat, uživatelské jméno a heslo pro přihlášení k řadiči.  

> Pokud potřebujete ověřit obor názvů, můžete spustit ```kubectl get pods -A``` a získat tak seznam všech oborů názvů v clusteru.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Vytvoření spravované instance Azure SQL v Arc Azure

- Spustit Azure Data Studio
- Na kartě připojení klikněte na tři tečky v levém horním rohu a vyberte nové nasazení.
- Z možností nasazení vyberte **Azure SQL Managed instance – ARC Azure** . 
  > [!NOTE]
  > Může se zobrazit výzva k instalaci [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] sem, pokud není aktuálně nainstalovaná.
- Přijměte ochranu osobních údajů a licenční podmínky a klikněte na tlačítko **Vybrat** v dolní části.



- V okně nasadit spravovanou instanci SQL Azure – Azure ARC zadejte následující informace:
  - Zadejte název instance SQL Server.
  - Zadejte a potvrďte heslo pro instanci SQL Server.
  - Vyberte třídu úložiště, která je vhodná pro data.
  - Vyberte třídu úložiště, která je vhodná pro protokoly.

- Klikněte na tlačítko **nasadit** .

- To by mělo iniciovat vytvoření spravované instance Azure SQL – ARC Azure na řadiči dat.

- Během několika minut by se vytváření mělo úspěšně dokončit.

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>Připojení k spravované instanci Azure SQL – Azure ARC z Azure Data Studio

- Přihlaste se k řadiči dat ARC Azure poskytnutím oboru názvů, uživatelského jména a hesla pro řadič dat: 
```console
azdata login
```

- Zobrazte všechny zřízené spravované instance Azure SQL pomocí následujících příkazů:

```console
azdata arc sql mi list
```

Výstup by měl vypadat takto: Zkopírujte ServerEndpoint (včetně čísla portu) sem.

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- V Azure Data Studio klikněte na kartě **připojení** na **nové připojení** v zobrazení **servery** .
- V okně **připojení** vložte ServerEndpoint do textového pole serveru.
- Jako typ ověřování vyberte **přihlášení SQL** .
- Jako uživatelské jméno zadejte *SA* .
- Zadejte heslo pro účet. `sa`
- Volitelně můžete zadat název konkrétní databáze, ke které se chcete připojit.
- Volitelně můžete podle potřeby vybrat nebo přidat novou skupinu serverů.
- Vyberte **připojit** a připojte se ke spravované instanci Azure SQL – ARC Azure.




## <a name="next-steps"></a>Další kroky

Teď se pokuste [monitorovat instanci SQL](monitor-grafana-kibana.md) .
