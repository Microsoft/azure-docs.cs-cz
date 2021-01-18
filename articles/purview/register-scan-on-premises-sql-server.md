---
title: Registrace a kontrola místního SQL serveru
description: V tomto kurzu se dozvíte, jak prověřit Prem SQL Server pomocí prostředí IR pro místní hostování.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
ms.openlocfilehash: 0d282ee805ac61ba17ceb3ecc6a3d8179ea7b319
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555895"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>Registrace a kontrola místního SQL serveru

Tento článek popisuje, jak zaregistrovat zdroj dat SQL serveru v dosah a nastavit pro něj kontrolu.

## <a name="supported-capabilities"></a>Podporované možnosti

Místní zdroj dat SQL serveru podporuje následující funkce:

- **Úplné a přírůstkové kontroly** pro zachycení metadat a klasifikace v místní síti nebo na SQL serveru nainstalovaném na virtuálním počítači Azure.

- Počet **řádků** mezi datovými prostředky pro aktivity kopírování a toku dat ADF

Místní zdroj dat SQL serveru podporuje:

- všechny verze SQL z SQL serveru 2019 zpátky na SQL Server 2000

- Metoda ověřování: ověřování SQL

### <a name="known-limitations"></a>Známá omezení

Azure dosah nepodporuje kontrolu [zobrazení](/sql/relational-databases/views/views) v SQL Server.

## <a name="prerequisites"></a>Požadavky

- Před registrací zdrojů dat vytvořte účet Azure dosah. Další informace o vytvoření účtu dosah najdete v tématu [rychlý Start: vytvoření účtu Azure dosah](create-catalog-portal.md).

- Nastavte [modul Integration runtime](manage-integration-runtimes.md) v místním prostředí pro kontrolu zdroje dat.

## <a name="setting-up-authentication-for-a-scan"></a>Nastavení ověřování pro kontrolu

Existuje pouze jeden způsob, jak nastavit ověřování pro místní SQL Server:

- Ověřování SQL

### <a name="sql-authentication"></a>Ověřování SQL

Identita SQL musí mít přístup k primární databázi. Místo, kde `sys.databases` je uloženo. Aby `sys.databases` bylo možné najít všechny instance SQL DB na serveru, musí se vypsat dosah skener.

#### <a name="using-an-existing-server-administrator"></a>Použití existujícího správce serveru

Pokud máte v úmyslu použít stávajícího uživatele správce serveru (SA) k prohledávání místního systému SQL Server, zajistěte následující:

1. `sa` není typ ověřování systému Windows.

2. Uživatel na úrovni serveru, který plánujete použít, musí mít role serveru Public a sysadmin. Můžete to ověřit tak, že přejdete na SQL Server Management Studio (SSMS), připojíte se k serveru, přejdete na zabezpečení a vyberete přihlašovací údaje, které plánujete použít, kliknete pravým tlačítkem na **vlastnosti** a pak vyberete **role serveru**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="Přihlášení na úrovni serveru.":::

3. Databáze jsou namapovány na uživatele, který má alespoň přístup na úrovni db_datareader v každé databázi.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping-sa.png" alt-text="mapování uživatelů pro SA":::

#### <a name="creating-a-new-login-and-user"></a>Vytvoření nového přihlášení a uživatele

Pokud chcete vytvořit nové přihlášení a uživatele, aby mohli kontrolovat SQL Server, postupujte podle následujících kroků:

1. Přejděte na SQL Server Management Studio (SSMS), připojte se k serveru, přejděte na zabezpečení, klikněte pravým tlačítkem na přihlásit a vytvořte nové přihlášení. Ujistěte se, že jste vybrali ověřování SQL.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Vytvořte nové přihlášení a uživatele.":::

2. Na levém navigačním panelu vyberte role serveru a vyberte obě veřejné i sysadmin.

3. V levém navigačním panelu vyberte mapování uživatelů a vyberte všechny databáze na mapě.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="mapování uživatele.":::

4. Kliknutím na tlačítko OK uložte.

5. Znovu přejděte na uživatele, kterého jste vytvořili, tak, že kliknete pravým tlačítkem a vyberete **vlastnosti**. Zadejte nové heslo a potvrďte ho. Vyberte možnost zadat staré heslo a zadejte původní heslo. **Po vytvoření nového přihlašovacího jména je nutné změnit heslo.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="Změňte heslo.":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>Uložení přihlašovacího hesla SQL do trezoru klíčů a vytvoření přihlašovacích údajů v dosah

1. V Azure Portal přejděte do svého trezoru klíčů.
1. Vyberte **nastavení > tajných** kódů.
1. Vyberte **+ Generovat/importovat** a zadejte **název** a **hodnotu** jako *heslo* z přihlášení k SQL serveru.
1. Vyberte **vytvořit** a dokončete
1. Pokud váš Trezor klíčů ještě není připojený k dosah, bude potřeba [vytvořit nové připojení trezoru klíčů](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) .
1. Nakonec vytvořte pomocí **uživatelského jména** a **hesla** [nové přihlašovací údaje](manage-credentials.md#create-a-new-credential) , abyste mohli nastavit kontrolu.

## <a name="register-a-sql-server-data-source"></a>Registrace zdroje dat systému SQL Server

1. Přejděte k účtu dosah.

1. V části zdroje a kontrola v levém navigačním panelu vyberte **modul runtime integrace**. Ujistěte se, že je nastavený modul Integration runtime v místním prostředí. Pokud není nastavený, postupujte podle kroků uvedených [tady](manage-integration-runtimes.md) a vytvořte prostředí Integration runtime v místním prostředí pro vyhledávání na místním nebo virtuálním počítači Azure, který má přístup k vaší místní síti.

1. Výběr **zdrojů** na levém navigačním panelu

1. Vybrat **registraci**

1. Vyberte **SQL Server** a pak **pokračovat** .

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Nastavte zdroj dat SQL.":::

5. Zadejte popisný název a koncový bod serveru a pak vyberte **Dokončit** pro registraci zdroje dat. Pokud je například váš plně kvalifikovaný název domény SQL serveru **foobar.Database.Windows.NET**, zadejte *panel* jako koncový bod serveru.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)
