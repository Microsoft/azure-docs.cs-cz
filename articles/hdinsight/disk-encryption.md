---
title: Klíčové šifrování disku spravované zákazníkem pro Azure HDInsight
description: Tento článek popisuje, jak použít vlastní šifrovací klíč z Azure Key Vault k šifrování dat uložených na spravovaných discích v clusterech Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2019
ms.openlocfilehash: 2c015db828bcbfa8b26f519b3a4707b5ec69b8f3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982496"
---
# <a name="customer-managed-key-disk-encryption"></a>Klíčové šifrování disku spravované zákazníkem

Azure HDInsight podporuje klíče spravované zákazníky, označované taky jako Bring Your Own Key (BYOK) šifrování dat na spravovaných discích a discích prostředků připojených k virtuálním počítačům clusteru HDInsight. Tato funkce umožňuje použít Azure Key Vault ke správě šifrovacích klíčů, které chrání neaktivní data v clusterech HDInsight. Clustery můžou mít jeden nebo více připojených účtů Azure Storage, kde by mohly být šifrovací klíče taky spravované Microsoftem nebo spravované zákazníkem, ale šifrovací služba se liší.

Tento dokument neřeší data uložená ve vašem účtu Azure Storage. Další informace o pomocném mechanismu služby najdete v tématu [Azure Storage Encryption for data v klidovém umístění](../storage/common/storage-service-encryption.md).

Všechny spravované disky v HDInsight jsou chráněné pomocí šifrování služby Azure Storage (SSE). Ve výchozím nastavení se data na těchto discích šifrují pomocí klíčů spravovaných Microsoftem. Pokud povolíte klíče spravované zákazníkem pro HDInsight, poskytnete šifrovací klíče pro HDInsight k použití a správě těchto klíčů pomocí Azure Key Vault.

Šifrování klíče spravovaného zákazníkem je proces, který se během vytváření clusteru zpracovává bez dalších poplatků. Vše, co potřebujete udělat, je zaregistrovat HDInsight jako spravovanou identitu s Azure Key Vault a přidat šifrovací klíč při vytváření clusteru.

Disk prostředků i spravované disky v každém uzlu clusteru jsou šifrované pomocí symetrického šifrovacího klíče (klíč DEK). KLÍČ DEK je chráněný pomocí klíčového šifrovacího klíče (KEK) z vašeho trezoru klíčů. Procesy šifrování a dešifrování jsou zpracovávány výhradně službou Azure HDInsight.

K bezpečnému střídání klíčů v trezoru klíčů můžete použít Azure Portal nebo Azure CLI. Když se klíč otáčí, cluster HDInsight začne používat nový klíč během několika minut. Povolte funkce ochrany před náhodným odstraněním, které chcete chránit před ransomwaremmi scénáři a náhodným odstraněním. Trezory klíčů bez této funkce ochrany se nepodporují.

## <a name="get-started-with-customer-managed-keys"></a>Začínáme s klíčem spravovaným zákazníkem

Pokud chcete vytvořit cluster HDInsight s povoleným klíčem, Projděte si následující kroky:

1. Vytvoření spravovaných identit pro prostředky Azure
2. Nastavení Azure Key Vault a klíčů
3. Vytvoření clusteru HDInsight s povoleným klíčem spravovaným zákazníkem
4. Otočení šifrovacího klíče

## <a name="create-managed-identities-for-azure-resources"></a>Vytvoření spravovaných identit pro prostředky Azure

