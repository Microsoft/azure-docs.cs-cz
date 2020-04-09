---
title: 'Kurz: Vytvoření projektu popisků pro klasifikaci obrázků'
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak spravovat proces označování obrázků tak, aby je bylo možné použít v modelech klasifikace obrázků ve více třídách.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/02/2020
ms.openlocfilehash: fa33861d86ff8bee3e2a34fb3d93032ac6180880
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879718"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Kurz: Vytvoření projektu označování pro klasifikaci obrázků více tříd 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Tento kurz ukazuje, jak spravovat proces označování (označované také jako označování) bitové kopie, které mají být použity jako data pro vytváření modelů strojového učení. Označování dat v Azure Machine Learning je ve verzi Public Preview.

Pokud chcete trénovat model strojového učení ke klasifikaci obrázků, potřebujete stovky nebo dokonce tisíce obrázků, které jsou správně označeny.  Azure Machine Learning vám pomůže spravovat průběh vašeho soukromého týmu odborníků na domény při označování vašich dat.
 
V tomto tutoriálu budete používat obrázky koček a psů.  Vzhledem k tomu, že každý obrázek je buď kočka nebo pes, jedná se o projekt *značení více tříd.* Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
>
> * Vytvořte si účet úložiště Azure a nahrajte do účtu ibi.Create a Azure storage account and upload images to the account.
> * Vytvořte pracovní prostor Azure Machine Learning.
> * Vytvořte projekt označování obrázků více tříd.
> * Označte data.  Tento úkol můžete provést vy nebo vaši labelers.
> * Dokončete projekt kontrolou a exportem dat.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pracovní prostor Azure Machine Learning je základní prostředek v cloudu, který používáte k experimentování, trénování a nasazování modelů strojového učení. Vazby předplatného Azure a skupiny prostředků na snadno spotřebované objektve službě.

Pracovní prostor vytvoříte prostřednictvím portálu Azure, webové konzoly pro správu prostředků Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Zahájení projektu označování

Dále budete spravovat projekt označování dat ve studiu Azure Machine Learning, konsolidovaném rozhraní, které obsahuje nástroje strojového učení k provádění scénářů datové vědy pro odborníky v oblasti datových věd všech úrovní dovedností. Studio není v prohlížečích Internet Explorer podporováno.

