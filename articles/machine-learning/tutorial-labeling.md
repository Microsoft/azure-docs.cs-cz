---
title: 'Kurz: vytvoření projektu s popisem pro klasifikaci obrázku'
titleSuffix: Azure Machine Learning
description: Naučte se spravovat proces označování imagí, aby je bylo možné použít v modelech klasifikace imagí s více třídami.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 04/09/2020
ms.custom: data4ml
ms.openlocfilehash: 41e93584937ca10740e9ee0be3353d1edf5efb3e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587676"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>Kurz: vytvoření projektu s popisem pro klasifikaci imagí ve více třídách 


V tomto kurzu se dozvíte, jak spravovat proces označování (označované také jako označování) obrázků, které se použijí jako data pro sestavování modelů strojového učení. Popisky dat v Azure Machine Learning jsou ve verzi Public Preview.

Pokud chcete vytvořit model strojového učení pro klasifikaci imagí, budete potřebovat stovky nebo dokonce tisíce imagí, které jsou označeny správně.  Azure Machine Learning pomáhá spravovat průběh vašeho privátního týmu odborníků na doménu při jejich označování Vašimi daty.
 
V tomto kurzu budete používat obrázky koček a psi.  Vzhledem k tomu, že každý obrázek je Cat nebo pes, jedná se o *více třídový* projekt pro označování. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
>
> * Vytvořte si účet Azure Storage a nahrajte obrázky na účet.
> * Vytvořte pracovní prostor Azure Machine Learning.
> * Vytvoří projekt pro označování imagí s více třídami.
> * Označte data.  Tato úloha může být provedena buď vy, nebo vašimi popisky.
> * Dokončete projekt tak, že zkontrolujete a exportujete data.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Azure Machine Learning pracovní prostor je základní prostředek v cloudu, který používáte k experimentování, výuce a nasazování modelů strojového učení. Přijedná se o vaše předplatné Azure a skupinu prostředků k snadno spotřebovanému objektu ve službě.

Existuje mnoho způsobů, jak [vytvořit pracovní prostor](how-to-manage-workspace.md). V tomto kurzu vytvoříte pracovní prostor prostřednictvím Azure Portal, webové konzoly pro správu prostředků Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>Spustit projekt značení

Dále budete spravovat projekt označování dat v aplikaci Azure Machine Learning Studio, konsolidované rozhraní, které zahrnuje nástroje strojového učení, které slouží k provádění scénářů pro datové vědy u všech úrovní dovedností. Aplikace Studio není podporována v prohlížečích aplikace Internet Explorer.

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com).

1. Vyberte své předplatné a pracovní prostor, který jste vytvořili.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>Vytvoření úložiště dat

Úložiště dat Azure Machine Learning slouží k ukládání informací o připojení, jako je vaše ID předplatného a autorizace tokenu. Tady použijete úložiště dat pro připojení k účtu úložiště, který obsahuje obrázky pro tento kurz.

1. Na levé straně pracovního prostoru vyberte **úložiště dat**.

1. Vyberte **+ nové úložiště dat**.

1. Vyplňte formulář s těmito nastaveními:

    Pole|Description 
    ---|---
    Název úložiště dat | Poskytněte úložiště dat název.  Zde používáme **labeling_tutorial**.
    Typ úložiště dat | Vyberte typ úložiště.  Tady používáme **Azure Blob Storage**, což je preferované úložiště pro obrázky.
    Metoda výběru účtu | Vyberte **zadat ručně**.
    URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    Typ ověřování | Vyberte **token SAS**.
    Klíč účtu | `?sv=2019-02-02&ss=bfqt&srt=sco&sp=rl&se=2025-03-25T04:51:17Z&st=2020-03-24T20:51:17Z&spr=https&sig=7D7SdkQidGT6pURQ9R4SUzWGxZ%2BHlNPCstoSRRVg8OY%3D`

1. Vyberte **vytvořit** a vytvořte úložiště dat.

