---
title: Vytvoření clusteru selhalo s chybou DomainNotFound ve službě Azure HDInsight
description: Postup řešení potíží a možná řešení pro problémy při komunikaci s clustery Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76776232"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Scénář: Vytvoření clusteru selhalo s chybou DomainNotFound ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Vytvoření clusteru HDI Secure (Balíček zabezpečení podniku) se nezdařilo s `DomainNotFound` chybovou zprávou.

## <a name="cause"></a>Příčina

Nesprávná nastavení DNS.

## <a name="resolution"></a>Řešení

Po nasazení clusterů připojených k doméně vytvoří HDI interní uživatelské jméno a heslo v AAD DS (pro každý cluster) a spojí všechny uzly clusteru s touto doménou. Připojení k doméně se provádí pomocí nástrojů pro Samba. Ujistěte se, že jsou splněné následující předpoklady:

* Název domény by se měl vyřešit prostřednictvím DNS.
* IP adresa řadičů domény by se měla nastavit v nastavení DNS pro virtuální síť, ve které se cluster nasazuje.
* Pokud má virtuální síť navázaný partnerský vztah s virtuální sítí služby AAD DS, je nutné ji provést ručně.
* Pokud používáte servery DNS pro přeposílání, musí se název domény správně přeložit v rámci virtuální sítě.
* Zásady zabezpečení (skupin zabezpečení sítě) by neměly blokovat připojení k doméně.

### <a name="additional-debugging-steps"></a>Další kroky ladění

* Nasaďte virtuální počítač s Windows ve stejné podsíti, doménu připojte k počítači pomocí uživatelského jména a hesla (můžete to udělat prostřednictvím uživatelského rozhraní ovládacích panelů) nebo

* Nasazení virtuálního počítače s Ubuntu ve stejné podsíti a doméně připojit se k počítači
  * Přihlaste se k počítači přes SSH
  * sudo Su
  * Spusťte skript s uživatelským jménem a heslem.
  * Skript provede příkaz k otestování testu, vytvoří požadované konfigurační soubory a pak doménu. Pokud je to úspěšné, vaše nastavení DNS budou dobrá.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
