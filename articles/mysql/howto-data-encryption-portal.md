---
title: Šifrování dat pro Azure Database for MySQL pomocí portálu
description: Naučte se, jak nastavit a spravovat šifrování dat pro Azure Database for MySQL pomocí Azure Portal.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 5bd100a5858660d80f3704844ed974f2bc9257b4
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028938"
---
# <a name="data-encryption-for-azure-database-for-mysql-server-using-azure-portal"></a>Šifrování dat pro Azure Database for MySQL server pomocí Azure Portal

V tomto článku se naučíte, jak nastavit a spravovat pro použití Azure Portal k nastavení šifrování dat pro Azure Database for MySQL.

## <a name="prerequisites-for-cli"></a>Předpoklady pro rozhraní příkazového řádku

* Musíte mít předplatné Azure a mít oprávnění správce k tomuto předplatnému.
* Vytvořte Azure Key Vault a klíč, který se použije pro klíč spravovaný zákazníkem.
* Key Vault musí mít následující vlastnost, která se má použít jako klíč spravovaný zákazníkem:
  * [Obnovitelné odstranění](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Vyprázdnit chráněné](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Klíč musí mít následující atributy, které se mají použít pro klíč spravovaný zákazníkem.
  * Žádné datum vypršení platnosti
  * Nezakázáno
  * Může provádět operace _Get_, _Wrap Key_, _rozbalení klíčových_ operací.

## <a name="setting-the-right-permissions-for-key-operations"></a>Nastavení správných oprávnění pro klíčové operace

1. V Azure Key Vault vyberte **zásady přístupu**a pak **přidejte zásady přístupu**:

   ![Přehled zásad přístupu](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Vyberte **oprávnění klíče**a vyberte **získat**, **zalamovat**, **rozbalení** a **objekt zabezpečení**, což je název serveru MySQL. Pokud se váš hlavní server nenašel v seznamu existujících objektů zabezpečení, budete ho muset zaregistrovat tak, že se pokusíte nastavit šifrování dat poprvé, což se nezdaří.

   ![Přehled zásad přístupu](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. **Uložte** nastavení.

## <a name="setting-data-encryption-for-azure-database-for-mysql"></a>Nastavení šifrování dat pro Azure Database for MySQL

1. V **Azure Database for MySQL**vyberte **šifrování dat** pro nastavení klíče spravovaného zákazníkem.

   ![Nastavení šifrování dat](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Můžete buď vybrat **Key Vault** a pár **klíčů** , nebo předat **identifikátor klíče**.

   ![Nastavení Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Uložte** nastavení.

4. Aby bylo zajištěno, že všechny soubory (včetně **dočasných souborů**) jsou úplné zašifrované, je **nutné** **restartovat** Server.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Obnovování nebo vytváření repliky serveru s povoleným šifrováním dat

Jakmile je Azure Database for MySQL zašifrovaný pomocí spravovaného klíče zákazníka uloženého v Key Vault, každá nově vytvořená kopie serveru buď i v případě, že operace místního nebo geografického obnovení nebo repliky (místní/mezioblast) je zašifrovaná. Pro zašifrovaný Server MySQL Můžete proto pomocí následujících kroků vytvořit zašifrovaný obnovený server.

1. Na serveru vyberte **Přehled**a pak vyberte **obnovit**.

   ![Inicializace – obnovení](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Nebo pro server s povolenou replikací vyberte v části **Nastavení** možnost **replikace**, jak je znázorněno zde:

   ![Spustit repliku](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. Po dokončení operace obnovení bude nový server vytvořen zašifrovaný pomocí klíče, který slouží k šifrování primárního serveru. Funkce a možnosti na serveru jsou ale zakázané a server je označený v **nepřístupovém** stavu. Toto chování je navrženo tak, aby zabránilo manipulaci s daty, protože identitě nového serveru ještě nebylo uděleno oprávnění pro přístup k Key Vault.

   ![Označení serveru jako nepřístupný](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Chcete-li opravit nepřístupný stav, je nutné znovu ověřit klíč na obnoveném serveru. Vyberte podokno **šifrování dat** a pak klikněte na tlačítko znovu **Ověřit klíč** .

   > [!NOTE]
   > První pokus o nové ověření selže, protože instanční objekt nového serveru musí mít udělen přístup k trezoru klíčů. Chcete-li vygenerovat instanční objekt, vyberte znovu **Ověřit klíč**, čímž dojde k chybě, ale vygeneruje objekt služby. Potom si přečtěte postup [v části 2](#setting-the-right-permissions-for-key-operations) výše.

   ![znovu ověřit server](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   K tomuto Key Vault budete muset udělit přístup k novému serveru.

4. Po registraci instančního objektu bude nutné znovu znovu ověřit klíč a server bude obnovovat své normální funkce.

   ![Normální obnovený server](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Další kroky

 Další informace o šifrování dat najdete v tématu [co je Azure Data Encryption](concepts-data-encryption-mysql.md).
