---
title: Vytvoření pracovního prostoru
titleSuffix: ML Studio (classic) - Azure
description: Pokud chcete použít Azure Machine Learning Studio (Classic), musíte mít pracovní prostor Machine Learning Studio (Classic). Tento pracovní prostor obsahuje nástroje potřebné k vytváření, správě a publikování experimentů.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 91ba4d1f7d32071cce0de1de528abf02982ce7be
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427618"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Vytvoření a sdílení pracovního prostoru Azure Machine Learning Studio (Classic)

Pokud chcete použít Azure Machine Learning Studio (Classic), musíte mít pracovní prostor Machine Learning Studio (Classic). Tento pracovní prostor obsahuje nástroje potřebné k vytváření, správě a publikování experimentů.

## <a name="create-a-studio-classic-workspace"></a>Vytvořit pracovní prostor studia (Classic)

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

    > [!NOTE]
    > Pokud se chcete přihlásit a vytvořit pracovní prostor studia (Classic), musíte být správcem předplatného Azure. 
    >
    > 

2. Klikněte na tlačítko **+ nová**

3. Do vyhledávacího pole zadejte **Machine Learning Studio (klasický) pracovní prostor** a vyberte si vyhovující položku. Potom klikněte na vybrat **vytvořit** v dolní části stránky.

4. Zadejte informace o pracovním prostoru:

   - *Název pracovního prostoru* může být až 260 znaků, není koncovou mezerou. Název nemůže obsahovat tyto znaky: `< > * % & : \ ? + /`
   - *Plán web service* vyberete (nebo vytvoření), spolu s přidruženou *cenovou úroveň* vybrat, se používá při nasazení webové služby z tohoto pracovního prostoru.

     ![Vytvořit nový pracovní prostor studia (Classic)](./media/create-workspace/create-new-workspace.png)

5. Klikněte na **Vytvořit**.

> [!NOTE]
> Machine Learning Studio (Classic) spoléhá na účet služby Azure Storage, který poskytnete k uložení zprostředkujících dat při spuštění pracovního postupu. Po vytvoření pracovního prostoru, pokud se odstraní účet úložiště, nebo pokud se přístupové klíče změní, pracovní prostor přestane fungovat a všechny experimenty v tomto pracovním prostoru selžou.
Pokud nechtěně odstraníte účet úložiště, vytvořte znovu účet úložiště se stejným názvem ve stejné oblasti jako odstraněný účet úložiště a znovu synchronizujte přístupový klíč. Pokud jste změnili přístupové klíče účtu úložiště, znovu je v pracovním prostoru synchronizujte přes portál Azure.

Po nasazení pracovního prostoru ho můžete otevřít v Machine Learning Studio (Classic).

1. V [https://studio.azureml.net/](https://studio.azureml.net/)přejděte na Machine Learning Studio (Classic).

2. V pravém horním rohu vyberte svůj pracovní prostor.

    ![Výběr pracovního prostoru](./media/create-workspace/open-workspace.png)

3. Klikněte na tlačítko **Moje experimenty**.

    ![Otevřít experimentů](./media/create-workspace/my-experiments.png)

Informace o správě pracovního prostoru studia (Classic) najdete v tématu [Správa pracovního prostoru Azure Machine Learning Studio (Classic)](manage-workspace.md).
Pokud narazíte na problém s vytvořením pracovního prostoru, přečtěte si téma [Průvodce odstraňováním potíží: vytvoření a připojení k pracovnímu prostoru Machine Learning Studio (Classic)](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Sdílení pracovního prostoru Azure Machine Learning Studio (Classic)
Jakmile se vytvoří pracovní prostor Machine Learning Studio (Classic), můžete uživatele pozvat do svého pracovního prostoru a sdílet tak přístup k vašemu pracovnímu prostoru a všem jeho experimentům, datovým sadám, poznámkovým blokům atd. Uživatele můžete přidat v jedné ze dvou rolí:

* **Uživatel** -uživatel pracovní prostor může vytvoření, otevření, úprava a odstranění experimenty, datové sady atd., v pracovním prostoru.
* **Vlastník** – může pozvat vlastník a odebírání uživatelů v pracovním prostoru, kromě uživatele, můžete provést.

> [!NOTE]
> Účet správce, který vytvoří pracovní prostor se automaticky přidá do pracovního prostoru jako vlastníka pracovního prostoru. Ale jiní správci nebo uživatelé v tomto předplatném nejsou automaticky udělí přístup k pracovnímu prostoru – je potřeba explicitně pozvánku.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Sdílení pracovního prostoru studia (Classic)

1. Přihlaste se k Machine Learning Studio (Classic) na [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. Na levém panelu klikněte na tlačítko **nastavení**

3. Klikněte na tlačítko **uživatelé** kartu

4. Klikněte na tlačítko **POZVAT další uživatele** v dolní části stránky

    ![Nastavení sady Studio](./media/create-workspace/settings.png)

5. Zadejte jeden nebo více e-mailové adresy. Uživatelé potřebují platný účet Microsoft nebo účtu organizace (z Azure Active Directory).

6. Vyberte, zda chcete přidat uživatele jako vlastníka nebo uživatele.

7. Klikněte na tlačítko **OK** značku zaškrtnutí.

Každý uživatel, kterého přidáte obdrží e-mail s pokyny, jak se přihlásit do sdíleného pracovního prostoru.

> [!NOTE]
> Pro uživatele, abyste mohli nasazovat nebo spravovat webové služby v tomto pracovním prostoru musí být přispěvatelem nebo správcem v rámci předplatného Azure. 



