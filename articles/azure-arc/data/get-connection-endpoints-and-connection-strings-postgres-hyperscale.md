---
title: Získání koncových bodů připojení a připojovacích řetězců formuláře pro PostgreSQL skupinu serverů s podporou ARC
titleSuffix: Azure Arc enabled data services
description: Získání koncových bodů připojení a připojovacích řetězců formuláře pro PostgreSQL skupinu serverů s podporou ARC
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 93b0547ba25f6534593a0a016ebfa5cbe4d2be2e
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936804"
---
# <a name="get-connection-endpoints-and-form-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>Získání koncových bodů připojení a připojovacích řetězců formuláře pro PostgreSQL skupinu serverů s podporou ARC

Tento článek vysvětluje, jak můžete načíst koncové body připojení pro skupinu serverů a jak formuláře připojovacích řetězců, které budete používat u svých aplikací a nástrojů.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>Získat koncové body připojení:

### <a name="from-cli-with-azdata"></a>Z CLI pomocí azdata
#### <a name="1-connect-to-your-arc-data-controller"></a>1. Připojte se k řadiči dat ARC:
- Pokud již máte relaci otevřenou na hostiteli řadiče dat ARC: spusťte následující příkaz:
```console
azdata login
```

- Pokud na hostiteli řadiče dat ARC nemáte otevřenou relaci, spusťte následující příkaz. 
```console
azdata login --endpoint https://<external IP address of host/data controller>:30080
```

#### <a name="2-show-the-connection-endpoints"></a>2. zobrazení koncových bodů připojení
Spusťte následující příkaz:
```console
azdata arc postgres endpoint list -n <server group name>
```
Vrátí výstup podobný tomuto:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```
Pomocí těchto koncových bodů můžete:
- Vytvoření připojovacích řetězců a připojení k vašim klientským nástrojům nebo aplikacím
- Přístup k řídicím panelům Grafana a Kibana z prohlížeče

Například můžete použít koncový bod s názvem _PostgreSQL instance_ pro připojení s psql ke skupině serverů. Příklad:
```console
psql postgresql://postgres:MyPassworkd@12.345.123.456:1234
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - Heslo uživatele _PostgreSQL_ uvedeného v koncovém bodu s názvem "_PostgreSQL instance_ " je heslo, které jste si zvolili při vytváření skupiny serverů.
> - O azdata: zapůjčení, které je přidruženo k vašemu připojení, trvá přibližně 10 hodin. Až se budete muset znovu připojit. Pokud vaše zapůjčení vypršelo, zobrazí se při pokusu o spuštění příkazu s azdata (kromě přihlášení azdata) následující chybová zpráva: _Chyba: (401)_ 
>  _Důvod: neautorizované_ 
>  _hlavičky HTTP odpovědi: HTTPHeaderDict ({' date ': ' Sun, 06 2020 16:58:38 GMT ', ' Content-Length ': ' 0 ', ' WWW-Authenticate ': '_ 
>  _základní sféra = "_ požadovány přihlašovací údaje", nosičová chyba = "invalid_token", error_description = "token vypršel" "}) _ Pokud k tomu dojde, budete se muset znovu připojit k azdata, jak je vysvětleno výše.

## <a name="from-cli-with-kubectl"></a>Z CLI pomocí kubectl
- Pokud má skupina serverů Postgres verze 12 (výchozí), pak následující příkaz:
```console
kubectl get postgresql-12/<server group name>
```
- Pokud má skupina serverů Postgres verze 11, pak následující příkaz:
```console
kubectl get postgresql-11/<server group name>
```

Tyto příkazy vytvoří výstup podobný následujícímu. Tyto informace můžete použít k vytvoření připojovacích řetězců:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 


## <a name="form-connection-strings"></a>Připojovací řetězce formuláře:
Pro skupinu serverů použijte níže uvedenou tabulku šablon pro řetězce připojení. Pak je můžete zkopírovat a vložit a přizpůsobit podle potřeby:

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

### <a name="web-app"></a>Webová aplikace

```webapp
Database=postgres; Data Source=192.168.1.121; User Id=postgres; Password={your_password_here}
```

## <a name="next-steps"></a>Další kroky
- Přečtěte si o vertikálním navýšení kapacity [(Přidání pracovních uzlů)](scale-out-postgresql-hyperscale-server-group.md) do skupiny serverů.
- Přečtěte si o vertikálním [navýšení nebo snížení kapacity (zvýšení nebo snížení velikosti paměti/virtuální jádra)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) skupiny serverů.


