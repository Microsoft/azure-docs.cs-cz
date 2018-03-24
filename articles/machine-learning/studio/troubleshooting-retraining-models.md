---
title: Řešení potíží s retraining webové služby Azure Machine Learning Classic | Microsoft Docs
description: Identifikujte a opravte narazil běžné problémy, když jsou retraining modelu pro webové služby Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 011/01/2017
ms.openlocfilehash: f5b826decd360ea0e8c2394c4205fafcf6cb16d0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-classic-web-service"></a>Řešení potíží s retraining webové služby Azure Machine Learning Classic
## <a name="retraining-overview"></a>Retraining – přehled
Když nasadíte prediktivní experiment jako vyhodnocování webové služby je statický model. Jakmile nová data k dispozici nebo pokud příjemce rozhraní API má svá vlastní data, musí být retrained modelu. 

Kompletní a podrobný postup retraining procesu Classic webové služby, najdete v části [Přeučování Machine Learning modely prostřednictvím kódu programu](retrain-models-programmatically.md).

## <a name="retraining-process"></a>Retraining procesu
Když potřebujete přeučování webovou službu, musíte přidat další některé jeho součásti:

* Webovou službu nasazenou z výukový Experiment. Musí mít experiment **výstup webové služby** modulu připojené k výstupu **Train Model** modulu.  
  
    ![Připojte k train model výstup webové služby.][image1]
* Nový koncový bod přidán do vyhodnocování webovou službu.  Můžete přidat koncový bod programově pomocí ukázkový kód, kterou se odkazuje v Machine Learning Přeučování modelů prostřednictvím kódu programu tématu nebo prostřednictvím portálu webové služby Azure Machine Learning.

Ukázka kódu C# ze stránky nápovědy školení webové rozhraní API pak můžete přeučování modelu. Po vyhodnocení výsledky a spokojeni s nimi, je třeba aktualizovat pro cvičný model vyhodnocování webové službě pomocí nového koncového bodu, který jste přidali.

Hlavní kroky, které je nutné provést při programovém modelu s všechny části na místě, jsou následující:

1. Volání webové služby školení: volání je do dávky spuštění služby (BES), není žádosti o odpověď služby (záznamy RR). Ukázkový kód jazyka C# můžete na stránce nápovědy rozhraní API pro volání. 
2. Najít hodnoty *BaseLocation*, *RelativeLocation*, a *SasBlobToken*: tyto hodnoty jsou vráceny ve výstupu z volání k webové službě školení. 
   ![zobrazuje výstup retraining ukázka a BaseLocation, RelativeLocation a SasBlobToken hodnoty.][image6]
3. Aktualizace přidané koncového bodu z vyhodnocování webové služby s novou trénovaného modelu: pomocí ukázkový kód, který je součástí Machine Learning Přeučování modelů prostřednictvím kódu programu, aktualizaci nový koncový bod, které jste přidali do vyhodnocování model s nově trénovaného modelu z Školení webové služby.

## <a name="common-obstacles"></a>Běžné překážek
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Zkontrolujte, jestli máte správnou adresu URL oprava
Adresa URL opravy, kterou používáte musí být přidružený nový vyhodnocovací koncový bod, které jste přidali k webové službě vyhodnocování. Existuje několik způsobů, jak získat adresu URL opravy:

**Možnost 1: programově**

Pokud chcete získat správnou adresu URL opravy:

1. Spustit [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) ukázkový kód.
2. Z výstupu AddEndpoint, Najít *HelpLocation* hodnotu a zkopírujte adresu URL.
   
   ![HelpLocation ve výstupu addEndpoint vzorku.][image2]
3. Vložte adresu URL do prohlížeče, přejděte na stránku, která poskytuje odkazy na nápovědu pro webovou službu.
4. Klikněte **aktualizace prostředků** odkazu k otevření stránky nápovědy opravy.

**Možnost 2: Použití portálu Azure Machine Learning webové služby**

1. Přihlaste se k [webové služby Azure Machine Learning](https://services.azureml.net/) portálu.
2. Klikněte na tlačítko **webové služby** nebo **Classic webové služby** v horní části.
4. Klikněte na práci s vyhodnocování webové služby (pokud nebyl upravit výchozí název webové služby, je vyprší za "[hodnocení Exp.]").
5. Klikněte na tlačítko **+ nový**.
6. Po přidání koncového bodu, klikněte na název koncového bodu.
7. V části **oprava** adresu URL, klikněte na tlačítko **rozhraní API nápovědy** chcete otevřít stránku nápovědy oprav.

> [!NOTE]
> Pokud jste přidali koncový bod k webové službě školení místo prediktivní webové služby, zobrazí se chybová zpráva po kliknutí na tlačítko **aktualizace prostředků** odkaz: "líto, ale tato funkce není podporována nebo k dispozici v Tento kontext. Tato webová služba nemá žádné aktualizovat prostředky. Omlouváme se za nepříjemnosti a práce na zlepšení tento pracovní postup."
> 
> 

Na stránce nápovědy oprava obsahuje adresu URL opravy, je nutné použít a poskytuje ukázkový kód, které můžete použít k volání.

![Adresa URL opravy.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Zkontrolujte aktualizaci správný vyhodnocování koncový bod
* Není oprava webovou službu školení: operace opravy se musí provést na vyhodnocování webové službě.
* Výchozí koncový bod webové služby není oprava: operace opravy se musí provést na nové vyhodnocování webové služby koncového bodu, který jste přidali.

Můžete ověřit, které webové služby, koncový bod je na portálu pro webové služby. 

> [!NOTE]
> Ujistěte se, že přidáváte koncový bod do prediktivní webové služby není školení webovou službu. Pokud jste nasadili správně školení a prediktivní webové služby, měli byste vidět dvě samostatné webové služby, které jsou uvedené. Prediktivní webové služby musí končit "[prediktivní exp.]".
> 
> 

1. Přihlaste se k [webové služby Azure Machine Learning](https://services.azureml.net/) portálu.
2. Klikněte na tlačítko **webové služby** nebo **Classic webové služby**.
3. Vyberte prediktivní webové služby.
4. Ověřte, že váš nový koncový bod byl přidán k webové službě.

### <a name="check-that-your-workspace-is-in-the-same-region-as-the-web-service"></a>Zkontrolujte, zda je pracovní prostor ve stejné oblasti jako webovou službu
1. Přihlaste se k [strojového učení Studio](https://studio.azureml.net/).
2. V horní části klikněte na rozevírací seznam vašich pracovních prostorů.

   ![Machine learning oblast uživatelského rozhraní.][image4]

3. Zkontrolujte oblast, kterou je pracovní prostor v.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/check-workspace-region.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
