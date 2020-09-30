---
title: Použití zprostředkovatele ID (Preview) pro správu přihlašovacích údajů – Azure HDInsight
description: Přečtěte si o službě HDInsight ID Broker, která zjednodušuje ověřování pro clustery Apache Hadoop připojené k doméně.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 8f1e0a6aecc9702552a3dd66acc8dc7eb5bf1d85
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91529919"
---
# <a name="azure-hdinsight-id-broker-preview"></a>Azure HDInsight ID Broker (Preview)

Tento článek popisuje, jak nastavit a používat funkci HDInsight ID Broker (HIB) ve službě Azure HDInsight. Tuto funkci můžete použít k získání moderního ověřování OAuth pro Apache Ambari a při vynucení Multi-Factor Authentication (MFA), aniž byste museli používat starší hodnoty hash hesel v Azure Active Directory Domain Services (AAD-DS).

## <a name="overview"></a>Přehled

HIB zjednodušuje nastavení komplexního ověřování v následujících scénářích:

* Vaše organizace spoléhá na federaci ověřování uživatelů pro přístup k prostředkům cloudu. Předtím, aby bylo možné použít clustery HDInsight Balíček zabezpečení podniku (ESP), bylo nutné povolit synchronizaci hodnot hash hesel z místního prostředí do Azure Active Directory (Azure AD). Tento požadavek může být u některých organizací obtížné nebo nežádoucí.

* Vaše organizace chce vymáhat MFA pro přístup na základě webu nebo HTTP k Apache Ambari a dalším prostředkům clusteru.

HIB poskytuje infrastrukturu ověřování, která umožňuje přechod protokolu z OAuth (moderní) na Kerberos (starší verze), aniž by se musely synchronizovat hodnoty hash hesel pro AAD-DS. Tato infrastruktura se skládá z komponent spuštěných na virtuálním počítači s Windows serverem (s uzlem ID zprostředkovatele) společně s uzly brány clusteru.

Následující diagram znázorňuje moderní tok ověřování založený na protokolu OAuth pro všechny uživatele, včetně federovaných uživatelů po povolení zprostředkovatele ID:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Tok ověřování s zprostředkovatelem ID":::

V tomto diagramu musí klient (například prohlížeč nebo aplikace) nejdřív získat token OAuth a potom tokenu předložit bráně v požadavku HTTP. Pokud jste se už přihlásili k jiným službám Azure, jako je Azure Portal, můžete se ke svému clusteru HDInsight přihlásit pomocí jednotného přihlašování (SSO).

Stále se může jednat o mnoho starších verzí aplikací, které podporují jenom základní ověřování (tj. uživatelské jméno a heslo). V těchto scénářích můžete k připojení ke branám clusteru používat i základní ověřování HTTP. V tomto nastavení musíte zajistit připojení k síti z uzlů brány do koncového bodu federace (koncový bod AD FS), aby se zajistila přímá řádková kontrola z uzlů brány.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Tok ověřování s zprostředkovatelem ID":::

Následující tabulku použijte k určení nejlepší možnosti ověřování podle potřeb vaší organizace:

|Možnosti ověřování |Konfigurace HDInsight | Faktory, které je třeba zvážit |
|---|---|---|
| Plně OAuth | ESP + HIB | 1. nejbezpečnější možnost (MFA je podporována) 2.    Předání synchronizace hodnot hash není vyžadováno. 3.  Nemáte přístup SSH/kinit/keytab pro účty Prem, které nemají hodnotu hash hesla v AAD-DS. 4.   Pouze cloudové účty mohou i nadále SSH/kinit/keytab. 5. Webový přístup k Ambari prostřednictvím OAuth 6.  Pro podporu OAuth vyžaduje aktualizace starších aplikací (JDBC/ODBC atd.).|
| OAuth + základní ověřování | ESP + HIB | 1. webový přístup k Ambari prostřednictvím OAuth 2. Starší verze aplikací nadále používají základní ověřování. 3. Pro základní přístup k ověřování musí být vícefaktorové ověřování zakázané. 4. Předání synchronizace hodnot hash není vyžadováno. 5. Nemáte přístup SSH/kinit/keytab pro účty Prem, které nemají hodnotu hash hesla v AAD-DS. 6. Pouze cloudové účty mohou i nadále SSH/kinit. |
| Plně základní ověřování | ŠIFROVANÉ | 1. podobá se Prem nastavením. 2. Synchronizace hodnot hash hesel do AAD-DS je povinná. 3. Účty on-Prem mohou používat SSH/kinit nebo používat keytab. 4. Pokud je úložiště zálohování ADLS Gen2, musí být zakázané MFA. |


