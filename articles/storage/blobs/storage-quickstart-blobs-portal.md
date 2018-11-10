---
title: Rychlý start Azure – Vytvoření objektu blob v úložišti objektů pomocí webu Azure Portal | Microsoft Docs
description: V tomto rychlém startu použijete v úložišti objektů (blob) web Azure Portal. Pak použijete Azure Portal k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů blob v kontejneru.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 10/22/2018
ms.author: tamram
ms.openlocfilehash: e615585fbdbef634a13a19aca4b85eef5b21815f
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740362"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí webu Azure Portal

V tomto rychlém startu se naučíte použít web [Azure Portal](https://portal.azure.com/) k vytvoření kontejneru ve službě Azure Storage a k odeslání a stažení objektů blob bloku v tomto kontejneru.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Vytvoření kontejneru

Při vytváření kontejneru na webu Azure Portal použijte tento postup:

1. Na webu Azure Portal přejděte k novému účtu úložiště.
2. V levé nabídce pro účet úložiště přejděte do části **Blob service** a pak vyberte **Objekty blob**.
3. Vyberte tlačítko **+ Kontejner**.
4. Zadejte název nového kontejneru. Název kontejneru musí obsahovat malá písmena, musí začínat písmenem nebo číslicí a smí obsahovat jenom písmena, číslice a spojovníky (-). Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).
5. Nastavte úroveň veřejného přístupu ke kontejneru. Výchozí úroveň je **Privátní (bez anonymního přístupu)**.
6. Kliknutím na **OK** kontejner vytvoříte.

    ![Snímek obrazovky ukazující, jak vytvořit kontejner na webu Azure Portal](media/storage-quickstart-blobs-portal/create-container.png)

## <a name="upload-a-block-blob"></a>Nahrání objektu blob bloku

Objekty blob bloku se skládají z bloků dat sestavených do objektu blob. Většina scénářů využívajících Blob Storage zahrnuje objekty blob bloku. Objekty blob bloku jsou ideální pro ukládání textových a binárních dat v cloudu, jako jsou soubory, image a videa. Tento rychlý start ukazuje, jak pracovat s objekty blob bloku. 

Pokud chcete do nového kontejneru na webu Azure Portal nahrát objekt blob bloku, použijte tento postup:

1. Na webu Azure Portal přejděte do kontejneru, který jste vytvořili v předchozí části.
2. Vyberte tento kontejner. Zobrazí se seznam objektů blob, které obsahuje. Protože je tento kontejner nový, nebude ještě obsahovat žádné objekty blob.
3. Vyberte tlačítko **Nahrát** a nahrajte do kontejneru objekt blob.
4. Projděte místní systém souborů a vyhledejte soubor, který chcete nahrát jako objekt blob bloku, a vyberte **Nahrát**.
     
    ![Snímek obrazovky ukazující, jak nahrát objekt blob z místního disku](media/storage-quickstart-blobs-portal/upload-blob.png)

5. Vyberte **Typ ověřování**. Výchozí hodnota je **SAS**.
6. Tímto způsobem můžete nahrát libovolný počet objektů blob. Uvidíte, že nové objekty blob jsou teď zařazené v kontejneru.

## <a name="download-a-block-blob"></a>Stažení objektu blob bloku

Objekt blob bloku si můžete si stáhnout a zobrazit v prohlížeči nebo uložit do místního systému souborů. Pokud chcete stáhnout objekt blob bloku, postupujte takto:

1. Přejděte k seznamu objektů blob, které jste nahráli v předchozí části. 
2. Klikněte pravým tlačítkem na objekt blob, který chcete stáhnout, a vyberte **Stáhnout**. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat prostředky, které jste vytvořili v tomto rychlém startu, můžete odstranit příslušný kontejner. Všechny objekty blob v kontejneru se také odstraní.

Odstranění kontejneru:

1. Na webu Azure Portal přejděte k seznamu kontejnerů ve vašem účtu úložiště.
2. Vyberte kontejner, který chcete odstranit.
3. Vyberte tlačítko **Další** (**...**) a pak **Odstranit**.
4. Potvrďte, že chcete kontejner skutečně odstranit.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním diskem a službou Azure Blob Storage pomocí webu Azure Portal. Další informace o práci s úložištěm objektů blob najdete v postupech pro úložiště objektů blob.

> [!div class="nextstepaction"]
> [Operace s úložištěm objektů blob – postupy](storage-dotnet-how-to-use-blobs.md)

