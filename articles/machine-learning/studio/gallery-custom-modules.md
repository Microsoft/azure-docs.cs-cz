---
title: Vlastní moduly Azure AI Galerie | Microsoft Docs
description: Zjistit moduly vlastní machine learning v Azure AI Gallery.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 16037a84-dad0-4a8c-9874-a1d3bd551cf0
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.openlocfilehash: c53bab2e838425dfdd124e64c3d7d3114fa30429
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834426"
---
# <a name="discover-custom-machine-learning-modules-in-azure-ai-gallery"></a>Zjistit moduly vlastní machine learning v Azure AI Gallery
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="custom-modules-for-machine-learning-studio"></a>Vlastní moduly pro Machine Learning Studio
Galerie Azure AI nabízí několik [vlastní moduly](https://gallery.cortanaintelligence.com/customModules) , rozšířit možnosti Azure Machine Learning Studio. Můžete naimportovat moduly pro použití v experimentů, takže můžete vyvíjet i pokročilejší řešení prediktivní analýzy.

V současné době Galerie nabízí modulů na *časové řady analytics*, *přidružení pravidla*, *clustering algoritmy* (kromě k-means), a  *vizualizace*a dalších modulů nástroj centrem.


## <a name="discover"></a>Informace
Chcete-li procházet vlastní moduly [v galerii](http://gallery.cortanaintelligence.com)v části **Další**, vyberte **vlastní moduly**.

![Vyberte vlastní moduly na domovské stránce Galerie](./media/gallery-custom-modules/select-custom-modules-in-gallery.png)

**[Vlastní moduly](https://gallery.cortanaintelligence.com/customModules)** stránky zobrazí seznam nedávno přidané a Oblíbené moduly. Chcete-li zobrazit všechny vlastní moduly, vyberte **zobrazit všechny** tlačítko. Chcete-li vyhledat konkrétní vlastní modul, vyberte **zobrazit všechny**a potom vyberte filtr kritéria. Také můžete zadat hledaný text v **vyhledávání** pole v horní části stránky galerie.

![Vyberte možnost "najdete všechny" procházet všechny vlastní moduly](./media/gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>Pochopení

Chcete-li pochopit, jak publikované vlastní modul funguje, vyberte vlastní modul, který chcete otevřít stránku Podrobnosti o modulu. Stránce s podrobnostmi o zajišťuje konzistentní a informativní learning prostředí. Například stránce s podrobnostmi o označuje účel modulu a vypíše očekávané vstupy, výstupy a parametry. Stránce s podrobnostmi o také obsahuje odkaz na základní zdrojový kód, který můžete zkontrolovat a upravit.

### <a name="comment-and-share"></a>Komentáře a sdílené složky
Na stránce Podrobnosti, vlastní modul **komentáře** části, můžete komentář, poskytnout zpětnou vazbu nebo klást otázky týkající se modul. Modul můžete také sdílet s přátele nebo kolegy na Twitteru nebo LinkedIn. Také můžete e-mailem odkaz na stránku podrobností modulu pozvaným jiní uživatelé při zobrazení stránky.

![Sdílet tuto položku s friends](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Přidat vlastní komentáře](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>Import
Všechny vlastní modul z galerie můžete importovat do vlastní experimenty.

Galerie AI Azure nabízí dva způsoby, jak importovat kopii modul:

* **Z Galerie**. Když importujete vlastní modul z galerie, získáte také ukázkový experiment, která poskytuje příklad toho, jak použít modul.
* **V nástroji strojového učení Studio**. Při práci v nástroji Machine Learning Studio můžete importovat všechny vlastní modul (v tomto případě neobdržíte ukázkový experiment).

### <a name="from-the-gallery"></a>Z Galerie

1. V galerii otevřete stránku Podrobnosti o modulu. 
2. Vyberte **Open in Studio**.
   
    ![Otevřít vlastní modul z Galerie](./media/gallery-custom-modules/open-custom-module-from-gallery.png)
   
Každý vlastní modul obsahuje ukázkový experiment, které ukazuje, jak chcete použít modul. Když vyberete **Open in Studio**, otevře se v pracovním prostoru Machine Learning Studio ukázkového experimentu. (Pokud jste již přihlášení do nástroje Studio, zobrazí se výzva k první přihlášení pomocí účtu Microsoft.)

Kromě ukázkového experimentu vlastní modul zkopírován do pracovního prostoru. Také je umístěna do vaší palety modulů se všechny vaše vestavěná nebo vlastní Machine Learning Studio moduly. Ho teď můžete použít v vlastní experimenty, podobně jako ostatní moduly v pracovním prostoru.

### <a name="from-within-machine-learning-studio"></a>V nástroji strojového učení Studio

1. V nástroji Machine Learning Studio, vyberte **nový**.
2. Vyberte **modulu**. Můžete vybrat ze seznamu modulů Galerie nebo vyhledání konkrétního modulu na základě **vyhledávání** pole.
3. Přesuňte ukazatel myši na modul a potom vyberte **Import modulu**. (Chcete-li získat informace o modulu, vyberte **zobrazit v galerii**. Tím přejdete na stránku podrobností modulu v galerii.)
   
    ![Vlastní modul importovat do nástroje Machine Learning Studio](./media/gallery-custom-modules/add-custom-module-in-studio.png)

Vlastní modul se zkopíruje do pracovního prostoru a umístit do vaší palety modulů s moduly vestavěné i vlastní Machine Learning Studio. Ho teď můžete použít v vlastní experimenty, podobně jako ostatní moduly v pracovním prostoru.

## <a name="use"></a>Použití

Bez ohledu na to, kterou metodu zvolit import vlastní modul, při importu modulu, modul se umístí do vaší palety modulů v Machine Learning Studio. Z palety modul můžete použít vlastní modul v jakékoli experimentu v pracovním prostoru, stejně jako ostatní moduly.

Použití importovaného modulu:

1. Vytvoření experimentu nebo otevřete stávající experimentu.
2. Chcete-li rozšířit seznam vlastní moduly v pracovním prostoru, paletě modulů, vyberte **vlastní**. Nalevo od plátna experimentu je paleta modulu.
   
    ![Seznam vlastních modulů v Studio palety](./media/gallery-custom-modules/custom-module-in-studio-palette.png)
3. Vyberte modul, který jste importovali a přetáhněte jej do experimentu.


**[Přejděte do Galerie](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

