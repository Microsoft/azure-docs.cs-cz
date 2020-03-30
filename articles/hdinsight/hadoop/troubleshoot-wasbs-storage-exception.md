---
title: K přistupující účet nepodporuje chybu http v Azure HDInsight
description: Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165550"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>K přistupující účet nepodporuje chybu http v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Je přijata následující chybová zpráva:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Příčina

Existuje několik důvodů, proč je přijata chybová zpráva:

* Účet úložiště má [povolený zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md) a používá se nesprávné [schéma URI.](../hdinsight-hadoop-linux-information.md#URI-and-scheme)

* Cluster byl vytvořen s účtem úložiště, který měl *zakázaný*zabezpečený přenos . Poté byl v účtu úložiště povolen zabezpečený přenos.

## <a name="resolution"></a>Řešení

Pokud je zabezpečený přenos povolen pro Azure Storage nebo `wasbs://` `abfss://`Data Lake Storage Gen2, identifikátor URI by byl nebo , v uvedeném pořadí.  Viz také [bezpečný přenos](../../storage/common/storage-require-secure-transfer.md).

Pro nové clustery použijte účet úložiště, který již má požadované nastavení zabezpečeného přenosu. Neměňte nastavení zabezpečeného přenosu pro účet úložiště, který používá existující cluster.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
