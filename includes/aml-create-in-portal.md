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
ms.date: 07/31/2019
ms.openlocfilehash: 0a497ce506e1aa3b0f9afc47bf1ab8382c9c0405
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951417"
---
1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí přihlašovacích údajů předplatného Azure, které používáte. 

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek**.

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

1. Pokud váš kód odkazuje na tento pracovní prostor z místního prostředí, vyberte **Stáhnout soubor config. JSON** v části **Přehled** pracovního prostoru.  

   ![Stažení souboru config. JSON](./media/aml-create-in-portal/configure.png)

    Tento soubor JSON umístěte do struktury adresáře, která obsahuje vaše skripty Pythonu nebo Jupyter poznámkové bloky. Může být ve stejném adresáři, v podadresáři s názvem *. AzureML*nebo v nadřazeném adresáři.

   Pokud budete používat [virtuální počítač poznámkového bloku](../articles/machine-learning/service/tutorial-1st-experiment-sdk-setup.md#azure) , nemusíte tento soubor stahovat, ale přidá se do správného adresáře na virtuálním počítači.
