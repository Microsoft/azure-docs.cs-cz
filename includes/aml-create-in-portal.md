---
title: zahrnout soubor
description: zahrnout soubor
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 07/21/2019
ms.openlocfilehash: 51bd3dfb33b1f445db8672e1b987ee6c6242e09c
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371007"
---
1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí přihlašovacích údajů předplatného Azure, které používáte. 

1. V levém horním rohu portálu vyberte **vytvořit prostředek**.

   ![Vytvořit prostředek na webu Azure Portal](./media/aml-create-in-portal/portal-create-a-resource-07-2019.png)

1. Pomocí panelu hledání vyhledejte **Machine Learning pracovní prostor služby**.

1. Vyberte **Machine Learning pracovní prostor služby**.

1. V podokně **pracovní prostor služby ml** vyberte **vytvořit** a začněte.

1. Nakonfigurujte nový pracovní prostor zadáním názvu pracovního prostoru, předplatného, skupiny prostředků a umístění.

    ![Vytvoření pracovního prostoru](./media/aml-create-in-portal/workspace-create-main-tab.png)

   Pole|Popis
   ---|---
   Název pracovního prostoru |Zadejte jedinečný název, který identifikuje váš pracovní prostor. V tomto příkladu používáme **docs-WS**. Názvy musí být v rámci skupiny prostředků jedinečné. Použijte název, který se dá snadno vyvolat a odlišit z pracovních prostorů vytvořených jinými uživateli.  
   Subscription |Vyberte předplatné Azure, které chcete použít.
   Resource group | Ve svém předplatném použijte existující skupinu prostředků nebo zadejte název pro vytvoření nové skupiny prostředků. Skupina prostředků obsahuje související prostředky pro řešení Azure. V tomto příkladu používáme **docs-AML**. 
   Location | Vyberte umístění, které je nejblíže vašim uživatelům a datovým prostředkům, abyste mohli vytvořit pracovní prostor.

1. Po dokončení konfigurace pracovního prostoru vyberte **vytvořit**. 

   Vytvořit pracovní prostor může chvíli trvat.

   Po dokončení procesu se zobrazí zpráva o úspěšném nasazení. Je také k dispozici v části oznámení. Pokud chcete zobrazit nový pracovní prostor, vyberte **Přejít k prostředku**.

   ![Stav vytvoření pracovního prostoru](./media/aml-create-in-portal/notifications.png)
