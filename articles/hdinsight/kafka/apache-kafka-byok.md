---
title: Přineste si vlastní klíč pro Apache Kafka v Azure HDInsight (Preview)
description: Tento článek popisuje, jak použít vlastní klíč ze služby Azure Key Vault k šifrování dat uložených v Apache Kafka v Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 85fea195b05bea8a1db70f8b5b81cabdfe7c6c72
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041505"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight-preview"></a>Přineste si vlastní klíč pro Apache Kafka v Azure HDInsight (Preview)

Azure HDInsight zahrnuje podporu přineste si vlastní klíč (BYOK) pro platformu Apache Kafka. Tato funkce umožňuje sami vlastnili a spravovali klíče používané k šifrování dat v klidovém stavu. 

Všechny spravované disky v HDInsight jsou chráněny s Azure Storage Service Encryption (SSE). Ve výchozím nastavení zašifrovaná data na těchto discích, pomocí klíčů spravovaných microsoftem. Pokud povolíte BYOK, zadejte šifrovací klíč pro službu HDInsight využívat a spravovat ho pomocí služby Azure Key Vault. 

BYOK šifrování je jednoduchý proces, při vytváření clusteru bez dalších poplatků. Všechno, co je potřeba je zaregistrovat HDInsight jako spravovanou identitu pomocí Azure Key Vault a přidat šifrovacího klíče při vytváření clusteru.

Všechny zprávy do clusteru Kafka (včetně replik spravuje Kafka) jsou šifrována pomocí symetrický datový šifrovací klíč (DEK). Klíč DEK je chráněn pomocí klíče šifrování klíčů (KEK) z trezoru klíčů. Procesy šifrování a dešifrování dat provádí zcela služba Azure HDInsight. 

Na webu Azure portal nebo rozhraní příkazového řádku Azure můžete bezpečně obměna klíčů v trezoru klíčů. Když otočí klíče clusteru HDInsight Kafka spustí během několika minut pomocí nového klíče. Povolení funkcí ochrany klíčů "Proveďte nelze vymazat" a "Obnovitelné odstranění" pro ochranu před ransomwarem scénáře a nechtěnému odstranění. Bez těchto funkcí ochrany klíče nejsou podporovány.

## <a name="get-started-with-byok"></a>Začínáme s BYOK

1. Vytvoření spravovaných identit pro prostředky Azure.

   K ověření do služby Key Vault, vytvořte pomocí uživatelsky přiřazené identity spravované [webu Azure Portal](../../active-directory/managed-service-identity/how-to-manage-ua-identity-portal.md), [prostředí Azure PowerShell](../../active-directory/managed-service-identity/how-to-manage-ua-identity-powershell.md), [Azure Resource Manageru](../../active-directory/managed-service-identity/how-to-manage-ua-identity-arm.md), nebo [ Azure CLI](../../active-directory/managed-service-identity/how-to-manage-ua-identity-cli.md). Azure Active directory je požadovaná pro spravované identity a funkce BYOK na Kafka, Enterprise Security Package (ESP) nejsou povinné. Nezapomeňte uložit spravovaná identita ID prostředku pro při přidání do zásad přístupu trezoru klíčů.

   ![Vytvoření uživatelsky přiřazené identity spravované v portálu Azure portal](./media/apache-kafka-byok/user-managed-identity-portal.png)

