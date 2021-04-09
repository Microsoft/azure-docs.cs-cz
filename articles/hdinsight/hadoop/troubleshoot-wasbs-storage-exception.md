---
title: Přistupující účet nepodporuje ve službě Azure HDInsight chybu HTTP.
description: Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 46063d5f2d9ff4b85914ad7c4cd74a2400298db0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943074"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>Přistupující účet nepodporuje ve službě Azure HDInsight chybu HTTP.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Byla přijata následující chybová zpráva:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Příčina

K dispozici je několik důvodů, proč chybová zpráva byla přijata:

* Účet úložiště má povolený [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md) a používá se nesprávné [schéma identifikátoru URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) .

* Cluster byl vytvořen s účtem úložiště, který měl *zakázaný* zabezpečený přenos. Potom byl v účtu úložiště povolený zabezpečený přenos.

## <a name="resolution"></a>Řešení

Pokud je pro Azure Storage nebo Data Lake Storage Gen2 povolený zabezpečený přenos, identifikátor URI by byl `wasbs://` nebo v `abfss://` uvedeném pořadí.  Viz také [zabezpečený přenos](../../storage/common/storage-require-secure-transfer.md).

Pro nové clustery použijte účet úložiště, který už má požadované nastavení zabezpečeného přenosu. Neměňte nastavení zabezpečeného přenosu pro účet úložiště, který používá existující cluster.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).