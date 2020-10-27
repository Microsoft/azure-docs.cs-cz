---
title: REST API dotazování Apache HBA ve službě Azure HDInsight
description: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 361eefa9b0dce811533bb4da9c236c207e31c61c
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547873"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>REST API dotazování Apache HBA ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Použití rozhraní REST pro Apache HBA k dotazování tabulky v jiném oboru názvů, než je výchozí výsledek v běhové chybě (stav HTTP 500).

## <a name="cause"></a>Příčina

REST API HBA jsou podporovány pouze při použití výchozího oboru názvů. Jedná se o známý problém s ohledem na použití oborů názvů HBA nebo volání, která odkazují na konkrétní get pro sloupce se rodinou sloupců se serverem REST v HDInsight. Důvodem je problémy se zabezpečením s bránou HDInsight. Když použijete rozhraní API k vytvoření tabulky s oborem názvů, budete mít přístup k sloupcům prostřednictvím rodin sloupců. je potřeba zadat `:` znak, který se považuje za problém se zabezpečením v modulu brány služby IIS.

## <a name="mitigation"></a>Omezení rizik

Nasaďte aplikaci na virtuální počítač, který je umístěný ve stejné virtuální síti Azure jako cluster HDInsight, a vynechejte server brány nebo REST. Pak můžete komunikovat s HBA buď přímo přes RPC (zcela obejít Server REST), nebo na jednotlivé servery REST běžící na pracovních uzlech, které obcházejí brány HDInsight.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).