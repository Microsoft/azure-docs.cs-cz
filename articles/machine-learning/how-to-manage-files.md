---
title: Vytváření a Správa souborů v pracovním prostoru
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet a spravovat soubory ve vašem pracovním prostoru v Azure Machine Learning Studiu.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 5bfa7d83c00386ae922f0eba221ad9f2f74639ae
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066139"
---
# <a name="how-to-create-and-manage-files-in-your-workspace"></a>Jak vytvářet a spravovat soubory v pracovním prostoru

Naučte se vytvářet a spravovat soubory v pracovním prostoru Azure Machine Learning.  Tyto soubory jsou uložené ve výchozím úložišti pracovních prostorů. Soubory a složky lze sdílet s kýmkoli jiným s přístupem pro čtení k pracovnímu prostoru a lze je použít z libovolných výpočetních instancí v pracovním prostoru.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://aka.ms/AMLFree).
* Machine Learning pracovní prostor. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-files"></a><a name="create"></a> Vytváření souborů

Vytvoření nového souboru ve výchozí složce ( `Users > yourname` ):

1. Otevřete pracovní prostor v [Azure Machine Learning Studiu](https://ml.azure.com).
1. Na levé straně vyberte **poznámkové bloky**.
1. Vyberte **+** bitovou kopii.
1. Vyberte bitovou kopii  **vytvořit nový soubor** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Create new file":::

1. Pojmenujte soubor.
1. Vyberte typ souboru.
1. Vyberte **Vytvořit**.

Poznámkové bloky a většina typů textových souborů se zobrazí v sekci Preview.  Většina ostatních typů souborů nemá náhled.

Chcete-li vytvořit nový soubor v jiné složce:
1. Vyberte... na konci složky
1. Vyberte **vytvořit nový soubor**.

> [!IMPORTANT]
> Obsah v poznámkových blocích a skriptech může potenciálně číst data z vašich relací a přistupovat k datům bez vaší organizace v Azure.  Načte jenom soubory z důvěryhodných zdrojů. Další informace najdete v tématu [bezpečné](concept-secure-code-best-practice.md#azure-ml-studio-notebooks)osvědčené postupy pro kód.

## <a name="manage-files-with-git"></a>Správa souborů pomocí Gitu

[Pomocí terminálu výpočetních instancí](how-to-access-terminal.md#git) můžete klonovat a spravovat úložiště Git.

## <a name="clone-samples"></a>Klonovat ukázky

Váš pracovní prostor obsahuje **ukázkovou složku poznámkových bloků** s poznámkami, které jsou navržené tak, aby vám pomohly prozkoumat sadu SDK a sloužit jako příklady pro vlastní projekty machine learningu.   jedinou tyto poznámkové bloky do vlastní složky, aby je bylo možné spouštět a upravovat.  

Příklad najdete v tématu [kurz: vytvoření prvního experimentu ml](tutorial-1st-experiment-sdk-setup.md#azure).

## <a name="share-files"></a>Sdílet soubory

Zkopírujte a vložte adresu URL pro sdílení souboru.  K této adrese URL mají přístup pouze jiní uživatelé pracovního prostoru.  Přečtěte si další informace o tom, jak [udělit přístup k pracovnímu prostoru](how-to-assign-roles.md).

## <a name="delete-a-file"></a>Odstranění souboru

Soubory **ukázkových poznámkových bloků** *nelze* odstranit.  Tyto soubory jsou součástí studia a aktualizují se při každém publikování nové sady SDK.  

Soubory, které se nachází v části **soubory** , *můžete* odstranit některým z těchto způsobů:

* V nástroji Studio vyberte **...** na konci složky nebo souboru.  Ujistěte se, že používáte podporovaný prohlížeč (Microsoft Edge, Chrome nebo Firefox).
* [Pomocí terminálu](how-to-access-terminal.md) z libovolné výpočetní instance v pracovním prostoru. Složka **~/CloudFiles** je namapovaná na úložiště v účtu úložiště pracovního prostoru.
* V Jupyter nebo JupyterLab s jejich nástroji.
