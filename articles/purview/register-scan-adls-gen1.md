---
title: Registrovat a kontrolovat Azure Data Lake Storage (ADLS) Gen1
description: V tomto kurzu se dozvíte, jak kontrolovat data z Azure Data Lake Storage Gen1 do Azure dosah.
author: shsandeep123
ms.author: sandeepshah
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: a831681f892de9f6aae50fa9a2fcf71e883fe6ba
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693709"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>Registrovat a kontrolovat Azure Data Lake Storage Gen1

Tento článek popisuje, jak zaregistrovat Azure Data Lake Storage Gen1 jako zdroj dat ve službě Azure dosah a nastavit pro něj kontrolu.

> [!Note]
> Platforma Azure Data Lake Storage Gen2 je teď obecně dostupná. Doporučujeme začít ji používat ještě dnes. Další informace najdete na [stránce produktu](https://azure.microsoft.com/services/storage/data-lake-storage/).

## <a name="supported-capabilities"></a>Podporované možnosti

Zdroj dat Azure Data Lake Storage Gen1 podporuje následující funkce:

- **Úplné a přírůstkové kontroly** pro zachycení metadat a klasifikace v Azure Data Lake Storage Gen1

- Počet **řádků** mezi datovými prostředky pro aktivity kopírování a toku dat ADF

## <a name="prerequisites"></a>Požadavky

- Před registrací zdrojů dat vytvořte účet Azure dosah. Další informace o vytvoření účtu dosah najdete v tématu [rychlý Start: vytvoření účtu Azure dosah](create-catalog-portal.md).
- Musíte být správcem zdroje dat služby Azure dosah.

## <a name="setting-up-authentication-for-a-scan"></a>Nastavení ověřování pro kontrolu

Pro Azure Data Lake Storage Gen1 jsou podporovány následující metody ověřování:

- Spravovaná identita
- Instanční objekt

### <a name="managed-identity-recommended"></a>Spravovaná identita (doporučeno)

Pro usnadnění a zabezpečení můžete použít dosah MSI k ověřování s úložištěm dat.

Chcete-li nastavit kontrolu pomocí MSI Data Catalog, musíte nejdřív účtu dosah udělit oprávnění ke skenování zdroje dat. Tento krok se musí provést *předtím, než* nastavíte kontrolu nebo dojde k selhání kontroly.

#### <a name="adding-the-data-catalog-msi-to-an-azure-data-lake-storage-gen1-account"></a>Přidání Data Catalog MSI do účtu Azure Data Lake Storage Gen1

MSI katalogu můžete přidat v předplatném, skupině prostředků nebo na úrovni prostředků v závislosti na tom, k čemu má oprávnění k prohledávání.

> [!Note]
> Abyste mohli přidat spravovanou identitu do prostředku Azure, musíte být vlastníkem předplatného.

1. V [Azure Portal](https://portal.azure.com)Najděte buď předplatné, skupinu prostředků, nebo prostředek (například účet úložiště Azure Data Lake Storage Gen1), který chcete v katalogu dovolit zkontrolovat.

2. Klikněte na **Přehled** a pak vyberte **Průzkumník dat** .

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Volba řízení přístupu":::

3. Kliknutí na **přístup** v horním navigačním panelu

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Kliknout na přístup":::

4. Klikněte na **Přidat**. Přidejte **katalog dosah** do výběru vybrat uživatele nebo skupinu. Vyberte oprávnění **číst** a **Spustit** . Nezapomeňte zvolit **tuto složku a všechny podřízené položky** v možnosti Přidat do, jak je znázorněno na následujícím snímku obrazovky, a klikněte na **OK** 
    :::image type="content" source="./media/register-scan-adls-gen1/managed-instance-authentication.png" alt-text="Podrobnosti ověřování MSI"::: .

5. Pokud váš Trezor klíčů ještě není připojený k dosah, bude potřeba [vytvořit nové připojení trezoru klíčů](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Nakonec [vytvořte nové přihlašovací údaje](manage-credentials.md#create-a-new-credential) pomocí instančního objektu a nastavte kontrolu.
> [!Note]
> Po přidání MSI katalogu ve zdroji dat počkejte až 15 minut, než se oprávnění rozšíří před nastavením kontroly.

Do 15 minut by měl katalog mít příslušná oprávnění, aby bylo možné prověřit prostředky.

### <a name="service-principal"></a>Instanční objekt

Chcete-li použít instanční objekt, musíte nejprve vytvořit jednu z následujících kroků:

1. Přejděte na [Azure Portal](https://portal.azure.com).

2. V nabídce na levé straně vyberte **Azure Active Directory** .

3. Vyberte **Registrace aplikací**.

4. Vyberte **+ Registrace nové aplikace**.

5. Zadejte název **aplikace** (hlavní název služby).

6. Vyberte **účty jenom v tomto organizačním adresáři**.

7. Jako **identifikátor URI přesměrování** vyberte **Web** a zadejte libovolnou adresu URL, kterou chcete. nemusí být skutečná nebo práce.

8. Pak vyberte **Register** (Registrovat).

9. Zkopírujte hodnoty ze zobrazovaného jména i z ID aplikace.

#### <a name="adding-the-data-catalog-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>Přidání Data Catalog instančního objektu k účtu Azure Data Lake Storage Gen1
1. V [Azure Portal](https://portal.azure.com)Najděte buď předplatné, skupinu prostředků, nebo prostředek (například účet úložiště Azure Data Lake Storage Gen1), který chcete v katalogu dovolit zkontrolovat.

2. Klikněte na **Přehled** a pak vyberte **Průzkumník dat** .

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Volba řízení přístupu":::

3. Kliknutí na **přístup** v horním navigačním panelu

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Kliknout na přístup":::

4. Klikněte na **Přidat**. Přidejte **aplikaci instančního objektu** do výběru vybrat uživatele nebo skupinu. Vyberte oprávnění **číst** a **Spustit** . Nezapomeňte zvolit **tuto složku a všechny podřízené položky** v možnosti Přidat k, jak je znázorněno na následujícím snímku obrazovky, a kliknout na tlačítko **OK** 
    :::image type="content" source="./media/register-scan-adls-gen1/service-principal-authentication.png" alt-text="Podrobnosti ověřování instančního objektu"::: .

5. Pokud váš Trezor klíčů ještě není připojený k dosah, bude potřeba [vytvořit nové připojení trezoru klíčů](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Nakonec vytvořte pomocí instančního objektu [nové přihlašovací údaje](manage-credentials.md#create-a-new-credential) , aby se prověřování nastavila.

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>Zaregistrovat Azure Data Lake Storage Gen1 zdroj dat

Pokud chcete zaregistrovat nový účet ADLS Gen1 v katalogu dat, udělejte toto:

1. Přejděte do dosah Data Catalog.
2. V levém navigačním panelu vyberte **zdroje** .
3. Vybrat **registraci**
4. V nabídce **zdroje registru** vyberte **Azure Data Lake Storage Gen1**. 
5. Vyberte **Pokračovat**.

Na obrazovce registrovat zdroje (Azure Data Lake Storage Gen1) udělejte toto:

1. Zadejte **název** , se kterým bude zdroj dat uveden v katalogu.
2. Výběr předplatného pro filtrování účtů úložiště
3. Vyberte účet úložiště.
4. Vyberte kolekci nebo vytvořte novou (volitelné).
5. Dokončete registraci zdroje dat.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)
