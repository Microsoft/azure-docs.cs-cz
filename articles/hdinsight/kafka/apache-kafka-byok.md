---
title: Přineste si vlastní klíč pro Apache Kafka ve službě Azure HDInsight
description: Tento článek popisuje, jak použít vlastní klíč z Azure Key Vault k šifrování dat uložených v Apache Kafka v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cba8a4fd64b948d7a3e443426ca1f779af68a3fe
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048982"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Přineste si vlastní klíč pro Apache Kafka ve službě Azure HDInsight

Azure HDInsight zahrnuje podporu Bring Your Own Key (BYOK) pro Apache Kafka. Tato funkce vám umožní vlastnit a spravovat klíče používané k šifrování neaktivních dat.

Všechny spravované disky v HDInsight jsou chráněné pomocí šifrování služby Azure Storage (SSE). Ve výchozím nastavení se data na těchto discích šifrují pomocí klíčů spravovaných Microsoftem. Pokud povolíte BYOK, zadáte šifrovací klíč, který bude HDInsight používat a spravovat ho pomocí Azure Key Vault.

Šifrování BYOK je proces, který se během vytváření clusteru zpracuje v jednom kroku, a to bez dalších nákladů. Vše, co potřebujete udělat, je zaregistrovat HDInsight jako spravovanou identitu s Azure Key Vault a přidat šifrovací klíč při vytváření clusteru.

Všechny zprávy do clusteru Kafka (včetně replik, které udržuje Kafka) jsou šifrované pomocí symetrického šifrovacího klíče (klíč DEK). KLÍČ DEK je chráněný pomocí klíčového šifrovacího klíče (KEK) z vašeho trezoru klíčů. Procesy šifrování a dešifrování jsou zpracovávány výhradně službou Azure HDInsight.

K bezpečnému střídání klíčů v trezoru klíčů můžete použít Azure Portal nebo Azure CLI. Když se klíč otáčí, cluster HDInsight Kafka začne používat nový klíč během několika minut. Povolte funkce ochrany před náhodným odstraněním, které chcete chránit před ransomwaremmi scénáři a náhodným odstraněním. Trezory klíčů bez této funkce ochrany se nepodporují.

## <a name="get-started-with-byok"></a>Začínáme s BYOK

Pokud chcete vytvořit cluster Kafka s podporou BYOK, Projděte si následující kroky:

1. Vytvoření spravovaných identit pro prostředky Azure
2. Nastavení Azure Key Vault a klíčů
3. Vytvoření clusteru HDInsight Kafka s povoleným BYOK
4. Otočení šifrovacího klíče

## <a name="create-managed-identities-for-azure-resources"></a>Vytvoření spravovaných identit pro prostředky Azure

