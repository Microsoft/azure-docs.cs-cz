---
title: Zálohování a obnovení pro Azure Database for PostgreSQL skupin serverů s škálovatelným škálováním
description: Zálohování a obnovení pro Azure Database for PostgreSQL skupin serverů s škálovatelným škálováním
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d300f3e02d2a1a83410d5b7d981298a4743fb223
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936382"
---
# <a name="backup-and-restore-for-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Zálohování a obnovení pro skupiny serverů PostgreSQL s podporou rozšíření Azure ARC

Můžete provést úplnou zálohu nebo obnovení skupiny serverů PostgreSQL s podporou rozšíření Azure ARC. V takovém případě se zálohuje a obnoví celá sada databází na všech uzlech PostgreSQL skupiny serverů s podporou ARC Azure.
Pokud chcete vytvořit zálohu a obnovit ji, musíte zajistit, aby byla pro skupinu serverů nakonfigurovaná třída úložiště zálohy. Prozatím musíte v okamžiku vytvoření skupiny serverů indikovat třídu úložiště zálohy. Není ještě možné konfigurovat skupinu serverů tak, aby po vytvoření používala třídu úložiště zálohy.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!CAUTION]
> Verze Preview nepodporuje zálohování a obnovení pro verzi 11 modulu Postgres. Podporuje jenom zálohování a obnovení pro Postgres verze 12.

## <a name="verify-configuration"></a>Ověřit konfiguraci

Nejdřív ověřte, jestli je vaše stávající skupina serverů nakonfigurovaná tak, aby používala třídu úložiště záloh.

Po nastavení názvu skupiny serverů spusťte následující příkaz:
```console
 azdata arc postgres server show -n postgres01
```
Podívejte se na část s úložištěm výstupu:
```console
...
"storage": {
      "backups": {
        "className": "local-storage"
      },
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
...
```
Pokud se vám zobrazí část zálohy, znamená to, že vaše skupina serverů byla nakonfigurovaná tak, aby používala třídu úložiště zálohy, a je připravená na zálohování a obnovení. Pokud se nezobrazí část zálohy, musíte skupinu serverů odstranit a znovu vytvořit, aby se nakonfigurovala třída úložiště záloh. V tuto chvíli není ještě možné konfigurovat třídu úložiště zálohy po vytvoření skupiny serverů.

>[!IMPORTANT]
>Pokud je vaše skupina serverů už nakonfigurovaná tak, aby používala třídu úložiště zálohy, přeskočte další krok a přejděte přímo na krok "provedení ručního úplného zálohování".

## <a name="create-a-server-group"></a>Vytvoření skupiny serverů 

Dále vytvořte skupinu serverů konfigurovanou pro zálohování a obnovení.

Aby bylo možné provádět zálohování a obnovovat je, je nutné vytvořit server, který je nakonfigurován s třídou úložiště.

Pokud chcete získat seznam tříd úložiště dostupných v clusteru Kubernetes, spusťte následující příkaz:

```console
kubectl get sc
```

<!--The general format of create server group command is documented [here](create-postgresql-instances.md)-->

```console
azdata arc postgres server create -n <name> --workers 2 --storage-class-backups <storage class name> [--storage-class-data <storage class name>] [--storage-class-logs <storage class name>]
```

Například pokud jste vytvořili jednoduché prostředí založené na kubeadm:
```console
azdata arc postgres server create -n postgres01 --workers 2 --storage-class-backups local-storage
```

## <a name="take-manual-full-backup"></a>Provedení ručního úplného zálohování

Dále proveďte ruční úplnou zálohu.

Pokud chcete vytvořit úplnou zálohu všech složek dat a protokolů vaší skupiny serverů, spusťte následující příkaz:

