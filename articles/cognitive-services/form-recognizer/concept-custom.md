---
title: Vlastní modely – Nástroj pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Přečtěte si o konceptech souvisejících s rozhraním API pro rozpoznávání formulářů – využití a omezení.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: lajanuar
ms.openlocfilehash: 74ced2ecadb5ccfe5cdb7966550e469ae4f8ab31
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612258"
---
# <a name="form-recognizer-custom-models"></a>Vlastní modely pro rozpoznávání formulářů

Nástroj pro rozpoznávání formulářů používá pokročilou technologii strojového učení k analýze a extrakci dat z formulářů a dokumentů. Model pro rozpoznávání formulářů je reprezentace extrahovaných dat, která se používají jako reference k analýze konkrétního obsahu. Existují dva typy modelů pro rozpoznávání formulářů:

* **Vlastní modely**. Vlastní modely pro rozpoznávání formulářů reprezentují extrahovaná data z _formulářů_ specifických pro vaši firmu. Aby bylo možné analyzovat data různých formulářů, je nutné, aby byly k disškole vlastní modely.

* **Předem připravené modely**. Nástroj pro rozpoznávání formulářů v současné době podporuje předem připravené modely pro _příjmy, obchodní karty, identifikační karty_ a _faktury_. Předem připravené modely zjišťují a extrahují informace z obrázků dokumentů a vracejí extrahovaná data ve strukturovaném výstupu JSON.

## <a name="what-does-a-custom-model-do"></a>Co dělá vlastní model?

Pomocí nástroje pro rozpoznávání formulářů můžete vytvořit model, který bude extrahovat informace z formulářů, které jsou relevantní pro váš případ použití. Chcete-li začít, stačí pouze pět příkladů stejného typu formuláře. Váš vlastní model se dá vyškolet pomocí nebo bez popisků datových sad.

## <a name="create-use-and-manage-your-custom-model"></a>Vytváření, používání a Správa vlastního modelu

Na nejvyšší úrovni je postup pro sestavování, školení a používání vlastního modelu následující:

> [!div class="nextstepaction"]
> [1. Sestavte datovou sadu školení](build-training-data-set.md#custom-model-input-requirements)

Sestavování vlastního modelu začíná vytvořením datové sady školení. Budete potřebovat minimálně pět dokončených forem stejného typu pro ukázkovou datovou sadu. Mohou mít různé typy souborů a obsahovat text i rukopis. Formuláře musí být stejného typu dokumentu a splňovat [požadavky na vstup](build-training-data-set.md#custom-model-input-requirements) pro nástroj pro rozpoznávání formulářů.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [2. Nahrajte datovou sadu školení.](build-training-data-set.md#upload-your-training-data)

Budete potřebovat nahrát školicí data do kontejneru úložiště objektů BLOB v Azure. Pokud si nejste jisti, jak vytvořit účet služby Azure Storage pomocí kontejneru, *Přečtěte si téma* [Azure Storage rychlý Start pro Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md). Pomocí bezplatné cenové úrovně (F0) můžete službu vyzkoušet a upgradovat ji později na placenou úroveň pro produkční prostředí.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;
> [!div class="nextstepaction"]
> [3. školení vlastního modelu](quickstarts/client-library.md#train-a-custom-model)

Model můžete optimalizovat [bez použití](quickstarts/client-library.md#train-a-model-without-labels) [popisků](quickstarts/client-library.md#train-a-model-with-labels) a datových sad. Neoznačené datové sady spoléhají výhradně na rozhraní API pro rozložení ke zjišťování a identifikaci klíčových informací bez přidání lidského vstupu. Označené datové sady se také spoléhají na rozhraní API pro rozložení, ale kromě vašich specifických popisků a umístění polí jsou zahrnuté i doplňkové lidské vstupy. Chcete-li použít data s popisky i bez popisku, začněte s alespoň pěti dokončenými formuláři stejného typu pro povinná školicí data a poté přidejte do požadované datové sady data bez popisků.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;  

>[!div class="nextstepaction"]
> [4. Analýza dokumentů pomocí vlastního modelu](quickstarts/client-library.md#analyze-forms-with-a-custom-model)

Otestujte nově vyškolený model pomocí formuláře, který není součástí školicí sady dat. Můžete pokračovat v provádění dalších školení pro zlepšení výkonu vlastního modelu.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [5. Správa vlastních modelů](quickstarts/client-library.md#manage-custom-models)

Kdykoli můžete zobrazit seznam všech vlastních modelů v rámci vašeho předplatného, načítat informace o konkrétním vlastním modelu nebo odstranit vlastní model z vašeho účtu.

## <a name="next-steps"></a>Další kroky

Další informace o klientské knihovně pro rozpoznávání formulářů najdete v naší referenční dokumentaci k rozhraní API.
> [!div class="nextstepaction"]
> [Reference k rozhraní API pro rozpoznávání formulářů](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)
>
