---
title: Rychlý Start – vytvoření objektu BLOB s Azure Portal
titleSuffix: Azure Storage
description: V tomto rychlém startu použijete v úložišti objektů (blob) web Azure Portal. Pak použijete Azure Portal k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů blob v kontejneru.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: tamram
ms.openlocfilehash: edadcc4025913052e048ea94d47cac253e4bcd1a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95523327"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí webu Azure Portal

V tomto rychlém startu se naučíte použít web [Azure Portal](https://portal.azure.com/) k vytvoření kontejneru ve službě Azure Storage a k odeslání a stažení objektů blob bloku v tomto kontejneru.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>Vytvoření kontejneru

Při vytváření kontejneru na webu Azure Portal použijte tento postup:

1. Na webu Azure Portal přejděte k novému účtu úložiště.
2. V levé nabídce účtu úložiště přejděte do části **BLOB Service** a pak vyberte **kontejnery**.
3. Vyberte tlačítko **+ Kontejner**.
4. Zadejte název nového kontejneru. Název kontejneru musí obsahovat malá písmena, musí začínat písmenem nebo číslicí a smí obsahovat jenom písmena, číslice a spojovníky (-). Další informace o názvech kontejnerů a objektů BLOB najdete v tématu [pojmenování a odkazování kontejnerů, objektů BLOB a metadat](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).
5. Nastavte úroveň veřejného přístupu ke kontejneru. Výchozí úroveň je **Privátní (bez anonymního přístupu)**.
6. Kliknutím na **OK** kontejner vytvoříte.

    :::image type="content" source="media/storage-quickstart-blobs-portal/create-container.png" alt-text="Snímek obrazovky ukazující, jak vytvořit kontejner na webu Azure Portal":::

## <a name="upload-a-block-blob"></a>Nahrání objektu blob bloku

Objekty blob bloku se skládají z bloků dat sestavených do objektu blob. Většina scénářů využívajících Blob Storage zahrnuje objekty blob bloku. Objekty blob bloku jsou ideální pro ukládání textových a binárních dat v cloudu, jako jsou soubory, image a videa. Tento rychlý start ukazuje, jak pracovat s objekty blob bloku.

Pokud chcete do nového kontejneru na webu Azure Portal nahrát objekt blob bloku, použijte tento postup:

1. Na webu Azure Portal přejděte do kontejneru, který jste vytvořili v předchozí části.
1. Vyberte tento kontejner. Zobrazí se seznam objektů blob, které obsahuje. Tento kontejner je nový, takže zatím neobsahuje žádné objekty blob.
1. Kliknutím na tlačítko **nahrát** otevřete okno Odeslat a vyhledejte soubor, který se má nahrát jako objekt blob bloku, a přejděte do místního systému souborů. Volitelně můžete rozšířit oddíl **Upřesnit** a nakonfigurovat další nastavení operace nahrávání.

    :::image type="content" source="media/storage-quickstart-blobs-portal/upload-blob.png" alt-text="Snímek obrazovky ukazující, jak nahrát objekt BLOB z místního disku prostřednictvím Azure Portal":::

1. Kliknutím na tlačítko **nahrát** nahrajte objekt BLOB.
1. Tímto způsobem můžete nahrát libovolný počet objektů blob. Uvidíte, že nové objekty blob jsou teď zařazené v kontejneru.

## <a name="download-a-block-blob"></a>Stažení objektu blob bloku

Objekt blob bloku si můžete si stáhnout a zobrazit v prohlížeči nebo uložit do místního systému souborů. Pokud chcete stáhnout objekt blob bloku, postupujte takto:

1. Přejděte k seznamu objektů blob, které jste nahráli v předchozí části.
1. Klikněte pravým tlačítkem na objekt blob, který chcete stáhnout, a vyberte **Stáhnout**.

    :::image type="content" source="media/storage-quickstart-blobs-portal/download-blob.png" alt-text="Snímek obrazovky ukazující, jak stáhnout objekt blob na webu Azure Portal":::

## <a name="delete-a-block-blob"></a>Odstranění objektu blob bloku

Pokud chcete odstranit jeden nebo víc objektů BLOB v Azure Portal, postupujte následovně:

1. V Azure Portal přejděte do kontejneru.
1. Zobrazí seznam objektů BLOB v kontejneru.
1. Pomocí zaškrtávacího políčka vyberte jeden nebo více objektů BLOB ze seznamu.
1. Vyberte tlačítko **Odstranit** a odstraňte vybrané objekty blob.
1. V dialogovém okně potvrďte odstranění a určete, zda chcete také odstranit snímky objektů BLOB.

:::image type="content" source="media/storage-quickstart-blobs-portal/delete-blobs.png" alt-text="Snímek obrazovky, který ukazuje, jak odstranit objekty BLOB z Azure Portal":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat všechny prostředky, které jste vytvořili v rámci tohoto rychlého startu, můžete kontejner jednoduše odstranit. Všechny objekty blob v kontejneru se také odstraní.

Odstranění kontejneru:

1. Na webu Azure Portal přejděte k seznamu kontejnerů ve vašem účtu úložiště.
1. Vyberte kontejner, který chcete odstranit.
1. Vyberte tlačítko **Další** (**...**) a pak **Odstranit**.
1. Potvrďte, že chcete kontejner skutečně odstranit.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit kontejner a nahrát objekt BLOB pomocí Azure Portal. Pokud se chcete dozvědět víc o práci s úložištěm objektů BLOB z webové aplikace, přejděte k kurzu, který ukazuje, jak nahrát obrázky do účtu úložiště.

> [!div class="nextstepaction"]
> [Kurz: nahrání obrazových dat v cloudu pomocí Azure Storage](storage-upload-process-images.md)