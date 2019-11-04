---
title: Vytvořit pracovní prostor Machine Learning Studio (Classic)
titleSuffix: Azure Machine Learning Studio (classic)
description: Pokud chcete použít Azure Machine Learning Studio (Classic), musíte mít pracovní prostor Machine Learning Studio (Classic). Tento pracovní prostor obsahuje nástroje potřebné k vytváření, správě a publikování experimentů.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 6302ded4ab951d0490b128989a45c41c013db7ea
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493207"
---
# <a name="create-and-share-an-azure-machine-learning-studio-classic-workspace"></a>Vytvoření a sdílení pracovního prostoru Azure Machine Learning Studio (Classic)

Pokud chcete použít Azure Machine Learning Studio (Classic), musíte mít pracovní prostor Machine Learning Studio (Classic). Tento pracovní prostor obsahuje nástroje potřebné k vytváření, správě a publikování experimentů.

## <a name="create-a-studio-classic-workspace"></a>Vytvořit pracovní prostor studia (Classic)

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

    > [!NOTE]
    > Pokud se chcete přihlásit a vytvořit pracovní prostor studia (Classic), musíte být správcem předplatného Azure. 
    >
    > 

2. Klikněte na **+ Nový** .

3. Do vyhledávacího pole zadejte **Machine Learning Studio (klasický) pracovní prostor** a vyberte si vyhovující položku. Pak vyberte v dolní části stránky klikněte na **vytvořit** .

4. Zadejte informace o pracovním prostoru:

   - *Název pracovního prostoru* může být až 260 znaků, ale nekončí mezerou. Název nesmí obsahovat tyto znaky: `< > * % & : \ ? + /`
   - *Plán webové služby* , který zvolíte (nebo vytvoříte), spolu s přidruženou *cenovou úrovní* , kterou vyberete, se použije, pokud nasadíte webové služby z tohoto pracovního prostoru.

     ![Vytvořit nový pracovní prostor studia (Classic)](./media/create-workspace/create-new-workspace.png)

5. Klikněte na **Vytvořit**.

> [!NOTE]
> Machine Learning Studio (Classic) spoléhá na účet služby Azure Storage, který poskytnete k uložení zprostředkujících dat při spuštění pracovního postupu. Po vytvoření pracovního prostoru, pokud se odstraní účet úložiště, nebo pokud se přístupové klíče změní, pracovní prostor přestane fungovat a všechny experimenty v tomto pracovním prostoru selžou.
Pokud nechtěně odstraníte účet úložiště, vytvořte znovu účet úložiště se stejným názvem ve stejné oblasti jako odstraněný účet úložiště a znovu synchronizujte přístupový klíč. Pokud jste změnili přístupové klíče účtu úložiště, znovu je v pracovním prostoru synchronizujte přes portál Azure.

Po nasazení pracovního prostoru ho můžete otevřít v klasické verzi Machine Learning Studio.

1. V [https://studio.azureml.net/](https://studio.azureml.net/)přejděte na Machine Learning Studio (Classic).

2. V pravém horním rohu vyberte svůj pracovní prostor.

    ![Výběr pracovního prostoru](./media/create-workspace/open-workspace.png)

3. Klikněte na **Moje experimenty**.

    ![Otevřené experimenty](./media/create-workspace/my-experiments.png)

Informace o správě pracovního prostoru studia (Classic) najdete v tématu [Správa pracovního prostoru Azure Machine Learning Studio (Classic)](manage-workspace.md).
Pokud narazíte na problém s vytvořením pracovního prostoru, přečtěte si téma [Průvodce odstraňováním potíží: vytvoření a připojení k pracovnímu prostoru Machine Learning Studio (Classic)](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Sdílení pracovního prostoru Azure Machine Learning Studio (Classic)
Jakmile se vytvoří pracovní prostor Machine Learning Studio (Classic), můžete uživatele pozvat do svého pracovního prostoru a sdílet tak přístup k vašemu pracovnímu prostoru a všem jeho experimentům, datovým sadám, poznámkovým blokům atd. Uživatele můžete přidat v jedné ze dvou rolí:

* **Uživatel pracovní** prostor může vytvořit, otevřít, upravit a odstranit experimenty, datové sady atd. v pracovním prostoru.
* **Vlastník** – vlastník může pozvat a odebrat uživatele v pracovním prostoru, a to i to, co může uživatel dělat.

> [!NOTE]
> Účet správce, který vytváří pracovní prostor, se automaticky přidá do pracovního prostoru jako vlastník pracovního prostoru. Dalším správcům nebo uživatelům v tomto předplatném není automaticky udělen přístup k pracovnímu prostoru – je třeba je pozvat explicitně.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Sdílení pracovního prostoru studia (Classic)

1. Přihlášení k klasické verzi Machine Learning Studio v [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. Na levém panelu klikněte na **Nastavení** .

3. Klikněte na kartu **Uživatelé** .

4. V dolní části stránky klikněte na **pozvat více uživatelů** .

    ![Nastavení studia](./media/create-workspace/settings.png)

5. Zadejte jednu nebo více e-mailových adres. Uživatelé potřebují platný účet Microsoft nebo účet organizace (z Azure Active Directory).

6. Vyberte, zda chcete přidat uživatele jako vlastníka nebo uživatele.

7. Klikněte na tlačítko pro zaškrtnutí tlačítka **OK** .

Každý přidaný uživatel dostane e-mail s pokyny, jak se přihlásit ke sdílenému pracovnímu prostoru.

> [!NOTE]
> Aby mohli uživatelé v tomto pracovním prostoru nasazovat nebo spravovat webové služby, musí být v předplatném Azure přispěvatel nebo správce. 



