---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74789763"
---
1. V [Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky. 

2. V Návrháři Logic Apps jako filtr zadejte GitHub. 

3. Vyberte konektor GitHub a Trigger, který chcete použít.

   ![Výběr konektoru GitHubu a triggeru](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Všechny pracovní postupy aplikace logiky musí začínat triggerem. Akce můžete vybrat pouze v případě, že váš pracovní postup logiky již začíná triggerem. 

4. Pokud jste dříve nevytvořili připojení, vyberte možnost **Přihlásit** se, abyste po zobrazení výzvy mohli zadat své přihlašovací údaje GitHubu.  

   ![Přihlaste se pomocí přihlašovacích údajů GitHubu.](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Vaše aplikace logiky používá tyto přihlašovací údaje k autorizaci připojení a přístupu k datům pro váš účet GitHub. 

5. Zadejte své uživatelské jméno a heslo k GitHubu a potvrďte svoji autorizaci.

   ![Zadejte přihlašovací údaje a potvrďte autorizaci.](./media/connectors-create-api-github/github-connector-authorize.png)   

   Vaše připojení je nyní vytvořeno v Azure Portal a je připraveno k použití.

6. Pokračujte v definování pracovního postupu aplikace logiky.

   ![Přidání dalších akcí do pracovního postupu aplikace logiky](./media/connectors-create-api-github/github-connector-logic-app.png)

