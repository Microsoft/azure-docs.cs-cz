---
title: Určení velikosti tabulky – Citus (škálování v tabulce) – Azure Database for PostgreSQL
description: Jak najít skutečnou velikost distribuovaných tabulek ve skupině serverů Citus ()
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 6ebdbe250862ccd462259900ba56d007f002a52f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97937577"
---
# <a name="determine-table-and-relation-size"></a>Určení velikosti tabulky a vztahu

Obvyklý způsob, jak najít velikosti tabulek v PostgreSQL, v `pg_total_relation_size` důsledku drastického generování sestav je velikost distribuovaných tabulek na úrovni Citus ().
Tato funkce má u skupiny serverů Citus () velikost tabulek v uzlu koordinátora.  Ve skutečnosti data v distribuovaných tabulkách žijí v pracovních uzlech (v horizontálních oddílů), nikoli v koordinátoru. Skutečná míra velikosti distribuované tabulky se získá jako součet velikosti horizontálních oddílů. Citus () poskytuje pomocné funkce pro dotazování těchto informací.

<table>
<colgroup>
<col style="width: 40%" />
<col style="width: 59%" />
</colgroup>
<thead>
<tr class="header">
<th>Funkce</th>
<th>Návraty</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size (relation_name)</td>
<td><ul>
<li>Velikost skutečných dat v tabulce (<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">hlavní rozvětvení</a>).</li>
<li>Relace může být název tabulky nebo indexu.</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size (relation_name)</td>
<td><ul>
<li><p>citus_relation_size plus:</p>
<blockquote>
<ul>
<li>velikost <a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">mapy volného místa</a></li>
<li>velikost <a href="https://www.postgresql.org/docs/current/static/storage-vm.html">mapy viditelnosti</a></li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size (relation_name)</td>
<td><ul>
<li><p>citus_table_size plus:</p>
<blockquote>
<ul>
<li>velikost indexů</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

Tyto funkce jsou analogické jako tři standardní [funkce velikosti objektu](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE)PostgreSQL, s výjimkou případů, kdy se nemohou připojit k uzlu, při chybě.

## <a name="example"></a>Příklad

Tady je postup, jak zobrazit seznam velikostí všech distribuovaných tabulek:

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

Výstup:

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>Další kroky

* Naučte se [škálovat skupinu serverů](howto-hyperscale-scale-grow.md) tak, aby obsahovala víc dat.
* Rozlišuje [typy tabulek](concepts-hyperscale-nodes.md) ve skupině serverů (Citus) s měřítkem.
