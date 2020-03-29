---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789763"
---
1. Na [webu Azure Portal](https://portal.azure.com)vytvořte prázdnou aplikaci logiky. 

2. V Návrháři aplikací logiky zadejte jako filtr "github". 

3. Vyberte konektor GitHub a aktivační událost, kterou chcete použít.

   ![Vyberte konektor GitHub a aktivační událost](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Všechny pracovní postupy aplikace logiky musí začínat aktivační událostí. Akce můžete vybrat pouze v případě, že váš logický pracovní postup již začíná aktivační událostí. 

4. Pokud jste dříve nevytvořili připojení, zvolte **Přihlásit** se, abyste po zobrazení výzvy mohli zadat přihlašovací údaje githubu.  

   ![Přihlaste se pomocí přihlašovacích údajů githubu](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Vaše aplikace logiky používá tato pověření k autorizaci připojení a přístupu k datům pro váš účet GitHub. 

5. Zadejte své uživatelské jméno a heslo GitHubu a potvrďte autorizaci.

   ![Zadejte pověření a potvrďte autorizaci](./media/connectors-create-api-github/github-connector-authorize.png)   

   Vaše připojení se teď vytvoří na webu Azure Portal a je připravené k použití.

6. Pokračujte v definování pracovního postupu aplikace logiky.

   ![Přidání dalších akcí do pracovního postupu aplikace logiky](./media/connectors-create-api-github/github-connector-logic-app.png)

