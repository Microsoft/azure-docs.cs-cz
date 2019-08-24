---
title: Nastavení oprávnění pro Data Lake Storage Gen2 s Průzkumník služby Azure Storage
description: V tomto postupu se naučíte, jak nastavit oprávnění Průzkumník služby Azure Storage u souborů a adresářů v rámci vašeho účtu úložiště s možností Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 2cc93ad9ae1d9d6d21064a2a80c3d62bf661dabc
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992286"
---
# <a name="set-file-and-directory-level-permissions-using-azure-storage-explorer-with-azure-data-lake-storage-gen2"></a>Nastavení oprávnění na úrovni souborů a adresářů pomocí Průzkumník služby Azure Storage s Azure Data Lake Storage Gen2

Soubory uložené v Azure Data Lake Storage Gen2 podporují jemně odstupňovaná oprávnění a správu seznamu řízení přístupu (ACL). Jemně odstupňovaná oprávnění a Správa ACL umožňují spravovat přístup k datům na velmi podrobné úrovni.

V tomto článku se naučíte, jak použít Průzkumník služby Azure Storage k těmto akcím:

> [!div class="checklist"]
> * Nastavit oprávnění na úrovni souboru
> * Nastavení oprávnění na úrovni adresáře
> * Přidání uživatelů nebo skupin do seznamu řízení přístupu

## <a name="prerequisites"></a>Požadavky

Aby bylo možné tento proces nejlépe vymezit, je potřeba, abyste dokončili náš [Průzkumník služby Azure Storage rychlý Start](data-lake-storage-Explorer.md). Tím se zajistí, že váš účet úložiště bude v nejvhodnějším stavu (vytvoří se kontejner a data se do něj nahrají).

## <a name="managing-access"></a>Správa přístupu

Oprávnění můžete nastavit v kořenu vašeho kontejneru. Abyste to mohli udělat, musíte být přihlášeni k Průzkumník služby Azure Storage s vaším individuálním účtem s právy k tomu, aby to bylo možné (na rozdíl od připojovacího řetězce). Klikněte pravým tlačítkem na svůj kontejner a vyberte **Spravovat oprávnění**a v dialogovém okně **Spravovat oprávnění** .

![Průzkumník služby Microsoft Azure Storage – Správa přístupu k adresáři](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

Dialog **Spravovat oprávnění** umožňuje spravovat oprávnění pro vlastníka a skupinu vlastníci. Umožňuje také přidat nové uživatele a skupiny do seznamu řízení přístupu, pro který pak můžete spravovat oprávnění.

Chcete-li přidat nového uživatele nebo skupinu do seznamu řízení přístupu, vyberte pole **Přidat uživatele nebo skupinu** .

Zadejte odpovídající položku Azure Active Directory (AAD), kterou chcete přidat do seznamu, a pak vyberte **Přidat**.

Uživatel nebo skupina se nyní zobrazí v poli **Uživatelé a skupiny:** , což vám umožní začít spravovat jejich oprávnění.

> [!NOTE]
> Osvědčeným postupem je a doporučujeme vytvořit v AAD skupinu zabezpečení a zachovat oprávnění pro skupinu, nikoli jednotlivé uživatele. Podrobnosti o tomto doporučení a další osvědčené postupy najdete v tématu osvědčené [postupy pro data Lake Storage Gen2](data-lake-storage-best-practices.md).

Existují dvě kategorie oprávnění, které můžete přiřadit: přístup k ACL a výchozí seznamy ACL.

* **Přístup**: Přístup k ovládacímu prvku ACL přístup k objektu. Přístupové seznamy ACL mají přístup k souborům a adresářům.

* **Výchozí**: Šablona seznamů ACL přidružených k adresáři, který určuje přístupové seznamy ACL pro všechny podřízené položky, které jsou vytvořeny v daném adresáři. Soubory nemají výchozí seznamy ACL.

V obou těchto kategoriích máte tři oprávnění, která pak můžete přiřadit k souborům nebo adresářům: **Čtení**, **zápis**a **spouštění**.

>[!NOTE]
> Výběrem těchto možností se nenastaví oprávnění k žádným aktuálně existujícím položkám v adresáři. Je nutné přejít na každou jednotlivou položku a nastavit oprávnění ručně, pokud soubor již existuje.

Můžete spravovat oprávnění pro jednotlivé adresáře a také jednotlivé soubory, což vám umožní jemně odstupňované řízení přístupu. Proces pro správu oprávnění pro adresáře i soubory je stejný, jak je popsáno výše. Klikněte pravým tlačítkem na soubor nebo adresář, u kterého chcete spravovat oprávnění, a postupujte podle stejného procesu.

## <a name="next-steps"></a>Další postup

V tomto postupu jste zjistili, jak nastavit oprávnění k souborům a adresářům pomocí **Průzkumník služby Azure Storage**. Další informace o seznamech ACL, včetně výchozích seznamů ACL, přístupových seznamů ACL, jejich chování a jejich odpovídajících oprávnění, najdete v našem článku o předmětu.

> [!div class="nextstepaction"]
> [Řízení přístupu ve službě Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