### <a name="create-a-labeling-project"></a>Vytvoření projektu s popisem

Teď, když máte přístup k datům, která mají být označená, vytvořte svůj projekt označování.

1. V horní části stránky vyberte **projekty**.

1. Vyberte **+ Přidat projekt**.

    :::image type="content" source="media/tutorial-labeling/create-project.png" alt-text="Vytvoření projektu":::

### <a name="project-details"></a>Podrobnosti o projektu

1. Pro formulář **Project Details** použijte následující vstup:

    Pole|Description 
    ---|---
    Název projektu | Zadejte název svého projektu.  Tady budeme používat **kurz-kočky-n-psi**.
    Popis typu úlohy | Vyberte možnost **klasifikace imagí s více třídami**.  
    
    Kliknutím na tlačítko **Další** pokračujte v vytváření projektu.

### <a name="select-or-create-a-dataset"></a>Vybrat nebo vytvořit datovou sadu

1.   Na formuláři **Vybrat nebo vytvořit datovou sadu** vyberte druhou volbu, **Vytvořte datovou sadu** a pak vyberte odkaz **z úložiště dat**.

1. Pro formulář **vytvořit datovou sadu z úložiště** dat použijte následující vstup:

    1. Do formuláře **základní informace** přidejte název, v tomto příkladu budeme používat **obrázky-for-tutorial**.  Pokud chcete, přidejte popis.  Pak vyberte **Další**.
    1. Ve formuláři pro **Výběr úložiště dat** vyberte **dříve vytvořené úložiště dat**, klikněte na název úložiště dat a vyberte **vybrat úložiště dat**.
    1. Na další stránce Ověřte, zda je aktuálně vybrané úložiště dat správné. Pokud ne, vyberte **dříve vytvořené úložiště dat** a opakujte předchozí krok.
    1. Dál ještě na formuláři pro **Výběr úložiště dat** vyberte **Procházet** a pak vyberte DogsCats **(více tříd**).  Vyberte **Uložit** a jako cestu použijte **/MultiClass-DogsCats** .
    1. Kliknutím na tlačítko **Další** potvrďte podrobnosti **a vytvořte** datovou sadu a vytvořte ji.
    1. V seznamu vyberte kroužek vedle názvu datové sady, například **obrázky-for-tutorial**.

1. Kliknutím na tlačítko **Další** pokračujte v vytváření projektu.

### <a name="incremental-refresh"></a>Přírůstková aktualizace

Pokud plánujete přidat nové image do datové sady, přírůstková aktualizace tyto nové image najde a přidá je do projektu.  Pokud tuto funkci povolíte, bude projekt pravidelně kontrolovat nové image.  Pro tento kurz nebudete přidávat nové image do úložiště dat, takže tuto funkci nechte nezaškrtnuté.

Pokračujte výběrem tlačítka **Další**.

### <a name="label-classes"></a>Třídy popisků

1. Ve formuláři **třídy popisků** zadejte název popisku a pak vyberte **+ Přidat popisek** k zadání dalšího popisku.  Pro tento projekt jsou popisky **Cat**, **pes** a **nejisté**.

1. Pokud jste přidali všechny popisky, vyberte **Další** .

### <a name="labeling-instructions"></a>Pokyny k označování

1. Na formuláři s **pokyny k označování** odkazů můžete zadat odkaz na web, který poskytuje podrobné pokyny pro vaše popisky.  Pro tento kurz necháte tuto položku prázdnou.

1. Stručný popis úlohy můžete také přidat přímo do formuláře.  **Kurz označování typů – kočky & psi.**

1. Vyberte **Další**.

1. V části s **asistencí pro označování** ponechejte políčko nezaškrtnuté. Označování s asistencí ML vyžaduje více dat, než v tomto kurzu budete používat.

1. Vyberte **Vytvořit projekt**.

Tato stránka se automaticky neaktualizuje. Po pozastavení ručně aktualizujte stránku, dokud se stav projektu nezmění na hodnotu **Vytvořeno**.

