---
title: Jak kontrolovat Azure Storage BLOB
description: Naučte se kontrolovat službu Azure Blob Storage ve službě Azure dosah Data Catalog.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: b27b46c68d018d2ddf79d284b20cc05b51640891
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880637"
---
# <a name="register-and-scan-azure-blob-storage"></a>Registrace a Prohledávání Azure Blob Storage

Tento článek popisuje, jak zaregistrovat účet Azure Blob Storage v dosah a nastavit kontrolu.

## <a name="supported-capabilities"></a>Podporované možnosti

Azure Blob Storage podporuje úplné a přírůstkové kontroly pro zachycení metadat a schématu. Také klasifikuje data automaticky podle systémových a vlastních pravidel klasifikace.

## <a name="prerequisites"></a>Požadavky

- Před registrací zdrojů dat vytvořte účet Azure dosah. Další informace o vytvoření účtu dosah najdete v tématu [rychlý Start: vytvoření účtu Azure dosah](create-catalog-portal.md).
- Musíte být správcem zdroje dat služby Azure dosah.

## <a name="setting-up-authentication-for-a-scan"></a>Nastavení ověřování pro kontrolu

Existují tři způsoby, jak nastavit ověřování pro úložiště objektů BLOB v Azure:

- Spravovaná identita
- Klíč účtu
- Instanční objekt

### <a name="managed-identity-recommended"></a>Spravovaná identita (doporučeno)

Když zvolíte **spravovanou identitu**, musíte nejdřív účtu dosah poskytnout oprávnění ke kontrole zdroje dat:

1. Přejděte na svůj účet úložiště.
1. V navigační nabídce vlevo vyberte **Access Control (IAM)** . 
1. Vyberte **+ Přidat**.
1. Nastavte **roli** na **čtečku dat objektů BLOB úložiště** a v části **Vyberte** vstupní pole zadejte název svého účtu Azure dosah. Pak vyberte **Uložit** a udělte tomuto přiřazení role vašemu účtu dosah.

> [!Note]
> Další podrobnosti najdete v tématu Postup [autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory](../storage/common/storage-auth-aad.md)

### <a name="account-key"></a>Klíč účtu

Když je vybraná metoda ověřování **klíč účtu**, musíte získat přístupový klíč a uložit ho do trezoru klíčů:

1. Přejděte k účtu úložiště.
1. Vyberte **nastavení > přístupové klíče** .
1. Zkopírujte *klíč* a uložte ho někam pro další kroky.
1. Přejděte do trezoru klíčů.
1. Vyberte **nastavení > tajných** kódů.
1. Vyberte **+ Generovat/importovat** a jako *klíč* z účtu úložiště zadejte **název** a **hodnotu** .
1. Vyberte **vytvořit** a dokončete
1. Pokud váš Trezor klíčů ještě není připojený k dosah, bude potřeba [vytvořit nové připojení trezoru klíčů](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) .
1. Nakonec vytvořte pomocí klíče [nové přihlašovací údaje](manage-credentials.md#create-a-new-credential) , které nastaví kontrolu.

### <a name="service-principal"></a>Instanční objekt

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

Je potřeba získat ID aplikace a tajný kód pro instanční objekt:

1. Přejděte k hlavnímu objektu služby v [Azure Portal](https://portal.azure.com)
1. Zkopírujte hodnoty **ID aplikace (klienta)** z **přehledu** a **tajného klíče klienta** z **certifikátů & tajných** kódů.
1. Přejděte do trezoru klíčů.
1. Vyberte **nastavení > tajných** kódů.
1. Vyberte **+ Generovat/importovat** a zadejte **název** a **hodnotu** jako **tajný klíč klienta** z instančního objektu.
1. Vyberte **vytvořit** a dokončete
1. Pokud váš Trezor klíčů ještě není připojený k dosah, bude potřeba [vytvořit nové připojení trezoru klíčů](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) .
1. Nakonec [vytvořte nové přihlašovací údaje](manage-credentials.md#create-a-new-credential) pomocí instančního objektu a nastavte kontrolu.

#### <a name="granting-the-service-principal-access-to-your-blob-storage"></a>Udělení přístupu instančnímu objektu k úložišti objektů BLOB

1. Přejděte na svůj účet úložiště.
1. V navigační nabídce vlevo vyberte **Access Control (IAM)** . 
1. Vyberte **+ Přidat**.
1. Nastavte **roli** na **čtečku dat objektů BLOB úložiště** a v části **Vybrat** vstupní pole zadejte svůj hlavní název služby nebo ID objektu. Pak vyberte **Save (Uložit** ) a udělte tomuto přiřazení role instančnímu objektu služby.

## <a name="firewall-settings"></a>Nastavení brány firewall

> [!NOTE]
> Pokud máte pro účet úložiště povolenou bránu firewall, při nastavování kontroly musíte použít metodu **spravovaného ověřování identity** .

1. Přejít do svého účtu úložiště v [Azure Portal](https://portal.azure.com)
1. Přejděte na **nastavení > sítě** a
1. Vyberte **vybrané sítě** v části **Povolení přístupu z**
1. V části **Brána firewall** vyberte možnost **povoluje přístup k tomuto účtu úložiště důvěryhodným službám Microsoftu** a stiskněte **Uložit** .

:::image type="content" source="./media/register-scan-azure-blob-storage-source/firewall-setting.png" alt-text="Snímek obrazovky s nastavením brány firewall":::

## <a name="register-an-azure-blob-storage-account"></a>Registrace účtu Azure Blob Storage

Pokud chcete zaregistrovat nový účet BLOB v katalogu dat, udělejte toto:

1. Přejděte k účtu dosah.
1. Výběr **zdrojů** na levém navigačním panelu
1. Vybrat **registraci**
1. V nabídce **zdroje registru** vyberte **Azure Blob Storage**
1. Vyberte **Pokračovat**.

Na obrazovce **Registrovat zdroje (Azure Blob Storage)** udělejte toto:

1. Zadejte **název** , se kterým bude zdroj dat uveden v katalogu. 
1. Výběr předplatného pro filtrování účtů úložiště
1. Vyberte účet úložiště.
1. Vyberte kolekci nebo vytvořte novou (volitelné).
1. **Dokončete** registraci zdroje dat.

:::image type="content" source="media/register-scan-azure-blob-storage-source/register-sources.png" alt-text="Možnosti registrace zdrojů" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)