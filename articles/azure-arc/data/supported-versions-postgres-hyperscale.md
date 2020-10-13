---
title: Podporované verze Postgres s povoleným PostgreSQLm škálováním Azure ARC
description: Podporované verze Postgres s povoleným PostgreSQLm škálováním Azure ARC
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0f3f9e7f4566800bdea6871cae1c5bafb3b37b95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936087"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Podporované verze Postgres s PostgreSQL Hyperscale s podporou služby Azure Arc

V tomto článku se dozvíte, jaké verze Postgres jsou k dispozici u PostgreSQL s povoleným rozšířením Azure ARC.
Seznam podporovaných verzí se vyvíjí v průběhu času. V současné době jsou podporované hlavní verze:
- Postgres 12 (výchozí)
- Postgres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>Jak zvolit mezi verzemi?
Doporučujeme se podívat na to, pro jaké verze aplikace byly navrženy, a jaké jsou možnosti jednotlivých verzí. Další informace najdete v článku o každé verzi na oficiálním webu Postgres:
- [Postgres 12 (výchozí)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Jak vytvořit konkrétní verzi v PostgreSQL s povoleným rozšířením Azure ARC?
V okamžiku vytvoření máte možnost určit, která verze se má vytvořit, předáním parametru _--Engine-Version_ . Pokud neuvedete informace o verzi, vytvoří se ve výchozím nastavení skupina serverů Postgres verze 12.

## <a name="how-do-be-notified-when-other-versions-are-available"></a>Jak se upozorní, když jsou k dispozici jiné verze?
Vraťte se zpět a přečtěte si tento článek. Bude se aktualizovat podle potřeby. V řadiči dat ARC v clusteru Kubernetes můžete také uvést typy vlastních definic prostředků (CRD).
Spusťte následující příkaz:
```console
kubectl get crds
```

Vrátí výstup podobný tomuto:
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

V tomto příkladu ukazuje tento výstup dva druhy CRD souvisejících s Postgres:
- postgresql-12s.arcdata.microsoft.com je CRD pro Postgres 12
- postgresql-11s.arcdata.microsoft.com je CRD pro Postgres 11

Jedná se o CRDs, ne skupiny serverů. Přítomnost CRD neoznačuje, že jste nevytvořili skupinu serverů této verze.
CRD je údaj o tom, jaký druh prostředků je možné vytvořit.

## <a name="next-steps"></a>Další kroky:
- [Přečtěte si o vytváření PostgreSQL škálování s povoleným rozšířením Azure ARC.](create-postgresql-hyperscale-server-group.md)
- [Přečtěte si o získání seznamu PostgreSQL skupin serverů s podporou rozšíření Azure ARC, které jsou vytvořené v řadiči dat ARC.](list-server-groups-postgres-hyperscale.md)