2. Import existujícího trezoru klíčů, nebo vytvořte novou.

   HDInsight podporuje jenom služby Azure Key Vault. Pokud máte trezor klíčů, můžete klíče importovat do Azure Key Vault. Mějte na paměti, že klíče musí mít "Obnovitelné odstranění" a "Proveďte není odstranění" povolena. Funkce "Obnovitelné odstranění" a "Proveďte nelze vymazat" jsou k dispozici prostřednictvím rozhraní REST, .NET / C#, prostředí PowerShell a rozhraní příkazového řádku Azure rozhraní.

   Chcete-li vytvořit nový trezor klíčů, postupujte [Azure Key Vault](../../key-vault/key-vault-get-started.md) rychlý start. Další informace o importu stávajících klíčů [informace o klíčích, tajných kódů a certifikátů](../../key-vault/about-keys-secrets-and-certificates.md).

   Chcete-li vytvořit nový klíč, vyberte **vygenerovat/importovat** z **klíče** nabídky v části **nastavení**.

   ![Generovat nový klíč ve službě Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png)

   Nastavte **možnosti** k **generovat** a pojmenujte klíč.

   ![Generovat nový klíč ve službě Azure Key Vault](./media/apache-kafka-byok/kafka-create-a-key.png)

   Vyberte klíč, který jste vytvořili ze seznamu klíčů.

   ![Seznam klíčů služby Azure Key Vault](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

   Při použití vlastního klíče pro šifrování clusteru Kafka, budete muset zadat identifikátor URI klíče. Kopírovat **identifikátor klíče** a uložte ho někam dokud jste připraveni vytvořit cluster.

   ![Zkopírujte identifikátor klíče](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
3. Přidáte spravovanou identitu do zásady přístupu trezoru klíčů.

   Vytvoření nové zásady přístupu trezoru klíčů Azure.

   ![Vytvořit novou zásadu přístupu služby Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy.png)

   V části **výběr objektu zabezpečení**, zvolte uživatel přiřazenou spravovanou identitu jste vytvořili.

   ![Nastavit výběr objektu zabezpečení pro zásady přístupu trezoru klíčů Azure](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

   Nastavte **oprávnění klíče** k **získat**, **rozbalit klíč**, a **zabalit klíč**.

   ![Nastavení oprávnění klíče zásad přístupu trezoru klíčů Azure](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

   Nastavte **oprávnění tajného klíče** k **získat**, **nastavit**, a **odstranit**.

   ![Nastavení oprávnění klíče zásad přístupu trezoru klíčů Azure](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

4. Vytvoření clusteru HDInsight

   Nyní jste připraveni vytvořit nový cluster HDInsight. BYOK dá používat jedině do nového clusteru během vytváření clusteru. Šifrování nelze odebrat z funkce BYOK clusterů a BYOK nelze přidat do existujících clusterů.

   ![Šifrování disku Kafka na webu Azure portal](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   Při vytváření clusteru, zadejte úplnou adresu URL, včetně klíčových verze klíče. Například, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Také musíte přiřadit spravovanou identitu clusteru a zadejte identifikátor URI klíče.

## <a name="faq-for-byok-to-kafka"></a>Nejčastější dotazy týkající se funkce BYOK na Kafka

**Jak clusteru Kafka přístup Moje služby key vault?**

   Spravovaná identita přidružte ke clusteru HDInsight Kafka při vytváření clusteru. Tuto spravovanou identitu můžete vytvořit před nebo během vytváření clusteru. Také budete muset udělit spravovanou identitu přístup k trezoru klíčů, ve kterém se má uložit klíč.

**Tato funkce je k dispozici pro všechny clustery Kafka v HDInsight?**

   BYOK šifrování je pouze možné Kafka 1.1 a vyšším clustery.

**Může mít odlišné klíče pro různé témata/oddíly?**

   Ne, všechny spravované disky v clusteru jsou zašifrovaná pomocí stejného klíče.

**Jak můžete obnovit clusteru, pokud se odstraní klíče?**

   Vzhledem k tomu, že jsou podporovány pouze klíče "Obnovitelné odstranění" povoleno, pokud se obnoví klíče v trezoru klíčů, clusteru by měl znovu získali přístup ke klíčům. Obnovit klíče služby Azure Key Vault, najdete v článku [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey).

**Může mít producenta/konzumenta najdete aplikace pracující s BYOK clusteru a clusteru bez BYOK se současně?**

   Ano. Použití funkce BYOK je pro producenta/konzumenta najdete aplikace transparentní. Šifrování se odehrává na vrstvě operačního systému. Žádné změny nezbytné provést existující producenta/konzumenta Kafka aplikací.

**Disky s operačním systémem disky nebo prostředek také zašifrují?**

   Ne. Disky s operačním systémem a prostředků disky nejsou šifrovány.

**Pokud je cluster škálovat, bude můžou být zprostředkovatelé podporovat BYOK bez problémů?**

   Ano. Cluster musí přístup ke klíči v trezoru klíčů při škálování nahoru. Stejný klíč slouží k šifrování všech spravovaných disků v clusteru.

**Je k dispozici funkce BYOK v umístění?**

   Kafka BYOK je k dispozici ve všech veřejných cloudech.

## <a name="next-steps"></a>Další postup

* Další informace o službě Azure Key Vault najdete v tématu [co je Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* Abyste mohli začít se službou Azure Key Vault, naleznete v tématu [Začínáme se službou Azure Key Vault](../../key-vault/key-vault-get-started.md).
