---
title: Úložiště pro zabezpečený přenos Apache Hadoop & – Azure HDInsight
description: Naučte se vytvářet clustery HDInsight s účty úložiště Azure s povoleným zabezpečeným přístupem.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: 69844a392aa03dc962f8af456996ab51f1adf561
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547465"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop clusterů s účty úložiště s bezpečným přenosem ve službě Azure HDInsight

Funkce [Vyžadovat zabezpečený přenos](../storage/common/storage-require-secure-transfer.md) zvyšuje zabezpečení účtu služby Azure Storage tím, že vynucuje přenos všech požadavků na účet přes zabezpečené připojení. Tuto funkci a schéma wasbs podporují pouze clustery HDInsight verze 3.6 nebo novější.

> [!IMPORTANT]
> Povolení přenosu zabezpečeného úložiště po vytvoření clusteru může způsobit chyby v účtu úložiště a nedoporučuje se ho použít. Je lepší vytvořit nový cluster pomocí účtu úložiště s povoleným zabezpečeným přenosem.

## <a name="storage-accounts"></a>Účty úložiště

### <a name="azure-portal"></a>portál Azure

Ve výchozím nastavení je při vytváření účtu úložiště v Azure Portal povolena vlastnost požadovaná zabezpečeným přenosem.

Pokud chcete aktualizovat existující účet úložiště pomocí Azure Portal, přečtěte si téma [vyžadování zabezpečeného přenosu s Azure Portal](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

Pro rutinu PowerShellu [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)ověřte, že parametr `-EnableHttpsTrafficOnly` je nastavený na `1` .

Pokud chcete aktualizovat existující účet úložiště pomocí PowerShellu, přečtěte si téma [vyžadování zabezpečeného přenosu pomocí PowerShellu](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell).

### <a name="azure-cli"></a>Azure CLI

Pro příkaz Azure CLI [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create), ujistěte se, že parametr `--https-only` je nastavený na `true` .

Pokud chcete aktualizovat existující účet úložiště pomocí Azure CLI, přečtěte si téma [vyžadování zabezpečeného přenosu pomocí Azure CLI](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

## <a name="add-additional-storage-accounts"></a>Přidání dalších účtů úložiště

Existuje několik možností, jak přidat další účty úložiště s povoleným zabezpečeným přenosem:

* Úprava šablony Azure Resource Manageru v poslední části.
* Vytvoření clusteru pomocí webu [Azure Portal](https://portal.azure.com) a zadání propojeného účtu úložiště.
* Použití akce skriptu k přidání dalších účtů úložiště s povoleným zabezpečeným přenosem do existujícího clusteru HDInsight. Další informace najdete v tématu [Přidání dalších účtů úložiště do služby HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Další kroky

* Použití Azure Storage (WASB) místo [Apache HADOOP HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) jako výchozího úložiště dat
* Informace o tom, jak HDInsight používá Azure Storage, najdete v tématu [Používání Azure Storage s HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Informace o tom, jak nahrát data do služby HDInsight, najdete v tématu [Nahrání dat do služby HDInsight](hdinsight-upload-data.md).