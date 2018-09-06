---
title: Přehled strojového učení s využitím služby Custom Vision Service – Azure Cognitive Services | Microsoft Docs
description: Custom Vision Service je služba Microsoft Cognitive Services, která umožňuje vytvářet vlastní klasifikátory obrázků na platformě Azure.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: d2daf7c211f9474f5636b6af69c5b700d597aa14
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43285240"
---
# <a name="what-is-the-custom-vision-service"></a>Co je Custom Vision Service?

Custom Vision Service je služba Microsoft Cognitive Services, která umožňuje vytvářet vlastní klasifikátory obrázků. Umožňuje rychle a snadno vytvořit, nasadit a vylepšovat klasifikátor obrázků. Custom Vision Service pro nahrávání obrázků a trénování klasifikátoru poskytuje rozhraní REST API a webové rozhraní.

## <a name="what-does-custom-vision-service-do-well"></a>K čemu je služba Custom Vision Service vhodná?

Custom Vision Service funguje nejlépe, když je položka, kterou se pokoušíte klasifikovat, na obrázku ta hlavní. 

K vytvoření klasifikátoru nebo detektoru stačí několik obrázků. Ke spuštění prototypu vám stačí 50 obrázků pro každou třídu. Metody, které služba Custom Vision Service využívá, jsou odolné proti rozdílům a proto vám k zahájení vytváření prototypů stačí tak málo dat. To znamená, že služba Custom Vision Service není vhodná pro scénáře, kdy chcete rozpoznávat drobné rozdíly. Například menší praskliny nebo promáčknutí ve scénářích kontroly kvality.

## <a name="next-steps"></a>Další kroky

[Informace o vytvoření klasifikátoru](getting-started-build-a-classifier.md)
