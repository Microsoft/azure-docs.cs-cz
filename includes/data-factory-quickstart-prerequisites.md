---
title: zahrnout soubor
description: zahrnout soubor
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: include
ms.custom: include file
ms.date: 06/27/2019
ms.openlocfilehash: a979cd0a4c2ee6466edebadf61e8a98b8f17c9f3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013347"
---
## <a name="prerequisites"></a>Požadavky

### <a name="azure-subscription"></a>Předplatné Azure

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/), ještě než začnete.

### <a name="azure-roles"></a>Role Azure

Pro vytvoření instancí služby Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem role *přispěvatel* nebo *vlastník* nebo *správcem* předplatného Azure. Pokud chcete zobrazit oprávnění, která máte v rámci předplatného, klikněte na [Azure Portal](https://portal.azure.com), v pravém horním rohu vyberte své uživatelské jméno, pro další možnosti vyberte ikonu **...** a pak vyberte **Moje oprávnění**. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné.

Při vytváření a správě podřízených prostředků pro službu Data Factory, včetně datových sad, propojených služeb, kanálů, triggerů a prostředí Integration Runtime, platí následující požadavky:

- Pokud chcete vytvářet a spravovat podřízené prostředky na webu Azure Portal, je potřeba, abyste patřili do role **Přispěvatel Data Factory** na úrovni skupiny prostředků nebo vyšší.
- Pro vytváření a správu podřízených prostředků pomocí PowerShellu nebo sady SDK na úrovni prostředku nebo vyšší je dostatečná role **Přispěvatel**.

Ukázku pokynů pro přidání uživatele do role najdete v článku věnovaném [přidávání rolí](../articles/cost-management-billing/manage/add-change-subscription-administrator.md).

Další informace najdete v následujících článcích:

- [Role Přispěvatel Data Factory](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Role a oprávnění pro službu Azure Data Factory](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Účet služby Azure Storage

V tomto rychlém startu použijete účet pro obecné účely Azure Storage (konkrétně úložiště objektů BLOB) jako *zdrojové* i *cílové* úložiště dat. Pokud nemáte účet pro obecné účely Azure Storage, přečtěte si článek [Vytvoření účtu úložiště](../articles/storage/common/storage-account-create.md) a vytvořte si ho. 

#### <a name="get-the-storage-account-name"></a>Získání názvu účtu úložiště

Pro účely tohoto rychlého startu potřebujete název účtu Azure Storage. Následující postup popisuje kroky pro získání názvu účtu úložiště: 

1. Ve webovém prohlížeči přejdete na [Azure Portal](https://portal.azure.com) a přihlaste se pomocí uživatelského jména a hesla Azure.
2. V nabídce Azure Portal vyberte **všechny služby** a pak vyberte **Storage**  >  **účty úložiště** úložiště. Můžete také vyhledat a vybrat *účty úložiště* z libovolné stránky.
3. Na stránce **účty úložiště** vyfiltrujte váš účet úložiště (Pokud je to potřeba) a pak vyberte svůj účet úložiště. 

Můžete také vyhledat a vybrat *účty úložiště* z libovolné stránky.

#### <a name="create-a-blob-container"></a>Vytvoření kontejneru objektů blob

V této části vytvoříte v úložišti objektů blob v Azure kontejner objektů blob s názvem **adftutorial**.

1. Na stránce účet úložiště vyberte **přehledové**  >  **kontejnery**.
2. Na *\<Account name>*  -  panelu nástrojů stránky **kontejnerů** vyberte **kontejner**.
3. V dialogovém okně **Nový kontejner** jako název zadejte **adftutorial** a pak vyberte **OK**. *\<Account name>*  -  Stránka **kontejnery** je aktualizována tak, aby zahrnovala **adftutorial** v seznamu kontejnerů.

   ![Seznam kontejnerů](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Přidání vstupní složky a souboru pro kontejner objektů BLOB

V této části vytvoříte ve vytvořeném kontejneru složku s názvem **input** a nahrajete do ní ukázkový soubor. Než začnete, otevřete textový editor, jako je například **Poznámkový blok**, a vytvořte soubor s názvem **emp.txt** s následujícím obsahem:

```emp.txt
John, Doe
Jane, Doe
```

Uložte soubor do složky **C:\ADFv2QuickStartPSH** . (Pokud složka ještě neexistuje, vytvořte ji.) Pak se vraťte do Azure Portal a proveďte následující kroky:

1. Na *\<Account name>*  -  stránce **kontejnery** , kde jste skončili, vyberte **adftutorial** z aktualizovaného seznamu kontejnerů.

   1. Pokud jste okno zavřeli nebo jste přešli na jinou stránku, přihlaste se k [Azure Portal](https://portal.azure.com) znovu.
   1. V nabídce Azure Portal vyberte **všechny služby** a pak vyberte **Storage**  >  **účty úložiště** úložiště. Můžete také vyhledat a vybrat *účty úložiště* z libovolné stránky.
   1. Vyberte svůj účet úložiště a pak vyberte **kontejnery**  >  **adftutorial**.

2. Na panelu nástrojů na stránce kontejneru **adftutorial** vyberte **nahrát**.
3. Na stránce **nahrát objekt BLOB** vyberte pole **soubory** a pak vyhledejte a vyberte soubor **emp.txt** .
4. Rozbalte nadpis **Upřesnit** . Stránka se teď zobrazí, jak je znázorněno na následujícím obrázku:

   ![Výběr odkazu Upřesnit](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. Do pole **Odeslat do složky** zadejte Input ( **vstup**).
6. Vyberte tlačítko **Nahrát**. Měli byste vidět soubor **emp.txt** a stav nahrávání v seznamu.
7. Vyberte ikonu **Zavřít** ( **X**) pro zavření stránky **nahrát objekt BLOB** .

Nechejte stránku kontejneru **adftutorial** otevřenou. Použijete ji k ověření výstupu na konci tohoto rychlého startu.