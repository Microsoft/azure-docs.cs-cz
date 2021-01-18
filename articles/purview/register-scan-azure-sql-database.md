---
title: Registrovat a kontrolovat Azure SQL Database
description: V tomto kurzu se dozvíte, jak kontrolovat Azure SQL Database
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 36781e7f975ee9d4a03cf899650701bf2d3940ac
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555946"
---
# <a name="register-and-scan-an-azure-sql-database"></a>Registrace a kontrola Azure SQL Database

Tento článek popisuje, jak v dosah zaregistrovat zdroj dat Azure SQL Database a jak na něm nastavovat kontrolu.

## <a name="supported-capabilities"></a>Podporované možnosti

Zdroj dat Azure SQL Database podporuje následující funkce:

- **Úplné a přírůstkové kontroly** pro zachycení metadat a klasifikace v Azure SQL Database.

-  Počet vydaných datových assetů pro kopírování ADF a aktivity toku dat

### <a name="known-limitations"></a>Známá omezení

Azure dosah nepodporuje kontrolu [zobrazení](/sql/relational-databases/views/views?view=azuresqldb-current&preserve-view=true) v Azure SQL Database.

## <a name="prerequisites"></a>Požadavky

1. Vytvořte nový účet dosah, pokud ho ještě nemáte.

1. Přístup k síti mezi účtem dosah a Azure SQL Database.


### <a name="set-up-authentication-for-a-scan"></a>Nastavení ověřování pro kontrolu

Ověřování pro kontrolu Azure SQL Database. Pokud potřebujete vytvořit nové ověřování, musíte [autorizovat přístup k databázi SQL Database](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage). Existují tři metody ověřování, které dnes dosah podporuje:

- Ověřování SQL
- Instanční objekt
- Spravovaná identita

#### <a name="sql-authentication"></a>Ověřování SQL

> [!Note]
> Nové přihlašovací údaje mohou vytvářet pouze přihlášení objektu zabezpečení na úrovni serveru (vytvořené procesem zřizování) nebo členové `loginmanager` databázové role v hlavní databázi. Po udělení oprávnění trvá přibližně **15 minut** , účet dosah by měl mít příslušná oprávnění, aby bylo možné kontrolovat prostředky.

Pokud nemáte tuto možnost k dispozici, můžete postupovat podle pokynů v tématu [Vytvoření přihlašovacích](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) údajů a vytvoření přihlašovacích údajů pro Azure SQL Database. Pro další kroky budete potřebovat **uživatelské jméno** a **heslo** .

