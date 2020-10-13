---
title: 'ML Studio (klasický): doplněk Excelu pro webové služby – Azure'
description: Jak používat Azure Machine Learning webové služby přímo v aplikaci Excel bez psaní kódu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 69eefce50cefe9f54ed931c96ccbe94b2057cae4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362363"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Doplněk Excelu pro webové služby Azure Machine Learning Studio (Classic)

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


Aplikace Excel usnadňuje volání webových služeb přímo bez nutnosti psát jakýkoli kód.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Postup použití existující webové služby v sešitu

1. Otevřete [vzorový excelový soubor](https://aka.ms/amlexcel-sample-2), který obsahuje excelový doplněk a data o cestujících na Titanic. 
 
    > [!NOTE]
    > - Zobrazí se seznam webových služeb vztahujících se k souboru a dolní políčko pro automatické předvídání. Pokud povolíte automatické předpovídání, předpovědi **všech** služeb se aktualizuje pokaždé, když dojde ke změně vstupů. Pokud toto políčko nezaškrtnuté, budete muset pro aktualizaci kliknout na možnost předpověď vše. Pro povolení automatické předpovědi na úrovni služby přejít na krok 6.
    > - Doplněk Azure Machine Learning Excel bude volat úložiště doplňků Office pro načtení. Pokud vaše organizace zakáže přístup k úložišti doplňků Office, zobrazí se při načítání doplňku chyba. V takovém případě prosím nasaďte Azure Machine Learning excelový doplněk z centra pro správu Microsoft 365. Pak vyvolejte doplněk a přidejte webovou službu ručně tak, že vložíte adresu URL a klíč rozhraní API.

 

2. Vyberte webovou službu kliknutím na ni – "Titanic pozůstalost prediktivní (Ukázka doplňku Excel) [skore]" v tomto příkladu.
   
    ![Vybrat webovou službu](./media/excel-add-in-for-web-services/image1.png)
3. Tím přejdete do části **předpověď** .  Tento sešit už obsahuje ukázková data, ale u prázdného sešitu můžete vybrat buňku v Excelu a kliknout na **použít vzorová data**.
4. Vyberte data se záhlavími a klikněte na ikonu rozsah vstupních dat.  Ujistěte se, že je zaškrtnuté políčko moje data obsahují záhlaví.
5. V části **výstup**zadejte číslo buňky, kde má být výstup, například "H1".
6. Klikněte na **předpověď**. Pokud zaškrtnete políčko automaticky vypovědět, všechny změny ve vybraných oblastech (které jsou zadané jako Input) spustí požadavek a aktualizuje výstupní buňky, aniž by bylo nutné stisknout tlačítko předpověď.
   
    ![Oddíl předpověď](./media/excel-add-in-for-web-services/image1.png)

Nasazení webové služby nebo použití existující webové služby. Další informace o nasazení webové služby najdete v [kurzu 3: nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md).

Získejte klíč rozhraní API pro webovou službu. Kde tuto akci provedete, záleží na tom, jestli jste publikovali klasický Machine Learning webové služby nové webové služby Machine Learning.

**Použití klasické webové služby** 

1. V Machine Learning Studio (Classic) klikněte v levém podokně na část **webové služby** a pak vyberte webovou službu.
   
    ![Studio vyberte webovou službu.](./media/excel-add-in-for-web-services/image4.png)
2. Zkopírujte klíč rozhraní API pro webovou službu.
   
    ![Klíč rozhraní API sady Studio](./media/excel-add-in-for-web-services/image5.png)
3. Na kartě **řídicí panel** pro webovou službu klikněte na odkaz **požadavek/odpověď** .
4. Vyhledejte část **identifikátor URI žádosti** .  Zkopírujte a uložte adresu URL.

> [!NOTE]
> Nyní se můžete přihlásit k portálu [Azure Machine Learning Web Services](https://services.azureml.net) , abyste získali klíč rozhraní API pro klasický Machine Learning webové služby.
> 
> 

**Použít novou webovou službu**

1. Na portálu [Azure Machine Learning Web Services](https://services.azureml.net) klikněte na možnost **webové služby**a pak vyberte webovou službu. 
2. Klikněte na možnost **spotřebovat**.
3. Vyhledejte část **základní informace o spotřebě** . Zkopírujte a uložte **primární klíč** a adresu URL **požadavku-odpovědi** .

## <a name="steps-to-add-a-new-web-service"></a>Postup přidání nové webové služby

1. Nasazení webové služby nebo použití existující webové služby. Další informace o nasazení webové služby najdete v [kurzu 3: nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md).
2. Klikněte na možnost **spotřebovat**.
3. Vyhledejte část **základní informace o spotřebě** . Zkopírujte a uložte **primární klíč** a adresu URL **požadavku-odpovědi** .
4. V Excelu přejděte do části **webové služby** (Pokud jste v části **předpověď** , přejděte kliknutím na šipku zpět do seznamu webových služeb).
   
    ![Přejít na výběr webové služby](./media/excel-add-in-for-web-services/image3.png)
5. Klikněte na **Přidat webovou službu**.
6. Vložte adresu URL do textového pole excelový doplněk s názvem **URL**.
7. Vložte rozhraní API/primární klíč do textového pole s popiskem **klíč rozhraní API**.
8. Klikněte na **Přidat**.
   
    ![Adresa URL a klíč rozhraní API pro klasickou webovou službu.](./media/excel-add-in-for-web-services/image6.png)
9. Chcete-li použít webovou službu, postupujte podle pokynů v části "postup použití existující webové služby".

## <a name="sharing-your-workbook"></a>Sdílení sešitu
Pokud sešit uložíte, uloží se taky rozhraní API/primární klíč pro webové služby, které jste přidali. To znamená, že byste měli sešit sdílet jenom s jednotlivci, kterým důvěřujete.

Požádejte o všechny otázky v následující části komentáře nebo na našem [fóru](https://docs.microsoft.com/answers/topics/azure-machine-learning.html).
