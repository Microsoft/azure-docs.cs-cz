---
title: Registrovat a kontrolovat Azure Data Lake Storage (ADLS) Gen2
description: V tomto kurzu se dozvíte, jak kontrolovat Azure Data Lake Storage Gen2.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 4b7f71b5405708cc1988fafa5ca9c4628fe0d80b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98882395"
---
# <a name="register-and-scan-azure-data-lake-storage-gen2"></a>Registrovat a kontrolovat Azure Data Lake Storage Gen2

Tento článek popisuje, jak zaregistrovat Azure Data Lake Storage Gen2 jako zdroj dat ve službě Azure dosah a nastavit kontrolu.

## <a name="supported-capabilities"></a>Podporované funkce

Zdroj dat Azure Data Lake Storage Gen2 podporuje následující funkce:

- **Úplné a přírůstkové kontroly** pro zachycení metadat a klasifikace v Azure Data Lake Storage Gen2

- Počet **řádků** mezi datovými prostředky pro aktivity kopírování a toku dat ADF

## <a name="prerequisites"></a>Požadavky

Před registrací zdrojů dat vytvořte účet Azure dosah. Další informace o vytvoření účtu dosah najdete v tématu [rychlý Start: vytvoření účtu Azure dosah](create-catalog-portal.md).

### <a name="setting-up-authentication-for-a-scan"></a>Nastavení ověřování pro kontrolu

Pro Azure Data Lake Storage Gen2 jsou podporovány následující metody ověřování:

- Spravovaná identita
- Instanční objekt
- Klíč účtu

#### <a name="managed-identity-recommended"></a>Spravovaná identita (doporučeno)

Když zvolíte **spravovanou identitu**, musíte nejdřív účtu dosah poskytnout oprávnění ke kontrole zdroje dat:

1. Přejděte do svého účtu úložiště ADLS Gen2.
1. V navigační nabídce vlevo vyberte **Access Control (IAM)** . 
1. Vyberte **+ Přidat**.
1. Nastavte **roli** na **čtečku dat objektů BLOB úložiště** a v části **Vyberte** vstupní pole zadejte název svého účtu Azure dosah. Pak vyberte **Uložit** a udělte tomuto přiřazení role vašemu účtu dosah.

> [!Note]
> Další podrobnosti najdete v tématu Postup [autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory](../storage/common/storage-auth-aad.md)

#### <a name="account-key"></a>Klíč účtu

Když je vybraná metoda ověřování **klíč účtu**, musíte získat přístupový klíč a uložit ho do trezoru klíčů:

1. Přejděte k účtu úložiště ADLS Gne2
1. Vyberte **nastavení > přístupové klíče** .
1. Zkopírujte *klíč* a uložte ho někam pro další kroky.
1. Přejděte do trezoru klíčů.
1. Vyberte **nastavení > tajných** kódů.
1. Vyberte **+ Generovat/importovat** a jako *klíč* z účtu úložiště zadejte **název** a **hodnotu** .
1. Vyberte **vytvořit** a dokončete
1. Pokud váš Trezor klíčů ještě není připojený k dosah, bude potřeba [vytvořit nové připojení trezoru klíčů](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) .
1. Nakonec vytvořte pomocí klíče [nové přihlašovací údaje](manage-credentials.md#create-a-new-credential) , které nastaví kontrolu.

#### <a name="service-principal"></a>Instanční objekt

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

##### <a name="granting-the-service-principal-access-to-your-adls-gen2-account"></a>Udělování přístupu instančního objektu k účtu služby ADLS Gen2

1. Přejděte na svůj účet úložiště.
1. V navigační nabídce vlevo vyberte **Access Control (IAM)** . 
1. Vyberte **+ Přidat**.
1. Nastavte **roli** na **čtečku dat objektů BLOB úložiště** a v části **Vybrat** vstupní pole zadejte svůj hlavní název služby nebo ID objektu. Pak vyberte **Save (Uložit** ) a udělte tomuto přiřazení role instančnímu objektu služby.
### <a name="firewall-settings"></a>Nastavení brány firewall

> [!NOTE]
> Pokud máte pro účet úložiště povolenou bránu firewall, při nastavování kontroly musíte použít metodu **spravovaného ověřování identity** .

1. Přejít do svého účtu úložiště ASLS Gen2 v [Azure Portal](https://portal.azure.com)
1. Přejděte na **nastavení > sítě** a
1. Vyberte **vybrané sítě** v části **Povolení přístupu z**
1. V části **výjimky** vyberte možnost **povoluje přístup k tomuto účtu úložiště důvěryhodným službám Microsoftu** a stiskněte **Uložit** .

:::image type="content" source="./media/register-scan-adls-gen2/firewall-setting.png" alt-text="Snímek obrazovky s nastavením brány firewall":::

## <a name="register-azure-data-lake-storage-gen2-data-source"></a>Zaregistrovat Azure Data Lake Storage Gen2 zdroj dat

Pokud chcete zaregistrovat nový účet ADLS Gen2 v katalogu dat, udělejte toto:

1. Přejděte k účtu dosah.
2. Výběr **zdrojů** na levém navigačním panelu
3. Vybrat **registraci**
4. V nabídce **zdroje registru** vyberte **Azure Data Lake Storage Gen2**
5. Vyberte **Pokračovat**.

Na obrazovce **Registrovat zdroje (Azure Data Lake Storage Gen2)** udělejte toto:

1. Zadejte **název** , se kterým bude zdroj dat uveden v katalogu.
2. Výběr předplatného pro filtrování účtů úložiště
3. Vyberte účet úložiště.
4. Vyberte kolekci nebo vytvořte novou (volitelné).
5. **Dokončete** registraci zdroje dat.

:::image type="content" source="media/register-scan-adls-gen2/register-sources.png" alt-text="Možnosti registrace zdrojů" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)