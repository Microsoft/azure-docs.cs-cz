---
title: Vytvoření a Správa přihlašovacích údajů pro kontroly
description: Přečtěte si o krocích při vytváření a správě přihlašovacích údajů v Azure dosah.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/11/2021
ms.openlocfilehash: 9ffc450294f186b77cc7a6c44c10eecf266161d3
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526683"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Přihlašovací údaje pro ověřování zdroje v Azure dosah

Tento článek popisuje, jak můžete vytvořit přihlašovací údaje v Azure dosah. Tyto uložené přihlašovací údaje vám umožní rychle znovu použít a použít uložené informace o ověřování pro vaše kontroly zdrojů dat.

## <a name="prerequisites"></a>Požadavky

- Trezor klíčů Azure. Další informace o tom, jak ho vytvořit, najdete v tématu [rychlý Start: vytvoření trezoru klíčů pomocí Azure Portal](../key-vault/general/quick-create-portal.md).

## <a name="introduction"></a>Úvod

Přihlašovací údaje jsou ověřovací informace, které může Azure dosah použít k ověřování vašich registrovaných zdrojů dat. Objekt přihlašovacích údajů se dá vytvořit pro různé typy scénářů ověřování, jako je základní ověřování vyžadující uživatelské jméno a heslo. Pověření zachytí konkrétní informace vyžadované k ověření na základě zvoleného typu metody ověřování. Přihlašovací údaje používají stávající tajné klíče Azure Key trezorů pro načítání citlivých ověřovacích informací během procesu vytváření přihlašovacích údajů.

## <a name="use-purview-managed-identity-to-set-up-scans"></a>Nastavení kontrol pomocí spravované identity dosah

Pokud k nastavení kontrol používáte spravovanou identitu dosah, nebudete muset explicitně vytvořit přihlašovací údaje a propojit svůj Trezor klíčů s dosah, abyste je mohli ukládat. Podrobné pokyny k přidání spravované identity dosah, která bude mít přístup ke kontrole zdrojů dat, najdete v níže uvedených částech ověřování specifických pro zdroj dat:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database spravovaná instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Vytvoření připojení k trezorům klíčů Azure v účtu Azure dosah

Než budete moct vytvořit přihlašovací údaje, nejdřív přidružte jednu nebo více stávajících instancí Azure Key Vault k vašemu účtu Azure dosah.

1. Z [Azure Portal](https://portal.azure.com)vyberte svůj účet Azure dosah. Přejděte do **centra pro správu** a potom přejděte na **přihlašovací údaje**.

2. Na stránce **pověření** vyberte **Spravovat Key Vault připojení**.

   :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Správa připojení Azure Key Vault":::

3. Na stránce Spravovat Key Vault připojení vyberte **+ Nový** .

4. Zadejte požadované informace a pak vyberte **vytvořit**.

5. Ověřte, že se váš Key Vault úspěšně přidružil k vašemu účtu Azure dosah, jak je znázorněno v tomto příkladu:

   :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Potvrďte zobrazení Azure Key Vault připojení.":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Udělte dosah spravované identitě přístup k vašemu Azure Key Vault

1. Přejděte na Azure Key Vault.

2. Vyberte stránku **zásady přístupu** .

3. Vyberte **Přidat zásady přístupu**.

   :::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Přidání MSI dosahu do integrace":::

4. V rozevíracím seznamu **oprávnění tajných klíčů** vyberte **získat** a **vypsat** oprávnění.

5. V části **Vybrat objekt zabezpečení** zvolte spravovanou identitu dosah. Dosah MSI můžete vyhledat buď pomocí názvu instance dosah, **nebo** pomocí ID aplikace spravované identity. V současné době nepodporujeme složené identity (název spravované identity + ID aplikace).

   :::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Přidat zásady přístupu":::

6. Vyberte **Přidat**.

7. Vyberte **Uložit** a uložte zásady přístupu.

   :::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Uložit zásady přístupu":::

## <a name="create-a-new-credential"></a>Vytvořit nové přihlašovací údaje

V dosah se podporují tyto typy přihlašovacích údajů:

- Základní ověřování: **heslo** přidáte jako tajný kód do trezoru klíčů.
- Instanční objekt: **klíč instančního objektu** přidáte jako tajný klíč do trezoru klíčů.
- Ověřování SQL: **heslo** přidáte jako tajný kód do trezoru klíčů.
- Klíč účtu: **klíč účtu** přidáte jako tajný klíč do trezoru klíčů.

Další informace najdete v tématu [Přidání tajného klíče do Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault).

Po uložení tajných kódů do trezoru klíčů:

1. V Azure dosah na stránce přihlašovací údaje.

2. Kliknutím na **+ Nový** vytvořte nové přihlašovací údaje.

3. Zadejte požadované informace. Vyberte **metodu ověřování** a **Key Vault připojení** , ze kterého chcete vybrat tajný klíč.

4. Po vyplnění všech podrobností vyberte **vytvořit**.

   :::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Nové přihlašovací údaje":::

5. Ověřte, že se vaše nové přihlašovací údaje zobrazují v zobrazení seznamu a jsou připravené k použití.

   :::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Zobrazit přihlašovací údaje":::

## <a name="manage-your-key-vault-connections"></a>Správa připojení trezoru klíčů

1. Vyhledat nebo najít Key Vault připojení podle jména

   :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Hledat Trezor klíčů":::

2. Odstranění jednoho nebo více Key Vaultch připojení

   :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Odstranit Trezor klíčů":::

## <a name="manage-your-credentials"></a>Správa přihlašovacích údajů

1. Vyhledejte nebo vyhledejte přihlašovací údaje podle jména.
  
2. Vyberte a proveďte Aktualizace existujícího pověření.

3. Odstraňte jednu nebo více přihlašovacích údajů.

## <a name="next-steps"></a>Další kroky

[Vytvoření sady pravidel prohledávání](create-a-scan-rule-set.md)