Pokud chcete ověřit Key Vault, vytvořte uživatelem přiřazenou identitu pomocí [Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)nebo [Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Další informace o tom, jak spravované identity fungují ve službě Azure HDInsight, najdete v tématu [spravované identity ve službě Azure HDInsight](../hdinsight-managed-identities.md). I když je služba Azure Active Directory nutná pro spravované identity a BYOK na Kafka, nepožaduje se Balíček zabezpečení podniku (ESP). Nezapomeňte uložit ID spravovaného prostředku identity, pokud ho přidáte do zásad přístupu Key Vault.

![Vytvoření spravované identity přiřazené uživatelem v Azure Portal](./media/apache-kafka-byok/azure-portal-create-managed-identity.png)

## <a name="set-up-the-key-vault-and-keys"></a>Nastavení Key Vault a klíčů

HDInsight podporuje jenom Azure Key Vault. Pokud máte vlastní Trezor klíčů, můžete klíče importovat do Azure Key Vault. Mějte na paměti, že klíče musí obsahovat "obnovitelné odstranění". Funkce "obnovitelné odstranění" je k dispozici prostřednictvím rozhraní REST, .NETC#/, PowerShellu a rozhraní příkazového řádku Azure CLI.

1. Pokud chcete vytvořit nový trezor klíčů, postupujte podle pokynů pro rychlý Start [Azure Key Vault](../../key-vault/quick-create-cli.md) . Další informace o importu existujících klíčů najdete v [informacích o klíčích, tajných klíčích a certifikátech](../../key-vault/about-keys-secrets-and-certificates.md).

1. V trezoru klíčů Povolte "obnovitelné odstranění" pomocí příkazu [AZ Key trezor Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) CLI.

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. Vytvořte klíče.

    a. Pokud chcete vytvořit nový klíč, vyberte v nabídce **klíče** v části **Nastavení**možnost **Generovat/importovat** .

    ![Vygenerovat nový klíč v Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png "Vygenerovat nový klíč v Azure Key Vault")

    b. Nastavte **Možnosti** , které se mají **vygenerovat** , a zadejte název klíče.

    ![Apache Kafka vygeneruje název klíče](./media/apache-kafka-byok/apache-kafka-create-key.png "Vygenerovat název klíče")

    c. Vyberte klíč, který jste vytvořili ze seznamu klíčů.

    ![Seznam klíčů trezoru klíčů Apache Kafka](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

    d. Pokud používáte vlastní klíč pro šifrování clusteru Kafka, musíte zadat identifikátor URI klíče. Zkopírujte **identifikátor klíče** a uložte ho někam do chvíle, kdy jste připraveni vytvořit cluster.

    ![Identifikátor klíče pro Apache Kafka získat](./media/apache-kafka-byok/kafka-get-key-identifier.png)

1. Přidejte spravovanou identitu do zásad přístupu trezoru klíčů.

    a. Vytvořte nové zásady přístupu Azure Key Vault.

    ![Vytvořit nové zásady přístupu Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy.png)

    b. V části **Vybrat objekt zabezpečení**vyberte spravovanou identitu přiřazenou uživatelem, kterou jste vytvořili.

    ![Nastavení výběru objektu zabezpečení pro zásady Azure Key Vaultho přístupu](./media/apache-kafka-byok/azure-portal-add-access-policy.png)

    c. Nastavte **klíčová oprávnění** pro **získání**, **rozbalení klíče**a **zabalení klíče**.

    ![Nastavení oprávnění klíče pro Azure Key Vault přístup policy1](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png "Nastavení oprávnění klíče pro Azure Key Vault přístup policy1")

    d. Nastavte **tajná oprávnění** pro **získání**, **Nastavení**a **odstranění**.

    ![Nastavení oprávnění klíče pro Azure Key Vault přístup policy2](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png "Nastavení oprávnění klíče pro Azure Key Vault přístup policy2")

    e. Vyberte **Save** (Uložit).

    ![Uložit zásady přístupu Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## <a name="create-hdinsight-cluster"></a>Vytvoření clusteru HDInsight

Nyní jste připraveni vytvořit nový cluster HDInsight. Na kartě **základy** vyberte **Kafka** pro **typ clusteru**.

![Azure Portal výběr typu Kafka](./media/apache-kafka-byok/azure-portal-cluster-basics-type-kafka.png)

BYOK se dá použít jenom pro nové clustery během vytváření clusteru. Šifrování nejde odebrat z clusterů BYOK a BYOK se nedá přidat do stávajících clusterů.

![Kafka Disk Encryption v Azure Portal](./media/apache-kafka-byok/azure-portal-cluster-security-networking-kafka-byok.png)

Během vytváření clusteru zadejte úplnou adresu URL klíče, včetně verze klíče. například `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Musíte také přiřadit spravovanou identitu ke clusteru a zadat identifikátor URI klíče. Úplné podrobnosti o vytváření clusterů najdete v tématu [Vytvoření clusterů Apache Hadoop pomocí Azure Portal](./apache-kafka-get-started.md)

## <a name="rotating-the-encryption-key"></a>Otočení šifrovacího klíče

Můžou nastat situace, kdy budete možná chtít změnit šifrovací klíče používané clusterem Kafka po jeho vytvoření. To může být snadné prostřednictvím portálu. Pro tuto operaci musí mít cluster přístup k aktuálnímu klíči i k zamýšlenému novému klíči. v opačném případě se operace otočení klíče nezdaří.

Chcete-li otočit klíč, je nutné mít úplnou adresu URL nového klíče (viz krok 3 [nastavení Key Vault a klíčů](#set-up-the-key-vault-and-keys)). Až to budete mít, přejděte do části vlastnosti clusteru Kafka na portálu a klikněte na **změnit klíč** pod **adresou URL klíče pro šifrování disku**. Zadejte novou adresu URL klíče a odešlete pro otočení klíče.

![Kafka otočit šifrovací klíč disku](./media/apache-kafka-byok/apache-kafka-change-key.png)

## <a name="faq-for-byok-to-apache-kafka"></a>Nejčastější dotazy k Apache Kafka BYOK

**Jak cluster Kafka přistupuje k trezoru klíčů?**

Během vytváření clusteru Přidružte ke clusteru HDInsight Kafka spravovanou identitu. Tato spravovaná identita se dá vytvořit před nebo během vytváření clusteru. Musíte taky udělit přístup spravované identity k trezoru klíčů, kde je klíč uložený.

**Je tato funkce dostupná pro všechny clustery Kafka ve službě HDInsight?**

Šifrování BYOK je možné jenom u clusterů Kafka 1,1 a novějších.

**Můžu mít různé klíče pro různá témata nebo oddíly?**

Ne, všechny spravované disky v clusteru se šifrují pomocí stejného klíče.

**Co se stane, když cluster ztratí přístup k trezoru klíčů nebo klíči?**

Pokud cluster ztratí přístup k tomuto klíči, zobrazí se na portálu Apache Ambari upozornění. V tomto stavu se operace **změny klíče** nezdaří. Po obnovení přístupu ke klíčům zmizí Ambari upozornění a operace, jako je například střídání klíčů, je možné úspěšně provést.

![Výstraha Ambari přístupu k klíči Apache Kafka](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**Jak mohu obnovit cluster, pokud jsou klíče odstraněny?**

Vzhledem k tomu, že se podporují jenom klíče s povoleným obnovitelném odstraněním, měl by cluster znovu získat přístup k klíčům, pokud se klíče obnoví v trezoru klíčů. Pokud chcete obnovit Azure Key Vault klíč, přečtěte si téma [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) nebo [AZ-klíčů trezor-Key-Recovery](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Můžu aplikace producent/příjemce pracovat s clusterem BYOK a současně s clusterem bez BYOK?**

Ano. Použití BYOK je transparentní pro aplikace producenta a příjemce. Šifrování probíhá na vrstvě operačního systému. U stávajících aplikací pro producenta a příjemce Kafka není potřeba provádět žádné změny.

**Šifrují se i disky s operačním systémem nebo disky prostředků?**

Ne. Disky s operačním systémem a disky prostředků nejsou šifrované.

**Pokud je cluster zapnutý, budou noví zprostředkovatelé BYOK bezproblémově podporovat?**

Ano. Cluster potřebuje během horizontálního navýšení kapacity přístup k klíči v trezoru klíčů. Stejný klíč se používá k šifrování všech spravovaných disků v clusteru.

**Je BYOK k dispozici v mém umístění?**

Kafka BYOK je k dispozici ve všech veřejných cloudech.

## <a name="next-steps"></a>Další kroky

* Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault](../../key-vault/key-vault-overview.md)?
* Pokud chcete začít pracovat s Azure Key Vault, přečtěte si téma [Začínáme with Azure Key Vault](../../key-vault/key-vault-overview.md).
