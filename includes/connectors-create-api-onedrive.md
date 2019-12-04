---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 951ab2300aa4ffed2c5f1039ff993cd7f6af543f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789657"
---
## <a name="prerequisites"></a>Předpoklady

* Účet Azure; Můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free) .
* Účet [OneDrivu](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Než budete moct použít svůj účet OneDrive v aplikaci logiky, autorizujte aplikaci logiky, aby se připojila k vašemu účtu OneDrive.  To můžete snadno provést v rámci aplikace logiky na Azure Portal. 

Autorizovat aplikaci logiky pro připojení k účtu OneDrive pomocí následujících kroků:

1. Vytvořte aplikaci logiky. V Návrháři Logic Apps v rozevíracím seznamu vyberte **Zobrazit spravovaná rozhraní API Microsoftu** a potom do vyhledávacího pole zadejte OneDrive. Vyberte jednu z aktivačních událostí nebo akcí:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Pokud jste předtím nevytvořili žádná připojení k OneDrivu, budete vyzváni k přihlášení pomocí přihlašovacích údajů k OneDrivu:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Vyberte **Přihlásit**se a zadejte své uživatelské jméno a heslo. Vyberte **Přihlásit se**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Tyto přihlašovací údaje se používají k autorizaci aplikace logiky k připojení a přístup k datům ve vašem účtu OneDrive. 
4. Vyberte **Ano** , pokud chcete autorizovat aplikaci logiky, aby používala váš účet OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Všimněte si, že připojení bylo vytvořeno. Teď pokračujte dalšími kroky ve vaší aplikaci logiky:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