## <a name="enable-hdinsight-id-broker"></a>Povolit HDInsight ID Broker

Pokud chcete vytvořit cluster ESP s povoleným zprostředkovatelem ID, proveďte následující kroky:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Postupujte podle základních kroků vytváření pro cluster ESP. Další informace najdete v tématu [Vytvoření clusteru HDInsight s](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)protokolem ESP.
1. Vyberte **Povolit zprostředkovatele ID HDInsight**.

Funkce zprostředkovatel ID přidá do clusteru jeden další virtuální počítač. Tento virtuální počítač je uzlem služby Service Broker a zahrnuje součásti serveru pro podporu ověřování. Uzel zprostředkovatele ID je připojený k doméně Azure služba AD DS.

![Možnost povolení zprostředkovatele ID](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Použití šablon Azure Resource Manageru
Pokud přidáte novou roli `idbrokernode` s názvem s následujícími atributy do výpočetního profilu šablony, cluster se vytvoří s povoleným uzlem ID zprostředkovatele:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 1,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

## <a name="tool-integration"></a>Integrace nástrojů

Nástroje HDIsngith jsou aktualizované, aby nativně podporovaly OAuth. Důrazně doporučujeme používat tyto nástroje pro moderní přístup k clusterům na základě OAuth. [Modul plug-in HDInsight IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) se dá použít pro aplikace založené na jazyce Java, jako je třeba Scala. [Nástroje Spark & podregistr pro vs Code](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) lze použít pro úlohy PySpark a podregistr. Podporují dávkové i interaktivní úlohy.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Přístup SSH bez hodnoty hash hesla v Azure služba AD DS

|Možnosti SSH |Faktory, které je třeba zvážit |
|---|---|
| Místní účet virtuálních počítačů (např. sshuser) | 1. Tento účet jste zadali v době vytváření clusteru. 2.  Pro tento účet neexistuje authication protokolu Kerberos. |
| Účet pouze cloudu (např. alice@contoso.onmicrosoft.com ) | 1. hodnota hash hesla je k dispozici v AAD-DS 2. Ověřování protokolem Kerberos je možné prostřednictvím protokolu SSH Kerberos. |
| Účet on-Prem (např. alice@contoso.com ) | 1. ověřování pomocí protokolu Kerberos protokolu SSH je možné pouze v případě, že je v AAD-DS k dispozici hodnota hash hesla, jinak tento uživatel nemůže v clusteru protokol SSH |

Pro SSH na virtuální počítač připojený k doméně nebo pro spuštění příkazu musíte `kinit` zadat heslo. Ověřování protokolem SSH protokolu Kerberos vyžaduje, aby byla hodnota hash k dispozici v AAD-DS. Pokud chcete použít SSH jenom pro scénáře správy, můžete vytvořit jenom jeden účet jenom pro Cloud a použít ho ke clusteru SSH. Jiní uživatelé Prem můžou dál používat Ambari nebo nástroje HDInsight nebo základní ověřování HTTP, aniž by byl k dispozici hodnota hash hesla v AAD-DS.

Pokud vaše organizace nesynchronizuje hodnoty hash hesel do AAD-DS, doporučuje se v Azure AD vytvořit jenom jednoho cloudového uživatele a přiřadit ho jako správce clusteru při vytváření clusteru a používat ho pro účely správy, které zahrnují přístup k virtuálním počítačům přes SSH.

Pokud chcete řešit problémy s ověřováním, přečtěte si tuto [příručku](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues).

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-hib"></a>Klienti, kteří používají OAuth pro připojení k bráně HDInsight pomocí HIB

V instalačním programu HIB se vlastní aplikace a klienti, kteří se připojují k bráně, dají aktualizovat tak, aby nejdřív získaly požadovaný token OAuth. Pomocí kroků v tomto [dokumentu](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) můžete získat token s následujícími informacemi:

*   Identifikátor URI prostředku OAuth: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-F040-46cc-875f-831a1ef6a28a
*   Oprávnění: (název: cluster. v/v, ID: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Po získání tokenu OAuth ho můžete použít v autorizační hlavičce požadavku HTTP do brány clusteru (např. https:// <clustername> -int.azurehdinsight.NET). Příklad: příklad oblého příkazu na rozhraní Apache Livy API může vypadat takto:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

## <a name="next-steps"></a>Další kroky

* [Konfigurace clusteru HDInsight s Balíček zabezpečení podniku pomocí Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Synchronizace uživatelů Azure Active Directory do clusteru HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorování výkonu clusteru](../hdinsight-key-scenarios-to-monitor.md)
