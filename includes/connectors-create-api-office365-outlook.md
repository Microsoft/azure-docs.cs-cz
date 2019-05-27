---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: b216de0a5094066977467b2899567122d585fb7e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66149730"
---
#### <a name="prerequisites"></a>Požadavky
* Účet Azure. můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* [Office 365](https://office365.com) účtu  

Před použitím účtu Office 365 v aplikaci logiky, autorizujte aplikaci logiky pro připojení k vašemu účtu Office 365. Můžete udělat jednoduše v rámci vaší aplikace logiky na webu Azure portal.  

Autorizace aplikace logiky pro připojení k vašemu účtu Office 365 pomocí následujících kroků:

1. Vytvoření aplikace logiky. V návrháři pro Logic Apps, vyberte **rozhraní API spravovaná Microsoftem zobrazit** v rozevíracím seznamu a pak do vyhledávacího pole zadejte "office 365". Vyberte jednu z aktivační události nebo akce:  
    ![Krok vytvoření připojení Office 365](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Pokud jste dosud nevytvořili žádná připojení k Office 365, zobrazí se výzva k přihlášení pomocí přihlašovacích údajů Office 365:  
    ![Krok vytvoření připojení Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Vyberte **přihlášení**a zadejte uživatelské jméno a heslo. Vyberte **přihlášení**:  
    ![Krok vytvoření připojení Office 365](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Tyto přihlašovací údaje se používají k autorizaci aplikace logiky k připojení a přístup k vašemu účtu Office 365. 
4. Všimněte si, že připojení bylo vytvořeno. Nyní postupujte podle dalších kroků ve vaší aplikaci logiky:   
    ![Krok vytvoření připojení Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

