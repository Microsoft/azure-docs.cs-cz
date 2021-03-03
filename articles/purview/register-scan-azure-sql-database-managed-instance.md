---
title: Registrace a kontrola Azure SQL Database spravované instance
description: V tomto kurzu se dozvíte, jak zkontrolovat Azure SQL Database spravovanou instanci.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: a30980ba61a1dfec918dce1a55e78f1be2a36dd7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677872"
---
# <a name="register-and-scan-an-azure-sql-database-managed-instance"></a>Registrace a kontrola Azure SQL Database spravované instance

Tento článek popisuje, jak v dosah zaregistrovat zdroj dat spravované instance Azure SQL Database a jak na něm nastavovat kontrolu.

## <a name="supported-capabilities"></a>Podporované možnosti

Zdroj dat spravované instance Azure SQL Database podporuje následující funkce:

- **Úplné a přírůstkové kontroly** pro zachycení metadat a klasifikace v Azure SQL Database Managed instance.

-  Počet vydaných datových assetů pro kopírování ADF a aktivity toku dat

### <a name="known-limitations"></a>Známá omezení

Azure dosah nepodporuje kontrolu [zobrazení](/sql/relational-databases/views/views?view=azuresqldb-mi-current&preserve-view=true) ve spravované instanci Azure SQL.

## <a name="prerequisites"></a>Požadavky

- Vytvořte nový účet dosah, pokud ho ještě nemáte.

- [Konfigurace veřejného koncového bodu ve spravované instanci Azure SQL](../azure-sql/managed-instance/public-endpoint-configure.md)
    > [!Note]
    > Vaše organizace musí mít možnost umožnit veřejný koncový bod jako **soukromý koncový bod** , který dosah ještě nepodporuje. Pokud použijete privátní koncový bod, kontrola nebude úspěšná.

### <a name="setting-up-authentication-for-a-scan"></a>Nastavení ověřování pro kontrolu

Ověřování pro kontrolu Azure SQL Database spravované instance. Pokud potřebujete vytvořit nové ověřování, musíte [autorizovat přístup k databázi pro SQL Database spravovanou instanci](../azure-sql/database/logins-create-manage.md). Existují tři metody ověřování, které dnes dosah podporuje:

- Ověřování SQL
- Instanční objekt
- Spravovaná identita

#### <a name="sql-authentication"></a>Ověřování SQL

> [!Note]
> Nové přihlašovací údaje mohou vytvářet pouze přihlášení objektu zabezpečení na úrovni serveru (vytvořené procesem zřizování) nebo členové `loginmanager` databázové role v hlavní databázi. Po udělení oprávnění trvá přibližně **15 minut** , účet dosah by měl mít příslušná oprávnění, aby bylo možné kontrolovat prostředky.

Pokud nemáte k dispozici, můžete postupovat podle pokynů v tématu [Vytvoření přihlašovacích](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) údajů a vytvoření přihlašovacích údajů pro Azure SQL Database spravovanou instanci. Pro další kroky budete potřebovat **uživatelské jméno** a **heslo** .

