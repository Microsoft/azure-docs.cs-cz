---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 635a69fc8e75e3a6249728dc1df31cf839b345c6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789600"
---
## <a name="prerequisites"></a>Předpoklady

* Účet Azure; Můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free) .
* Účet [Office 365](https://office365.com)  

Před použitím účtu Office 365 v aplikaci logiky autorizujte aplikaci logiky, aby se připojila k vašemu účtu Office 365. To můžete snadno provést v rámci aplikace logiky na Azure Portal.  

Autorizovat aplikaci logiky pro připojení k účtu Office 365 pomocí následujících kroků:

1. Vytvořte aplikaci logiky. V Návrháři Logic Apps v rozevíracím seznamu vyberte **Zobrazit spravovaná rozhraní API Microsoftu** a pak do vyhledávacího pole zadejte "Office 365". Vyberte jednu z aktivačních událostí nebo akcí:  
    ![krok vytvoření připojení k Office 365](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Pokud jste předtím nevytvořili žádná připojení k sadě Office 365, budete vyzváni k přihlášení pomocí přihlašovacích údajů pro Office 365:  
    ![Krok vytvoření připojení k Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Vyberte **Přihlásit**se a zadejte své uživatelské jméno a heslo. Vyberte **Přihlásit se**:  
    ![krok vytvoření připojení k Office 365](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Tyto přihlašovací údaje se používají k autorizaci aplikace logiky pro připojení k účtu Office 365 a k přístupu k němu. 
4. Všimněte si, že připojení bylo vytvořeno. Teď pokračujte dalšími kroky ve vaší aplikaci logiky:   
    ![Krok vytvoření připojení k Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

