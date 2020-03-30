---
title: zahrnout soubor
description: zahrnout soubor
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 06/27/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 4d77cb8128105a40143a40e48ebe450115f7cf1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164115"
---
## <a name="prerequisites"></a>Požadavky

### <a name="azure-subscription"></a>Předplatné Azure
Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

### <a name="azure-roles"></a>Role Azure
Pro vytvoření instancí služby Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem role *přispěvatel* nebo *vlastník* nebo *správcem* předplatného Azure. Chcete-li zobrazit oprávnění, která máte v předplatném, přejděte na [portál Azure](https://portal.azure.com), vyberte své uživatelské jméno v pravém horním rohu, vyberte ikonu **" ...** pro další možnosti a pak vyberte Moje **oprávnění**. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné.

Při vytváření a správě podřízených prostředků pro službu Data Factory, včetně datových sad, propojených služeb, kanálů, triggerů a prostředí Integration Runtime, platí následující požadavky:

- Pokud chcete vytvářet a spravovat podřízené prostředky na webu Azure Portal, je potřeba, abyste patřili do role **Přispěvatel Data Factory** na úrovni skupiny prostředků nebo vyšší.
- Pro vytváření a správu podřízených prostředků pomocí PowerShellu nebo sady SDK na úrovni prostředku nebo vyšší je dostatečná role **Přispěvatel**.

Ukázku pokynů pro přidání uživatele do role najdete v článku věnovaném [přidávání rolí](../articles/cost-management-billing/manage/add-change-subscription-administrator.md).

Další informace najdete v následujících článcích:

- [Role Přispěvatel Data Factory](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Role a oprávnění pro službu Azure Data Factory](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Účet služby Azure Storage
V tomto rychlém startu používáte účet úložiště Azure pro obecné účely (konkrétně úložiště objektů blob) jako úložiště *zdrojových* i *cílových* dat. Pokud nemáte účet úložiště Azure pro obecné účely, přečtěte si, že si ho můžete [vytvořit.](../articles/storage/common/storage-account-create.md) 

#### <a name="get-the-storage-account-name"></a>Získání názvu účtu úložiště
Pro tento rychlý start budete potřebovat název účtu služby Azure Storage. Následující postup obsahuje postup, jak získat název vašeho účtu úložiště: 

1. Ve webovém prohlížeči přejděte na [portál Azure](https://portal.azure.com) a přihlaste se pomocí uživatelského jména a hesla Azure.
2. V nabídce Portál Azure vyberte **Všechny služby**a pak **vyberte** > **Účty úložiště**. Účty *úložiště* můžete také vyhledat a vybrat na libovolné stránce.
3. Na stránce **Účty úložiště** vyfiltrujte svůj účet úložiště (v případě potřeby) a vyberte účet úložiště. 

Účty *úložiště* můžete také vyhledat a vybrat na libovolné stránce.

#### <a name="create-a-blob-container"></a>Vytvoření kontejneru objektů blob
V této části vytvoříte v úložišti objektů blob v Azure kontejner objektů blob s názvem **adftutorial**.

1. Na stránce účtu úložiště vyberte **Přehled** > **kontejnerů**.
2. Na * \< *panelu nástrojů stránky Název účtu> -  **Kontejnery** vyberte kontejner **.**
3. V dialogovém okně **Nový kontejner** jako název zadejte **adftutorial** a pak vyberte **OK**. Název * \<účtu>*  -  **kontejnery** stránka je aktualizována tak, aby **zahrnovala adftutorial** v seznamu kontejnerů.

   ![Seznam kontejnerů](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Přidání vstupní složky a souboru pro kontejner objektů blob
V této části vytvoříte složku s názvem **input** v kontejneru, který jste právě vytvořili, a potom nahrajete ukázkový soubor do vstupní složky. Než začnete, otevřete textový editor, například **Poznámkový blok**, a vytvořte soubor s názvem **emp.txt** s následujícím obsahem:

```emp.txt
John, Doe
Jane, Doe
```

Uložte soubor do složky **C:\ADFv2QuickStartPSH.** (Pokud složka ještě neexistuje, vytvořte ji.) Pak se vraťte na portál Azure a postupujte takto:

1. Na stránce * \<Název účtu>*  -  **kontejnery,** kde jste skončili, vyberte **adftutorial** z aktualizovaného seznamu kontejnerů.

   1. Pokud jste zavřeli okno nebo přešli na jinou stránku, přihlaste se znovu na [portál Azure.](https://portal.azure.com)
   1. V nabídce Portál Azure vyberte **Všechny služby**a pak **vyberte** > **Účty úložiště**. Účty *úložiště* můžete také vyhledat a vybrat na libovolné stránce.
   1. Vyberte účet úložiště a pak vyberte **kontejnery** > **adftutorial**.

2. Na panelu nástrojů kontejneru **adftutorial** vyberte **nahrát**.
3. Na stránce **Nahrát objekt blob** vyberte pole **Soubory** a pak vyhledejte soubor **emp.txt** a vyberte ho.
4. Rozbalte **nadpis Upřesnit.** Stránka se nyní zobrazí takto:

   ![Výběr odkazu Upřesnit](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. Do pole **Nahrát do složky** zadejte **vstup**.
6. Vyberte tlačítko **Nahrát**. Měli byste vidět soubor **emp.txt** a stav nahrávání v seznamu.
7. Kliknutím na ikonu **Zavřít** **(X)** zavřete stránku **objektu blob pro nahrávání.**

Udržujte stránku kontejneru **adftutorial** otevřenou. Použijete ji k ověření výstupu na konci tohoto rychlého startu.