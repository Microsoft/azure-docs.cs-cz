---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 27df83552b450b7657c0595d1d419001df98148c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040247"
---
## <a name="prerequisites"></a>Předpoklady

* Účet [OneDrivu](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Předtím, než budete moci použít svůj účet OneDrivu s Logic Apps, musíte autorizovat Logic Apps a připojit se k účtu OneDrive v Azure Portal.

Pomocí těchto kroků autorizujte Logic Apps připojení k účtu OneDrive:  

1. Přihlaste se k webu Azure Portal. 

1. V části **služby Azure** vyberte **Logic Apps**. Pak v seznamu vyberte název vaší aplikace logiky.

1. V nabídce aplikace logiky vyberte **Návrhář aplikace logiky** v části **vývojové nástroje**.

1. V Návrháři Logic Apps v rozevíracím seznamu vyberte **Zobrazit spravovaná rozhraní API Microsoftu** a potom do vyhledávacího pole zadejte *OneDrive* . Vyberte aktivační událost nebo akci, která se má použít:

   ![Snímek obrazovky návrháře Logic Apps, který zobrazuje seznam akcí rozhraní API OneDrivu, které se mají přidat](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Pokud jste předtím nevytvořili žádná připojení k OneDrivu, pomocí výzvy k přihlášení použijte přihlašovací údaje OneDrivu:  

   ![Snímek obrazovky návrháře Logic Apps a zobrazení výzvy k přihlášení pro rozhraní API OneDrivu](./media/connectors-create-api-onedrive/onedrive-2.png)

3. Vyberte **Přihlásit** se a zadejte své uživatelské jméno a heslo. Vyberte **Přihlásit se**: 

   ![Snímek obrazovky účet Microsoft přihlašovací stránky pro autorizaci rozhraní API pro OneDrive](./media/connectors-create-api-onedrive/onedrive-3.png)   

    Tyto přihlašovací údaje se používají k autorizaci aplikace logiky pro přístup k datům ve vašem účtu OneDrive. 

4. Vyberte **Ano** , pokud chcete autorizovat aplikaci logiky, aby používala váš účet OneDrive:  

   ![Snímek obrazovky účet Microsoft autorizaci pro Logic Apps se zobrazením povolených akcí](./media/connectors-create-api-onedrive/onedrive-4.png)   
   
5. Připojení je teď uvedené v kroku. Vyberte Uložit a pak pokračujte v vytváření aplikace logiky. 

   ![Snímek obrazovky návrháře Logic Apps, který zobrazuje Editor akcí s připojením rozhraní API pro OneDrive](./media/connectors-create-api-onedrive/onedrive-5.png)
