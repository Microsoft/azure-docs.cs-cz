---
title: Vytvoření clusteru selhalo s chybou DomainNotFound ve službě Azure HDInsight
description: Postup řešení potíží a možná řešení pro problémy při komunikaci s clustery Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 79cd3b687dc8a2eb838146a6dc8a40b184a55fe6
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285571"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]