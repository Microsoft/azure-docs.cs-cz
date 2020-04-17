---
title: Šifrování dat – portál Azure – pro Azure Database for PostgreSQL – jeden server
description: Zjistěte, jak nastavit a spravovat šifrování dat pro váš server Azure Database for PostgreSQL Single pomocí portálu Azure.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 07e103c3e1f56e8a46ea24e750d83e719abab3d5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457973"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-portal"></a>Šifrování dat pro Azure Database for PostgreSQL Single server pomocí portálu Azure

Zjistěte, jak pomocí portálu Azure nastavit a spravovat šifrování dat pro server Azure Database for PostgreSQL Single.

## <a name="prerequisites-for-azure-cli"></a>Požadavky pro azure cli

* Musíte mít předplatné Azure a být správcem tohoto předplatného.
* V trezoru klíčů Azure vytvořte trezor klíčů a klíč, který se použije pro klíč spravovaný zákazníkem.
* Trezor klíčů musí mít následující vlastnosti, které chcete použít jako klíč spravovaný zákazníkem:
  * [Obnovitelné odstranění](../key-vault/general/overview-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Vyčištění chráněno](../key-vault/general/overview-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Klíč musí mít následující atributy, které chcete použít jako klíč spravovaný zákazníkem:
  * Bez data vypršení platnosti
  * Není zakázáno
  * Možnost provádět operace získání, obtékání klíče a rozbalení klíče

## <a name="set-the-right-permissions-for-key-operations"></a>Nastavení správných oprávnění pro klíčové operace

1. V trezoru klíčů vyberte **možnost Přístupové zásady** > **Přidat zásady přístupu**.

   ![Snímek obrazovky s trezorem klíčů se zvýrazněnými zásadami přístupu a funkceMi Přidat zásady přístupu](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Vyberte **Klíčová oprávnění**a vyberte **Možnost Získat**, **Obtékat**, **Rozbalit**a **Hlavní**, což je název postgresql serveru. Pokud objekt zabezpečení serveru nelze nalézt v seznamu existujících objektů zabezpečení, je třeba jej zaregistrovat. Při prvním pokusu o nastavení šifrování dat se zobrazí výzva k registraci objektu zabezpečení serveru a selže.  

   ![Přehled zásad přístupu](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Vyberte **Uložit**.

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Nastavení šifrování dat pro azure databázi pro server PostgreSQL Single

1. V Azure Database for PostgreSQL vyberte **Šifrování dat** a nastavte klíč spravovaný zákazníkem.

   ![Snímek obrazovky s databází Azure pro PostgreSQL se zvýrazněným šifrováním dat](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Můžete buď vybrat trezor klíčů a dvojici klíčů, nebo zadat identifikátor klíče.

   ![Snímek obrazovky s databází Azure pro PostgreSQL se zvýrazněnými možnostmi šifrování dat](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Vyberte **Uložit**.

4. Chcete-li zajistit, aby všechny soubory (včetně dočasných souborů) byly plně zašifrovány, restartujte server.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Použití šifrování dat pro servery pro obnovení nebo repliky

Po Azure Database for PostgreSQL Single server je šifrována s klientem spravovaný klíč uložený v trezoru klíčů, všechny nově vytvořené kopie serveru je také šifrována. Tuto novou kopii můžete vytvořit buď prostřednictvím místní operace nebo operace geografického obnovení, nebo prostřednictvím operace repliky (místní/mezi oblastmi). Takže pro šifrovaný PostgreSQL server můžete použít následující kroky k vytvoření šifrovaného obnoveného serveru.

1. Na serveru vyberte **možnost Obnovení přehledu** > **Restore**.

   ![Snímek obrazovky s databází Azure pro PostgreSQL se zvýrazněnou možností Přehled a Obnovení](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Nebo pro server s podporou replikace vyberte v části **Nastavení** v záhlaví **Replikace**.

   ![Snímek obrazovky s databází Azure pro PostgreSQL se zvýrazněnou možností Replikace](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. Po dokončení operace obnovení je nový vytvořený server zašifrován pomocí klíče primárního serveru. Funkce a možnosti na serveru jsou však zakázány a server je nepřístupný. Tím se zabrání jakékoli manipulaci s daty, protože identita nového serveru ještě nebyla udělena oprávnění k přístupu do trezoru klíčů.

   ![Snímek obrazovky s databází Azure pro PostgreSQL se zvýrazněným stavem Nepřístupné](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Chcete-li server zpřístupnit, znovu ověřte klíč na obnoveném serveru. Vyberte **možnost Znovu** > ověřit**klíč šifrování**dat .

   > [!NOTE]
   > První pokus o opětovné ověření se nezdaří, protože instanční objekt nového serveru musí mít přístup k trezoru klíčů. Chcete-li generovat instanční objekt, vyberte **možnost Znovu ověřit klíč**, který zobrazí chybu, ale vygeneruje instanční objekt. Poté naleznete [v těchto krocích](#set-the-right-permissions-for-key-operations) dříve v tomto článku.

   ![Snímek obrazovky s databází Azure pro PostgreSQL se zvýrazněným krokem prodloužení platnosti](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Budete muset udělit klíč trezoru přístup k novému serveru.

4. Po registraci instančního objektu znovu obnovte klíč a server obnoví normální funkčnost.

   ![Snímek obrazovky s databází Azure pro PostgreSQL s obnovenou funkcí](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Další kroky

 Další informace o šifrování dat najdete v [tématu Azure Database for PostgreSQL Jednoserverové šifrování dat pomocí klíče spravovaného zákazníkem](concepts-data-encryption-postgresql.md).
