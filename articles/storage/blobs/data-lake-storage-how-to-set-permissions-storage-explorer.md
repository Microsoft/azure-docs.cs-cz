---
title: 'Postupy: Sada seznamy ACL pro soubory a adresáře pomocí Průzkumníka služby Azure Storage'
description: V tomto jak, se dozvíte, jak nastavit seznamy ACL pro soubory a adresáře
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/05/2018
ms.author: rogarana
ms.openlocfilehash: c1bcb373fcf21906cbce9b7276e2a7dd7ebb7ed8
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975848"
---
# <a name="how-to-set-file-and-directory-level-permissions-using-azure-storage-explorer"></a>Postupy: nastavení souborů a adresářů úrovně oprávnění pomocí Průzkumníka služby Azure Storage

Tento článek ukazuje, jak nastavit souborů a adresářů úrovni seznamy ACL v desktopové verzi Průzkumníka služby Azure Storage.

Tento článek vyžaduje instalaci Průzkumníka služby Azure Storage. Instalaci Průzkumníka služby Azure Storage pro Windows, Macintosh nebo Linux popisuje článek [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Přihlaste se do Průzkumníka služby Storage

Při prvním spuštění se objeví okno **Průzkumník služby Microsoft Azure Storage – Připojení**. Zatímco Průzkumník služby Storage nabízí několik způsobů, jak se připojit k účtům úložiště, pouze jedním ze způsobů aktuálně podporovány pro správu seznamů řízení přístupu.

|Úkol|Účel|
|---|---|
|Přidání účtu Azure | Přesměruje vás na přihlašovací stránku vaší organizace, kde budete moci ověřit svůj přístup do Azure. Toto je aktuálně jedinou metodou ověřování, které vám umožní spravovat seznamy řízení přístupu pomocí Průzkumníka služby storage.|

Vyberte **přidat účet Azure** a klikněte na tlačítko **přihlášení**. Podle pokynů na obrazovce se přihlaste ke svému účtu Azure.

![Průzkumník služby Microsoft Azure Storage – okno Připojení](media/storage-quickstart-blobs-storage-explorer/connect.png)

Po dokončení připojení se Průzkumník služby Azure Storage otevře se zobrazenou kartou **Průzkumník**. V tomto zobrazení uvidíte místní úložiště i přehled všech svých účtů úložiště Azure nakonfigurovaných pomocí účtů [emulátoru úložiště Azure](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nebo prostředí [Azure Stack](../../azure-stack/user/azure-stack-storage-connect-se.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Průzkumník služby Microsoft Azure Storage – okno Připojení](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="managing-access"></a>Správa přístupu

Oprávnění můžete nastavit v kořenovém adresáři vašeho systému souborů. Uděláte to tak, klikněte pravým tlačítkem na váš systém souborů a vyberte **spravovat oprávnění**.

Vyvolejte tento dříve, než **spravovat oprávnění** řádku.

![Průzkumník služby Microsoft Azure Storage - spravovat přístup k adresáři](media/storage-quickstart-blobs-storage-explorer/manageperms.png)

**Spravovat přístup** řádku vám umožní spravovat oprávnění pro vlastníka a vlastníci skupiny i přidání nových uživatelů do seznamu řízení přístupu, pro kterého pak můžete spravovat oprávnění. Další informace o oprávnění, včetně výchozích oprávnění, oprávnění k přístupu a jejich chování, najdete v našich článku na [řízení přístupu v data lake storage gen2](data-lake-storage-access-control.md#access-control-lists-on-files-and-directories). Výběr sem nenastaví seznamy řízení přístupu na všechny aktuálně existující položku do adresáře.

Doporučujeme, abyste vytváření skupin zabezpečení a správu oprávnění ve skupině místo jednotlivým uživatelům. Podrobnosti o tomto doporučení a doporučené postupy, najdete v našich [osvědčené postupy pro data lake storage gen2](data-lake-storage-best-practices.md) článku.

Můžete spravovat oprávnění pro jednotlivé adresáře, stejně jako jednotlivé soubory, umožňuje řízení přístupu na nich spočívá v jemné. Proces pro správu oprávnění na adresářů a souborů je stejný, jak je popsáno výše.

## <a name="next-steps"></a>Další postup

V tomto návodu jste zjistili, jak nastavit oprávnění pro soubory a adresáři používajícími **Průzkumníka služby Azure Storage**. Další informace o seznamy řízení přístupu a oprávnění, i nadále naše konceptuální článek k tomuto tématu.

> [!div class="nextstepaction"]
> [Ovládací prvek HAccess v Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)