---
title: zahrnout soubor
description: zahrnout soubor
services: logic-apps
author: MandiOhlinger
ms.service: logic-apps
ms.topic: include
ms.date: 03/02/2018
ms.author: mandia
ms.custom: include file
ms.openlocfilehash: 11280e1678f52ede928cb2a85ea83add222e15fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66149214"
---
1. V [webu Azure portal](https://portal.azure.com), vytvoření prázdné aplikace logiky. 

2. V návrháři pro Logic Apps zadejte jako filtr "githubu". 

3. Vyberte konektor GitHub a aktivační události, kterou chcete použít.

   ![Vyberte konektor GitHub a aktivační události](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Všechny pracovní postupy aplikace logiky musí spouštět triggerem. Můžete vybrat akce, pouze v případě, že pracovní postup vaší logiky už se spouští triggerem. 

4. Pokud jste nevytvořili dříve připojení, zvolit **přihlášení** tak můžete zadat své přihlašovací údaje Githubu po zobrazení výzvy.  

   ![Přihlaste se pomocí svých přihlašovacích údajů Githubu](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Aplikace logiky používá tyto přihlašovací údaje k autorizaci připojení a přístup k datům pro váš účet GitHub. 

5. Zadejte své uživatelské jméno Githubu a heslo a potvrzení vašich oprávnění.

   ![Zadejte přihlašovací údaje a potvrdit povolení](./media/connectors-create-api-github/github-connector-authorize.png)   

   Připojení je vytvořený na webu Azure Portal a je připravená k použití.

6. Pokračujte v definování pracovního postupu aplikace logiky.

   ![Přidat další akce pracovního postupu aplikace logiky](./media/connectors-create-api-github/github-connector-logic-app.png)

