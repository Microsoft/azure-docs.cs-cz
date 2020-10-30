---
title: Použití zprostředkovatele ID (Preview) pro správu přihlašovacích údajů – Azure HDInsight
description: Přečtěte si o službě Azure HDInsight ID Broker, která zjednodušuje ověřování pro clustery Apache Hadoop připojené k doméně.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 6617c778c0b79a55058eafb40fd9b49b627819ea
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043266"
---
# <a name="azure-hdinsight-id-broker-preview"></a>Azure HDInsight ID Broker (Preview)

Tento článek popisuje, jak nastavit a používat funkci služby Azure HDInsight ID Broker. Tuto funkci můžete použít k získání moderního ověřování OAuth pro Apache Ambari a při vynucení vícefaktorového ověřování, aniž byste museli používat starší hodnoty hash hesel v Azure Active Directory Domain Services (Azure služba AD DS).

## <a name="overview"></a>Přehled

Zprostředkovatel ID HDInsight zjednodušuje nastavení komplexního ověřování v následujících scénářích:

* Vaše organizace spoléhá na federaci ověřování uživatelů pro přístup k prostředkům cloudu. Předtím, abyste mohli používat clustery HDInsight Balíček zabezpečení podniku, museli byste povolit synchronizaci hodnot hash hesel z místního prostředí do Azure Active Directory (Azure AD). Tento požadavek může být u některých organizací obtížné nebo nežádoucí.
* Vaše organizace chce vymáhat vícefaktorové ověřování pro webový přístup nebo pro přístup na základě protokolu HTTP k Apache Ambari a dalším prostředkům clusteru.

HDInsight ID Broker poskytuje infrastrukturu ověřování, která umožňuje přechod protokolu z OAuth (moderní) na Kerberos (starší verze), aniž by se musely synchronizovat hodnoty hash hesel do Azure služba AD DS. Tato infrastruktura se skládá z komponent spuštěných na virtuálním počítači s Windows serverem, který má povolený uzel služby HDInsight ID, společně s uzly brány clusteru.

Pomocí následující tabulky můžete určit nejlepší možnost ověřování podle potřeb vaší organizace.

|Možnosti ověřování |Konfigurace HDInsight | Faktory, které je třeba zvážit |
|---|---|---|
| Plně OAuth | Zprostředkovatel ID Balíček zabezpečení podniku + HDInsight | Nejbezpečnější možnost. (Podpora vícefaktorového ověřování je podporovaná.) Předání synchronizace hodnot hash *není vyžadováno.* Neexistuje přístup přes SSH/kinit/keytab pro místní účty, který nemá hodnotu hash hesla v Azure služba AD DS. Pouze cloudové účty mohou i nadále SSH/kinit/keytab. Webový přístup k Ambari prostřednictvím protokolu OAuth. Pro podporu OAuth vyžaduje aktualizace starších aplikací (například JDBC/ODBC).|
| OAuth + základní ověřování | Zprostředkovatel ID Balíček zabezpečení podniku + HDInsight | Webový přístup k Ambari prostřednictvím protokolu OAuth. Starší verze aplikací nadále používají základní ověřování. Pro základní přístup k ověřování musí být zakázáno vícefaktorového ověřování. Předání synchronizace hodnot hash *není vyžadováno.* Neexistuje přístup přes SSH/kinit/keytab pro místní účty, který nemá hodnotu hash hesla v Azure služba AD DS. Účty jenom pro Cloud můžou i nadále SSH/kinit. |
| Plně základní ověřování | Balíček zabezpečení podniku | Nejčastěji se podobá místním nastavením. Synchronizace hodnoty hash hesla na Azure služba AD DS je povinná. Místní účty můžou používat SSH/kinit nebo keytab. Pokud je Azure Data Lake Storage Gen2 zálohování úložiště, musí být zakázané vícefaktorové ověřování. |

Následující diagram znázorňuje moderní tok ověřování založený na protokolu OAuth pro všechny uživatele, včetně federovaných uživatelů po povolení služby HDInsight ID Broker:

:::image type="content" source="media/identity-broker/identity-broker-architecture.png" alt-text="Diagram znázorňující tok ověřování pomocí služby HDInsight ID Broker.":::

V tomto diagramu musí klient (tj. prohlížeč nebo aplikace) nejdřív získat token OAuth. Pak prezentuje token bráně v požadavku HTTP. Pokud jste se už přihlásili k jiným službám Azure, jako je Azure Portal, můžete se k vašemu clusteru HDInsight přihlásit pomocí jednotného přihlašování.

Stále se může jednat o mnoho starších verzí aplikací, které podporují jenom základní ověřování (tj. uživatelské jméno a heslo). V těchto scénářích můžete k připojení ke branám clusteru používat i základní ověřování HTTP. V takovém případě je nutné zajistit připojení k síti z uzlů brány do koncového bodu Active Directory Federation Services (AD FS) (AD FS), aby se zajistila přímá čára pozorování z uzlů brány.

Následující diagram znázorňuje základní tok ověřování pro federované uživatele. Nejdřív se brána pokusí dokončit ověřování pomocí [ROPC toku](../../active-directory/develop/v2-oauth-ropc.md). V případě, že se do služby Azure AD nesynchronizují žádné hodnoty hash hesel, vrátí se ke zjištění AD FSho koncového bodu a dokončí ověřování tím, že se přistoupí ke koncovému bodu AD FS.

:::image type="content" source="media/identity-broker/basic-authentication.png" alt-text="Diagram znázorňující tok ověřování pomocí služby HDInsight ID Broker.":::


## <a name="enable-hdinsight-id-broker"></a>Povolit HDInsight ID Broker