Pokud chcete ověřit Key Vault, vytvořte uživatelem přiřazenou identitu pomocí [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)nebo [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Další informace o tom, jak spravované identity fungují ve službě Azure HDInsight, najdete v tématu [spravované identity ve službě Azure HDInsight](hdinsight-managed-identities.md). Nezapomeňte uložit ID spravovaného prostředku identity, pokud ho přidáte do zásad přístupu Key Vault.

## <a name="set-up-the-key-vault-and-keys"></a>Nastavení Key Vault a klíčů

HDInsight podporuje jenom Azure Key Vault. Pokud máte vlastní Trezor klíčů, můžete klíče importovat do Azure Key Vault. Mějte na paměti, že klíče musí obsahovat "obnovitelné odstranění". Funkce "obnovitelné odstranění" je k dispozici prostřednictvím rozhraní REST, .NETC#/, PowerShellu a rozhraní příkazového řádku Azure CLI.

1. Pokud chcete vytvořit nový trezor klíčů, postupujte podle pokynů pro rychlý Start [Azure Key Vault](../key-vault/key-vault-overview.md) . Další informace o importu existujících klíčů najdete v [informacích o klíčích, tajných klíčích a certifikátech](../key-vault/about-keys-secrets-and-certificates.md).

1. V trezoru klíčů Povolte "obnovitelné odstranění" pomocí příkazu [AZ Key trezor Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) CLI.

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. Vytvořte klíče.

    a. Pokud chcete vytvořit nový klíč, vyberte v nabídce **klíče** v části **Nastavení**možnost **Generovat/importovat** .

    ![Vygenerovat nový klíč v Azure Key Vault](./media/disk-encryption/create-new-key.png "Vygenerovat nový klíč v Azure Key Vault")

    b. Nastavte **Možnosti** , které se mají **vygenerovat** , a zadejte název klíče.

    ![vygeneruje název klíče.](./media/disk-encryption/create-key.png "Vygenerovat název klíče")

    c. Vyberte klíč, který jste vytvořili ze seznamu klíčů.

    ![seznam klíčů trezoru klíčů](./media/disk-encryption/key-vault-key-list.png)

    d. Když použijete vlastní klíč pro šifrování clusteru HDInsight, musíte zadat identifikátor URI klíče. Zkopírujte **identifikátor klíče** a uložte ho někam do chvíle, kdy jste připraveni vytvořit cluster.

    ![získat identifikátor klíče](./media/disk-encryption/get-key-identifier.png)

1. Přidejte spravovanou identitu do zásad přístupu trezoru klíčů.

    a. Vytvořte nové zásady přístupu Azure Key Vault.

    ![Vytvořit nové zásady přístupu Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy.png)

    b. V části **Vybrat objekt zabezpečení**vyberte spravovanou identitu přiřazenou uživatelem, kterou jste vytvořili.

    ![Nastavení výběru objektu zabezpečení pro zásady Azure Key Vaultho přístupu](./media/disk-encryption/add-key-vault-access-policy-select-principal.png)

    c. Nastavte **klíčová oprávnění** pro **získání**, **rozbalení klíče**a **zabalení klíče**.

    ![Nastavení oprávnění klíče pro Azure Key Vault přístup policy1](./media/disk-encryption/add-key-vault-access-policy-keys.png "Nastavení oprávnění klíče pro Azure Key Vault přístup policy1")

    d. Nastavte **tajná oprávnění** pro **získání**, **Nastavení**a **odstranění**.

    ![Nastavení oprávnění klíče pro Azure Key Vault přístup policy2](./media/disk-encryption/add-key-vault-access-policy-secrets.png "Nastavení oprávnění klíče pro Azure Key Vault přístup policy2")

    e. Vyberte **Uložit**.

    ![Uložit zásady přístupu Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Vytvoření clusteru s použitím šifrování klíčového disku spravovaného zákazníkem

Nyní jste připraveni vytvořit nový cluster HDInsight. Klíč spravovaný zákazníkem se dá použít jenom pro nové clustery během vytváření clusteru. Šifrování nejde odebrat z klíčových clusterů spravovaných zákazníkem a klíč spravovaný zákazníkem se nedá přidat k existujícím clusterům.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Během vytváření clusteru zadejte úplnou adresu URL klíče, včetně verze klíče. Například, `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Musíte také přiřadit spravovanou identitu ke clusteru a zadat identifikátor URI klíče.

### <a name="using-azure-cli"></a>Použití Azure CLI

Následující příklad ukazuje, jak pomocí rozhraní příkazového řádku Azure vytvořit nový cluster Apache Spark s povoleným šifrováním disku. Další informace najdete v tématu [Azure CLI AZ HDInsight Create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) Document.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

## <a name="rotating-the-encryption-key"></a>Otočení šifrovacího klíče

Můžou nastat situace, kdy budete možná chtít změnit šifrovací klíče používané clusterem HDInsight po jeho vytvoření. To může být snadné prostřednictvím portálu. Pro tuto operaci musí mít cluster přístup k aktuálnímu klíči i k zamýšlenému novému klíči. v opačném případě se operace otočení klíče nezdaří.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Chcete-li otočit klíč, je nutné mít úplnou adresu URL nového klíče (viz krok 3 [nastavení Key Vault a klíčů](#set-up-the-key-vault-and-keys)). Až to uděláte, přejděte do části vlastnosti clusteru HDInsight na portálu a klikněte na **změnit klíč** pod **adresou URL klíče pro šifrování disku**. Zadejte novou adresu URL klíče a odešlete pro otočení klíče.

![otočit šifrovací klíč disku](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Použití Azure CLI

Následující příklad ukazuje, jak otočit šifrovací klíč disku pro existující cluster HDInsight. Další podrobnosti najdete v tématu [Azure CLI AZ HDInsight otočit-Disk-Encryption-Key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key) .

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Nejčastější dotazy týkající se šifrování klíčů spravovaných zákazníkem

**Jak cluster HDInsight přistupuje k trezoru klíčů?**

HDInsight přistupuje k vaší instanci Azure Key Vault pomocí spravované identity, kterou přidružíte ke clusteru HDInsight. Tato spravovaná identita se dá vytvořit před nebo během vytváření clusteru. Musíte taky udělit přístup spravované identity k trezoru klíčů, kde je klíč uložený.

**Je tato funkce dostupná pro všechny clustery v prostředí HDInsight?**

Šifrování klíče spravovaného zákazníkem je k dispozici pro všechny typy clusterů kromě Spark 2,1 a 2,2.

**Můžu k šifrování různých disků nebo složek použít více klíčů?**

Ne, všechny spravované disky a disky prostředků jsou šifrovány stejným klíčem.

**Co se stane, když cluster ztratí přístup k trezoru klíčů nebo klíči?**

Pokud cluster ztratí přístup k tomuto klíči, zobrazí se na portálu Apache Ambari upozornění. V tomto stavu se operace **změny klíče** nezdaří. Po obnovení přístupu ke klíčům zmizí Ambari upozornění a operace, jako je například střídání klíčů, je možné úspěšně provést.

![Výstraha Ambari přístupu k klíčům](./media/disk-encryption/ambari-alert.png)

**Jak mohu obnovit cluster, pokud jsou klíče odstraněny?**

Vzhledem k tomu, že se podporují jenom klíče s povoleným obnovitelném odstraněním, měl by cluster znovu získat přístup k klíčům, pokud se klíče obnoví v trezoru klíčů. Pokud chcete obnovit Azure Key Vault klíč, přečtěte si téma [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) nebo [AZ-klíčů trezor-Key-Recovery](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Které typy disků jsou zašifrované? Šifrují se i disky s operačním systémem nebo disky prostředků?**

Disky prostředků a data/spravované disky jsou zašifrované. Disky s operačním systémem nejsou šifrované.

**Pokud je cluster škálovatelný, budou nové uzly bezproblémově podporovat klíče spravované zákazníky?**

Ano. Cluster potřebuje během horizontálního navýšení kapacity přístup k klíči v trezoru klíčů. Stejný klíč se používá k šifrování spravovaných disků i disků prostředků v clusteru.

**Jsou v mém umístění dostupné klíče spravované zákazníky?**

Klíče spravované zákazníkem HDInsight jsou k dispozici ve všech veřejných cloudech a národních cloudech.

## <a name="next-steps"></a>Další kroky

* [Přehled podnikového zabezpečení ve službě Azure HDInsight](./domain-joined/hdinsight-security-overview.md)
