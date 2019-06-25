---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 53c683dacbb3b94e34bd8662743673c3a0490d94
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205826"
---
#### <a name="prerequisites"></a>Požadavky
* Účet Azure. můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* A [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) účtu 

Před použitím svého účtu Dynamics v aplikaci logiky, autorizujte aplikaci logiky se připojit ke svému účtu aplikace CRM Online. Můžete udělat jednoduše v rámci vaší aplikace logiky na webu Azure portal. 

Autorizujte aplikaci logiky se připojit ke svému účtu aplikace CRM Online pomocí následujících kroků:

1. Vytvoření aplikace logiky. V návrháři pro Logic Apps, vyberte **rozhraní API spravovaná Microsoftem zobrazit** v rozevíracím seznamu a pak do vyhledávacího pole zadejte "dynamics". Vyberte jednu z aktivační události nebo akce:  
   ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Pokud jste dosud nevytvořili žádná připojení k Dynamics, zobrazí se výzva k přihlášení pomocí přihlašovacích údajů Dynamics:  
   ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Vyberte **přihlášení**a zadejte uživatelské jméno a heslo. Vyberte **přihlášení**. 
   
    Tyto přihlašovací údaje se používají k autorizaci aplikace logiky k připojení a přístup k datům v účtu Dynamics. 
4. Všimněte si, že připojení bylo vytvořeno. Nyní postupujte podle dalších kroků ve vaší aplikaci logiky:  
   ![](./media/connectors-create-api-crmonline/dynamics-properties.png)

