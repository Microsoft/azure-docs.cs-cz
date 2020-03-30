---
title: Clustery Azure HDInsight s šifrováním disku ztrácejí přístup k trezoru klíčů
description: Řešení potíží s kroky a možná řešení problémů při interakci s clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: 2ae389be25cd8633a53a49cf000796c1510733a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965161"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Scénář: Clustery Azure HDInsight s šifrováním disku ztratí přístup k trezoru klíčů

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Výstraha Resource Health Center (RHC), `The HDInsight cluster is unable to access the key for BYOK encryption at rest`, se zobrazí pro clustery Bring Your Own Key (BYOK), kde uzly clusteru ztratily přístup k trezoru klíčů zákazníkům (KV). Podobné výstrahy lze také vidět na Apache Ambari UI.

## <a name="cause"></a>Příčina

Výstraha zajišťuje, že KV je přístupná z uzlů clusteru, čímž zajišťuje síťové připojení, stav KV a zásady přístupu pro uživatele přiřazenou spravovanou identitu. Tato výstraha je pouze upozornění na blížící se vypnutí broker při následném restartování uzlu, cluster nadále fungovat až do restartování uzlů.

Přejděte do režimu Apache Ambari a vyhledejte další informace o výstraze ze **stavu trezoru klíče šifrování disku**. Tato výstraha bude mít podrobnosti o důvodu selhání ověření.

## <a name="resolution"></a>Řešení

### <a name="kvaad-outage"></a>Výpadek KV/AAD

Další podrobnosti naleznou na [stránku dostupnosta a redundance služby](../../key-vault/key-vault-disaster-recovery-guidance.md) Azure Key Vault a stránka stavu Azure.https://status.azure.com/

### <a name="kv-accidental-deletion"></a>KV náhodné odstranění

* Obnovit odstraněný klíč na KV automaticky obnovit. Další informace naleznete v [tématu Recover Deleted Key](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey).
* Oslovte tým KV, abyste se zotavili z náhodného odstranění.

### <a name="kv-access-policy-changed"></a>Zásady přístupu KV byly změněny.

Obnovte zásady přístupu pro uživatele přiřazenou spravovanou identitu, která je přiřazena clusteru HDI pro přístup k KV.

### <a name="key-permitted-operations"></a>Klíčové povolené operace

Pro každý klíč v KV můžete zvolit sadu povolených operací. Ujistěte se, že máte povoleny operace zalamování a rozbalení pro klíč BYOK

### <a name="expired-key"></a>Klíč s prošlou platností

Pokud uplynula platnost a klíč není otočen, obnovte klíč ze záložního hsm nebo kontaktujte tým KV, abyste vymažou datum vypršení platnosti.

### <a name="kv-firewall-blocking-access"></a>KV brána firewall blokuje přístup

Opravte nastavení brány firewall KV, aby se uzly clusteru BYOK mohly dostat k KV.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>Pravidla nsg pro blokování přístupu virtuální sítě

Zkontrolujte pravidla skupiny zabezpečení sítě přidružená k virtuální síti připojené ke clusteru.

## <a name="mitigation-and-prevention-steps"></a>Kroky ke zmírnění a prevenci

### <a name="kv-accidental-deletion"></a>KV náhodné odstranění

* Konfigurace trezoru klíčů pomocí [sady Zámku prostředků](../../azure-resource-manager/management/lock-resources.md).
* Zálohovat klíče k modulu hardwarového zabezpečení.

### <a name="key-deletion"></a>Odstranění klíče

Cluster by měl být odstraněn před odstraněním klíče.

### <a name="kv-access-policy-changed"></a>Zásady přístupu KV byly změněny.

Pravidelně auditujte a otestujte zásady přístupu.

### <a name="expired-key"></a>Klíč s prošlou platností

* Zálohujte klíče k vašemu hsm.
* Použijte klíč bez nastavení vypršení platnosti.
* Pokud je třeba nastavit vypršení platnosti, otočte klíče před datem vypršení platnosti.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
