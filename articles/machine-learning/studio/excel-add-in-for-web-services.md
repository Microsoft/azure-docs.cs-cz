---
title: Doplněk Excelu pro webové služby
titleSuffix: ML Studio (classic) - Azure
description: Jak používat Azure Machine Learning Web services přímo v aplikaci Excel bez psaní kódu.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: f7b5ca7112a6fb79586dc66b385e8015fe10e0b0
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153447"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Doplněk Excelu pro webové služby Azure Machine Learning Studio (Classic)
Excel usnadňuje volání webové služby přímo, bez nutnosti psát jakýkoli kód.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Postup používání webové služby existující v sešitu

1. Otevřete [vzorový excelový soubor](https://aka.ms/amlexcel-sample-2), který obsahuje excelový doplněk a data o cestujících na Titanic. 
 
    > [!NOTE]
    > Zobrazí se že seznam webových služeb týkající se do souboru a v dolní části zaškrtávacího políčka "Automatické předpovědět". Pokud povolíte automatické předpovídání, předpovědi **všech** služeb se aktualizuje pokaždé, když dojde ke změně vstupů. Pokud není zaškrtnuto budete muset kliknout na "Předpovědět vše" pro aktualizaci. Pro povolení automatického. odhad na úrovni služby přejít ke kroku 6.

2. Kliknutím ji vyberte webovou službu – "Titanic pozůstalým prediktivní (ukázka doplňku Excel) [skóre]" v tomto příkladu.
   
    ![Vyberte webovou službu](./media/excel-add-in-for-web-services/image1.png)
3. Tím přejdete do části **předpověď** .  Tento sešit už obsahuje ukázková data, ale u prázdného sešitu můžete vybrat buňku v Excelu a kliknout na **použít vzorová data**.
4. Vyberte data, která se záhlavími a klikněte na ikonu rozsah vstupní data.  Ujistěte se, že je zaškrtnuté políčko "má data obsahují záhlaví".
5. V části **výstup**zadejte číslo buňky, kde má být výstup, například "H1".
6. Klikněte na **předpověď**. Pokud zaškrtnete políčko "auto-predict" jakákoli změna ve vybrané oblasti (úlohy uvedené jako vstup) spustí požadavek a aktualizace buněk výstup bez nutnosti až stisknete tlačítko predict.
   
    ![Předpověď oddílu](./media/excel-add-in-for-web-services/image1.png)

Nasazení webové služby nebo použijte existující webové služby. Další informace o nasazení webové služby najdete v [kurzu 3: nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md).

Získání klíče rozhraní API pro webovou službu. Pokud provedete tuto akci závisí na, jestli jste publikovali webovou službu Classic Machine Learning webové služby Machine Learning nové.

**Použití klasické webové služby** 

1. V Machine Learning Studio (Classic) klikněte v levém podokně na část **webové služby** a pak vyberte webovou službu.
   
    ![Vyberte Studio webové služby](./media/excel-add-in-for-web-services/image4.png)
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

1. Nasazení webové služby nebo použijte existující webové služby. Další informace o nasazení webové služby najdete v [kurzu 3: nasazení modelu úvěrového rizika](tutorial-part3-credit-risk-deploy.md).
2. Klikněte na možnost **spotřebovat**.
3. Vyhledejte část **základní informace o spotřebě** . Zkopírujte a uložte **primární klíč** a adresu URL **požadavku-odpovědi** .
4. V Excelu přejděte do části **webové služby** (Pokud jste v části **předpověď** , přejděte kliknutím na šipku zpět do seznamu webových služeb).
   
    ![Přejít na vybranou webovou službu](./media/excel-add-in-for-web-services/image3.png)
5. Klikněte na **Přidat webovou službu**.
6. Vložte adresu URL do textového pole excelový doplněk s názvem **URL**.
7. Vložte rozhraní API/primární klíč do textového pole s popiskem **klíč rozhraní API**.
8. Klikněte na **Přidat**.
   
    ![Adresa URL a klíč rozhraní API pro klasické webové služby.](./media/excel-add-in-for-web-services/image6.png)
9. Webová služba, postupujte podle předchozích pokynů, "Postup použijte existující webové služby."

## <a name="sharing-your-workbook"></a>Sdílení vašeho sešitu
Pokud váš sešit uložit, rozhraní API a primární klíč pro webové služby, které jste přidali také uloží. To znamená, že sešit by měly sdílet jenom uživatele, kterému můžete důvěřovat.

Požádejte o všechny otázky v následující části komentáře nebo na našem [fóru](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
