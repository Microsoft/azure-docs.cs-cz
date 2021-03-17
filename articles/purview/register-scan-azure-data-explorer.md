---
title: Jak kontrolovat Azure Průzkumník dat
description: V této příručce najdete popis postupu při kontrole Průzkumník dat Azure.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 7adc7f568fb82692f2c96f610575076e397bd99c
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896098"
---
# <a name="register-and-scan-azure-data-explorer"></a>Registrace a kontrola Průzkumník dat v Azure

Tento článek popisuje, jak zaregistrovat účet Azure Průzkumník dat v Azure dosah a nastavit kontrolu.

## <a name="supported-capabilities"></a>Podporované možnosti

Azure Průzkumník dat podporuje úplné a přírůstkové kontroly pro zachycení metadat a schématu. Kontroly také automaticky klasifikují data na základě systémových a vlastních pravidel klasifikace.

## <a name="prerequisites"></a>Požadavky

- Před registrací zdrojů dat vytvořte účet Azure dosah. Další informace o vytvoření účtu dosah najdete v tématu [rychlý Start: vytvoření účtu Azure dosah](create-catalog-portal.md).
- Musíte být správcem zdroje dat služby Azure dosah.

## <a name="setting-up-authentication-for-a-scan"></a>Nastavení ověřování pro kontrolu

Existuje pouze jeden způsob, jak nastavit ověřování pro Průzkumníka Azure data:

- Instanční objekt

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

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>Udělení přístupu instančnímu objektu vaší instanci Azure Data Exploreru

1. Přejděte na Azure Portal. Pak přejděte do instance Azure Data Exploreru.

1. Přidejte instanční objekt k roli **AllDatabasesViewer** na kartě **oprávnění** , jak je znázorněno na následujícím snímku obrazovky.

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="Snímek obrazovky s přidáním instančního objektu do oprávnění" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Registrace účtu Azure Průzkumník dat

Pokud chcete zaregistrovat nový účet Azure Průzkumník dat (Kusto) v katalogu dat, udělejte toto:

1. Přejděte k účtu dosah.
1. Výběr **zdrojů** na levém navigačním panelu
1. Vybrat **registraci**
1. V nabídce **zdroje registru** vyberte **Azure Průzkumník dat**
1. Vyberte **Pokračovat**.

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="registrovat nový zdroj dat" border="true":::

Na obrazovce **Registrovat zdroje (Azure Průzkumník dat (Kusto))** udělejte toto:

1. Zadejte **název** , se kterým bude zdroj dat uveden v katalogu.
1. Vyberte, jak chcete odkazovat na požadovaný účet úložiště:
   1. Vyberte **z předplatného Azure**, vyberte příslušné předplatné z rozevíracího seznamu **předplatné Azure** a příslušný cluster z rozevíracího seznamu **clusteru** .
   1. Případně můžete vybrat možnost **zadat ručně** a zadat koncový bod služby (URL).
1. **Dokončete** registraci zdroje dat.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="Možnosti registrace zdrojů" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans-azure-data-explorer.md)]

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)
