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
ms.openlocfilehash: e571c65e64fad73c646aa05366cab685aa745caa
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968844"
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

1. Pokud budete z portálu používat možnost bez kódu, jako je například vizuální rozhraní nebo automatizované experimenty ML, jste hotovi nyní. Pokud budete vytvářet [virtuální počítač poznámkového bloku](../articles/machine-learning/service/tutorial-1st-experiment-sdk-setup.md#azure) , budete také hotovi. 

1. Pokud plánujete použít kód v místním prostředí, které odkazuje na tento pracovní prostor, vyberte **Stáhnout soubor config. JSON** v části **Přehled** pracovního prostoru.  

   ![Stažení souboru config. JSON](./media/aml-create-in-portal/configure.png)
   
   Soubor umístěte do struktury adresáře pomocí skriptů Pythonu nebo poznámkových bloků Jupyter. Může být ve stejném adresáři, v podadresáři s názvem *. AzureML*nebo v nadřazeném adresáři. Když vytváříte virtuální počítač poznámkového bloku, tento soubor se přidá do správného adresáře na VIRTUÁLNÍm počítači za vás.

    

