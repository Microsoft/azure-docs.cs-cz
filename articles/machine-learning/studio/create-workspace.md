---
title: Vytvoření pracovního prostoru
titleSuffix: ML Studio (classic) - Azure
description: Chcete-li používat Azure Machine Learning Studio (klasické), musíte mít machine learning studio (klasické) pracovní prostor. Tento pracovní prostor obsahuje nástroje potřebné k vytváření, správě a publikování experimentů.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 1a391a7a061d1382b5e07b45625c44fc0f5dec54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204456"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Vytvoření a sdílení pracovního prostoru Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Chcete-li používat Azure Machine Learning Studio (klasické), musíte mít machine learning studio (klasické) pracovní prostor. Tento pracovní prostor obsahuje nástroje potřebné k vytváření, správě a publikování experimentů.

## <a name="create-a-studio-classic-workspace"></a>Vytvoření studiového (klasického) pracovního prostoru

1. Přihlášení k [portálu Azure](https://portal.azure.com/)

    > [!NOTE]
    > Pokud se chcete přihlásit a vytvořit studio (klasický) pracovní prostor, musíte být správcepředplatného Azure. 
    >
    > 

2. Klikněte na **+Nový**

3. Do vyhledávacího pole zadejte **Machine Learning Studio (klasický) Pracovní prostor** a vyberte odpovídající položku. Potom vyberte klikněte na **Vytvořit** v dolní části stránky.

4. Zadejte informace o pracovním prostoru:

   - *Název pracovního prostoru* může mít až 260 znaků, což nekončí mezerou. Název nemůže obsahovat tyto znaky:`< > * % & : \ ? + /`
   - Plán *webové služby,* který zvolíte (nebo vytvoříte) spolu s přidruženou *cenovou úrovní,* kterou vyberete, se použije, pokud z tohoto pracovního prostoru nasadíte webové služby.

     ![Vytvoření nového pracovního prostoru Studia (klasického)](./media/create-workspace/create-new-workspace.png)

5. Klikněte na **Vytvořit**.

> [!NOTE]
> Machine Learning Studio (klasické) spoléhá na účet úložiště Azure, který poskytujete k uložení zprostředkujících dat při spuštění pracovního postupu. Po vytvoření pracovního prostoru, pokud je účet úložiště odstraněn nebo pokud se změní přístupové klávesy, pracovní prostor přestane fungovat a všechny experimenty v tomto pracovním prostoru se nezdaří.
Pokud omylem odstraníte účet úložiště, znovu vytvořte účet úložiště se stejným názvem ve stejné oblasti jako odstraněný účet úložiště a znovu synchronizujte přístupový klíč. Pokud jste změnili přístupové klíče účtu úložiště, znovu je v pracovním prostoru synchronizujte přes portál Azure.

Po nasazení pracovního prostoru jej můžete otevřít ve Studiu machine learningu (klasika).

1. Přejděte na Machine Learning [https://studio.azureml.net/](https://studio.azureml.net/)Studio (classic) na .

2. V pravém horním rohu vyberte příslušný pracovní prostor.

    ![Výběr pracovního prostoru](./media/create-workspace/open-workspace.png)

3. Klikněte na **moje experimenty**.

    ![Otevřené experimenty](./media/create-workspace/my-experiments.png)

Informace o správě pracovního prostoru studia (klasické) najdete v [tématu Správa pracovního prostoru Azure Machine Learning Studio (klasické).](manage-workspace.md)
Pokud narazíte na problém s přivytvářením pracovního prostoru, [přečtěte si článek Poradce při potížích s průvodcem: Vytvoření a připojení k pracovnímu prostoru Machine Learning Studio (klasické).](troubleshooting-creating-ml-workspace.md)


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Sdílení pracovního prostoru Azure Machine Learning Studio (klasické)
Po vytvoření pracovního prostoru Machine Learning Studio (classic) můžete pozvat uživatele do pracovního prostoru ke sdílení přístupu k vašemu pracovnímu prostoru a všem jeho experimentům, datovým sestavám, poznámkovým blokům atd. Uživatele můžete přidat v jedné ze dvou rolí:

* **Uživatel** – Uživatel pracovního prostoru může v pracovním prostoru vytvářet, otevírat, upravovat a odstraňovat experimenty, datové sady atd.
* **Vlastník** – Vlastník může pozvat a odebrat uživatele v pracovním prostoru, kromě toho, co uživatel může dělat.

> [!NOTE]
> Účet správce, který vytvoří pracovní prostor, se automaticky přidá do pracovního prostoru jako vlastník pracovního prostoru. Ostatní správci nebo uživatelé v tomto předplatném však nemají automaticky přístup k pracovnímu prostoru – je třeba je explicitně pozvat.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Sdílení pracovního prostoru studia (klasického)

1. Přihlaste se do Machine Learning Studio (classic) na[https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. V levém panelu klikněte na **NASTAVENÍ**

3. Klikněte na kartu **UŽIVATELÉ.**

4. V dolní části stránky klikněte na **Pozvat další uživatele.**

    ![Nastavení studia](./media/create-workspace/settings.png)

5. Zadejte jednu nebo více e-mailových adres. Uživatelé potřebují platný účet Microsoft nebo účet organizace (z Azure Active Directory).

6. Vyberte, zda chcete přidat uživatele jako vlastníka nebo uživatele.

7. Klikněte na zaškrtávací tlačítko **OK.**

Každý uživatel, který přidáte, obdrží e-mail s pokyny, jak se přihlásit ke sdílenému pracovnímu prostoru.

> [!NOTE]
> Aby uživatelé mohli nasadit nebo spravovat webové služby v tomto pracovním prostoru, musí být přispěvatelem nebo správcem předplatného Azure. 



