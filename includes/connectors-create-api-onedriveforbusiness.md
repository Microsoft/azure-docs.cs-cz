---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 06a503e800309d8689735417931820d9f16a6e75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102562"
---
## <a name="prerequisites"></a>Požadavky

* Účet [OneDrivu pro firmy](https://OneDrive.com) 

Než budete moct použít účet OneDrivu pro firmy s Logic Apps, musíte autorizovat Logic Apps a připojit se k účtu OneDrivu pro firmy v Azure Portal.

Pomocí následujícího postupu autorizujete Logic Apps připojení k účtu OneDrivu pro firmy:  

1. Přihlaste se k portálu Azure. 

1. V části **služby Azure**vyberte **Logic Apps**. Pak v seznamu vyberte název vaší aplikace logiky.

1. V nabídce aplikace logiky vyberte **Návrhář aplikace logiky** v části **vývojové nástroje**.

1. V Návrháři Logic Apps v rozevíracím seznamu vyberte **Zobrazit spravovaná rozhraní API Microsoftu** a potom do vyhledávacího pole zadejte *OneDrive pro firmy* . Vyberte aktivační událost nebo akci, která se má použít:  

   ![Snímek obrazovky návrháře Logic Apps, který zobrazuje Trigger opakování s akcemi rozhraní API OneDrivu pro firmy.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)

2. Pokud jste ještě nevytvořili žádná připojení k OneDrivu pro firmy, postupujte podle pokynů k zadání přihlašovacích údajů pro OneDrive pro firmy. Tyto přihlašovací údaje se používají k autorizaci aplikace logiky pro přístup k datům účtu OneDrivu pro firmy:  

   ![Snímek obrazovky návrháře Logic Apps a zobrazení výzvy k přihlášení pro OneDrive pro firmy](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)

3. Zadání uživatelského jména a hesla OneDrivu pro firmy k autorizaci vaší aplikace logiky:  

   ![Snímek přihlašovací stránky OneDrivu pro firmy, na které se zobrazuje výzva k přihlášení](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   

4. Připojení je teď uvedené v kroku. Vyberte Uložit a pak pokračujte v vytváření aplikace logiky. 

   ![Snímek obrazovky návrháře Logic Apps zobrazující aktivační událost s uvedeným připojením OneDrivu pro firmy.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   
