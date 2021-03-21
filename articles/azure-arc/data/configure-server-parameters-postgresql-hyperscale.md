---
title: Konfigurace parametrů serveru Postgres Engine pro skupinu serverů PostgreSQL s vlastním škálováním na ARC Azure
titleSuffix: Azure Arc enabled data services
description: Konfigurace parametrů serveru Postgres Engine pro skupinu serverů PostgreSQL s vlastním škálováním na ARC Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: cdbddfc84b3f71576cfd0299f2babec859b4ef1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92311049"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>Nastavení databázového stroje PostgreSQL Hyperscale s podporou služby Azure Arc

Tento dokument popisuje postup nastavení databázového stroje skupiny serverů PostgreSQL Hyperscale na vlastní (jiné než výchozí) hodnoty. Podrobnosti o tom, jaké parametry databázového stroje lze nastavit a jaké jsou jejich výchozí hodnoty, najdete [v dokumentaci k](https://www.postgresql.org/docs/current/runtime-config.html)PostgreSQL.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> Verze Preview nepodporuje nastavení následujících parametrů: 
>
> - `archive_command`
> - `archive_timeout`
> - `log_directory`
> - `log_file_mode`
> - `log_filename`
> - `restore_command`
> - `shared_preload_libraries`
> - `synchronous_commit`
> - `ssl`
> - `wal_level`

## <a name="syntax"></a>Syntax

Obecný formát příkazu pro konfiguraci nastavení databázového stroje je:

```console
azdata arc postgres server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-engine-settings, --re}] {'<parameter name>=<parameter value>, ...'}
```

## <a name="show-current-custom-values"></a>Zobrazit aktuální vlastní hodnoty

### <a name="with-azure-data-cli-azdata-command"></a>Pomocí [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] příkazu

```console
azdata arc postgres server show -n <server group name>
```

Například:

```console
azdata arc postgres server show -n postgres01
```

Tento příkaz vrátí specifikaci skupiny serverů, ve které byste viděli parametry, které jste nastavili. Pokud není k dispozici žádný oddíl engine\settings, znamená to, že všechny parametry jsou spuštěny na výchozí hodnotě:

```console
 "
...
engine": {
      "settings": {
        "default": {
          "autovacuum_vacuum_threshold": "65"
        }
      }
    },
...
```

### <a name="with-kubectl-command"></a>Pomocí příkazu kubectl

Postupujte podle následujících kroků.

1. Načíst druh definice vlastního prostředku pro skupinu serverů

   Spusťte tento příkaz:

   ```console
   azdata arc postgres server show -n <server group name>
   ```

   Například:

   ```console
   azdata arc postgres server show -n postgres01
   ```

   Tento příkaz vrátí specifikaci skupiny serverů, ve které byste viděli parametry, které jste nastavili. Pokud není k dispozici žádný oddíl engine\settings, znamená to, že všechny parametry jsou spuštěny na výchozí hodnotě:

   ```output
   > {
     >"apiVersion": "arcdata.microsoft.com/v1alpha1",
     >"**kind**": "**postgresql-12**",
     >"metadata": {
       >"creationTimestamp": "2020-08-25T14:32:23Z",
       >"generation": 1,
       >"name": "postgres01",
       >"namespace": "arc",  
   ```

   Ve výsledcích výstupu vyhledejte pole `kind` a nastavte jeho hodnotu, například: `postgresql-12` .

2. Popište vlastní prostředek Kubernetes, který odpovídá vaší skupině serverů. 

   Obecný formát příkazu je:

   ```console
   kubectl describe <kind of the custom resource> <server group name> -n <namespace name>
   ```

   Například:

   ```console
   kubectl describe postgresql-12 postgres01
   ```

   Pokud jsou pro nastavení modulu nastaveny vlastní hodnoty, vrátí se. Například:

   ```output
   Engine:
   ...
    Settings:
      Default:
        autovacuum_vacuum_threshold:  65
   ```

   Pokud jste nestavili vlastní hodnoty pro některá z nastavení modulu, bude sekce nastavení modulu v nástroji `resultset` prázdná, například:

   ```output
   Engine:
   ...
       Settings:
         Default:
   ```

## <a name="set-custom-values-for-engine-settings"></a>Nastavení vlastních hodnot pro nastavení modulu

Níže uvedené příkazy nastaví parametry uzlu koordinátora a pracovní uzly ve vašem PostgreSQL měřítku na stejné hodnoty. V rámci skupiny serverů ještě není možné nastavovat parametry pro roli. To znamená, že ještě není možné nakonfigurovat daný parametr na konkrétního uzlu koordinátora a na jinou hodnotu pro pracovní uzly.

### <a name="set-a-single-parameter"></a>Nastavení jednoho parametru

```console
azdata arc server edit -n <server group name> -e <parameter name>=<parameter value>
```

Například:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=8MB
```

### <a name="set-multiple-parameters-with-a-single-command"></a>Nastavení více parametrů jediným příkazem

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>=<parameter value>, <parameter name>=<parameter value>,...'
```

Například:

```console
azdata arc postgres server edit -n postgres01 -e 'shared_buffers=8MB, max_connections=50'
```

### <a name="reset-a-parameter-to-its-default-value"></a>Resetování parametru na jeho výchozí hodnotu

Chcete-li obnovit výchozí hodnotu parametru, nastavte jej bez označení hodnoty. 

Například:

```console
azdata arc postgres server edit -n postgres01 -e shared_buffers=
```

### <a name="reset-all-parameters-to-their-default-values"></a>Resetovat všechny parametry na jejich výchozí hodnoty

```console
azdata arc postgres server edit -n <server group name> -e '' -re
```

Například:

```console
azdata arc postgres server edit -n postgres01 -e '' -re
```

## <a name="special-considerations"></a>Zvláštní požadavky

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>Nastavení parametru, který hodnota obsahuje čárku, mezeru nebo speciální znak

```console
azdata arc postgres server edit -n <server group name> -e '<parameter name>="<parameter value>"'
```

Například:

```console
azdata arc postgres server edit -n postgres01 -e 'custom_variable_classes = "plpgsql,plperl"'
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>Předat proměnnou prostředí v hodnotě parametru

Proměnná prostředí by měla být zabalená v rámci "" "", aby se nevyřešila před nastavením.

Například: 

```console
azdata arc postgres server edit -n postgres01 -e 'search_path = "$user"'
```

## <a name="next-steps"></a>Další kroky
- Přečtěte si o vertikálním navýšení kapacity [(Přidání pracovních uzlů)](scale-out-postgresql-hyperscale-server-group.md) do skupiny serverů.
- Přečtěte si o vertikálním [navýšení nebo snížení kapacity (zvýšení nebo snížení velikosti paměti/virtuální jádra)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) skupiny serverů.