Vytvoření clusteru Balíček zabezpečení podniku s povoleným zprostředkovatelem HDInsight ID:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Postupujte podle základních kroků pro vytvoření clusteru Balíček zabezpečení podniku. Další informace najdete v tématu [Vytvoření clusteru HDInsight s balíček zabezpečení podniku](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Vyberte **Povolit zprostředkovatele ID HDInsight** .

Funkce služby HDInsight ID Broker přidá do clusteru jeden virtuální počítač navíc. Tento virtuální počítač je uzlem zprostředkovatele služby HDInsight ID a zahrnuje součásti serveru pro podporu ověřování. Uzel služby HDInsight ID je připojený k doméně Azure služba AD DS.

![Diagram, který ukazuje možnost povolit zprostředkovatele ID HDInsight.](./media/identity-broker/identity-broker-enable.png)

### <a name="use-azure-resource-manager-templates"></a>Použití šablon Azure Resource Manageru

Pokud přidáte novou roli `idbrokernode` s názvem s následujícími atributy do výpočetního profilu šablony, cluster se vytvoří s povoleným uzlem zprostředkovatel ID HDInsight:

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
            "targetInstanceCount": 2,
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

Pokud chcete zobrazit úplnou ukázku šablony ARM, podívejte se prosím na šablonu publikovanou [tady](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).


## <a name="tool-integration"></a>Integrace nástrojů

Nástroje HDInsight jsou aktualizované tak, aby nativně podporovaly OAuth. Tyto nástroje použijte pro moderní přístup založený na protokolu OAuth k clusterům. [Modul plug-in HDInsight IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#integrate-with-hdinsight-identity-broker-hib) se dá použít pro aplikace založené na jazyce Java, jako je třeba Scala. [Nástroje Spark a podregistr pro Visual Studio Code](../hdinsight-for-vscode.md) lze použít pro úlohy PySpark a podregistr. Nástroje podporují dávkové i interaktivní úlohy.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Přístup SSH bez hodnoty hash hesla v Azure služba AD DS

|Možnosti SSH |Faktory, které je třeba zvážit |
|---|---|
| Místní účet VM (například sshuser) | Tento účet jste zadali v době vytváření clusteru. Pro tento účet není k dispozici žádné ověřování pomocí protokolu Kerberos. |
| Pouze cloudový účet (například alice@contoso.onmicrosoft.com ) | Hodnota hash hesla je dostupná ve službě Azure služba AD DS. Ověřování protokolem Kerberos je možné prostřednictvím protokolu SSH Kerberos. |
| Místní účet (například alice@contoso.com ) | Ověřování protokolem Kerberos protokolu SSH je možné pouze v případě, že je v Azure služba AD DS k dispozici hodnota hash hesla. V opačném případě nemůže tento uživatel do clusteru protokol SSH. |

Pokud chcete SSH na virtuální počítač připojený k doméně nebo spustit `kinit` příkaz, musíte zadat heslo. Ověřování protokolem SSH protokolu Kerberos vyžaduje, aby byla hodnota hash k dispozici v Azure služba AD DS. Pokud chcete použít SSH jenom pro scénáře správy, můžete vytvořit jenom jeden účet jenom pro Cloud a použít ho ke clusteru SSH. Jiní místní uživatelé můžou dál používat Ambari nebo nástroje HDInsight nebo základní ověřování HTTP, aniž by byl k dispozici hodnota hash hesla v Azure služba AD DS.

Pokud vaše organizace nesynchronizuje hodnoty hash hesel do Azure služba AD DS, doporučujeme v Azure AD vytvořit jednoho pouze cloudového uživatele. Pak ji přiřaďte jako správce clusteru při vytváření clusteru a použijte ji pro účely správy. Můžete ji použít k získání kořenového přístupu k virtuálním počítačům přes SSH.

Pokud chcete řešit problémy s ověřováním, přečtěte si [tuto příručku](./domain-joined-authentication-issues.md).

## <a name="clients-using-oauth-to-connect-to-an-hdinsight-gateway-with-hdinsight-id-broker"></a>Klienti, kteří používají OAuth pro připojení k bráně HDInsight pomocí služby HDInsight ID Broker

V instalačním programu HDInsight ID Broker se můžou vlastní aplikace a klienti, kteří se připojují k bráně, aktualizovat tak, aby nejdřív získaly požadovaný token OAuth. Použijte postup v [tomto dokumentu](../../storage/common/storage-auth-aad-app.md) k získání tokenu s následujícími informacemi:

*   Identifikátor URI prostředku OAuth: `https://hib.azurehdinsight.net` 
*   AppId: 7865c1d2-F040-46cc-875f-831a1ef6a28a
*   Oprávnění: (název: cluster. v/v, ID: 8f89faa0-ffef-4007-974d-4989b39ad77d)

Po získání tokenu OAuth ho použijte v autorizační hlavičce požadavku HTTP do brány clusteru (například https:// <clustername> -int.azurehdinsight.NET). Vzorový příkaz složené na rozhraní Apache Livy API může vypadat jako v tomto příkladu:
    
```bash
curl -k -v -H "Authorization: Bearer Access_TOKEN" -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://<clustername>-int.azurehdinsight.net/livy/batches" -H "X-Requested-By:<username@domain.com>"
``` 

Pro použití Beeline a Livy můžete také použít kódy ukázek, které [tady](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/HIB/HIBSamples) najdete, a nastavit tak klienta tak, aby používal OAuth a připojil se ke clusteru.

## <a name="next-steps"></a>Další kroky

* [Konfigurace clusteru HDInsight s Balíček zabezpečení podniku pomocí Azure Active Directory Domain Services](apache-domain-joined-configure-using-azure-adds.md)
* [Synchronizace uživatelů Azure Active Directory do clusteru HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Monitorování výkonu clusteru](../hdinsight-key-scenarios-to-monitor.md)
