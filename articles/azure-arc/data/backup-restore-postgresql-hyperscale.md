---
title: Zálohování a obnovení pro skupiny serverů Azure Database for PostgreSQL Hyperscale
description: Zálohování a obnovení pro skupiny serverů Azure Database for PostgreSQL Hyperscale
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 8b3304c673e8606667246a7d0df9ad8f3be11d9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686695"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Zálohování a obnovení PostgreSQL skupin serverů s podporou rozšíření Azure ARC

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Když zálohujete nebo obnovíte skupinu serverů PostgreSQL s podporou škálování Azure, celá sada databází na všech uzlech PostgreSQL skupiny serverů se zálohuje a obnoví.

## <a name="take-a-manual-full-backup"></a>Provedení ručního úplného zálohování

Pokud chcete vytvořit úplnou zálohu všech složek dat a protokolů vaší skupiny serverů, spusťte následující příkaz:
```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Kde:
- __název__ označuje název zálohy.
- __název serveru__ označuje skupinu serverů.
- __No-Wait__ indikuje, že příkazový řádek nebude čekat na dokončení zálohování, aby bylo možné pokračovat v používání tohoto okna příkazového řádku.

Tento příkaz koordinuje distribuovanou úplnou zálohu napříč všemi uzly, které tvoří PostgreSQL serverovou skupinu s povoleným rozšířením Azure ARC. Jinými slovy, budou zálohovat všechna data ve vašem koordinátorovi a uzlech pracovních procesů.

Například:

```console
azdata arc postgres backup create --name backup12082020-0250pm --server-name postgres01
```

Po dokončení zálohování se vrátí ID, název, velikost, stav a časové razítko zálohy. Například:
```console
{
  "ID": "8085723fcbae4aafb24798c1458f4bb7",
  "name": "backup12082020-0250pm",
  "size": "9.04 MiB",
  "state": "Done",
  "timestamp": "2020-12-08 22:50:22+00:00"
}
```
`+xx:yy` označuje časové pásmo pro čas pořízení zálohy. V tomto příkladu "+ 00:00" znamená čas UTC (UTC + 00 hodina 00 minut).

> [!NOTE]
> Ještě není možné:
> - Naplánovat automatické zálohování
> - Zobrazit průběh zálohování během jeho pořízení

## <a name="list-backups"></a>Zobrazení seznamu záloh

Chcete-li zobrazit seznam záloh, které jsou k dispozici pro obnovení, spusťte následující příkaz:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Například:

```console
azdata arc postgres backup list --server-name postgres01
```

Vrátí výstup podobný tomuto:

```output
ID                                Name                   Size       State    Timestamp
--------------------------------  ---------------------  ---------  -------  -------------------------
d744303b1b224ef48be9cba4f58c7cb9  backup12072020-0731pm  13.83 MiB  Done     2020-12-08 03:32:09+00:00
c4f964d28da34318a420e6d14374bd36  backup12072020-0819pm  9.04 MiB   Done     2020-12-08 04:19:49+00:00
a304c6ef99694645a2a90ce339e94714  backup12072020-0822pm  9.1 MiB    Done     2020-12-08 04:22:26+00:00
47d1f57ec9014328abb0d8fe56020760  backup12072020-0827pm  9.06 MiB   Done     2020-12-08 04:27:22+00:00
8085723fcbae4aafb24798c1458f4bb7  backup12082020-0250pm  9.04 MiB   Done     2020-12-08 22:50:22+00:00
```

Sloupec časového razítka označuje bod v čase UTC, kdy byla provedena záloha.

## <a name="restore-a-backup"></a>Obnovení zálohy
V této části vám ukážeme, jak provést úplné obnovení nebo obnovení k určitému bodu v čase. Po obnovení úplné zálohy obnovíte celý obsah zálohy. Při obnovení bodu v čase můžete obnovit až k určitému bodu v čase. Všechny transakce, které byly provedeny později než tento bod v čase, nebudou obnoveny.

### <a name="restore-a-full-backup"></a>Obnovení úplné zálohy
Chcete-li obnovit celý obsah zálohy, spusťte příkaz:
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

Kde:
- __Backup-ID__ je ID zálohy zobrazené v příkazu seznamu zálohování uvedeného výše.
Tím se koordinuje distribuované úplné obnovení napříč všemi uzly, které tvoří PostgreSQL serverovou skupinu s povoleným rozšířením Azure ARC. Jinými slovy, obnoví všechna data v koordinátorech a uzlech pracovních procesů.

#### <a name="examples"></a>Příklady:

__Obnovte skupinu serverů postgres01 sám na sebe:__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Tato operace je podporována pouze pro PostgreSQL verze 12 a vyšší.

__Obnovte skupinu serverů postgres01 na jinou skupinu serverů postgres02:__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
Tato operace je podporována pro všechny verze PostgreSQL počínaje verzí 11. Cílová skupina serverů musí být vytvořená před operací obnovení, musí být stejná konfigurace a musí používat stejný trvalý virtuální okruh (PVC) pro zálohování jako skupinu zdrojového serveru.

Po dokončení operace obnovení vrátí výstup podobný tomuto příkazovému řádku:

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> Ještě není možné:
> - Obnovte zálohu tím, že uvedete její název.
> - Zobrazit průběh operace obnovení


### <a name="do-a-point-in-time-restore"></a>Obnovení bodu v čase

Chcete-li obnovit skupinu serverů až do konkrétního časového okamžiku, spusťte příkaz:
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

Chcete-li si přečíst obecný formát příkazu RESTORE, spusťte příkaz: `azdata arc postgres backup restore --help` .

Kde `time` je bod v čase k obnovení do. Zadejte buď časové razítko, nebo číslo a příponu ( `m` pro minuty, `h` hodiny, `d` dny nebo `w` týdny). Například se `1.5h` vrátí 90 minut.

#### <a name="examples"></a>Příklady:
__Proveďte obnovení skupiny serverů postgres01 do samotného bodu v čase:__

Obnovení skupiny serverů na sebe sama o bod v čase ještě není možné.

__Proveďte obnovení skupiny serverů postgres01 na jinou skupinu serverů postgres02 do konkrétního časového razítka:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

Tento příklad obnoví do skupiny serverů postgres02 stav, ve kterém byla skupina serverů postgres01 dne 8. prosince 2020 ve 04:23:48.75 UTC. Všimněte si, že "+ 00" označuje časové pásmo v čase, které označíte. Pokud neuvedete časové pásmo, bude použito časové pásmo klienta, ze kterého spouštíte operaci obnovení.

Například:
- `2020-12-08 04:23:48.751326+00` je interpretována jako `2020-12-08 04:23:48.751326` UTC
- Pokud jste v tichomořském časovém pásmu (PST = UTC + 08), `2020-12-08 04:23:48.751326` je interpretována jako `2020-12-08 12:23:48.751326` UTC. Tato operace se podporuje pro všechny verze PostgreSQL počínaje verzí 11. Skupina cílových serverů musí být vytvořená před operací obnovení a musí používat stejný trvalý virtuální okruh (PVC) pro zálohování jako skupinu zdrojového serveru.


__Udělejte v čase obnovení skupiny serverů postgres01 na jinou skupinu serverů postgres02 na určitou dobu v minulosti:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

Tento příklad obnoví do skupiny serverů postgres02 stav, ve kterém se postgres01 skupiny serverů před 22 minutami.
Tato operace je podporována pro všechny verze PostgreSQL počínaje verzí 11. Skupina cílových serverů musí být vytvořená před operací obnovení a musí používat stejný trvalý virtuální okruh (PVC) pro zálohování jako skupinu zdrojového serveru.

> [!NOTE]
> Ještě není možné:
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

Například:

```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```

Název a ID záloh můžete načíst spuštěním příkazu list Backup, jak je popsáno v předchozím odstavci.

Další podrobnosti o příkazu Delete získáte spuštěním příkazu:

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Další kroky
- Přečtěte si o vertikálním navýšení kapacity [(Přidání pracovních uzlů)](scale-out-postgresql-hyperscale-server-group.md) do skupiny serverů.
- Přečtěte si o vertikálním [navýšení nebo snížení kapacity (zvýšení nebo snížení velikosti paměti/virtuální jádra)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) skupiny serverů.
