---
title: Vytvoření clusteru se nezdaří s chybou DomainNotFound v Azure HDInsight
description: Řešení potíží s kroky a možnými řešeními problémů při interakci s clustery Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776232"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Scénář: Vytvoření clusteru se nezdaří s chybou DomainNotFound v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Vytvoření clusteru HDI Secure (Enterprise `DomainNotFound` Security Package) se nezdaří s chybovou zprávou.

## <a name="cause"></a>Příčina

Nesprávné nastavení DNS.

## <a name="resolution"></a>Řešení

Při nasazení clusterů spojených s doménou vytvoří rozhraní HDI interní uživatelské jméno a heslo ve službách AAD DS (pro každý cluster) a připojí všechny uzly clusteru k této doméně. Spojení domény se provádí pomocí nástrojů Samba. Ujistěte se, že jsou splněny následující požadavky:

* Název domény by měl přeložit prostřednictvím služby DNS.
* Ip adresa řadičů domény by měla být nastavena v nastavení DNS pro virtuální síť, ve které je cluster nasazován.
* Pokud virtuální síť je peered s virtuální sítí AAD DS, pak je nutné provést ručně.
* Pokud používáte servery pro předávání DNS, musí se název domény správně přeložit ve virtuální síti.
* Zásady zabezpečení (NSG) by neměly blokovat připojení k doméně.

### <a name="additional-debugging-steps"></a>Další kroky ladění

* Nasazení virtuálního počítače se systémem Windows ve stejné podsíti, připojení k počítači pomocí uživatelského jména a hesla (to lze provést prostřednictvím uživatelského rozhraní ovládacího panelu) nebo

* Nasazení virtuálního počítače Ubuntu ve stejné podsíti a doméně připojit k počítači
  * SSH do stroje
  * sudo su
  * Spuštění skriptu s uživatelským jménem a heslem
  * Skript bude ping, vytvořit požadované konfigurační soubory a pak domény. Pokud se to podaří, nastavení DNS je dobré.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
