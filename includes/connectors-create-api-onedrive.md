---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 8b5f72f18ee5bf9c1e4dbaaa87d6705f55b09bc5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524199"
---
## <a name="prerequisites"></a>Předpoklady

* Účet Azure; Můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free) .
* Účet [OneDrivu](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Než budete moct použít svůj účet OneDrive v aplikaci logiky, autorizujte aplikaci logiky, aby se připojila k vašemu účtu OneDrive.  To můžete snadno provést v rámci aplikace logiky na Azure Portal. 

Autorizovat aplikaci logiky pro připojení k účtu OneDrive pomocí následujících kroků:

1. Vytvořte aplikaci logiky. V Návrháři Logic Apps v rozevíracím seznamu vyberte **Zobrazit spravovaná rozhraní API Microsoftu** a potom do vyhledávacího pole zadejte OneDrive. Vyberte jednu z aktivačních událostí nebo akcí:  
   ![Dialogové okno s názvem "Zobrazit spravovaná rozhraní API společnosti Microsoft" obsahuje vyhledávací pole, které obsahuje "OneDrive". Níže je uveden seznam čtyř aktivačních událostí. První v seznamu je OneDrive – při vytvoření souboru, který je vybraný.](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Pokud jste předtím nevytvořili žádná připojení k OneDrivu, budete vyzváni k přihlášení pomocí přihlašovacích údajů k OneDrivu:  
   ![Dialogové okno s názvem OneDrive – při vytvoření souboru má tlačítko označené jako přihlásit se.](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Vyberte **Přihlásit**se a zadejte své uživatelské jméno a heslo. Vyberte **Přihlásit se**:  
   ![Dialogové okno s názvem "přihlásit se" vás provede pokyny k použití účet Microsoft. Má dvě textová pole označená jako "E-mail nebo telefon" a "heslo", má také zaškrtávací políčko s označením "zůstat přihlášený" a tlačítko s označením "přihlásit".](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Tyto přihlašovací údaje se používají k autorizaci aplikace logiky k připojení a přístup k datům ve vašem účtu OneDrive. 
4. Vyberte **Ano** , pokud chcete autorizovat aplikaci logiky, aby používala váš účet OneDrive:  
   ![Dialogové okno s názvem "umožnit této aplikaci přístup k informacím?" vyzve k provedení následujících čtyř věcí: 1) "automatické přihlášení", 2) "přístup k vašim e-mailovým adresám", 3) "přístup k informacím kdykoli a 4)" přístup k souborům OneDrivu ". K udělení oprávnění je k dispozici tlačítko Ano a tlačítko Ne pro zamítnutí. K dispozici je odkaz pro změnu těchto oprávnění aplikace.](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Všimněte si, že připojení bylo vytvořeno. Teď pokračujte dalšími kroky ve vaší aplikaci logiky:  
   ![Dialogové okno s názvem "při vytvoření souboru" obsahuje textové pole s názvem "složka" s přiřazeným tlačítkem Procházet.](./media/connectors-create-api-onedrive/onedrive-5.png)

