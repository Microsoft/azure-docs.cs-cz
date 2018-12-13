---
title: Řešení potíží s přetrénování klasickou webovou službou Machine Learning Studio – Azure | Dokumentace Microsoftu
description: Identifikovat a vyřešit běžné problémy k chybě, když jsou přetrénování modelu pro webové služby Azure Machine Learning Studio.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 75cac53c-185c-437d-863a-5d66d871921e
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.openlocfilehash: 0f12627e169af00f575347796d1f2e79fe1f6fa2
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53252775"
---
# <a name="troubleshooting-the-retraining-of-an-azure-machine-learning-studio-classic-web-service"></a>Řešení potíží s přeučováním klasické webové služby Azure Machine Learning Studio
## <a name="retraining-overview"></a>Přeškolení – přehled
Při nasazování prediktivní experiment jako hodnoticí webové služby je statický model. K dispozici nová data nebo když příjemce rozhraní API má svá vlastní data, musí být retrained modelu. 

Kompletní návod retraining procesu webové služby Classic najdete v tématu [Přeučování Machine Learning modelů prostřednictvím kódu programu](retrain-models-programmatically.md).

## <a name="retraining-process"></a>Přeškolení procesu
Když budete potřebovat přeučování webové služby, je nutné přidat některé další části:

* Webovou službu nasazenou z experimentu školení. Musí mít experimentu **výstup webové služby** modulu přiřazená výstup **Train Model** modulu.  
  
    ![Připojte výstupní webové služby k trénování modelu.][image1]
* Nový koncový bod přidán bodovací webové služby.  Můžete přidat koncový bod prostřednictvím kódu programu pomocí ukázkového kódu, který je odkazováno v Machine Learning Přeučování modelů prostřednictvím kódu programu tématu nebo prostřednictvím portálu Azure Machine Learning Web Services.

Pak můžete použít vzorek C# kód ze stránky nápovědy rozhraní API webové služby školení k programovém přeučení modelů. Po vyhodnocení výsledky a spokojeni, aktualizujete trénovaný model bodování webové služby s použitím nového koncového bodu, který jste přidali.

Dali všechno na místě jsou hlavní kroky, které je třeba provést při programovém přeučení modelu:

1. Volání webové služby školení:  Volání je k Batch Execution Service (BES), ne Request Response Service (RRS). Použitím této ukázky C# kód na stránce rozhraní API nápovědy k uskutečnění volání. 
2. Najít hodnoty *BaseLocation*, *RelativeLocation*, a *SasBlobToken*: Tyto hodnoty jsou vráceny ve výstupu z volání webové služby školení. 
   ![zobrazuje výstup retraining ukázky a BaseLocation RelativeLocation a SasBlobToken hodnoty.][image6]
3. Přidání koncového bodu z hodnoticí webové služby s novou trénovaného modelu aktualizace: Pomocí ukázkového kódu, který je k dispozici ve službě Machine Learning Přeučování modelů prostřednictvím kódu programu, aktualizujte nový koncový bod, kterou jste přidali do vyhodnocení modelu s nově trénovaného modelu z webové služby školení.

## <a name="common-obstacles"></a>Běžné překážky
### <a name="check-to-see-if-you-have-the-correct-patch-url"></a>Zkontrolujte, jestli máte správnou adresu URL oprava
Oprava adresa URL, které používáte musí být přidružený k nový bodovací koncový bod, který jste přidali do hodnoticí webové služby. Existuje mnoho způsobů, jak získat adresu URL opravy:

**Možnost 1: Programově**

Chcete-li získat správnou adresu URL opravy:

1. Spustit [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) ukázkový kód.
2. Z výstupu AddEndpoint najít *HelpLocation* hodnotu a zkopírujte adresu URL.
   
   ![HelpLocation ve výstupu příkazu addEndpoint vzorku.][image2]
3. Vložte adresu URL do prohlížeče přejděte na stránku, která obsahuje odkazy na nápovědu pro webovou službu.
4. Klikněte na tlačítko **aktualizace prostředku** odkaz k otevření stránky s nápovědou opravy.

**Možnost 2: Použití portálu Azure Machine Learning Web Services**

1. Přihlaste se k [Azure Machine Learning Web Services](https://services.azureml.net/) portálu.
2. Klikněte na tlačítko **webových služeb** nebo **klasické webové služby** v horní části.
4. Klikněte na tlačítko pracujete s hodnoticí webové služby (pokud nebyl změnit výchozí název webové služby, skončí v "[bodování Exp.]").
5. Klikněte na tlačítko **+ nová**.
6. Po přidání koncového bodu, klikněte na název koncového bodu.
7. V části **opravy** adresu URL, klikněte na tlačítko **API nápovědy** otevřete oprav stránky s nápovědou.

> [!NOTE]
> Pokud jste přidali na koncový bod webové služby školení místo prediktivní webové služby, zobrazí se následující chyba po kliknutí **aktualizace prostředku** odkaz: "Je nám líto, ale tato funkce není podporována nebo v tomhle kontextu dostupné. Tato webová služba nemá žádné prostředky aktualizovat. Omlouváme se za nepříjemnosti a pracujeme na vylepšení tohoto pracovního postupu."
> 
> 

Stránky s nápovědou PATCH obsahuje adresu URL OPRAVIT, je nutné použít a poskytuje ukázkový kód, který slouží k jeho volání.

![Adresa URL Patch.][image5]

### <a name="check-to-see-that-you-are-updating-the-correct-scoring-endpoint"></a>Zkontrolujte, že aktualizujete správný bodovací koncový bod
* Oprava není školení webové služby: Operace opravy je provést na hodnoticí webové služby.
* Oprava není výchozí koncový bod webové služby: Operace opravy je provést na nové bodovací koncový bod webové služby, který jste přidali.

Můžete ověřit, které webové služby, koncový bod je na portálu webových služeb. 

> [!NOTE]
> Ujistěte se, že chcete přidat koncový bod prediktivní webové služby, ne webová služba školení. Pokud jste nasadili správně Trénovací a prediktivní webové služby, měli byste vidět uvedené dvě samostatné webové služby. Prediktivní webová služba by měla končit "[prediktivní exp.]".
> 
> 

1. Přihlaste se k [Azure Machine Learning Web Services](https://services.azureml.net/) portálu.
2. Klikněte na tlačítko **webové služby** nebo **klasické webové služby**.
3. Vyberte prediktivní webové služby.
4. Ověřte, že byl přidán nový koncový bod webové služby.

### <a name="check-that-your-workspace-is-in-the-same-region-as-the-web-service"></a>Zkontrolujte, jestli je váš pracovní prostor ve stejné oblasti jako webovou službu
1. Přihlaste se k [ve službě Machine Learning Studio](https://studio.azureml.net/).
2. V horní části stránky klikněte na rozevírací seznam vašich pracovních prostorů.

   ![Machine learning oblasti uživatelského rozhraní.][image4]

3. Zkontrolujte oblast pracovního prostoru v.

<!-- Image Links -->

[image1]: ./media/troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/troubleshooting-retraining-a-model/check-workspace-region.png
[image5]: ./media/troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/troubleshooting-retraining-a-model/retraining-output.png
[image7]: ./media/troubleshooting-retraining-a-model/web-services-tab.png
