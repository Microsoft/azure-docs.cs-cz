---
title: Importovat data ze souboru do Azure Machine Learning Studio | Microsoft Docs
description: Zjistěte, jak nahrát soubor školení data z pevného disku do Azure Machine Learning Studio. Tím se vytvoří datová sada modulu v pracovním prostoru.
keywords: Importujte dat, formát dat, datové typy, zdroje dat, Cvičná data
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: c0dd9e90-23c4-4f64-8b8f-489ad79f047b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: f7096ee9e887f13e5bff1f79b7e81702061b8dea
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-machine-learning-studio"></a>Importu trénovacích dat ze souboru na pevném disku do nástroje Machine Learning Studio
[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Naučte se nahrát soubor dat z pevného disku pro použití jako Cvičná data v Azure Machine Learning Studio. Importováním datového souboru máte datovou sadu modul, který je připravený k použití v pracovním prostoru.

## <a name="steps-to-import-data-from-a-local-file"></a>Postup při importu dat z místního souboru
Pokud chcete importovat data z místního pevného disku, postupujte takto:

1. Klikněte na tlačítko **+ nový** v dolní části okna Machine Learning Studio.
2. Vyberte **datovou sadu** a **z místního souboru**.
3. V **nahrát nová datová sada** dialogové okno, přejděte na soubor, který chcete odeslat
4. Zadejte název, identifikace typu dat a volitelně zadat popis. Popis se doporučuje – umožňuje zaznamenávat žádné charakteristiky data, která chcete mějte na paměti, při použití data v budoucnosti.
5. Zaškrtávací políčko **jde o nové verzi existující datovou sadu** umožňuje aktualizovat existující datovou sadu s nová data. Klikněte na toto políčko a potom zadejte název existující datovou sadu.

![Nahrát nová datová sada](./media/import-data-from-local-file/upload-dataset.png)

Během nahrávání se zobrazí zpráva, že váš soubor se nahrává. Nahrát doba závisí na velikosti vaší dat a rychlosti připojení ke službě. Pokud víte, že soubor bude trvat dlouhou dobu, můžete provést jiných věcí uvnitř Machine Learning Studio popředí. Ale zavření prohlížeče způsobí, že nahrání dat selžou.

## <a name="dataset-module-is-ready-for-use"></a>Modul datové sady je připravený k použití
Po nahrání dat je uložený v modulu datová sada a je k dispozici pro všechny experimentu v pracovním prostoru.

Při úpravě experiment, můžete najít datové sady, které jste vytvořili v **Moje datové sady** v rámci **uložit datové sady** seznamu paletě modulů. Můžete přetáhnout a vyřadit datovou sadu na plátno experimentu, pokud chcete použít datovou sadu pro další analýzy a strojové učení.
