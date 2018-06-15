---
title: V aplikaci Excel add-in pro Machine Learning webové služby | Microsoft Docs
description: Jak používat Azure Machine Learning webové služby přímo v aplikaci Excel bez psaní jakéhokoli kódu.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 9618079d-502f-4974-a3e2-8f924042a23f
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 2/1/2018
ms.openlocfilehash: 68e2f72dfd8cc58d42263f4b6378d89304aaaa4d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834188"
---
# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Doplněk Excelu pro webové služby Azure Machine Learning
Excel usnadňuje volání webové služby přímo bez nutnosti psaní jakéhokoli kódu.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Kroky při použití webové služby existující v sešitu

1. Otevřete [ukázkový soubor aplikace Excel](http://aka.ms/amlexcel-sample-2), který obsahuje doplněk aplikace Excel a data o cestujících na Titanic.
2. Vyberte webovou službu kliknutím-"Titanic pozůstalým předpověď (ukázka doplňku Excel) [skóre]" v tomto příkladu.
   
    ![Vyberte webovou službu][01]
3. Tím přejdete **Predict** části.  Tento sešit už obsahuje ukázková data, ale pro prázdný sešit můžete vybrat buňku v aplikaci Excel a klikněte na tlačítko **pomocí ukázkových dat**.
4. Vyberte data, se záhlavími a klikněte na ikonu rozsah vstupní data.  Ujistěte se, že je zaškrtnuté políčko "data obsahují záhlaví".
5. V části **výstup**, zadejte číslo buňky, kde má výstup, například "H1" zde být.
6. Klikněte na tlačítko **předpovědi**.
   
    ![Předpověď části][02]

Nasazení webové služby nebo použijte existující webovou službu. Další informace o nasazení webové služby najdete v tématu [návod krok 5: nasazení služby Azure Machine Learning webové](walkthrough-5-publish-web-service.md).

Získáte klíč rozhraní API pro webovou službu. Tam, kde můžete provést tuto akci, závisí na tom, jestli jste publikovali webovou službu Classic Machine Learning webové služby Machine Learning nové.

**Použít klasický webovou službu** 

1. V nástroji Machine Learning Studio, klikněte **webové služby** v levém podokně a pak vyberte webovou službu.
   
    ![Studio vyberte webové služby][04]
2. Zkopírujte klíč rozhraní API pro webovou službu.
   
    ![Klíč Studio rozhraní API][05]
3. Na **řídicí panel** pro webovou službu, klikněte na **požadavků a odpovědí** odkaz.
4. Vyhledejte **URI požadavku** části.  Zkopírujte a uložte adresu URL.

> [!NOTE]
> Je nyní možné pro přihlášení do [webové služby Azure Machine Learning](https://services.azureml.net) portál k získání klíč rozhraní API pro webovou službu Classic Machine Learning.
> 
> 

**Použít novou webovou službu**

1. V [webové služby Azure Machine Learning](https://services.azureml.net) portálu klikněte na **webové služby**, potom vyberte svoji webovou službu. 
2. Klikněte na tlačítko **využívat**.
3. Vyhledejte **informace o základní spotřeby** části. Zkopírujte a uložte **primární klíč** a **požadavků a odpovědí** adresy URL.

## <a name="steps-to-add-a-new-web-service"></a>Postup přidání nové webové služby

1. Nasazení webové služby nebo použijte existující webovou službu. Další informace o nasazení webové služby najdete v tématu [návod krok 5: nasazení služby Azure Machine Learning webové](walkthrough-5-publish-web-service.md).
2. Klikněte na tlačítko **využívat**.
3. Vyhledejte **informace o základní spotřeby** části. Zkopírujte a uložte **primární klíč** a **požadavků a odpovědí** adresy URL.
4. V aplikaci Excel, přejděte na **webové služby** část (Pokud jste v **Predict** klikněte na šipku Zpět Přejít na seznam webové služby).
   
    ![Přejděte na výběr webové služby][03]
5. Klikněte na tlačítko **přidat webovou službu**.
6. Vložte adresu URL do aplikace Excel doplňku textové pole s popiskem **URL**.
7. Vložte klíč rozhraní API nebo primární do textového pole s názvem bez přípony **klíč rozhraní API**.
8. Klikněte na tlačítko **Add** (Přidat).
   
    ![Adresa URL a klíč API pro classic webovou službu.][06]
9. Webovou službu, postupujte podle předchozích pokynů, "Postup použijte existující webové služby."

## <a name="sharing-your-workbook"></a>Sdílení sešitu
Pokud jste uložili vašeho sešitu, rozhraní API nebo primární klíč pro webové služby, které jste přidali také uloženy. To znamená, že sešit by měly sdílet jenom s osob, kterým důvěřujete.

Požádejte všechny dotazy v následující části komentář nebo na našem [fórum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/excel-add-in-for-web-services/image1.png
[02]: ./media/excel-add-in-for-web-services/image2.png
[03]: ./media/excel-add-in-for-web-services/image3.png
[04]: ./media/excel-add-in-for-web-services/image4.png
[05]: ./media/excel-add-in-for-web-services/image5.png
[06]: ./media/excel-add-in-for-web-services/image6.png
