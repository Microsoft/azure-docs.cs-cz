---
title: Použití Průzkumníka úložiště Azure s Programem Azure Data Lake Storage Gen2
description: Průzkumník úložišť Azure slouží ke správě adresářů a seznamů řízení přístupu k souborům a adresářům (ACL) v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: fca9fa8a964c6c9d69ffbb3036bd4774e0d1bd34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255545"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Správa adresářů, souborů a seznamů ACL v Azure Data Lake Storage Gen2 pomocí Průzkumníka úložišť Azure

Tento článek ukazuje, jak pomocí [Průzkumníka úložiště Azure](https://azure.microsoft.com/features/storage-explorer/) vytvářet a spravovat adresáře, soubory a oprávnění v účtech úložiště, které mají povolený hierarchický obor názvů (HNS).

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má povolen hierarchický obor názvů (HNS). [Chcete-li](data-lake-storage-quickstart-create-account.md) jej vytvořit, postupujte podle těchto pokynů.
> * Průzkumník a okolí Azure nainstalovaný v místním počítači. Instalaci Průzkumníka služby Azure Storage pro Windows, Macintosh nebo Linux popisuje článek [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Přihlášení k Průzkumníkovi úložiště

Když poprvé spustíte Průzkumníka služby Storage, objeví se okno **Průzkumník služby Microsoft Azure Storage – Připojení**. Zatímco Průzkumník úložiště poskytuje několik způsobů připojení k účtům úložiště, pro správu seznamů AC je aktuálně podporován pouze jeden způsob.

|Úkol|Účel|
|---|---|
|Přidání účtu Azure | Přesměruje vás na přihlašovací stránku vaší organizace, abyste se ověřili do Azure. V současné době se jedná o jedinou podporovanou metodu ověřování, pokud chcete spravovat a nastavovat počet klientů AC.|
|Použití připojovacího řetězce nebo sdíleného přístupového podpisu URI | Ty můžete použít k přímému přístupu ke kontejneru nebo účtu úložiště pomocí tokenu SAS nebo sdíleného připojovacího řetězce. |
|Použití názvu a klíče účtu úložiště| Pro připojení k úložišti Azure můžete použít název a klíč.|

Vyberte **Přidat účet Azure** a klikněte na **Přihlásit se..**. Podle pokynů na obrazovce se přihlaste ke svému účtu Azure.

![Průzkumník služby Microsoft Azure Storage – okno Připojení](media/storage-quickstart-blobs-storage-explorer/connect.png)

Po dokončení připojení se Průzkumník služby Azure Storage otevře se zobrazenou kartou **Průzkumník**. V tomto zobrazení uvidíte místní úložiště i přehled všech svých účtů úložiště Azure nakonfigurovaných pomocí účtů [emulátoru úložiště Azure](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nebo prostředí [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Průzkumník služby Microsoft Azure Storage – okno Připojení](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Vytvoření kontejneru

Kontejner obsahuje adresáře a soubory. Chcete-li jej vytvořit, rozbalte účet úložiště, který jste vytvořili v kroku řízení. Klikněte pravým tlačítkem na **Kontejnery objektů blob** a vyberte **Vytvořit kontejner objektů blob**. Zadejte název kontejneru. Seznam pravidel a omezení týkajících se pojmenování kontejnerů naleznete v části [Vytvoření kontejneru.](storage-quickstart-blobs-dotnet.md#create-a-container) Po dokončení vytvořte kontejner stisknutím **klávesy Enter.** Jakmile byl kontejner úspěšně vytvořen, zobrazí se ve složce **Kontejnery objektů blob** pro vybraný účet úložiště.

![Průzkumník úložiště Microsoft Azure – vytvoření kontejneru](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Vytvoření adresáře

Chcete-li vytvořit adresář, vyberte kontejner, který jste vytvořili v kroku řízení. Na pásu karet kontejneru zvolte tlačítko **Nová složka.** Zadejte název adresáře. Po dokončení vytvořte adresář stisknutím **klávesy Enter.** Jakmile je adresář úspěšně vytvořen, zobrazí se v okně editoru.

![Průzkumník úložiště Microsoft Azure – vytvoření adresáře](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Nahrání objektů BLOB do adresáře

Na pásu karet adresářů zvolte tlačítko **Nahrát.** Tato operace umožňuje nahrát složku nebo soubor.

Vyberte soubory nebo složku k nahrání.

![Průzkumník služby Microsoft Azure Storage – nahrání objektu blob](media/data-lake-storage-explorer/upload-file.png)

Když vyberete **OK**, vybrané soubory se zařadí do fronty a postupně se nahrají. Po dokončení nahrávání se výsledky zobrazí v okně **Aktivity**.

## <a name="view-blobs-in-a-directory"></a>Zobrazení objektů BLOB v adresáři

V aplikaci **Azure Storage Explorer** vyberte adresář pod účtem úložiště. V hlavním podokně se zobrazí seznam objektů BLOB ve vybraném adresáři.

![Průzkumník úložiště Microsoft Azure – seznam objektů BLOB v adresáři](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Stáhnout objekty blob

Pokud chcete stahovat soubory pomocí **Průzkumníka úložiště Azure**, s vybraným souborem vyberte **Stáhnout** z pásu karet. Otevře se dialogové okno pro výběr souboru, kde můžete zadat umístění a název staženého souboru. Výběrem **možnosti Uložit** zahájíte stahování souboru do místního umístění.

## <a name="managing-access"></a>Správa přístupu

Oprávnění můžete nastavit v kořenovém adresáři kontejneru. Chcete-li tak učinit, musíte být přihlášeni do Průzkumníka úložiště Azure s vaším individuálním účtem s právy k tomu (na rozdíl od připojovacího řetězce). Klikněte pravým tlačítkem myši na kontejner a vyberte **Spravovat oprávnění**a vyvěste dialogové okno **Spravovat oprávnění.**

![Průzkumník úložiště Microsoft Azure – správa přístupu k adresářům](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

Dialogové okno **Spravovat oprávnění** umožňuje spravovat oprávnění pro vlastníka a skupinu vlastníků. Umožňuje také přidat nové uživatele a skupiny do seznamu řízení přístupu, pro které pak můžete spravovat oprávnění.

Chcete-li přidat nového uživatele nebo skupinu do seznamu řízení přístupu, vyberte pole **Přidat uživatele nebo skupinu.**

Zadejte odpovídající položku služby Azure Active Directory (AAD), kterou chcete přidat do seznamu, a pak vyberte **Přidat**.

Uživatel nebo skupina se nyní zobrazí v poli **Uživatelé a skupiny:** což vám umožní začít spravovat jejich oprávnění.

> [!NOTE]
> Je osvědčeným postupem a doporučujeme vytvořit skupinu zabezpečení ve službě AAD a udržovat oprávnění pro skupinu spíše než pro jednotlivé uživatele. Podrobnosti o tomto doporučení a další osvědčené postupy naleznete [v doporučených postupech pro úložiště datového jezera Gen2](data-lake-storage-best-practices.md).

Existují dvě kategorie oprávnění, které můžete přiřadit: přístup k seznamu AC a výchozím seznamu ACL.

* **Přístup**: Přístup k knihovnám AC řídí přístup k objektu. Soubory i adresáře mají přístup k seznamům ACL.

* **Výchozí**: Šablona seznamů AC přidružených k adresáři, která určuje přístupové seznamy AC pro všechny podřízené položky, které jsou vytvořeny v tomto adresáři. Soubory nemají výchozí seznamy ACL.

V obou těchto kategoriích jsou tři oprávnění, která pak můžete přiřadit k souborům nebo adresářům: **Čtení**, **Zápis**a **Spuštění**.

>[!NOTE]
> Provedení výběrů zde nenastaví oprávnění pro všechny aktuálně existující položky uvnitř adresáře. Pokud soubor již existuje, je nutné přejít ke každé jednotlivé položce a ručně nastavit oprávnění.

Můžete spravovat oprávnění pro jednotlivé adresáře, stejně jako jednotlivé soubory, které jsou to, co umožňuje jemně odstupňované řízení přístupu. Proces správy oprávnění pro adresáře i soubory je stejný, jak je popsáno výše. Klikněte pravým tlačítkem myši na soubor nebo adresář, ke kterým chcete spravovat oprávnění, a postupujte stejným způsobem.

## <a name="next-steps"></a>Další kroky

Seznamy řízení přístupu naučte v aplikaci Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Řízení přístupu ve službě Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
