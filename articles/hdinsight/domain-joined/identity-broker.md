---
title: Použití zprostředkovatele ID (Preview) pro správu přihlašovacích údajů – Azure HDInsight
description: Přečtěte si o službě HDInsight ID Broker, která zjednodušuje ověřování pro clustery Apache Hadoop připojené k doméně.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/12/2019
ms.openlocfilehash: 3b2807ccd6d83511dd0c9a32a177ea9fe2c4b642
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662101"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Použití zprostředkovatele ID (Preview) pro správu přihlašovacích údajů

Tento článek popisuje, jak nastavit a použít funkci zprostředkovatele ID ve službě Azure HDInsight. Pomocí této funkce se můžete přihlásit k Apache Ambari prostřednictvím Azure Multi-Factor Authentication a získat požadované lístky protokolu Kerberos bez nutnosti použití hodnot hash hesla v Azure Active Directory Domain Services (Azure služba AD DS).

## <a name="overview"></a>Přehled

Zprostředkovatel ID zjednodušuje nastavení komplexního ověřování v následujících scénářích:

* Vaše organizace spoléhá na federaci ověřování uživatelů pro přístup k prostředkům cloudu. Předtím, aby bylo možné použít clustery HDInsight Balíček zabezpečení podniku (ESP), bylo nutné povolit synchronizaci hodnot hash hesel z místního prostředí a Azure Active Directory. Tento požadavek může být u některých organizací obtížné nebo nežádoucí.

* Vytváříte řešení, která používají technologie, které využívají různé mechanismy ověřování. Například Apache Hadoop a Apache Ranger spoléhají na protokol Kerberos, zatímco Azure Data Lake Storage spoléhá na OAuth.

Zprostředkovatel ID poskytuje jednotnou infrastrukturu ověřování a odebírá požadavek na synchronizaci hodnot hash hesel do Azure služba AD DS. Zprostředkovatel ID se skládá z komponent spuštěných na virtuálním počítači s Windows serverem (u uzlu služby ID) společně s uzly brány clusteru. 

Následující diagram znázorňuje tok ověřování pro všechny uživatele, včetně federovaných uživatelů po povolení zprostředkovatele ID:

![Tok ověřování s zprostředkovatelem ID](./media/identity-broker/identity-broker-architecture.png)

Zprostředkovatel ID umožňuje přihlásit se k clusterům ESP pomocí Multi-Factor Authentication bez zadání hesla. Pokud jste se už přihlásili k jiným službám Azure, jako je Azure Portal, můžete se ke svému clusteru HDInsight přihlásit pomocí jednotného přihlašování (SSO).

## <a name="enable-hdinsight-id-broker"></a>Povolit HDInsight ID Broker

Pokud chcete vytvořit cluster ESP s povoleným zprostředkovatelem ID, proveďte následující kroky:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
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

[Modul plug-in HDInsight IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) se aktualizuje tak, aby podporoval OAuth. Tento modul plug-in můžete použít pro připojení ke clusteru a odeslání úloh.

Pomocí [nástrojů pro podregistr Spark &](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) lze také využít vs Code k využití poznámkových bloků a odesílání úloh.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Přístup SSH bez hodnoty hash hesla v Azure služba AD DS

Jakmile je zprostředkovatel ID povolený, budete pořád potřebovat hodnotu hash hesla uloženou v Azure služba AD DS pro scénáře SSH s doménovým účtem. Pro SSH na virtuální počítač připojený k doméně nebo pro spuštění příkazu musíte `kinit` zadat heslo. 

Ověřování SSH vyžaduje, aby byla hodnota hash k dispozici v Azure služba AD DS. Pokud chcete použít SSH jenom pro scénáře správy, můžete vytvořit jenom jeden účet jenom pro Cloud a použít ho ke clusteru SSH. Jiní uživatelé stále můžou používat Ambari nebo nástroje HDInsight (například modul plug-in IntelliJ), aniž by byl k dispozici hodnota hash hesla v Azure služba AD DS.

Pokud chcete řešit problémy s ověřováním, přečtěte si tuto [příručku](https://docs.microsoft.com/azure/hdinsight/domain-joined/domain-joined-authentication-issues).

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>Klienti, kteří používají OAuth pro připojení k bráně HDInsight s nastavením služby ID Broker

V instalačním programu služby ID se můžou vlastní aplikace a klienti, kteří se připojují k bráně, aktualizovat tak, aby nejdřív získaly požadovaný token OAuth. Pomocí kroků v tomto [dokumentu](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) můžete získat token s následujícími informacemi:

*   Identifikátor URI prostředku OAuth: `https://hib.azurehdinsight.net` 
* AppId: 7865c1d2-F040-46cc-875f-831a1ef6a28a
*   Oprávnění: (název: cluster. v/v, ID: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Po načítá tokenu OAuth ho můžete použít v autorizační hlavičce pro požadavek HTTP na bránu clusteru (např. <clustername> -int.azurehdinsight.NET). Například vzorový příkaz složeného příkazu na rozhraní API Livy může vypadat takto:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

## <a name="next-steps"></a>Další kroky

* [Konfigurace clusteru HDInsight s Balíček zabezpečení podniku pomocí Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Synchronizace uživatelů Azure Active Directory do clusteru HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorování výkonu clusteru](../hdinsight-key-scenarios-to-monitor.md)
