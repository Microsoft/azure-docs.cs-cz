---
title: zahrnout soubor
description: zahrnout soubor
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/20/2018
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: ac6b53926ca6c44c8ec1e71db67321366aacb00e
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617610"
---
## <a name="prerequisites"></a>Požadavky

### <a name="azure-subscription"></a>Předplatné Azure
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

### <a name="azure-roles"></a>Role Azure
Pro vytvoření instancí služby Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem role *přispěvatel* nebo *vlastník* nebo *správcem* předplatného Azure. Na webu Azure Portal v pravém horním rohu vyberte své uživatelské jméno a pak výběrem **Oprávnění** zobrazte oprávnění, která v předplatném máte. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné. Ukázku pokynů pro přidání uživatele k roli najdete v článku věnovaném [přidání rolí](../articles/billing/billing-add-change-azure-subscription-administrator.md).

Pokud chcete vytvářet a spravovat podřízené prostředky (včetně datových sad, propojených služeb, kanálů, triggerů a modulů runtime integrace) pro Data Factory prostřednictvím uživatelského rozhraní pro vytváření a monitorování na webu Azure Portal, musíte patřit do role **Přispěvatel Data Factory**. Pro vytváření a správu podřízených prostředků pomocí PowerShellu nebo sady SDK je dostatečná role **přispěvatele**.

### <a name="azure-storage-account"></a>Účet služby Azure Storage
V tomto rychlém startu budete účet úložiště Azure (konkrétně úložiště objektů blob) pro obecné účely používat jako *zdrojové* i *cílové úložiště dat*. Pokud nemáte účet úložiště Azure pro obecné účely, přečtěte si téma [Vytvoření účtu úložiště](../articles/storage/common/storage-quickstart-create-account.md) a účet si vytvořte. 

#### <a name="get-the-storage-account-name-and-account-key"></a>Získání názvu a klíče účtu úložiště
V tomto rychlém startu použijete název a klíč svého účtu úložiště Azure. Následující postup předvádí kroky k získání názvu a klíče vašeho účtu úložiště. 

1. Přejděte ve webovém prohlížeči na web [Azure Portal](https://portal.azure.com). Přihlaste se pomocí uživatelského jména a hesla pro Azure. 
2. V nabídce vlevo vyberte **Další služby**, použijte filtr s klíčovým slovem **úložiště** a vyberte **Účty úložiště**.

   ![Vyhledání účtu úložiště](media/data-factory-quickstart-prerequisites/search-storage-account.png)
3. V seznamu účtů úložiště vyfiltrujte váš účet úložiště (pokud je to potřeba) a pak vyberte váš účet úložiště. 
4. Na stránce **Účet úložiště** vyberte v nabídce **Přístupové klíče**.

   ![Získání názvu a klíče účtu úložiště](media/data-factory-quickstart-prerequisites/storage-account-name-key.png)
5. Zkopírujte do schránky hodnoty z polí **Název účtu úložiště** a **klíč1**. Vložte je do Poznámkového bloku nebo jiného editoru a uložte je. Použijete je později v tomto rychlém startu.   

#### <a name="create-the-input-folder-and-files"></a>Vytvoření vstupní složky a souborů
V této části vytvoříte v úložišti objektů blob v Azure kontejner objektů blob s názvem **adftutorial**. V kontejneru vytvoříte složku **input** a nahrajete do ní ukázkový soubor. 

1. Na stránce **Účet úložiště** přepněte na **Přehled** a pak vyberte **Objekty blob**. 

   ![Výběr možnosti Objekty blob](media/data-factory-quickstart-prerequisites/select-blobs.png)
2. Na stránce **Blob service** vyberte na panelu nástrojů **+ Kontejner**. 

   ![Tlačítko pro přidání kontejneru](media/data-factory-quickstart-prerequisites/add-container-button.png)    
3. V dialogovém okně **Nový kontejner** jako název zadejte **adftutorial** a pak vyberte **OK**. 

   ![Zadání názvu kontejneru](media/data-factory-quickstart-prerequisites/new-container-dialog.png)
4. V seznamu kontejnerů vyberte **adftutorial**. 

   ![Výběr kontejneru](media/data-factory-quickstart-prerequisites/seelct-adftutorial-container.png)
1. Na stránce **Kontejner** vyberte na panelu nástrojů **Nahrát**.  

   ![Tlačítko Nahrát](media/data-factory-quickstart-prerequisites/upload-toolbar-button.png)
6. Na stránce **Nahrát objekt blob** vyberte **Upřesnit**.

   ![Výběr odkazu Upřesnit](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
7. Spusťte **Poznámkový blok** a vytvořte soubor **emp.txt** s následujícím obsahem. Uložte ho do složky **c:\ADFv2QuickStartPSH**. Pokud složka **ADFv2QuickStartPSH** ještě neexistuje, vytvořte ji.
    
   ```
   John, Doe
   Jane, Doe
   ```    
8. Na webu Azure Portal na stránce **Nahrát objekt blob** najděte a vyberte soubor **emp.txt** v poli **Soubory**. 
9. Jako hodnotu pole **Nahrát do složky** zadejte **input**. 

    ![Nastavení pro nahrání objektu blob](media/data-factory-quickstart-prerequisites/upload-blob-settings.png)    
10. Potvrďte, že je nastavená složka **input** a soubor **emp.txt**, a vyberte **Nahrát**.
    
    Měli byste vidět soubor **emp.txt** a stav nahrávání v seznamu. 
12. Zavřete okno **Nahrát objekt blob** kliknutím na **X** v rohu. 

    ![Zavření okna Nahrát objekt blob](media/data-factory-quickstart-prerequisites/close-upload-blob.png)
1. Stránku **Kontejner** nechte otevřenou. Použijete ji k ověření výstupu na konci tohoto rychlého startu.