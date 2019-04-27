---
title: Přineste si vlastní klíč pro Apache Kafka v Azure HDInsight (Preview)
description: Tento článek popisuje, jak použít vlastní klíč ze služby Azure Key Vault k šifrování dat uložených v Apache Kafka v Azure HDInsight.
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: ce9df58e9640cab2e6ba50fce772f1e30739dc5a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115444"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Přineste si vlastní klíč pro Apache Kafka v Azure HDInsight

Azure HDInsight zahrnuje podporu přineste si vlastní klíč (BYOK) pro platformu Apache Kafka. Tato funkce umožňuje sami vlastnili a spravovali klíče používané k šifrování dat v klidovém stavu. 

Všechny spravované disky v HDInsight jsou chráněny s Azure Storage Service Encryption (SSE). Ve výchozím nastavení zašifrovaná data na těchto discích, pomocí klíčů spravovaných microsoftem. Pokud povolíte BYOK, zadejte šifrovací klíč pro službu HDInsight využívat a spravovat ho pomocí služby Azure Key Vault. 

BYOK šifrování je jednoduchý proces, při vytváření clusteru bez dalších poplatků. Všechno, co je potřeba je zaregistrovat HDInsight jako spravovanou identitu pomocí Azure Key Vault a přidat šifrovacího klíče při vytváření clusteru.

Všechny zprávy do clusteru Kafka (včetně replik spravuje Kafka) jsou šifrována pomocí symetrický datový šifrovací klíč (DEK). Klíč DEK je chráněn pomocí klíče šifrování klíčů (KEK) z trezoru klíčů. Procesy šifrování a dešifrování dat provádí zcela služba Azure HDInsight. 

Na webu Azure portal nebo rozhraní příkazového řádku Azure můžete bezpečně obměna klíčů v trezoru klíčů. Když otočí klíče clusteru HDInsight Kafka spustí během několika minut pomocí nového klíče. Povolení ochrany klíčů funkcí "Obnovitelné odstranění" pro ochranu před ransomwarem scénáře a nechtěnému odstranění. Trezory klíčů bez této funkce ochrany nejsou podporovány.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-byok"></a>Začínáme s BYOK
K vytvoření clusteru Kafka povolená BYOK, provedeme následující kroky:
1. Vytvoření spravovaných identit pro prostředky Azure
2. Nastavení služby Azure Key Vault a klíči
3. Vytvoření clusteru HDInsight Kafka s BYOK povoleno
4. Otáčení šifrovacího klíče

