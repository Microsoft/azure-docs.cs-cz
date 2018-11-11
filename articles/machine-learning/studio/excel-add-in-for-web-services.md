---
title: Aplikace Excel add-in pro Machine Learning Web services | Dokumentace Microsoftu
description: Jak používat Azure Machine Learning Web services přímo v aplikaci Excel bez psaní kódu.
services: machine-learning
documentationcenter: ''
author: marthalc
ms.author: marthalc
ms.assetid: 9618079d-502f-4974-a3e2-8f924042a23f
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 2/1/2018
ms.openlocfilehash: ed3fc6d1d2a4b674e2866d2e168fb72490575869
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281829"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-web-services"></a>Doplněk Excelu pro webové služby Azure Machine Learning Studio
Excel usnadňuje volání webové služby přímo, bez nutnosti psát jakýkoli kód.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Postup používání webové služby existující v sešitu

1. Otevřít [Excelový soubor ukázka](https://aka.ms/amlexcel-sample-2), který obsahuje doplněk aplikace Excel a data o cestujících Titaniku. 
 
> [!NOTE]
> Zobrazí se že seznam webových služeb týkající se do souboru a v dolní části zaškrtávacího políčka "Automatické předpovědět". Pokud povolíte automatické odhad predikce **všechny** vašich služeb se aktualizují pokaždé, když dojde ke změně na vstupy. Pokud není zaškrtnuto budete muset kliknout na "Předpovědět vše" pro aktualizaci. Pro povolení automatického. odhad na úrovni služby přejít ke kroku 6.

2. Kliknutím ji vyberte webovou službu – "Titanic pozůstalým prediktivní (ukázka doplňku Excel) [skóre]" v tomto příkladu.
   
    ![Vyberte webovou službu][01]
3. Tím přejdete **Predict** oddílu.  Tento sešit už obsahuje ukázková data, ale pro prázdný sešit můžete vybrat buňku v aplikaci Excel a klikněte na tlačítko **použít vzorová data**.
4. Vyberte data, která se záhlavími a klikněte na ikonu rozsah vstupní data.  Ujistěte se, že je zaškrtnuté políčko "má data obsahují záhlaví".
5. V části **výstup**, zadejte počet buněk, ve kterém má výstup, například "H1" tady se.
6. Klikněte na tlačítko **předpovědět**. Pokud zaškrtnete políčko "auto-predict" jakákoli změna ve vybrané oblasti (úlohy uvedené jako vstup) spustí požadavek a aktualizace buněk výstup bez nutnosti až stisknete tlačítko predict.
   
    ![Předpověď oddílu][02]

Nasazení webové služby nebo použijte existující webové služby. Další informace o nasazení webové služby najdete v tématu [návod krok 5: nasazení služby Azure Machine Learning Web](walkthrough-5-publish-web-service.md).

Získání klíče rozhraní API pro webovou službu. Pokud provedete tuto akci závisí na, jestli jste publikovali webovou službu Classic Machine Learning webové služby Machine Learning nové.

**Pomocí webové služby Classic** 

1. V nástroji Machine Learning Studio, klikněte na tlačítko **webových služeb** části v levém podokně a pak vyberte webovou službu.
   
    ![Vyberte Studio webové služby][04]
2. Zkopírujte klíč rozhraní API pro webovou službu.
   
    ![Klíč rozhraní API sady Studio][05]
3. Na **řídicí panel** karta pro webovou službu, klikněte na tlačítko **žádostí a odpovědí** odkaz.
4. Hledat **Request URI** oddílu.  Zkopírujte a uložte adresu URL.

> [!NOTE]
> Nyní je možné se přihlásit [Azure Machine Learning Web Services](https://services.azureml.net) portál k získání klíče rozhraní API pro webové služby Classic Machine Learning.
> 
> 

**Pomocí nové webové služby**

1. V [Azure Machine Learning Web Services](https://services.azureml.net) portálu klikněte na **webových služeb**, pak vyberte webovou službu. 
2. Klikněte na tlačítko **využívat**.
3. Hledat **informace o základní spotřeby** oddílu. Zkopírujte a uložte **primární klíč** a **Request-Response** adresy URL.

## <a name="steps-to-add-a-new-web-service"></a>Postup přidání nové webové služby

1. Nasazení webové služby nebo použijte existující webové služby. Další informace o nasazení webové služby najdete v tématu [návod krok 5: nasazení služby Azure Machine Learning Web](walkthrough-5-publish-web-service.md).
2. Klikněte na tlačítko **využívat**.
3. Hledat **informace o základní spotřeby** oddílu. Zkopírujte a uložte **primární klíč** a **Request-Response** adresy URL.
4. V aplikaci Excel přejděte na **webových služeb** části (Pokud jste v **Predict** klikněte na šipku zpět a přejděte na seznam webové služby).
   
    ![Přejít na vybranou webovou službu][03]
5. Klikněte na tlačítko **přidat webovou službu**.
6. Vložte adresu URL do aplikace Excel, přidejte do textového pole s popiskem **URL**.
7. Vložení rozhraní API a primární klíč do textového pole s popiskem **klíč rozhraní API**.
8. Klikněte na tlačítko **Add** (Přidat).
   
    ![Adresa URL a klíč rozhraní API pro klasické webové služby.][06]
9. Webová služba, postupujte podle předchozích pokynů, "Postup použijte existující webové služby."

## <a name="sharing-your-workbook"></a>Sdílení vašeho sešitu
Pokud váš sešit uložit, rozhraní API a primární klíč pro webové služby, které jste přidali také uloží. To znamená, že sešit by měly sdílet jenom uživatele, kterému můžete důvěřovat.

Jakékoli otázky v části komentáře nebo na našem [fórum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/excel-add-in-for-web-services/image1.png
[02]: ./media/excel-add-in-for-web-services/image2.png
[03]: ./media/excel-add-in-for-web-services/image3.png
[04]: ./media/excel-add-in-for-web-services/image4.png
[05]: ./media/excel-add-in-for-web-services/image5.png
[06]: ./media/excel-add-in-for-web-services/image6.png
