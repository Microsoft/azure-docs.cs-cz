---
title: Změna portu PostgreSQL
description: Změňte port, na kterém je naslouchat PostgreSQL skupina serverů s podporou rozšíření Azure ARC.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/02/2020
ms.topic: how-to
ms.openlocfilehash: 45424408c790e4921be48464818c55fe74313fd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93328719"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>Změna portu, na kterém skupina serverů naslouchá 

Změna portu je standardní operace úprav skupiny serverů. Chcete-li změnit port, spusťte následující příkaz:
```console
 azdata arc postgres server edit -n <server group name> --port <desired port number>
```

Předpokládejme například, že název vaší skupiny serverů je _postgres01_ a chcete, aby naslouchal na portu _866_. Spusťte následující příkaz:
```console
 azdata arc postgres server edit -n postgres01 --port 866
```

## <a name="verify-that-the-port-was-changed"></a>Ověření, že port byl změněn

Chcete-li ověřit, zda byl port změněn, spusťte následující příkaz, který zobrazí konfiguraci skupiny serverů:
```console
azdata arc postgres server show -n <server group name>
```

Ve výstupu tohoto příkazu se podívejte na číslo portu zobrazené u položky "port" v části "služba" specifikací vaší skupiny serverů.
Alternativně můžete ověřit položku externalEndpoint v části stav ve specifikacích skupiny serverů, za kterou je tato IP adresa následována číslem portu, který jste nakonfigurovali.

Na ilustraci, pokud budeme pokračovat v příkladu výše, spusťte příkaz:
```console
azdata arc postgres server show -n postgres01
```

a tady vidíte port 866, na který odkazuje:

```console
"service": {
      "port": 866,
      "type": "LoadBalancer"
    },
```
a tady.

```console
"status": {
    "externalEndpoint": "12.678.345.09:866",
    "logSearchDashboard": "https://12.345.678.90:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))",
    "metricsDashboard": "https://12.345.678.90:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01",
    "readyPods": "3/3",
    "state": "Ready"
  }
```
## <a name="next-steps"></a>Další kroky
- Přečtěte si informace o [tom, jak se připojit ke skupině serverů](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).
- Přečtěte si informace o tom, jak můžete nakonfigurovat další aspekty skupiny serverů v části How-to\Manage\Configure & Scale v dokumentaci.