1. Přihlaste se do [studia Azure Machine Learning Studio](https://ml.azure.com).

1. Vyberte předplatné a pracovní prostor, který jste vytvořili.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Vytvoření úložiště dat

Úložiště dat Azure Machine Learning se používají k ukládání informací o připojení, jako je ID předplatného a autorizace tokenu. Zde použijete úložiště dat pro připojení k účtu úložiště, který obsahuje bitové kopie pro tento kurz.

1. Na levé straně pracovního prostoru vyberte **Datastores**.

1. Vyberte **+ Nové úložiště dat**.

1. Vyplňte formulář s těmito nastaveními:

    Pole|Popis 
    ---|---
    Název úložiště dat | Pojmenujte úložiště dat.  Zde používáme **labeling_tutorial**.
    Typ úložiště dat | Vyberte typ úložiště.  Tady používáme **Azure Blob Storage**, upřednostňované úložiště pro image.
    Metoda výběru účtu | Vyberte **zadat ručně**.
    zprostředkovatele identity | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Typ ověřování | Vyberte **token SAS**.
    Klíč účtu | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Vyberte **Vytvořit,** chcete-li vytvořit úložiště dat.

### <a name="add-labelers-to-workspace"></a>Přidání štítků do pracovního prostoru

Nastavte pracovní prostor tak, aby zahrnoval všechny osoby, které budou označovat data pro některý z vašich projektů.  Později přidáte tyto štítky do konkrétního projektu označování.

1. Na levé straně vyberte **Popisky dat**.

1. V horní části stránky vyberte **Labelers**.

1. Vyberte **Přidat labeler,** chcete-li přidat e-mailovou adresu štítkovače.

1. Pokračujte v přidávání dalších štítků, dokud neskončíte.

### <a name="create-a-labeling-project"></a>Vytvoření projektu popisování

Nyní, když máte seznam štítků a přístup k datům, které chcete mít označeny, vytvořte projekt označování.

1. V horní části stránky vyberte **Projekty**.

1. Vyberte **+ Přidat projekt**.

    ![Vytvoření projektu](media/tutorial-labeling/create-project.png)

### <a name="project-details"></a>Podrobnosti o projektu

1. Pro formulář **Podrobnosti projektu** použijte následující vstup:

    Pole|Popis 
    ---|---
    Název projektu | Pojmenujte projekt.  Zde budeme používat **tutorial-kočky-n-psi**.
    Typ úkolu popisky | Vyberte **klasifikace obrázků více třídy**.  
    
    Chcete-li pokračovat ve vytváření projektu, vyberte možnost **Další.**

### <a name="select-or-create-a-dataset"></a>Výběr nebo vytvoření datové sady

1.   Ve formuláři **Vybrat nebo vytvořit datovou sadu** vyberte druhou **volbu, Vytvořit datovou sadu**a pak vyberte propojení ze úložiště **dat**.

1. Pro formulář Vytvořit **datovou sadu z** úložiště dat použijte následující vstup:

    1. Ve formuláři **Základní informace** přidejte název, zde použijeme **obrázky pro výuku**.  Pokud chcete, přidejte popis.  Pak vyberte **Další**.
    1. Ve formuláři **pro výběr úložiště dat** vyberte pomocí rozevíracího souboru dříve vytvořené úložiště **dat**, například **tutorial_images (Azure Blob Storage).**
    1. Dále ve výběrovém formuláři **Datastore** vyberte **Procházet** a pak vyberte **MultiClass - DogsCats**.  Vyberte **Uložit,** chcete-li jako cestu použít **/MultiClass - DogsCats.**
    1. Chcete-li potvrdit podrobnosti, vyberte **možnost Další** a potom **vytvořit** datovou sadu.
    1. Vyberte kruh vedle názvu datové sady v seznamu, například **obrázky pro kurz**.

1. Chcete-li pokračovat ve vytváření projektu, vyberte možnost **Další.**

### <a name="label-classes"></a>Třídy popisků

1. Ve formuláři **Label třídy** zadejte název popisku a pak vyberte **+Přidat popisek** a zadejte další popisek.  Pro tento projekt jsou popisky **Cat**, **Dog**a **Uncertain**.

1. Vyberte **Další,** když jste přidali všechny popisky.

### <a name="labeling-instructions"></a>Pokyny pro označování

1. Ve formuláři **Pokyny pro označení** můžete poskytnout odkaz na web, který obsahuje podrobné pokyny pro štítkovače.  Necháme to prázdné pro tento kurz.

1. Můžete také přidat krátký popis úkolu přímo do formuláře.  Kurz označování typů **- Kočky & psi.**

1. Vyberte **další**.

1. Ve formuláři **s asistencí ml ponechte** políčko nezaškrtnuté. Ml asistované označování vyžaduje více dat, než budete používat v tomto kurzu.

1. Vyberte **Vytvořit projekt**.

Tato stránka se automaticky neaktualizuje. Po pauze ručně aktualizujte stránku, dokud se stav projektu nezmění na **Vytvořeno**.

### <a name="add-labelers-to-your-project"></a>Přidání štítkovačů do projektu

Přidejte některé nebo všechny štítkovače do tohoto projektu.

1. Vyberte název projektu, který chcete otevřít.  

1. V horní části stránky vyberte **Týmy**.

1. Vyberte odkaz **labeling_tutorial výchozí tým.**

1. Nyní použijte **přiřadit labelery** k přidání štítků, které chcete účastnit tohoto projektu. 

1. Vyberte ze seznamu štítků, které jste vytvořili dříve.  Jakmile vyberete všechny štítkovače, které chcete použít, vyberte **Přiřadit štítkovače,** abyste je přidali do výchozího projektového týmu.

## <a name="start-labeling"></a>Zahájení popisování

Teď jste nastavili prostředky Azure a nakonfigurovali projekt označování dat. Je čas přidat štítky k datům.

### <a name="notify-labelers"></a>Upozornit labelery

Pokud máte spoustu obrázků na štítku, doufejme, že máte také spoustu labelers k dokončení úkolu.  Nyní budete chtít odeslat pokyny, aby měli přístup k datům a začali označovat.

1. V [machine learningovém studiu](https://ml.azure.com)vyberte **Popisky dat** na levé straně a najděte svůj projekt.  

1. Vyberte odkaz na název projektu.

1. V horní části stránky vyberte **Podrobnosti**.  Zobrazí se souhrn projektu.

    ![Podrobnosti o projektu](media/tutorial-labeling/project-details.png)

1. Zkopírujte odkaz **URL portálu pro popisky** a odešlete je svým labelerům.

1. Nyní vyberte **tým** v horní části a najděte svůj tým pro označování.  

1. Vyberte odkaz na název týmu.

1. V horní části stránky vyberte **E-mailový tým** a začněte s e-mailem.  Vložte do adresy URL portálu pro popisky, kterou jste právě zkopírovali.  

Pokaždé, když labeler přejde na adresu URL portálu, zobrazí se jim více obrázků, které budou označeny, dokud nebude fronta prázdná.  

### <a name="tag-the-images"></a>Označení obrázků

V této části kurzu přepnete role ze *správce projektu* na *roli labeleru*.  Použijte adresu URL, kterou jste odeslali týmu.  Tato adresa URL vás přenese na portál pro označování vašeho projektu.  Pokud jste přidali pokyny, uvidíte je zde, když dorazíte na stránku.

1. V horní části stránky vyberte **Úkoly, které** mají začít s popisováním.

1. Vyberte miniaturu vpravo, chcete-li zobrazit počet obrázků, které chcete označit najednou. Než budete moci pokračovat, musíte označit všechny tyto obrázky. Rozložení přepínejte pouze v případě, že máte novou stránku s neoznačenými daty. Přepínání rozložení vymaže probíhající tagování stránky.

1. Vyberte jeden nebo více obrazů a pak vyberte značku, která se má na výběr aplikovat. Značka se zobrazí pod obrázkem.  Pokračujte v výběru a označení všech obrázků na stránce.  Chcete-li vybrat všechny zobrazené obrazy současně, vyberte **vybrat vše**. Vyberte alespoň jeden obrázek, který chcete použít značku.


    > [!TIP]
    > Prvních devět značek můžete vybrat pomocí číselné klávesy na klávesnici.

1. Jakmile jsou všechny obrázky na stránce označeny, vyberte **Odeslat,** chcete-li odeslat tyto štítky.

    ![Označování obrázků značkami](media/tutorial-labeling/catsndogs.gif)

1. Po odeslání značek pro data po ruce Azure aktualizuje stránku s novou sadou ibiz z pracovní fronty.

## <a name="complete-the-project"></a>Dokončení projektu

Nyní přepnete role zpět na *správce projektu* pro projekt označování.

Jako manažer můžete zkontrolovat práci svého štítkovače.  

### <a name="review-labeled-data"></a>Kontrola označených dat

1. V [machine learningovém studiu](https://ml.azure.com)vyberte **Popisky dat** na levé straně a najděte svůj projekt.  

1. Vyberte odkaz na název projektu.

1. Řídicí panel zobrazuje průběh projektu.

1. V horní části stránky vyberte **Data**.

1. Na levé straně vyberte **Označená data,** abyste viděli označené obrázky.  

1. Pokud nesouhlasíte s popiskem, vyberte obrázek a pak v dolní části stránky vyberte **Odmítnout.**  Značky budou odebrány a obrázek se vrátí zpět do fronty obrázků bez popisků.

### <a name="export-labeled-data"></a>Export označených dat

Data popisků pro experimentování machine learningu můžete kdykoli exportovat. Uživatelé často exportují vícekrát a trénují různé modely, místo aby čekali na označení všech obrázků.

Popisky obrázků lze exportovat ve [formátu COCO](http://cocodataset.org/#format-data) nebo jako datovou sadu Azure Machine Learning. Formát datové sady usnadňuje použití pro školení v Azure Machine Learning.  

1. V [machine learningovém studiu](https://ml.azure.com)vyberte **Popisky dat** na levé straně a najděte svůj projekt.  

1. Vyberte odkaz na název projektu.

1. Vyberte **Exportovat** a zvolte **Exportovat jako datovou sadu Azure ML**. 

    Stav exportu se zobrazí těsně pod tlačítkem **Exportovat.** 

1. Po úspěšném exportu popisků vyberte **datové sady** na levé straně, abyste zobrazili výsledky.

## <a name="clean-up-resources"></a>Vyčištění prostředků


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste označili obrázky.  Nyní použijte označená data:

> [!div class="nextstepaction"]
> [Trénování modelu rozpoznávání obrazu strojového učení](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb).
