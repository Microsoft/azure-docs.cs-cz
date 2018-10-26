---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 7cfce34cb2d6002dba5ec570bf859ec47e894c65
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133527"
---
#### <a name="prerequisites"></a>Požadavky
* Účet Azure. můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* A [Onedrivu](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) účtu 

Předtím, než v aplikaci logiky můžete používat svůj účet Onedrivu, autorizujte aplikaci logiky pro připojení k účtu OneDrive.  Můžete udělat jednoduše v rámci vaší aplikace logiky na webu Azure portal. 

Autorizujte aplikaci logiky se připojit ke svému účtu Onedrivu, pomocí následujících kroků:

1. Vytvoření aplikace logiky. V návrháři pro Logic Apps, vyberte **rozhraní API spravovaná Microsoftem zobrazit** v rozevíracím seznamu a pak do vyhledávacího pole zadejte "onedrivu". Vyberte jednu z aktivační události nebo akce:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Pokud jste dosud nevytvořili žádná připojení k Onedrivu, zobrazí se výzva k přihlášení pomocí přihlašovacích údajů k OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Vyberte **přihlášení**a zadejte uživatelské jméno a heslo. Vyberte **přihlášení**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Tyto přihlašovací údaje se používají k autorizaci aplikace logiky k připojení a přístup k datům v účtu OneDrive. 
4. Vyberte **Ano** autorizovat aplikaci logiky, aby používala vašeho účtu OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Všimněte si, že připojení bylo vytvořeno. Nyní postupujte podle dalších kroků ve vaší aplikaci logiky:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

