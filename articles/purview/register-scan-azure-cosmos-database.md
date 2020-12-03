---
title: Jak kontrolovat databázi Azure Cosmos (SQL API)
description: V této příručce najdete popis postupu při kontrole databáze Azure Cosmos (SQL API).
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: e1d2035b787380d9b93943b92fbe81c09fc6a527
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553171"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Registrace a kontrola Azure Cosmos Database (SQL API)

Tento článek popisuje, jak zaregistrovat účet Azure Cosmos Database (SQL API) v Azure dosah a nastavit kontrolu.

## <a name="supported-capabilities"></a>Podporované možnosti

Azure Cosmos Database (SQL API) podporuje úplné a přírůstkové kontroly pro zachycení metadat a schématu. Kontroly také automaticky klasifikují data na základě systémových a vlastních pravidel klasifikace.

## <a name="prerequisites"></a>Předpoklady

- Před registrací zdrojů dat vytvořte účet Azure dosah. Další informace o vytvoření účtu dosah najdete v tématu [rychlý Start: vytvoření účtu Azure dosah](create-catalog-portal.md).
- Musíte být správcem zdroje dat služby Azure dosah.

## <a name="setting-up-authentication-for-a-scan"></a>Nastavení ověřování pro kontrolu

Existuje pouze jeden způsob, jak nastavit ověřování pro databázi Azure Cosmos (SQL API):

- Klíč účtu
 
### <a name="account-key"></a>Klíč účtu

Když je vybraná metoda ověřování **klíč účtu**, musíte získat přístupový klíč a uložit ho do trezoru klíčů:

1. Přejděte na účet Cosmos DB v Azure Portal 
1. Vybrat **Nastavení**  >  **klíčů** 
1. Zkopírujte *klíč* a uložte ho někam pro další kroky.
1. Přejděte do trezoru klíčů.
1. Vyberte **nastavení > tajných** kódů.
1. Vyberte **+ Generovat/importovat** a jako *klíč* z účtu úložiště zadejte **název** a **hodnotu** .
1. Vyberte **vytvořit** a dokončete
1. Pokud váš Trezor klíčů ještě není připojený k dosah, bude potřeba [vytvořit nové připojení trezoru klíčů](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) .
1. Nakonec vytvořte pomocí klíče [nové přihlašovací údaje](manage-credentials.md#create-a-new-credential) , které nastaví kontrolu.

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Registrace účtu Azure Cosmos Database (SQL API)

Pokud chcete zaregistrovat nový účet Azure Cosmos Database (SQL API) v katalogu dat, udělejte toto:

1. Přejděte k účtu dosah.
1. Výběr **zdrojů** na levém navigačním panelu
1. Vybrat **registraci**
1. V nabídce **zdroje registru** vyberte možnost **Azure Cosmos dB (SQL API)** .
1. Vyberte **Pokračovat**.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="registrovat nový zdroj dat" border="true":::

Na obrazovce **Registrovat zdroje (Azure Cosmos dB (SQL API))** udělejte toto:

1. Zadejte **název** , se kterým bude zdroj dat uveden v katalogu.
1. Vyberte, jak chcete odkazovat na požadovaný účet úložiště:
   1. Vyberte **z předplatného Azure**, vyberte příslušné předplatné z rozevíracího seznamu **předplatné Azure** a příslušný účet cosmosDB z rozevíracího seznamu **název účtu Cosmos DB** .
   1. Případně můžete vybrat možnost **zadat ručně** a zadat koncový bod služby (URL).
1. **Dokončete** registraci zdroje dat.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="Možnosti registrace zdrojů" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)