## <a name="start-labeling"></a>Úvodní značení

Nyní jste nastavili prostředky Azure a nakonfigurovali projekt pro popisky dat. Je čas přidat popisky k vašim datům.

### <a name="tag-the-images"></a>Označení obrázků

V této části kurzu přepnete role ze *správce projektu* na *štítek s popiskem*.  Každý, kdo má přístup přispěvatele k vašemu pracovnímu prostoru, se může stát popiskem.

1. V [Machine Learning Studiu](https://ml.azure.com)vyberte na levé straně **popisky dat** , které se mají najít v projektu.  

1. Vyberte **odkaz popisek** pro projekt.

1. Přečtěte si pokyny a pak vyberte **úlohy**.

1. Vyberte obrázek miniatury na pravé straně, abyste zobrazili počet obrázků, které chcete označit v jednom snímku. Aby bylo možné přejít na, je nutné označit všechny tyto obrázky. Pokud máte novou stránku neoznačených dat, přepínejte jenom rozložení. Přepínání rozložení vymaže práci s označováním probíhajících prvků stránky.

1. Vyberte jeden nebo více obrázků a pak vyberte značku, kterou chcete použít pro výběr. Značka se zobrazí pod obrázkem.  Pokračujte výběrem a označením všech imagí na stránce.  Chcete-li vybrat všechny zobrazené obrázky současně, vyberte **možnost Vybrat vše**. Vyberte alespoň jeden obrázek pro použití značky.


    > [!TIP]
    > První devět značek můžete vybrat pomocí klávesových zkratek na klávesnici.

1. Jakmile budou všechny obrázky na stránce označeny, vyberte **Odeslat** a odešlete tyto štítky.

    ![Označování obrázků značkami](media/tutorial-labeling/catsndogs.gif)

1. Po odeslání značek pro data v Azure aktualizuje stránku pomocí nové sady imagí z pracovní fronty.

## <a name="complete-the-project"></a>Dokončení projektu

Nyní přepnete role zpět na *správce projektu* pro projekt značení.

Jako vedoucí může být vhodné zkontrolovat práci s popisem.  

### <a name="review-labeled-data"></a>Zkontrolovat data s popisky

1. V [Machine Learning Studiu](https://ml.azure.com)vyberte na levé straně **popisky dat** , které se mají najít v projektu.  

1. Vyberte odkaz název projektu.

1. Na řídicím panelu se zobrazí průběh projektu.

1. V horní části stránky vyberte **data**.

1. Na levé straně vyberte **označená data** , aby se zobrazily označené obrázky.  

1. Pokud nesouhlasím s popiskem, vyberte obrázek a potom v dolní části stránky vyberte **odmítnout** .  Značky budou odebrány a obrázek bude vrácen do fronty neoznačených obrázků.

### <a name="export-labeled-data"></a>Exportovat data s popisky

Data popisku můžete exportovat pro Machine Learning experimentování kdykoli. Uživatelé často exportují několikrát a využívají různé modely, ale nečekají na označení všech imagí.

Popisky obrázků lze exportovat ve [formátu díky Coco](http://cocodataset.org/#format-data) nebo jako datovou sadu Azure Machine Learning. Formát DataSet usnadňuje použití pro školení v Azure Machine Learning.  

1. V [Machine Learning Studiu](https://ml.azure.com)vyberte na levé straně **popisky dat** , které se mají najít v projektu.  

1. Vyberte odkaz název projektu.

1. Vyberte **exportovat** a zvolte **exportovat jako datovou sadu Azure ml**. 

    Stav exportu se zobrazí hned pod tlačítkem **exportovat** . 

1. Po úspěšném exportu popisků vyberte **datové sady** na levé straně pro zobrazení výsledků.

## <a name="clean-up-resources"></a>Vyčištění prostředků


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se model rozpoznávání imagí ve strojovém učení](/azure/machine-learning/how-to-use-labeled-dataset).

