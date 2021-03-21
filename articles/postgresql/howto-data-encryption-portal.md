---
title: Šifrování dat – Azure Portal – pro Azure Database for PostgreSQL jeden server
description: Naučte se, jak nastavit a spravovat šifrování dat pro váš Azure Database for PostgreSQL samostatný server pomocí Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 98c413f85fe556f5fb413716037163931753e1d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93240728"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Šifrování dat pro Azure Database for PostgreSQL jeden server pomocí Azure Portal

Naučte se používat Azure Portal k nastavení a správě šifrování dat pro Azure Database for PostgreSQL jeden server.

## <a name="prerequisites-for-azure-cli"></a>Předpoklady pro Azure CLI

* Musíte mít předplatné Azure a mít oprávnění správce k tomuto předplatnému.
* V Azure Key Vault vytvořte Trezor klíčů a klíč, který se použije pro klíč spravovaný zákazníkem.
* Trezor klíčů musí mít následující vlastnosti, které se mají použít jako klíč spravovaný zákazníkem:
  * [Obnovitelné odstranění](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Vyprázdnit chráněné](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Aby bylo možné klíč použít jako klíč spravovaný zákazníkem, musí mít následující atributy:
  * Nesmí mít žádné datum ukončení platnosti.
  * Nesmí být zakázaný.
  * Může provádět operace získání, zabalení a rozbalení klíčových operací.

## <a name="set-the-right-permissions-for-key-operations"></a>Nastavení správných oprávnění pro klíčové operace

1. V Key Vault vyberte **zásady přístupu**  >  **Přidat zásady přístupu**.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png" alt-text="Snímek obrazovky Key Vault se zvýrazněnými zásadami přístupu a přidáním zásad přístupu":::

2. Vyberte **klíčová oprávnění** a vyberte **získat**, **zalamovat**, **rozbalení** a **objekt zabezpečení**, což je název serveru PostgreSQL. Pokud se váš hlavní server nenašel v seznamu existujících objektů zabezpečení, je potřeba ho zaregistrovat. Budete vyzváni k registraci objektu zabezpečení serveru, když se pokusíte nastavit šifrování dat poprvé, a dojde k chybě.  

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png" alt-text="Přehled zásad přístupu":::

3. Vyberte **Uložit**.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Nastavení šifrování dat pro Azure Database for PostgreSQL jeden server

1. V Azure Database for PostgreSQL pro nastavení klíče spravovaného zákazníkem vyberte možnost **šifrování dat** .

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png" alt-text="Snímek obrazovky Azure Database for PostgreSQL se zvýrazněným šifrováním dat":::

2. Můžete buď vybrat Trezor klíčů a pár klíčů, nebo zadat identifikátor klíče.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png" alt-text="Snímek obrazovky Azure Database for PostgreSQL s zvýrazněnými možnostmi šifrování dat":::

3. Vyberte **Uložit**.

4. Aby bylo zajištěno, že všechny soubory (včetně dočasných souborů) jsou plně zašifrované, restartujte server.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Použití šifrování dat pro obnovení nebo servery repliky

Po zašifrování Azure Database for PostgreSQL jednoho serveru pomocí spravovaného klíče zákazníka uloženého v Key Vault jsou všechny nově vytvořené kopie serveru taky zašifrované. Tuto novou kopii můžete vytvořit buď prostřednictvím operace místního nebo geografického obnovení, nebo prostřednictvím operace repliky (místní/napříč oblastí). Pro zašifrovaný server PostgreSQL můžete například pomocí následujících kroků vytvořit zašifrovaný obnovený server.

1. Na serveru vyberte **Přehled**  >  **obnovení**.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore.png" alt-text="Snímek obrazovky Azure Database for PostgreSQL s zvýrazněným přehledem a obnovením":::

   V případě serveru s povolenou replikací vyberte v části **Nastavení** možnost **replikace**.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/postgresql-replica.png" alt-text="Snímek obrazovky Azure Database for PostgreSQL s zvýrazněnou replikací":::

2. Po dokončení operace obnovení bude nový server vytvořen zašifrovaný pomocí klíče primárního serveru. Funkce a možnosti na serveru jsou ale zakázané a server není dostupný. Tím zabráníte manipulaci s daty, protože identitě nového serveru ještě nebyla udělena oprávnění pro přístup k trezoru klíčů.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png" alt-text="Snímek obrazovky Azure Database for PostgreSQL s zvýrazněným stavem nepřístupu":::

3. Aby byl server přístupný, znovu ověřte klíč na obnoveném serveru. Vyberte klíč znovu ověřit **šifrování dat**  >  .

   > [!NOTE]
   > První pokus o nové ověření se nezdaří, protože instanční objekt nového serveru musí mít přístup k trezoru klíčů. Chcete-li vygenerovat instanční objekt, vyberte znovu **Ověřit klíč**, čímž se zobrazí chyba, ale vygeneruje se instanční objekt. Potom si přečtěte tento [postup](#set-the-right-permissions-for-key-operations) výše v tomto článku.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png" alt-text="Snímek obrazovky Azure Database for PostgreSQL se zvýrazněným krokem replatným":::

   Bude nutné poskytnout Trezor klíčů k novému serveru.

4. Po registraci instančního objektu znovu ověřte klíč a server obnoví své běžné funkce.

   :::image type="content" source="media/concepts-data-access-and-security-data-encryption/restore-successful.png" alt-text="Snímek obrazovky Azure Database for PostgreSQL se zobrazením obnovených funkcí":::

## <a name="next-steps"></a>Další kroky

 Další informace o šifrování dat najdete v tématu [Azure Database for PostgreSQL šifrování dat s jedním serverem pomocí klíče spravovaného zákazníkem](concepts-data-encryption-postgresql.md).