```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Kde:
- __název__ označuje název zálohy.
- __název serveru__ označuje skupinu serverů.
- __No-Wait__ indikuje, že příkazový řádek nebude čekat na dokončení zálohování, aby bylo možné pokračovat v používání tohoto okna příkazového řádku.

>**Poznámka**: příkaz, který umožňuje zobrazit seznam záloh, které jsou k obnovení k dispozici, zatím nezobrazuje datum a čas, kdy byla záloha provedena. Proto je doporučeno zadat název zálohy (pomocí parametru--name), který obsahuje informace o datu a čase.

Tento příkaz koordinuje distribuovanou úplnou zálohu napříč všemi uzly, které tvoří PostgreSQL serverovou skupinu s povoleným rozšířením Azure ARC. Jinými slovy, budou zálohovat všechna data ve vašem koordinátorovi a uzlech pracovních procesů.

Příklad:
```console
azdata arc postgres backup create --name MyBackup_Aug31_0730amPST --server-name postgres01
```

Po dokončení zálohování se vrátí ID, název a stav zálohy. Příklad:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "name": "MyBackup_Aug31_0730amPS",
  "state": "Done"
}
```

> [!NOTE]
> Ještě není možné:
> - Naplánovat automatické zálohování
> - Zobrazit průběh zálohování během jeho pořízení

## <a name="list-backups"></a>Vypsat zálohy

Zobrazí seznam záloh, které jsou k dispozici pro obnovení.

Chcete-li zobrazit seznam záloh, které jsou k dispozici pro obnovení, spusťte následující příkaz:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Příklad:
```console
azdata arc postgres backup list --server-name postgres01
```

Vrátí výstup podobný tomuto:
```console
ID                                Name                      State
--------------------------------  ------------------------  -------
d134f51aa87f4044b5fb07cf95cf797f  MyBackup_Aug31_0730amPST  Done
```

## <a name="restore-a-backup"></a>Obnovení zálohy

Chcete-li obnovit zálohu celé skupiny serverů, spusťte příkaz:

```console
azdata arc postgres backup restore --server-name <server group name> --backup-id <backup id>
```

Kde:
- __Backup-ID__ je ID zálohy zobrazené v příkazu pro zálohování seznamu (viz krok 3).
Tím se koordinuje distribuované úplné obnovení napříč všemi uzly, které tvoří PostgreSQL serverovou skupinu s povoleným rozšířením Azure ARC. Jinými slovy, obnoví všechna data v koordinátorech a uzlech pracovních procesů.

Příklad:
```console
azdata arc postgres backup restore --server-name postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Po dokončení operace obnovení vrátí výstup podobný tomuto příkazovému řádku:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```
> [!NOTE]
> Ještě není možné:
> - Obnovte zálohu tím, že uvedete její název.
> - Obnovení skupiny serverů pod jiným názvem nebo na jinou skupinu serverů
> - Zobrazit průběh operace obnovení

## <a name="delete-backups"></a>Odstranění záloh
Uchovávání záloh nelze nastavit ve verzi Preview. Zálohy, které nepotřebujete, ale můžete odstranit ručně.
Příkaz Obecné pro odstranění záloh je následující:
```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```
kde:
- `--server-name` je název skupiny serverů, ze které chce uživatel odstranit zálohu.
- `--name` je název zálohy, která se má odstranit.
- `-id`je ID zálohy, která se má odstranit.

> [!NOTE]
> `--name` a `-id` se vzájemně vylučují.

Název a ID záloh můžete načíst spuštěním příkazu list Backup, jak je popsáno v předchozím odstavci.

Můžete například zvážit, že máte uvedené následující zálohy:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
5b0481dfc1c94b4cac79dd56a1bb21f4  MyBackup091720200110am  Done
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```
a chcete odstranit první z nich, spusťte následující příkaz:
```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```
Pokud byste chtěli v tomto okamžiku vypsat zálohy, získáte následující výstup:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```

Další podrobnosti o příkazu Delete získáte spuštěním příkazu:
```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Další kroky
- Přečtěte si o vertikálním navýšení kapacity [(Přidání pracovních uzlů)](scale-out-postgresql-hyperscale-server-group.md) do skupiny serverů.
- Přečtěte si o vertikálním [navýšení nebo snížení kapacity (zvýšení nebo snížení velikosti paměti/virtuální jádra)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) skupiny serverů.
