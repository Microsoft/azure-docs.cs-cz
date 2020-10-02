---
title: Obnovení ukázkové databáze AdventureWorks do spravované instance SQL
description: Obnovení ukázkové databáze AdventureWorks do spravované instance SQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d238a889648e789087a0803f6b50288318462c7b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629059"
---
# <a name="restore-the-adventureworks-sample-database-into-sql-managed-instance---azure-arc"></a>Obnovení ukázkové databáze AdventureWorks do spravované instance SQL – ARC Azure

[AdventureWorks](/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=tsql&preserve-view=true) je ukázková databáze obsahující databázi OLTP, která se často používá v kurzech a příkladech. Je poskytována a spravována společností Microsoft jako součást [úložiště GitHub v ukázkách SQL Server](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases).

Tento dokument popisuje jednoduchý proces, který načte ukázkovou databázi AdventureWorks do spravované instance SQL – Azure ARC.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>Stažení záložního souboru AdventureWorks

Stáhněte soubor zálohy AdventureWorks (. bak) do kontejneru spravované instance SQL. V tomto příkladu použijte `kubectl exec` příkaz pro vzdálené spuštění příkazu uvnitř kontejneru spravované instance SQL ke stažení souboru. bak do kontejneru. Tento soubor si stáhněte z libovolného umístění přístupného v `wget` případě, že máte jiné záložní soubory databáze, které chcete vyžádat, aby byly uvnitř kontejneru spravované instance SQL. Jakmile se nachází uvnitř kontejneru spravované instance SQL, je snadné ho obnovit pomocí standardního `RESTORE DATABASE` T-SQL.

Spusťte příkaz podobný tomuto: ke stažení souboru. bak, který nahradí hodnotu názvu pod a názvem oboru názvů před spuštěním.
> [!NOTE]
>  Aby váš kontejner mohl stáhnout soubor z GitHubu, bude muset mít připojení k internetu přes 443.

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

Příklad

```console
kubectl exec sqltest1-0 -n arc -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

## <a name="restore-the-adventureworks-database"></a>Obnovení databáze AdventureWorks

Podobně můžete spustit `kubectl` příkaz exec `sqlcmd` , který použije nástroj CLI, který je součástí kontejneru spravované instance SQL, a spustí příkaz T-SQL pro obnovení databáze.

Chcete-li obnovit databázi, spusťte příkaz podobný tomuto. Před spuštěním nahraďte hodnotu názvu pod, heslem a názvem oboru názvů.

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P <password> -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
Příklad

```console
kubectl exec sqltest1-0 -n arc -- -c arc-sqlmi /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P MyPassword! -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
