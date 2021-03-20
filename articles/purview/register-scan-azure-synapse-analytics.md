---
title: Jak kontrolovat Azure synapse Analytics
description: V této příručce najdete podrobné informace o tom, jak kontrolovat Azure synapse Analytics.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: 3111b3a102abd923169cf655f1d71e79b19f7d5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598129"
---
# <a name="register-and-scan-azure-synapse-analytics"></a>Registrace a kontrola Azure synapse Analytics

Tento článek popisuje, jak zaregistrovat a zkontrolovat instanci Azure synapse Analytics (dřív SQL DW) v dosah.

## <a name="supported-capabilities"></a>Podporované funkce

Azure synapse Analytics (dřív SQL DW) podporuje úplné a přírůstkové kontroly pro zachycení metadat a schématu. Kontroly také automaticky klasifikují data na základě systémových a vlastních pravidel klasifikace.

### <a name="known-limitations"></a>Známá omezení

Azure dosah nepodporuje kontrolu [zobrazení](/sql/relational-databases/views/views?view=azure-sqldw-latest&preserve-view=true) v Azure synapse Analytics.

## <a name="prerequisites"></a>Předpoklady

- Před registrací zdrojů dat vytvořte účet Azure dosah. Další informace o vytvoření účtu dosah najdete v tématu [rychlý Start: vytvoření účtu Azure dosah](create-catalog-portal.md).
- Musíte být správcem zdroje dat služby Azure dosah.
- Přístup k síti mezi účtem dosah a službou Azure synapse Analytics.
 
## <a name="setting-up-authentication-for-a-scan"></a>Nastavení ověřování pro kontrolu

Existují tři způsoby, jak nastavit ověřování pro Azure synapse Analytics:

- Spravovaná identita
- Ověřování SQL
- Instanční objekt

    > [!Note]
    > Nové přihlašovací údaje mohou vytvářet pouze přihlášení objektu zabezpečení na úrovni serveru (vytvořené procesem zřizování) nebo členové `loginmanager` databázové role v hlavní databázi. Po udělení oprávnění trvá přibližně 15 minut, účet dosah by měl mít příslušná oprávnění, aby bylo možné kontrolovat prostředky.

### <a name="managed-identity-recommended"></a>Spravovaná identita (doporučeno) 
   
Váš účet dosah má svou vlastní spravovanou identitu, která je v podstatě vaším dosah názvem. Musíte vytvořit uživatele služby Azure AD ve službě Azure synapse Analytics (dříve SQL DW) s přesným názvem spravované identity dosah podle požadavků a kurzu [vytváření uživatelů Azure AD pomocí aplikací Azure AD](../azure-sql/database/authentication-aad-service-principal-tutorial.md).

Příklad syntaxe SQL pro vytvoření uživatele a udělení oprávnění:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [PurviewManagedIdentity]
GO
```

Ověřování musí mít oprávnění k získání metadat pro databázi, schémata a tabulky. Musí být také schopné dotazovat tabulky na ukázku pro klasifikaci. Doporučením je přiřazení `db_owner` oprávnění k identitě.

### <a name="service-principal"></a>Instanční objekt

Chcete-li použít ověřování instančního objektu pro kontroly, můžete použít existující nebo vytvořit nový. 

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

Je potřeba získat ID aplikace a tajný kód pro instanční objekt:

1. Přejděte k hlavnímu objektu služby v [Azure Portal](https://portal.azure.com)
1. Zkopírujte hodnoty **ID aplikace (klienta)** z **přehledu** a **tajného klíče klienta** z **certifikátů & tajných** kódů.
1. Přejděte do trezoru klíčů.
1. Vyberte **nastavení > tajných** kódů.
1. Vyberte **+ Generovat/importovat** a zadejte **název** a **hodnotu** jako **tajný klíč klienta** z instančního objektu.
1. Vyberte **vytvořit** a dokončete
1. Pokud váš Trezor klíčů ještě není připojený k dosah, bude potřeba [vytvořit nové připojení trezoru klíčů](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) .
1. Nakonec [vytvořte nové přihlašovací údaje](manage-credentials.md#create-a-new-credential) pomocí instančního objektu a nastavte kontrolu. 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>Udělení přístupu instančního objektu ke službě Azure synapse Analytics (dříve SQL DW)

Kromě toho musíte také vytvořit uživatele služby Azure AD ve službě Azure synapse Analytics podle požadavků a kurzu [vytváření uživatelů Azure AD pomocí aplikací Azure AD](../azure-sql/database/authentication-aad-service-principal-tutorial.md). Příklad syntaxe SQL pro vytvoření uživatele a udělení oprávnění:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [ServicePrincipalName]
GO
```

> [!Note]
> Dosah bude pro kontrolu potřebovat **ID aplikace (klienta)** a **tajný klíč klienta** .

### <a name="sql-authentication"></a>Ověřování SQL

Můžete postupovat podle pokynů v tématu [Vytvoření přihlašovacích](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1) údajů a vytvoření přihlašovacích údajů pro Azure synapse Analytics (dřív SQL DW), pokud ho ještě nemáte.

Když je vybraná metoda ověřování **SQL**, budete muset získat heslo a uložit ho do trezoru klíčů:

1. Získání hesla pro přihlášení SQL
1. Přejděte do trezoru klíčů.
1. Vyberte **nastavení > tajných** kódů.
1. Vyberte **+ Generovat/importovat** a zadejte **název** a **hodnotu** jako *heslo* pro vaše přihlášení SQL.
1. Vyberte **vytvořit** a dokončete
1. Pokud váš Trezor klíčů ještě není připojený k dosah, bude potřeba [vytvořit nové připojení trezoru klíčů](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) .
1. Nakonec vytvořte pomocí klíče [nové přihlašovací údaje](manage-credentials.md#create-a-new-credential) , které nastaví kontrolu.

## <a name="register-an-azure-synapse-analytics-instance-formerly-sql-dw"></a>Registrace instance Azure synapse Analytics (dříve SQL DW)

Pokud chcete zaregistrovat nový server Azure synapse Analytics v Data Catalog, udělejte toto:

1. Přejděte k účtu dosah.
1. Výběr **zdrojů** na levém navigačním panelu
1. Vybrat **registraci**
1. V **seznamu registrovat zdroje** vyberte **Azure synapse Analytics (dřív SQL DW)** .
1. Vyberte **Pokračovat**.

Na obrazovce **Registrovat zdroje (Azure synapse Analytics)** udělejte toto:

1. Zadejte **název** , se kterým bude zdroj dat uveden v katalogu.
1. Vyberte způsob, jakým chcete Ukázat na požadované logické SQL Server:
   1. Vyberte **z předplatného Azure**, vyberte příslušné předplatné z rozevíracího seznamu **předplatné Azure** a příslušný server z rozevíracího seznamu **název serveru** .
   1. Případně můžete vybrat možnost **zadat ručně** a zadat **název serveru**.
1. **Dokončete** registraci zdroje dat.

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="Možnosti registrace zdrojů" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)