1. V Azure Portal přejděte do svého trezoru klíčů.
1. Vyberte **nastavení > tajných** kódů.
1. Vyberte **+ Generovat/importovat** a zadejte **název** a **hodnotu** jako *heslo* z spravované instance Azure SQL Database.
1. Vyberte **vytvořit** a dokončete
1. Pokud váš Trezor klíčů ještě není připojený k dosah, bude potřeba [vytvořit nové připojení trezoru klíčů](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) .
1. Nakonec vytvořte pomocí **uživatelského jména** a **hesla** [nové přihlašovací údaje](manage-credentials.md#create-a-new-credential) , abyste mohli nastavit kontrolu.

#### <a name="service-principal-and-managed-identity"></a>Instanční objekt a spravovaná identita

Existuje několik kroků, které umožní dosah použít instanční objekt k prověřování Azure SQL Database spravované instance.

##### <a name="create-or-use-an-existing-service-principal"></a>Vytvoření nebo použití existujícího instančního objektu

> [!Note]
> Tento krok přeskočte, pokud používáte **spravovanou identitu** .

Chcete-li použít instanční objekt, můžete použít existující nebo vytvořit nový. 

> [!Note]
> Pokud potřebujete vytvořit nový instanční objekt, postupujte podle těchto kroků:
> 1. Přejděte na [Azure Portal](https://portal.azure.com).
> 1. V nabídce na levé straně vyberte **Azure Active Directory** .
> 1. Vyberte **Registrace aplikací**.
> 1. Vyberte **+ Registrace nové aplikace**.
> 1. Zadejte název **aplikace** (hlavní název služby).
> 1. Vyberte **účty jenom v tomto organizačním adresáři**.
> 1. Jako identifikátor URI přesměrování vyberte **Web** a zadejte libovolnou adresu URL, kterou chcete. nemusí být skutečná nebo práce.
> 1. Pak vyberte **Register** (Registrovat).

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Konfigurace ověřování Azure AD v databázovém účtu

Objekt služby nebo spravovaná identita musí mít oprávnění k získání metadat pro databázi, schémata a tabulky. Musí být také schopné dotazovat tabulky na ukázku pro klasifikaci.
- [Konfigurace a Správa ověřování Azure AD pomocí Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Pomocí požadavků a kurzu Vytvoření uživatele, který je [namapován na identity Azure AD](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities) , vytvořte uživatele Azure AD v Azure SQL Database Managed instance.
- Přiřadit `db_owner` (**doporučeno**) oprávnění k identitě

##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Přidání instančního objektu do trezoru klíčů a přihlašovacích údajů služby dosah

> [!Note]
> Pokud plánujete použít **spravovanou identitu**, můžete tento krok přeskočit, protože výchozí identita dosah je už v **dosah – MSI** .

Je potřeba získat ID aplikace a tajný kód pro instanční objekt:

1. Přejděte k hlavnímu objektu služby v [Azure Portal](https://portal.azure.com)
1. Zkopírujte hodnoty **ID aplikace (klienta)** z **přehledu** a **tajného klíče klienta** z **certifikátů & tajných** kódů.
1. Přejděte do trezoru klíčů.
1. Vyberte **nastavení > tajných** kódů.
1. Vyberte **+ Generovat/importovat** a zadejte **název** a **hodnotu** jako **tajný klíč klienta** z instančního objektu.
1. Vyberte **vytvořit** a dokončete
1. Pokud váš Trezor klíčů ještě není připojený k dosah, bude potřeba [vytvořit nové připojení trezoru klíčů](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) .
1. Nakonec [vytvořte nové přihlašovací údaje](manage-credentials.md#create-a-new-credential) pomocí instančního objektu a nastavte kontrolu.

## <a name="register-an-azure-sql-database-managed-instance-data-source"></a>Registrace zdroje dat spravované instance Azure SQL Database

1. Přejděte k účtu dosah.

1. Výběr **zdrojů** na levém navigačním panelu

1. Vybrat **registraci**

1. Vyberte **Azure SQL Database spravovaná instance** a potom **pokračujte** .

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="Nastavení zdroje dat SQL":::

1. Vyberte **z předplatného Azure**, vyberte příslušné předplatné z rozevíracího seznamu **předplatné Azure** a příslušný server z rozevíracího seznamu **název serveru** .

1. Zadejte plně kvalifikovaný název domény a **číslo portu** **veřejného koncového bodu** . Pak vyberte **Dokončit** pro registraci zdroje dat.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Přidat Azure SQL Database spravovanou instanci":::

    Například `foobar.public.123.database.windows.net,3342`

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> Odstraněním kontroly nedojde k odstranění assetů z předchozích Azure SQL Databasech kontrol spravovaných instancí.

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)