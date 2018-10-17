---
title: Co je Custom Vision Service?
titlesuffix: Azure Cognitive Services
description: Služba Custom Vision Service umožňuje vytvářet vlastní klasifikátory obrázků v cloudu Azure.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: 1d3d1a82cf59b06625487fb241a63f51352e18e5
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365855"
---
# <a name="what-is-the-custom-vision-service"></a>Co je Custom Vision Service?

Custom Vision Service je služba Azure Cognitive Services, která umožňuje vytvářet vlastní klasifikátory obrázků. Umožňuje rychle a snadno vytvořit, nasadit a vylepšovat klasifikátor obrázků. Custom Vision Service pro nahrávání obrázků a trénování klasifikátoru poskytuje rozhraní REST API a webové rozhraní.

## <a name="what-does-custom-vision-service-do-well"></a>K čemu je služba Custom Vision Service vhodná?

Custom Vision Service funguje nejlépe, když je položka, kterou se pokoušíte klasifikovat, na obrázku ta hlavní. 

K vytvoření klasifikátoru nebo detektoru stačí několik obrázků. Ke spuštění prototypu vám stačí 50 obrázků pro každou třídu. Metody, které služba Custom Vision Service využívá, jsou odolné proti rozdílům a proto vám k zahájení vytváření prototypů stačí tak málo dat. To znamená, že služba Custom Vision Service není vhodná pro scénáře, kdy chcete rozpoznávat drobné rozdíly. Například menší praskliny nebo promáčknutí ve scénářích kontroly kvality.

## <a name="next-steps"></a>Další kroky

[Informace o vytvoření klasifikátoru](getting-started-build-a-classifier.md)
