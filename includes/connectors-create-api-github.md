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
ms.openlocfilehash: ec5b3ca9ccd139cbdf17768056eb1d835336e7a7
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
1. V [portál Azure](https://portal.azure.com), vytvoření aplikace logiky prázdné. 

2. V návrháři aplikace logiky zadejte "githubu" jako filtr. 

3. Vyberte konektor Githubu a aktivační událost, která chcete použít.

   ![Vyberte konektor Githubu a aktivační události](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Všechny pracovní postupy aplikace logiky musí začínat aktivační událost. Akce můžete vybrat jenom v případě, že logika pracovního postupu již začíná aktivační událost. 

4. Pokud jste nevytvořili dříve připojení, zvolte **přihlášení** , můžete zadat přihlašovací údaje Githubu po zobrazení výzvy.  

   ![Přihlaste se pomocí přihlašovacích údajů Githubu](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Aplikace logiky použije tyto přihlašovací údaje k autorizaci připojení a přístup k datům pro váš účet GitHub. 

5. Zadejte svoje Githubu uživatelské jméno a heslo a potvrďte vašich oprávnění.

   ![Zadejte přihlašovací údaje a potvrďte autorizace](./media/connectors-create-api-github/github-connector-authorize.png)   

   Připojení je teď vytvořené na portálu Azure a je připravený k použití.

6. Chcete-li určit pracovní postup aplikace logiky.

   ![Do pracovního postupu logiku aplikace přidat další akce](./media/connectors-create-api-github/github-connector-logic-app.png)

