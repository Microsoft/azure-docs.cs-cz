---
title: 'Průzkumník služby Storage: nastavení seznamů ACL v Azure Data Lake Storage Gen2'
description: Pomocí Průzkumník služby Azure Storage můžete spravovat seznamy řízení přístupu (ACL) v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3f5bd22619e49246583d8b9fc4e62ad8ab266993
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654140"
---
# <a name="use-azure-storage-explorer-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Správa seznamů ACL v Azure Data Lake Storage Gen2 pomocí Průzkumník služby Azure Storage

V tomto článku se dozvíte, jak pomocí [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) spravovat seznamy řízení přístupu (ACL) v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).

Pomocí Průzkumník služby Storage můžete zobrazit a následně aktualizovat seznamy ACL adresářů a souborů. Dědičnost seznamů ACL je již k dispozici pro nové podřízené položky, které jsou vytvořeny v nadřazeném adresáři. Nastavení seznamu ACL můžete také rekurzivně použít u existujících podřízených položek nadřazeného adresáře bez nutnosti provádět tyto změny jednotlivě pro každou podřízenou položku. 

V tomto článku se dozvíte, jak upravit seznam ACL souboru nebo adresáře a jak rekurzivně použít nastavení seznamu ACL pro podřízené adresáře.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

- Účet úložiště, který má povolený hierarchický obor názvů (HNS). Pokud ho chcete vytvořit, postupujte podle [těchto](../common/storage-account-create.md) pokynů.

- Průzkumník služby Azure Storage nainstalované na místním počítači. Instalaci Průzkumníka služby Azure Storage pro Windows, Macintosh nebo Linux popisuje článek [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> Průzkumník služby Storage využívá při práci s Azure Data Lake Storage Gen2 i [koncové body](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) objektů BLOB (blob) & Data Lake Storage Gen2 (DFS). Pokud je přístup k Azure Data Lake Storage Gen2 nakonfigurovaný pomocí privátních koncových bodů, zajistěte, aby se pro účet úložiště vytvořily dva privátní koncové body: jeden s cílovým dílčím prostředkem `blob` a druhý s cílovým dílčím prostředkem `dfs` .

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

## <a name="manage-an-acl"></a>Správa seznamu ACL

Klikněte pravým tlačítkem na kontejner, adresář nebo soubor a potom klikněte na možnost **spravovat Access Control seznamy**.  Na následujícím snímku obrazovky se zobrazí nabídka, která se zobrazí po kliknutí pravým tlačítkem myši na adresář.

> [!div class="mx-imgBorder"]
> ![Pravým tlačítkem myši klikněte na adresář v Průzkumník služby Azure Storage](./media/data-lake-storage-explorer-acl/manage-access-control-list-option.png)

Dialogové okno **spravovat přístup** umožňuje spravovat oprávnění pro vlastníka a skupinu vlastníci. Umožňuje také přidat nové uživatele a skupiny do seznamu řízení přístupu, pro který pak můžete spravovat oprávnění.

> [!div class="mx-imgBorder"]
> ![Dialogové okno spravovat přístup](./media/data-lake-storage-explorer-acl/manage-access-dialog-box.png)

Chcete-li přidat nového uživatele nebo skupinu do seznamu řízení přístupu, vyberte tlačítko **Přidat** . Pak zadejte odpovídající položku Azure Active Directory (Azure AD), kterou chcete přidat do seznamu, a pak vyberte **Přidat**.  Uživatel nebo skupina se nyní zobrazí v poli **Uživatelé a skupiny:** , což vám umožní začít spravovat jejich oprávnění.

> [!NOTE]
> Osvědčeným postupem je a doporučujeme vytvořit skupinu zabezpečení ve službě Azure AD a spravovat oprávnění pro skupinu, nikoli jednotlivé uživatele. Podrobnosti o tomto doporučení a další osvědčené postupy najdete [v tématu model řízení přístupu v Azure Data Lake Storage Gen2](data-lake-storage-explorer-acl.md).

Pomocí ovládacích prvků zaškrtávací políčko nastavte přístup a výchozí seznamy ACL. Další informace o rozdílu mezi těmito typy seznamů ACL najdete v tématu [typy seznamů ACL](data-lake-storage-access-control.md#types-of-acls).

## <a name="apply-acls-recursively"></a>Rekurzivní použití seznamů ACL

Položky ACL můžete rekurzivně použít u existujících podřízených položek nadřazeného adresáře bez nutnosti provádět tyto změny jednotlivě pro každou podřízenou položku.

Chcete-li použít položky seznamu ACL rekurzivně, klikněte pravým tlačítkem na kontejner nebo na adresář a potom klikněte na možnost **rozšířit Access Control seznamy**.  Na následujícím snímku obrazovky se zobrazí nabídka, která se zobrazí po kliknutí pravým tlačítkem myši na adresář.

> [!div class="mx-imgBorder"]
> ![Klikněte pravým tlačítkem na adresář a zvolte nastavení rozšířit řízení přístupu.](./media/data-lake-storage-explorer-acl/propagate-access-control-list-option.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si o modelu oprávnění Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Model řízení přístupu v Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md)
