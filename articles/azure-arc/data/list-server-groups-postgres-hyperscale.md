---
title: Seznam skupin serverů s podporou PostgreSQL ARC, které jsou vytvořené v řadiči dat ARC Azure
description: Seznam skupin serverů s podporou PostgreSQL ARC, které jsou vytvořené v řadiči dat ARC Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7577ca4b8a1d7db7ea99aadfef4fd2a445b66425
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90936799"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Seznam skupin serverů s podporou PostgreSQL ARC, které jsou vytvořené v řadiči dat ARC Azure

Tento článek vysvětluje, jak můžete načíst seznam skupin serverů vytvořených v řadiči dat ARC.

Chcete-li načíst tento seznam, použijte některou z následujících metod, jakmile se připojíte k řadiči dat ARC:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azdata"></a>Z CLI pomocí azdata
Obecný formát příkazu je:
```console
azdata arc postgres server list
```

Vrátí výstup podobný tomuto:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
Další podrobnosti o parametrech, které jsou k dispozici pro tento příkaz, získáte spuštěním příkazu:
```console
azdata arc postgres server list --help
```

## <a name="from-cli-with-kubectl"></a>Z CLI pomocí kubectl
Spusťte některý z následujících příkazů.

**Chcete-li zobrazit seznam skupin serverů bez ohledu na verzi Postgres, spusťte příkaz:**
```console
kubectl get postgresqls
```
Vrátí výstup podobný tomuto:
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**Pokud chcete zobrazit seznam skupin serverů konkrétní verze Postgres, spusťte:**
```console
kubectl get postgresql-12
```

Pokud chcete zobrazit seznam skupin serverů, na kterých běží verze 11 Postgres, nahraďte _PostgreSQL-12_ _PostgreSQL-11_.

## <a name="next-steps"></a>Další kroky:

* [Přečtěte si článek o tom, jak získat koncové body připojení a jak vytvořit připojovací řetězce pro připojení ke skupině serverů.](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Přečtěte si článek o zobrazení konfigurace skupiny serverů s povoleným PostgreSQLm rozšířením Azure ARC.](show-configuration-postgresql-hyperscale-server-group.md)
