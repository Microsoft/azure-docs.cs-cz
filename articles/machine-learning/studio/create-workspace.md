---
title: Vytvoření Machine Learning Studio prostoru | Microsoft Docs
description: Postup vytvoření pracovního prostoru pro Azure Machine Learning Studio
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 8da10e474c62868ca109a44e7c66dc11ee62b6de
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>Vytvoření a sdílení pracovního prostoru Azure Machine Learning
Tato nabídka odkazy na témata, které popisují, jak nastavit různé vědě prostředí data používá Cortana proces pro analýzu (CAP).

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

Pokud chcete používat Azure Machine Learning Studio, musíte mít pracovní prostor Machine Learning. Tento pracovní prostor obsahuje nástroje potřebné k vytváření, správě a publikování experimentů.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="to-create-a-workspace"></a>Vytvoření pracovního prostoru
1. Přihlaste se k [portálu Azure](https://portal.azure.com/)

    > [!NOTE]
    > Přihlaste se a vytvořit pracovní prostor, musíte být správce předplatného Azure. 
    >
    > 

2. Klikněte na tlačítko **+ nové**

3. Do vyhledávacího pole zadejte **pracovní prostor Machine Learning Studio** a vyberte odpovídající položku. Potom klikněte na vybrat **vytvořit** v dolní části stránky.

4. Zadejte informace o pracovním prostoru:

    - *Název pracovního prostoru* může být až 260 znaků, ne končí v prostoru. Název nesmí obsahovat tyto znaky: `< > * % & : \ ? + /`
    - *Plán webové služby* vyberete (nebo vytvořte), spolu s příslušnými *cenová úroveň* můžete vybrat, se používá při nasazování webových služeb z tohoto pracovního prostoru.

    ![Vytvořit nový pracovní prostor](./media/create-workspace/create-new-workspace.png)

5. Klikněte na možnost **Vytvořit**.

Po nasazení pracovním prostoru můžete ho otevřít v nástroji Machine Learning Studio.

1. Procházet a strojového učení Studio na [ https://studio.azureml.net/ ](https://studio.azureml.net/).

2. V pravém horním rohu vyberte pracovní prostor.

    ![Vyberte pracovní prostor](./media/create-workspace/open-workspace.png)

3. Klikněte na tlačítko **Moje experimenty**.

    ![Otevřete experimenty](./media/create-workspace/my-experiments.png)

Informace o správě pracovního prostoru najdete v tématu [spravovat pracovní prostor služby Azure Machine Learning](manage-workspace.md).
Pokud narazíte na potíže při vytváření pracovního prostoru, přečtěte si téma [Průvodce odstraňováním potíží: Vytvořte a připojte se k pracovní prostor Machine Learning](troubleshooting-creating-ml-workspace.md).


## <a name="sharing-an-azure-machine-learning-workspace"></a>Sdílení pracovní prostor služby Azure Machine Learning
Jednou Machine Learning při vytváření pracovního prostoru, můžete uživatele pozvat, do pracovního prostoru sdílet přístup do pracovního prostoru a všechny jeho experimenty, datové sady, poznámkové bloky, atd. Můžete přidat uživatele v jednom ze dvou rolí:

* **Uživatel** -prostoru uživatel může vytvořit, otevřít, upravit a odstranit experimenty, datové sady atd., v pracovním prostoru.
* **Vlastník** – vlastníka můžete pozvat a odebírání uživatelů v pracovním prostoru, kromě uživatele, můžete provést.

> [!NOTE]
> Účet správce, který vytvoří pracovním prostoru se automaticky přidá do pracovního prostoru jako pracovní prostor vlastníka. Ale jiní správci nebo uživatelé v tomto předplatném nejsou automaticky udělí přístup k pracovním prostoru – je potřeba je explicitně pozvat.
> 
> 

### <a name="to-share-a-workspace"></a>Chcete-li sdílet pracovního prostoru

1. Přihlaste se k strojového učení Studio na [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. V levém panelu klikněte na tlačítko **nastavení**

3. Klikněte **uživatelé** karta

4. Klikněte na tlačítko **POZVAT uživatele více** v dolní části stránky

    ![Nastavení Studio](./media/create-workspace/settings.png)

5. Zadejte jeden nebo více e-mailové adresy. Uživatelé, potřebujete účet Microsoft nebo účet organizace (z Azure Active Directory).

6. Vyberte, zda chcete přidat uživatele jako vlastníka nebo uživatele.

7. Klikněte **OK** značku zaškrtnutí.

Každý uživatel, které přidáte obdrží e-mail s pokyny, jak se přihlásit do sdíleného pracovního prostoru.

> [!NOTE]
> Pro uživatele, abyste mohli nasadit nebo spravovat webových služeb v tomto pracovním prostoru musí být Přispěvatel nebo správce v rámci předplatného Azure. 



