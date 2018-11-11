---
title: Vlastní moduly Azure AI Galerie | Dokumentace Microsoftu
description: Zjištění modulů služby vlastní machine learning v galerii Azure AI.
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
ms.openlocfilehash: fe58b335a029e558ce29871483589908f17c03cd
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345687"
---
# <a name="discover-custom-machine-learning-modules-in-azure-ai-gallery"></a>Zjištění modulů služby vlastní machine learning v galerii Azure AI

[Galerie Azure AI](http://gallery.cortanaintelligence.com) nabízí několik [vlastních modulů](https://gallery.cortanaintelligence.com/customModules) , které rozšiřují možnosti nástroje Azure Machine Learning Studio. Můžete importovat další moduly pro použití v své experimenty, takže můžete vyvíjet i sofistikovanějších řešení prediktivní analýzy.

V současné době nabízí v galerii modulů na *časové řady analytics*, *asociačních pravidel*, *clustering algoritmy* (nad rámec k-means), a  *vizualizace*a ostatní moduly nástroj centrem.


## <a name="discover"></a>Informace
Chcete-li procházet vlastní moduly [ve galerii](http://gallery.cortanaintelligence.com)v části **Další**vyberte **vlastních modulů**.

![Vyberte vlastní moduly na domovskou stránku Galerie](./media/gallery-custom-modules/select-custom-modules-in-gallery.png)

**[Vlastní moduly](https://gallery.cortanaintelligence.com/customModules)** stránky zobrazí seznam nedávno přidaných a Oblíbené moduly. Chcete-li zobrazit všechny vlastní moduly, vyberte **zobrazit všechny** tlačítko. Chcete-li vyhledat konkrétní vlastního modulu, vyberte **zobrazit všechny**a potom vyberte kritéria. Také můžete zadat hledaný text v **hledání** pole v horní části na stránku galerie.

![Vyberte možnost "Zobrazit všechny" procházet všechny vlastní moduly](./media/gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>Vysvětlení

Pokud chcete pochopit, jak funguje publikované vlastního modulu, vyberte vlastní modul a otevřete stránku Podrobnosti modulu. Stránce s podrobnostmi o zajišťuje konzistentní vzhledem k aplikacím a informativní learning prostředí. Například stránce s podrobnostmi o zvýrazní účel modulu a uvádí seznam očekávaných vstupy, výstupy a parametry. Stránce s podrobnostmi o také obsahuje odkaz na základní zdrojový kód, který můžete prozkoumat a upravit.

### <a name="comment-and-share"></a>Komentář a sdílené složky
Na stránce Podrobnosti, vlastní modul **komentáře** oddílu, můžete komentovat, poskytnout zpětnou vazbu nebo pokládání otázek na modul. Modul můžete sdílet i s přáteli nebo kolegy na Twitteru nebo LinkedIn. Také můžete poslat e-mail odkaz na stránku podrobností modulu, a pozvat další uživatele zobrazíte stránku.

![Sdílet tuto položku s přáteli](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Přidat vlastní komentáře](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>Import
Jakýkoli vlastní modul z galerie můžete importovat do vlastní experimenty.

Galerie Azure AI nabízí dva způsoby, jak importovat kopii těchto modul:

* **Z Galerie**. Při importu vlastního modulu z galerie, budete mít také ukázkový experiment, který poskytuje příklad, jak používat modul.
* **V rámci služby Machine Learning Studio**. Při práci v nástroji Machine Learning Studio můžete naimportovat všechny vlastní modul (v takovém případě vám ukázkový experiment).

### <a name="from-the-gallery"></a>Z Galerie

1. V galerii otevřete stránku Podrobnosti modulu. 
2. Vyberte **Open in Studio**.
   
    ![Otevřít vlastní modul z Galerie](./media/gallery-custom-modules/open-custom-module-from-gallery.png)
   
Každý vlastní modul obsahuje ukázkový experiment, který ukazuje, jak chcete použít modul. Když vyberete **Open in Studio**, ukázkový experiment otevře ve vašem pracovním prostoru Machine Learning Studio. (Pokud jste ještě nejste přihlášení k sadě Studio, zobrazí se výzva k první přihlášení pomocí účtu Microsoft.)

Kromě ukázkový experiment vlastního modulu zkopírován do pracovního prostoru. Je také umístěná v paletě modulů s všechny vestavěné i vlastní Machine Learning Studio moduly. Nyní ji můžete použít ve své vlastní experimenty, stejně jako ostatní moduly ve vašem pracovním prostoru.

### <a name="from-within-machine-learning-studio"></a>V rámci služby Machine Learning Studio

1. V nástroji Machine Learning Studio, vyberte **nový**.
2. Vyberte **modulu**. Můžete vybrat ze seznamu modulů Galerie nebo najít konkrétní modul pomocí **hledání** pole.
3. Přesuňte ukazatel myši na modul a vyberte **Import Module**. (Chcete-li získat informace o modulu, vyberte **zobrazit v galerii**. Tím přejdete na stránku podrobností modulu v galerii.)
   
    ![Import vlastního modulu do nástroje Machine Learning Studio](./media/gallery-custom-modules/add-custom-module-in-studio.png)

Vlastní modul je zkopírován do pracovního prostoru a umístěn v paletě modulů s integrovanou nebo vlastní modulů Machine Learning Studio. Nyní ji můžete použít ve své vlastní experimenty, stejně jako ostatní moduly ve vašem pracovním prostoru.

## <a name="use"></a>Použití

Bez ohledu na to, kterou metodu zvolit import vlastního modulu, při importování modulu, modul je umístěn v paletě modulů v Machine Learning Studio. Z palety modul můžete použít vlastní modul v jakékoli experimentu ve vašem pracovním prostoru, stejně jako ostatní moduly.

Použití importovaného modulu:

1. Vytvořit nový experiment, nebo otevřete existující experiment.
2. Rozbalte seznam vlastních modulů ve vašem pracovním prostoru, paletě modulů, vyberte **vlastní**. Je na paletě modulů nalevo od plátna experimentu.
   
    ![Vlastní modul seznamu paletě Studio](./media/gallery-custom-modules/custom-module-in-studio-palette.png)
3. Vyberte modul, který jste naimportovali a přetáhněte ho do experimentu.


**[Přejít do Galerie](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

