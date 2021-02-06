---
title: Použití Průzkumník služby Azure Storage s Azure Data Lake Storage Gen2
description: Pomocí Průzkumník služby Azure Storage můžete spravovat adresáře a seznamy řízení přístupu (ACL) souborů a adresářů v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 5ccef241a37e63467b681d5fd12c65072cb92e58
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626447"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Správa adresářů, souborů a seznamů ACL ve službě Azure Data Lake Storage Gen2 pomocí Průzkumníka služby Azure Storage

V tomto článku se dozvíte, jak pomocí [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) vytvářet a spravovat adresáře, soubory a seznamy řízení přístupu (ACL) v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](../common/storage-account-create.md) pokynů.

- Průzkumník služby Azure Storage nainstalované na místním počítači. Instalaci Průzkumníka služby Azure Storage pro Windows, Macintosh nebo Linux popisuje článek [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Přihlášení k Průzkumník služby Storage

Když poprvé spustíte Průzkumníka služby Storage, objeví se okno **Průzkumník služby Microsoft Azure Storage – Připojení**. I když Průzkumník služby Storage poskytuje několik způsobů, jak se připojit k účtům úložiště, v současné době se podporuje jenom jeden způsob správy seznamů ACL.

|Úkol|Účel|
|---|---|
|Přidání účtu Azure | Vás přesměruje na přihlašovací stránku vaší organizace a provede ověření v Azure. V současné době se jedná o jedinou podporovanou metodu ověřování, pokud chcete spravovat a nastavovat seznamy ACL.|
|Použití připojovacího řetězce nebo sdíleného přístupového podpisu URI | Ty můžete použít k přímému přístupu ke kontejneru nebo účtu úložiště pomocí tokenu SAS nebo sdíleného připojovacího řetězce. |
|Použití názvu a klíče účtu úložiště| Pro připojení k úložišti Azure můžete použít název a klíč.|

Vyberte **Přidat účet Azure** a klikněte na **Přihlásit se.**.. Podle pokynů na obrazovce se přihlaste k účtu Azure.

![Snímek obrazovky zobrazující Průzkumník služby Microsoft Azure Storage a zvýrazní možnost Přidat účet Azure a tlačítko přihlásit.](media/storage-quickstart-blobs-storage-explorer/connect.png)

Po dokončení připojení se Průzkumník služby Azure Storage otevře se zobrazenou kartou **Průzkumník**. Toto zobrazení vám umožní získat přehled o všech účtech úložiště Azure i o místním úložišti nakonfigurovaném pomocí [emulátoru úložiště Azurite](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), účtů [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nebo [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) prostředí.

![Průzkumník služby Microsoft Azure Storage – okno Připojení](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejner obsahuje adresáře a soubory. Pokud ho chcete vytvořit, rozbalte účet úložiště, který jste vytvořili v kroku pokračování. Vyberte **kontejnery objektů BLOB**, klikněte pravým tlačítkem myši a vyberte **vytvořit kontejner objektů BLOB**. Zadejte název kontejneru. Seznam pravidel a omezení pro pojmenování kontejnerů najdete v části [vytvoření kontejneru](storage-quickstart-blobs-dotnet.md#create-a-container) . Po dokončení stiskněte klávesu **ENTER** a kontejner se vytvoří. Jakmile se kontejner úspěšně vytvoří, zobrazí se ve složce **kontejnery objektů BLOB** pro vybraný účet úložiště.

![Průzkumník služby Microsoft Azure Storage – vytváření kontejneru](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Vytvoření adresáře

Chcete-li vytvořit adresář, vyberte kontejner, který jste vytvořili v kroku pokračování. Na pásu karet kontejner klikněte na tlačítko **Nová složka** . Zadejte název adresáře. Po dokončení stiskněte klávesu **ENTER** a vytvořte adresář. Po úspěšném vytvoření adresáře se zobrazí v okně editoru.

![Průzkumník služby Microsoft Azure Storage – vytváření adresáře](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Nahrajte objekty blob do adresáře.

Na pásu karet Adresář klikněte na tlačítko **nahrát** . Tato operace umožňuje nahrát složku nebo soubor.

Vyberte soubory nebo složku k nahrání.

![Průzkumník služby Microsoft Azure Storage – nahrání objektu blob](media/data-lake-storage-explorer/upload-file.png)

Když vyberete **OK**, vybrané soubory se zařadí do fronty a postupně se nahrají. Po dokončení nahrávání se výsledky zobrazí v okně **Aktivity**.

## <a name="view-blobs-in-a-directory"></a>Zobrazení objektů BLOB v adresáři

V aplikaci **Průzkumník služby Azure Storage** vyberte adresář pod účtem úložiště. Hlavní podokno zobrazuje seznam objektů BLOB ve vybraném adresáři.

![Průzkumník služby Microsoft Azure Storage – vypíše objekty BLOB v adresáři.](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Stáhnout objekty blob

Pokud chcete soubory stáhnout pomocí **Průzkumník služby Azure Storage** s vybraným souborem, na pásu karet vyberte **Stáhnout** . Otevře se dialogové okno pro výběr souboru, kde můžete zadat umístění a název staženého souboru. Výběrem **Uložit** zahájíte stahování souboru do místního umístění.

<a id="managing-access"></a>

## <a name="manage-acls"></a>Správa seznamů ACL

Klikněte pravým tlačítkem na kontejner, adresář nebo soubor a potom klikněte na možnost **spravovat Access Control seznamy**.  Na následujícím snímku obrazovky se zobrazí nabídka, která se zobrazí po kliknutí pravým tlačítkem myši na adresář.

> [!div class="mx-imgBorder"]
> ![Pravým tlačítkem myši klikněte na adresář v Průzkumník služby Azure Storage](./media/data-lake-storage-explorer/manage-access-control-list-option.png)

Dialogové okno **spravovat přístup** umožňuje spravovat oprávnění pro vlastníka a skupinu vlastníci. Umožňuje také přidat nové uživatele a skupiny do seznamu řízení přístupu, pro který pak můžete spravovat oprávnění.

> [!div class="mx-imgBorder"]
> ![Dialogové okno spravovat přístup](./media/data-lake-storage-explorer/manage-access-dialog-box.png)

Chcete-li přidat nového uživatele nebo skupinu do seznamu řízení přístupu, vyberte tlačítko **Přidat** . Pak zadejte odpovídající položku Azure Active Directory (AAD), kterou chcete přidat do seznamu, a pak vyberte **Přidat**.  Uživatel nebo skupina se nyní zobrazí v poli **Uživatelé a skupiny:** , což vám umožní začít spravovat jejich oprávnění.

> [!NOTE]
> Osvědčeným postupem je a doporučujeme vytvořit v AAD skupinu zabezpečení a zachovat oprávnění pro skupinu, nikoli jednotlivé uživatele. Podrobnosti o tomto doporučení a další osvědčené postupy najdete [v tématu model řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md).

Pomocí ovládacích prvků zaškrtávací políčko nastavte přístup a výchozí seznamy ACL. Další informace o rozdílu mezi těmito typy seznamů ACL najdete v tématu [typy seznamů ACL](data-lake-storage-access-control.md#types-of-acls).

<a id="apply-acls-recursively"></a>

## <a name="apply-acls-recursively"></a>Rekurzivní použití seznamů ACL

Položky ACL můžete rekurzivně použít u existujících podřízených položek nadřazeného adresáře bez nutnosti provádět tyto změny jednotlivě pro každou podřízenou položku.

Chcete-li použít položky seznamu ACL rekurzivně, klikněte pravým tlačítkem na kontejner nebo na adresář a potom klikněte na možnost **rozšířit Access Control seznamy**.  Na následujícím snímku obrazovky se zobrazí nabídka, která se zobrazí po kliknutí pravým tlačítkem myši na adresář.

> [!div class="mx-imgBorder"]
> ![Klikněte pravým tlačítkem na adresář a zvolte nastavení rozšířit řízení přístupu.](./media/data-lake-storage-explorer/propagate-access-control-list-option.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si seznam řízení přístupu v Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Řízení přístupu ve službě Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md)