1. V Azure Portal přejděte do svého trezoru klíčů.
1. Vyberte **nastavení > tajných** kódů.
1. Vyberte **+ Generovat/importovat** a zadejte **název** a **hodnotu** jako *heslo* z Azure SQL Database
1. Vyberte **vytvořit** a dokončete
1. Pokud váš Trezor klíčů ještě není připojený k dosah, bude potřeba [vytvořit nové připojení trezoru klíčů](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) .
1. Nakonec vytvořte pomocí **uživatelského jména** a **hesla** [nové přihlašovací údaje](manage-credentials.md#create-a-new-credential) , abyste mohli nastavit kontrolu.

#### <a name="service-principal-and-managed-identity"></a>Instanční objekt a spravovaná identita

K dispozici je několik kroků, které umožní dosah použít **spravovanou identitu** instančního objektu nebo dosah ke kontrole vašeho Azure SQL Database

   > [!Note]
   > Dosah bude pro kontrolu potřebovat **ID aplikace (klienta)** a **tajný klíč klienta** .

##### <a name="create-or-use-an-existing-service-principal"></a>Vytvoření nebo použití existujícího instančního objektu

> [!Note]
> Pokud používáte **spravovanou identitu** dosah, přeskočte tento krok.

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

- [Konfigurace a Správa ověřování Azure AD pomocí Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure)
- Pokud používáte spravovanou identitu, účet dosah má svou vlastní spravovanou identitu, která je v podstatě vaším dosah názvem. Musíte vytvořit uživatele služby Azure AD v Azure SQL Database s přesnou identitou spravované dosah nebo vlastním instančním objektem, a to pomocí kurzu [Vytvoření uživatele instančního objektu v Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial#create-the-service-principal-user-in-azure-sql-database). K identitě musíte přiřadit správné oprávnění (například `db_owner` nebo `db_datareader` ). Příklad syntaxe SQL pro vytvoření uživatele a udělení oprávnění:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [Username]
    GO
    ```

    > [!Note]
    > `Username`Je to váš vlastní instanční objekt nebo spravovaná identita dosah. Můžete si přečíst další informace o [rolích s pevnou databází a jejich schopnostech](/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15&preserve-view=true#fixed-database-roles).
    
##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Přidání instančního objektu do trezoru klíčů a přihlašovacích údajů služby dosah

> [!Note]
> Pokud plánujete použít **spravovanou identitu** dosah, můžete tento krok přeskočit, protože spravovaná identita výchozí dosah je už v přihlašovacích údajích **dosah-MSI** .

Je potřeba získat ID aplikace a tajný kód pro instanční objekt:

1. Přejděte k hlavnímu objektu služby v [Azure Portal](https://portal.azure.com)
1. Zkopírujte hodnoty **ID aplikace (klienta)** z **přehledu** a **tajného klíče klienta** z **certifikátů & tajných** kódů.
1. Přejděte do trezoru klíčů.
1. Vyberte **nastavení > tajných** kódů.
1. Vyberte **+ Generovat/importovat** a zadejte **název** a **hodnotu** jako **tajný klíč klienta** z instančního objektu.
1. Vyberte **vytvořit** a dokončete
1. Pokud váš Trezor klíčů ještě není připojený k dosah, bude potřeba [vytvořit nové připojení trezoru klíčů](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) .
1. Nakonec [vytvořte nové přihlašovací údaje](manage-credentials.md#create-a-new-credential) pomocí instančního objektu a nastavte kontrolu.

### <a name="firewall-settings"></a>Nastavení brány firewall

Váš databázový server musí umožňovat povolení připojení Azure. To umožní službě Azure dosah dosáhnout a připojit server. Můžete postupovat podle pokynů pro [připojení v rámci Azure](../azure-sql/database/firewall-configure.md#connections-from-inside-azure).

1. Přejít k vašemu databázovému účtu
1. Vyberte název serveru na stránce **Přehled** .
1. Vybrat **zabezpečení > brány firewall a virtuální sítě**
1. Vyberte **Ano** , pokud **chcete, aby služby a prostředky Azure měly přístup k tomuto serveru** .

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="Povolte službám a prostředkům Azure přístup k tomuto serveru." border="true":::
    
> [!Note]
> V současné době Azure dosah nepodporuje konfiguraci virtuální sítě. Proto nemůžete provést nastavení brány firewall založené na protokolu IP.

## <a name="register-an-azure-sql-database-data-source"></a>Registrace zdroje dat Azure SQL Database

Pokud chcete zaregistrovat nový Azure SQL Database v katalogu dat, udělejte toto:

1. Přejděte k účtu dosah.

1. Výběr **zdrojů** na levém navigačním panelu

1. Vybrat **registraci**

1. V nabídce **zdroje registru** vyberte **Azure SQL Database**. Vyberte **Pokračovat**.

:::image type="content" source="media/register-scan-azure-sql-database/register-new-data-source.png" alt-text="registrovat nový zdroj dat" border="true":::

Na obrazovce **Registrovat zdroje (Azure SQL Database)** udělejte toto:

1. Zadejte **název** , se kterým bude zdroj dat uveden v katalogu.
1. Vyberte, jak chcete odkazovat na požadovaný účet úložiště:
   1. Vyberte **z předplatného Azure**, vyberte příslušné předplatné z rozevíracího seznamu **předplatné Azure** a příslušný server z rozevíracího seznamu **název serveru** .
   1. Případně můžete vybrat možnost **zadat ručně** a zadat **název serveru**.
1. **Dokončete** registraci zdroje dat.

:::image type="content" source="media/register-scan-azure-sql-database/add-azure-sql-database.png" alt-text="Možnosti registrace zdrojů" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> Odstraněním kontroly nedojde k odstranění assetů z předchozích Azure SQL Databasech kontrol.

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)
