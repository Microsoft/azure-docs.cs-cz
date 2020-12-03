---
title: Vytvoření a Správa přihlašovacích údajů pro kontroly
description: Tento článek vysvětluje kroky pro vytváření a správu přihlašovacích údajů v Azure dosah.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: c991559d550b351ce70bcc5834f96f313f856a82
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551879"
---
# <a name="credentials-for-source-authentication-in-azure-purview"></a>Přihlašovací údaje pro ověřování zdroje v Azure dosah

Tento článek popisuje, jak v Azure dosah vytvořit přihlašovací údaje pro rychlé opakované použití a použití uložených ověřovacích informací do vašich kontrol zdrojů dat.

## <a name="prerequisites"></a>Předpoklady

* Trezor klíčů Azure. Pokud ho ještě nemáte, můžete ho vytvořit sami (vložením odkazu na článek o vytvoření KV).

## <a name="introduction"></a>Úvod
Přihlašovací údaje jsou ověřovací informace, které může Azure dosah použít k ověřování vašich registrovaných zdrojů dat. Objekt přihlašovacích údajů se dá vytvořit pro různé typy scénářů ověřování (například základní ověřování vyžadující uživatelské jméno/heslo) a zachytí konkrétní informace požadované na základě zvoleného typu metody ověřování. Přihlašovací údaje používají stávající tajné klíče Azure Key trezorů pro načítání citlivých ověřovacích informací během procesu vytváření přihlašovacích údajů.

## <a name="using-purview-managed-identity-to-set-up-scans"></a>Nastavení kontrol pomocí spravované identity dosah
Pokud k nastavení kontrol používáte spravovanou identitu dosah, nebudete muset explicitně vytvořit přihlašovací údaje a propojit svůj Trezor klíčů s dosah, abyste je mohli ukládat. Podrobné pokyny k přidání spravované identity dosah, která bude mít přístup ke kontrole zdrojů dat, najdete v níže uvedených částech ověření zdroje dat:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database spravovaná instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)

## <a name="create-azure-key-vaults-connections-in-your-azure-purview-account"></a>Vytvoření připojení k trezorům klíčů Azure v účtu Azure dosah

Předtím, než budete moct vytvořit přihlašovací údaje, musíte nejdřív přidružit jednu nebo více stávajících instancí Azure Key Vault k vašemu účtu Azure dosah.

1. V navigační nabídce Azure dosah přejděte do centra pro správu a potom přejděte na přihlašovací údaje.

2. Na panelu příkazů pověření vyberte Spravovat Key Vault připojení.

    :::image type="content" source="media/manage-credentials/manage-kv-connections.png" alt-text="Správa připojení integrace":::

3. Na panelu Správa Key Vaultch připojení vyberte + nový. 

4. Zadejte požadované informace a pak vyberte vytvořit.

5. Ověřte, že se váš Key Vault úspěšně přidružil k vašemu účtu Azure dosah.

    :::image type="content" source="media/manage-credentials/view-kv-connections.png" alt-text="Zobrazit integrace připojení":::

## <a name="grant-the-purview-managed-identity-access-to-your-azure-key-vault"></a>Udělte dosah spravované identitě přístup k vašemu Azure Key Vault

Přejděte do trezoru klíčů – > zásady přístupu – > přidat zásady přístupu. Udělte oprávnění Get v rozevíracím seznamu oprávnění tajných kódů a vyberte objekt zabezpečení dosah MSI. 

:::image type="content" source="media/manage-credentials/add-msi-to-akv.png" alt-text="Přidání MSI dosahu do integrace":::


:::image type="content" source="media/manage-credentials/add-access-policy.png" alt-text="Přidat zásady přístupu":::


:::image type="content" source="media/manage-credentials/save-access-policy.png" alt-text="Uložit zásady přístupu":::

## <a name="create-a-new-credential"></a>Vytvořit nové přihlašovací údaje

Typ přihlašovacích údajů podporovaný v dosah ještě dnes:
* Základní ověřování: **heslo** přidáte jako tajný kód do trezoru klíčů.
* Instanční objekt: **klíč instančního objektu** se přidá jako tajný klíč do trezoru klíčů. 
* Ověřování SQL: **heslo** přidáte jako tajný kód do trezoru klíčů.
* Klíč účtu: **klíč účtu** přidáte jako tajný klíč do trezoru klíčů.

Zde najdete další informace o tom, jak přidat tajné klíče do trezoru klíčů: (vložte článek o trezoru klíčů).

Po uložení tajných kódů do trezoru klíčů vytvořte nové přihlašovací údaje tak, že na panelu příkazů v přihlašovacích údajích vyberete + nový. Zadejte požadované informace, včetně výběru metody ověřování a instance Key Vault, ze které se má vybrat tajný klíč. Po vyplnění všech podrobností klikněte na vytvořit.

:::image type="content" source="media/manage-credentials/new-credential.png" alt-text="Nové přihlašovací údaje":::

Ověřte, že se nové přihlašovací údaje zobrazují v zobrazení seznamu přihlašovacích údajů a jsou připravené k použití.

:::image type="content" source="media/manage-credentials/view-credentials.png" alt-text="Zobrazit přihlašovací údaje":::

## <a name="manage-your-key-vault-connections"></a>Správa připojení trezoru klíčů

1. Vyhledat nebo najít Key Vault připojení podle jména

    :::image type="content" source="media/manage-credentials/search-kv.png" alt-text="Hledat Trezor klíčů":::

1. Odstranění jednoho nebo více Key Vaultch připojení
 
    :::image type="content" source="media/manage-credentials/delete-kv.png" alt-text="Odstranit Trezor klíčů":::

## <a name="manage-your-credentials"></a>Správa přihlašovacích údajů

1. Vyhledat nebo najít pověření podle jména
  
2. Vyberte a proveďte Aktualizace existujícího přihlašovacího údaje.

3. Odstranit jednu nebo více přihlašovacích údajů