## <a name="create-managed-identities-for-azure-resources"></a>Vytvoření spravovaných identit pro prostředky Azure

   K ověření do služby Key Vault, vytvořte pomocí uživatelsky přiřazené identity spravované [webu Azure portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [prostředí Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manageru](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md), nebo [ Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Další informace o spravovaných pracovních identit v Azure HDInsight, naleznete v tématu [spravovaných identit v Azure HDInsight](../hdinsight-managed-identities.md). Azure Active directory je požadovaná pro spravované identity a funkce BYOK na Kafka, Enterprise Security Package (ESP) nejsou povinné. Nezapomeňte uložit spravovaná identita ID prostředku pro při přidání do zásad přístupu trezoru klíčů.

   ![Vytvoření uživatelsky přiřazené identity spravované v portálu Azure portal](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>Nastavení služby Key Vault a klíčů

   HDInsight podporuje jenom služby Azure Key Vault. Pokud máte trezor klíčů, můžete klíče importovat do Azure Key Vault. Mějte na paměti, že klíče musí mít "Obnovitelné odstranění". Funkce "Obnovitelné odstranění" je k dispozici prostřednictvím rozhraní REST, .NET /C#, rozhraní prostředí PowerShell a rozhraní příkazového řádku Azure.

   1. Chcete-li vytvořit nový trezor klíčů, postupujte [Azure Key Vault](../../key-vault/key-vault-overview.md) rychlý start. Další informace o importu stávajících klíčů [informace o klíčích, tajných kódů a certifikátů](../../key-vault/about-keys-secrets-and-certificates.md).

   2. Povolte "obnovitelného odstranění" v trezoru klíčů pomocí [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) příkazu rozhraní příkazového řádku.
        "" Aktualizace trezoru klíčů azure CLI az--name <Key Vault Name> – povolit obnovitelné odstranění
        ```

   3. Create keys

        a. To create a new key, select **Generate/Import** from the **Keys** menu under **Settings**.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png)

        b. Set **Options** to **Generate** and give the key a name.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-a-key.png)

        c. Select the key you created from the list of keys.

        ![Azure Key Vault key list](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. When you use your own key for Kafka cluster encryption, you need to provide the key URI. Copy the **Key identifier** and save it somewhere until you're ready to create your cluster.

        ![Copy key identifier](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
    4. Add managed identity to the key vault access policy.

        a. Create a new Azure Key Vault access policy.

        ![Create new Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. Under **Select Principal**, choose the user-assigned managed identity you created.

        ![Set Select Principal for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. Set **Key Permissions** to **Get**, **Unwrap Key**, and **Wrap Key**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

        d. Set **Secret Permissions** to **Get**, **Set**, and **Delete**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

        e. Click on **Save**. 

        ![Save Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## Create HDInsight cluster

   You're now ready to create a new HDInsight cluster. BYOK can only be applied to new clusters during cluster creation. Encryption can't be removed from BYOK clusters, and BYOK can't be added to existing clusters.

   ![Kafka disk encryption in Azure portal](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   During cluster creation, provide the full key URL, including the key version. For example, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. You also need to assign the managed identity to the cluster and provide the key URI.

## Rotating the Encryption key
   There might be scenarios where you might want to change the encryption keys used by the Kafka cluster after it has been created. This can be easily via the portal. For this operation, the cluster must have access to both the current key and the intended new key, otherwise the rotate key operation will fail.

   To rotate the key, you must have the full url of the new key (See Step 3 of [Setup the Key Vault and Keys](#setup-the-key-vault-and-keys)). Once you have that, go to the Kafka cluster properties section in the portal and click on **Change Key** under **Disk Encryption Key URL**. Enter in the new key url and submit to rotate the key.

   ![Kafka rotate disk encryption key](./media/apache-kafka-byok/kafka-change-key.png)

## FAQ for BYOK to Apache Kafka

**How does the Kafka cluster access my key vault?**

   Associate a managed identity with the HDInsight Kafka cluster during cluster creation. This managed identity can be created before or during cluster creation. You also need to grant the managed identity access to the key vault where the key is stored.

**Is this feature available for all Kafka clusters on HDInsight?**

   BYOK encryption is only possible for Kafka 1.1 and above clusters.

**Can I have different keys for different topics/partitions?**

   No, all managed disks in the cluster are encrypted by the same key.

**What happens if the cluster loses access to the key vault or the key?**
   If the cluster loses access to the key, warnings will be shown in the Ambari portal. In this state, the **Change Key** operation will fail. Once key access is restored, ambari warnings will go away and operations such as key rotation can be successfully performed.

   ![Kafka key access ambari alert](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**How can I recover the cluster if the keys are deleted?**

   Since only “Soft Delete” enabled keys are supported, if the keys are recovered in the key vault, the cluster should regain access to the keys. To recover an Azure Key Vault key, see [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) or [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Can I have producer/consumer applications working with a BYOK cluster and a non-BYOK cluster simultaneously?**

   Yes. The use of BYOK is transparent to producer/consumer applications. Encryption happens at the OS layer. No changes need to be made to existing producer/consumer Kafka applications.

**Are OS disks/Resource disks also encrypted?**

   No. OS disks and Resource disks are not encrypted.

**If a cluster is scaled up, will the new brokers support BYOK seamlessly?**

   Yes. The cluster needs access to the key in the key vault during scale up. The same key is used to encrypt all managed disks in the cluster.

**Is BYOK available in my location?**

   Kafka BYOK is available in all public clouds.

## Next steps

* For more information about Azure Key Vault, see [What is Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* To get started with Azure Key Vault, see [Getting Started with Azure Key Vault](../../key-vault/key-vault-overview.md).
