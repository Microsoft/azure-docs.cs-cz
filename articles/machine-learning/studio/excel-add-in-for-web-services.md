---
title: Doplněk aplikace Excel pro webové služby
titleSuffix: ML Studio (classic) - Azure
description: Jak používat webové služby Azure Machine Learning přímo v Excelu bez psaní kódu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e30103589c1baf9a165839cd041ff511a119c5ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204371"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Doplněk Excelu pro webové služby Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Aplikace Excel usnadňuje přímé volání webových služeb bez nutnosti psát libovolný kód.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Postup použití existující webové služby v sešitu

1. Otevřete [ukázkový soubor aplikace Excel](https://aka.ms/amlexcel-sample-2), který obsahuje doplněk aplikace Excel a údaje o cestujících na titaniku. 
 
    > [!NOTE]
    > Zobrazí se seznam webových služeb souvisejících se souborem a v dolní části zaškrtávací políčko "Auto-predict". Pokud povolíte automatické předpovídání předpovědi **všech** vašich služeb budou aktualizovány pokaždé, když dojde ke změně na vstupy. Pokud nezaškrtnuté, budete muset kliknout na "Předpovědět vše" pro aktualizaci. Povolení automatického předvídání na úrovni služby naleznete ke kroku 6.

2. Vyberte webovou službu kliknutím na ni - "Titanic Survivor Predictor (Ukázka doplňku aplikace Excel) [Skóre]" v tomto příkladu.
   
    ![Vybrat webovou službu](./media/excel-add-in-for-web-services/image1.png)
3. Tím přejdete do sekce **Předpovědět.**  Tento sešit již obsahuje ukázková data, ale pro prázdný sešit můžete vybrat buňku v aplikaci Excel a klepnout na **použít ukázková data**.
4. Vyberte data se záhlavími a klepněte na ikonu oblasti vstupních dat.  Zkontrolujte, zda je zaškrtnuté políčko Moje data mají záhlaví.
5. V části **Výstup**zadejte číslo buňky, kde má být výstup, například "H1".
6. Klepněte na tlačítko **Předpovědět**. Pokud zaškrtnete políčko "auto-predict" jakákoli změna ve vybraných oblastech (ty, které jsou zadány jako vstup), spustí požadavek a aktualizaci výstupních buněk bez nutnosti stisknutí tlačítka předpovědět.
   
    ![Oddíl Předpovídat](./media/excel-add-in-for-web-services/image1.png)

Nasaďte webovou službu nebo použijte existující webovou službu. Další informace o nasazení webové služby naleznete v [tématu Návod 3: Nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md).

Získejte klíč rozhraní API pro webovou službu. Kde provedete tuto akci závisí na tom, zda jste publikovali webovou službu Classic Machine Learning nové webové služby Machine Learning.

**Použití klasické webové služby** 

1. V Machine Learning Studio (klasické) klikněte v levém podokně na oddíl **WEB SERVICES** a vyberte webovou službu.
   
    ![Aplikace Studio vybrala webovou službu](./media/excel-add-in-for-web-services/image4.png)
2. Zkopírujte klíč rozhraní API pro webovou službu.
   
    ![Klávesa Studio API](./media/excel-add-in-for-web-services/image5.png)
3. Na kartě **ŘÍDICÍ PANEL** pro webovou službu klikněte na odkaz **POŽADAVEK/ODPOVĚĎ.**
4. Vyhledejte část **Identifikátor URI požadavku.**  Zkopírujte a uložte adresu URL.

> [!NOTE]
> Teď je možné se přihlásit k portálu [Azure Machine Learning Web Services](https://services.azureml.net) k získání klíče rozhraní API pro webovou službu Classic Machine Learning.
> 
> 

**Použití nové webové služby**

1. Na portálu [Azure Machine Learning Web Services](https://services.azureml.net) klikněte na Web **Services**a vyberte webovou službu. 
2. Klepněte na **tlačítko Spotřebovat**.
3. Podívejte se na část **Základní informace o spotřebě.** Zkopírujte a uložte **primární klíč** a adresu URL požadavku **a odpovědi.**

## <a name="steps-to-add-a-new-web-service"></a>Postup přidání nové webové služby

1. Nasaďte webovou službu nebo použijte existující webovou službu. Další informace o nasazení webové služby naleznete v [tématu Návod 3: Nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md).
2. Klepněte na **tlačítko Spotřebovat**.
3. Podívejte se na část **Základní informace o spotřebě.** Zkopírujte a uložte **primární klíč** a adresu URL požadavku **a odpovědi.**
4. V excelu přejděte do části **Webové služby** (pokud jste v části **Předpovědět,** klikněte na šipku zpět a přejděte do seznamu webových služeb).
   
    ![Přejít na výběr webové služby](./media/excel-add-in-for-web-services/image3.png)
5. Klepněte na tlačítko **Přidat webovou službu**.
6. Vložte adresu URL do textového pole doplňku excelu **označeného url**.
7. Vložte klíč ROZHRANÍ API/Primární do textového pole **označeného klíčem ROZHRANÍ API**.
8. Klikněte na **Přidat**.
   
    ![Url a klíč rozhraní API pro klasickou webovou službu.](./media/excel-add-in-for-web-services/image6.png)
9. Chcete-li používat webovou službu, postupujte podle předchozích pokynů "Kroky k použití existující webové služby".

## <a name="sharing-your-workbook"></a>Sdílení sešitu
Pokud sešit uložíte, uloží se také klíč API/Primární pro webové služby, které jste přidali. To znamená, že sešit byste měli sdílet pouze s osobami, kterým důvěřujete.

Ptejte se v následující sekci komentáře nebo na našem [fóru](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
