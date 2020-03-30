---
title: Apache Hadoop & zabezpečené úložiště přenosu - Azure HDInsight
description: Naučte se vytvářet clustery HDInsight s účty úložiště Azure s povoleným zabezpečeným přístupem.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: c1e5ca8b0bb828e5e8ce896bba6a5278266b118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560078"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Clustery Apache Hadoop s účty zabezpečeného úložiště přenosu v Azure HDInsight

Funkce [Vyžadovat zabezpečený přenos](../storage/common/storage-require-secure-transfer.md) zvyšuje zabezpečení účtu služby Azure Storage tím, že vynucuje přenos všech požadavků na účet přes zabezpečené připojení. Tuto funkci a schéma wasbs podporují pouze clustery HDInsight verze 3.6 nebo novější.

> [!IMPORTANT]
> Povolení zabezpečeného přenosu úložiště po vytvoření clusteru může vést k chybám pomocí účtu úložiště a nedoporučuje se. Je lepší vytvořit nový cluster pomocí účtu úložiště s již povoleným zabezpečeným přenosem.

## <a name="storage-accounts"></a>Účty úložiště

### <a name="azure-portal"></a>portál Azure

Ve výchozím nastavení je vlastnost požadovaná pro zabezpečený přenos povolena při vytváření účtu úložiště na webu Azure Portal.

Pokud chcete aktualizovat existující účet úložiště pomocí portálu Azure, přečtěte si informace [o vyžadování zabezpečeného přenosu pomocí portálu Azure](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

U rutiny prostředí PowerShell [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)zkontrolujte, zda je parametr `-EnableHttpsTrafficOnly` nastaven na . `1`

Informace o aktualizaci existujícího účtu úložiště pomocí PowerShellu najdete [v tématu Vyžadovat zabezpečený přenos pomocí PowerShellu](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell).

### <a name="azure-cli"></a>Azure CLI

Pro vytvoření účtu [úložiště příkazu](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)Azure CLI az zkontrolujte, zda je parametr `--https-only` nastaven na `true`.

Pokud chcete aktualizovat existující účet úložiště pomocí azure cli, přečtěte si informace [o vyžadování zabezpečeného přenosu pomocí příkazového příkazového příkazu k řešení Azure](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

## <a name="add-additional-storage-accounts"></a>Přidání dalších účtů úložiště

Existuje několik možností, jak přidat další účty úložiště s povoleným zabezpečeným přenosem:

* Úprava šablony Azure Resource Manageru v poslední části.
* Vytvoření clusteru pomocí webu [Azure Portal](https://portal.azure.com) a zadání propojeného účtu úložiště.
* Použití akce skriptu k přidání dalších účtů úložiště s povoleným zabezpečeným přenosem do existujícího clusteru HDInsight. Další informace najdete v tématu [Přidání dalších účtů úložiště do služby HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Další kroky

* Použití Azure Storage (WASB) místo [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) jako výchozí úložiště dat
* Informace o tom, jak HDInsight používá Azure Storage, najdete v tématu [Používání Azure Storage s HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Informace o tom, jak nahrát data do služby HDInsight, najdete v tématu [Nahrání dat do služby HDInsight](hdinsight-upload-data.md).
