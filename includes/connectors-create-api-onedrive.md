---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 951ab2300aa4ffed2c5f1039ff993cd7f6af543f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789657"
---
## <a name="prerequisites"></a>Požadavky

* Účet Azure; můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free)
* Účet [OneDrivu](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Než budete moct používat svůj účet OneDrivu v aplikaci logiky, autorizujte aplikaci logiky pro připojení k účtu OneDrivu.  Můžete to udělat snadno v rámci aplikace logiky na webu Azure Portal. 

Autorizujte aplikaci logiky pro připojení k účtu OneDrivu pomocí následujících kroků:

1. Vytvořte aplikaci logiky. V návrháři logic apps vyberte v rozevíracím seznamu **zobrazit rozhraní API spravovaná společností Microsoft** a do vyhledávacího pole zadejte "onedrive". Vyberte jednu z aktivačních událostí nebo akcí:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Pokud jste ještě nevytvořili žádná připojení k OneDrivu, budete vyzváni k přihlášení pomocí přihlašovacích údajů onedru:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Vyberte **Přihlásit**se a zadejte své uživatelské jméno a heslo. Vyberte **Přihlásit se**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Tato pověření se používají k autorizaci aplikace logiky pro připojení a přístup k datům ve vašem účtu OneDrive. 
4. Výběrem **možnosti Ano** autorizujete aplikaci logiky k používání účtu OneDrivu:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Všimněte si, že připojení bylo vytvořeno. Nyní pokračujte dalšími kroky v aplikaci logiky:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

