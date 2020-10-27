---
title: Clustery Azure HDInsight se ztrátou šifrování disku Key Vault přístup
description: Postup řešení potíží a možná řešení pro problémy s přístupem k Key Vault při komunikaci s clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: bc8162f3a7ca8744a94aba039996275b5f13c727
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533406"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Scénář: clustery Azure HDInsight se ztrátou šifrování disku Key Vault přístup

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Výstraha centra Resource Health (RHC) se `The HDInsight cluster is unable to access the key for BYOK encryption at rest` zobrazuje pro clustery Bring Your Own Key (BYOK), ve kterých uzly clusteru ztratily přístup ke zákazníkům Key Vault (KV). Podobné výstrahy je také možné zobrazit v uživatelském rozhraní Apache Ambari.

## <a name="cause"></a>Příčina

Výstraha zajišťuje, že je KV přístupný z uzlů clusteru, a zajišťuje tak síťové připojení, stav KV a zásady přístupu pro spravovanou identitu přiřazenou uživateli. Tato výstraha je jenom upozornění na blížící se vypnutí zprostředkovatele při následném restartování uzlu. cluster bude dál fungovat, dokud se uzly nerestartují.

Přejděte na uživatelské rozhraní Apache Ambari, kde najdete další informace o upozornění ze **stavu šifrování disku Key Vault** . Tato výstraha bude obsahovat podrobnosti o příčině selhání ověřování.

## <a name="resolution"></a>Řešení

### <a name="kvaad-outage"></a>KV/výpadek AAD

Další podrobnosti najdete v [Azure Key Vault dostupnosti a redundanci](../../key-vault/general/disaster-recovery-guidance.md) a na stránce stavu Azure. https://status.azure.com/

### <a name="kv-accidental-deletion"></a>Nepředvídatelné odstranění KV

* Obnovte odstraněný klíč v KV na automatické obnovení. Další informace najdete v tématu [Obnovení odstraněné klíče](/rest/api/keyvault/recoverdeletedkey).
* Vyzkoušením týmu KV se můžete zotavit z neúmyslných odstranění.

### <a name="kv-access-policy-changed"></a>KV – zásady přístupu se změnily

Obnovte zásady přístupu pro spravovanou identitu přiřazenou uživatelem, která je přiřazená HDI clusteru pro přístup ke KV.

### <a name="key-permitted-operations"></a>Klíč – povolené operace

Pro každý klíč v KV můžete zvolit sadu povolených operací. Ujistěte se, že máte pro klíč BYOK povolené zabalení a rozbalení operací.

### <a name="expired-key"></a>Klíč vypršení platnosti

Pokud uplynula platnost a klíč se neotočí, obnovte klíč ze zálohy HSM nebo kontaktujte tým KV a vymažte datum vypršení platnosti.

### <a name="kv-firewall-blocking-access"></a>KV brány firewall blokující přístup

Opravte nastavení brány firewall KV, aby uzly clusteru BYOK měly přístup k KV.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>Pravidla NSG pro blokování přístupu k virtuální síti

Ověřte pravidla NSG přidružená k virtuální síti připojené ke clusteru.

## <a name="mitigation-and-prevention-steps"></a>Postup zmírnění a prevence

### <a name="kv-accidental-deletion"></a>Nepředvídatelné odstranění KV

* Nakonfigurujte Key Vault se [sadou zámků prostředků](../../azure-resource-manager/management/lock-resources.md).
* Zálohujte klíče do svého modulu hardwarového zabezpečení.

### <a name="key-deletion"></a>Odstranění klíče

Cluster by měl být před odstraněním klíče odstraněn.

### <a name="kv-access-policy-changed"></a>KV – zásady přístupu se změnily

Pravidelné audit a zásady přístupu k testování.

### <a name="expired-key"></a>Klíč vypršení platnosti

* Zálohujte klíče do modulu HARDWAROVÉho zabezpečení.
* Použijte klíč bez nastavené platnosti.
* Pokud je potřeba nastavit vypršení platnosti, otočte klíče před datem vypršení platnosti.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).