---
title: 'Převést na datovou sadu: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Převést na datovou sadu v Azure Machine Learning převést vstup dat do formátu interní datové sady používaného Microsoft Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: fd45db779b7a828d247f09cae38f90fc20d12c16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456586"
---
# <a name="convert-to-dataset"></a>Převod na datovou sadu

Tento článek popisuje, jak použít modul Převést na datovou sadu v návrháři Azure Machine Learning (preview) převést všechna data pro kanál do interního formátu návrháře.
  
Převod není vyžadován ve většině případů. Azure Machine Learning implicitně převádí data do nativního formátu datové sady při provádění libovolné operace s daty. 

Doporučujeme uložit data do formátu datové sady, pokud jste provedli nějaký druh normalizace nebo čištění na sadu dat a chcete zajistit, že změny jsou použity v jiných kanálech.  
  
> [!NOTE]
> Převést na datovou sadu změní pouze formát dat. Neuloží novou kopii dat v pracovním prostoru. Chcete-li datovou sadu uložit, poklepejte na výstupní port, vyberte **Uložit jako datovou sadu**a zadejte nový název.  
  
## <a name="how-to-use-convert-to-dataset"></a>Použití možnosti Převést na datovou sadu  

Doporučujeme použít modul [Upravit metadata](edit-metadata.md) k přípravě datové sady před použitím převést na datovou sadu. Můžete přidávat nebo měnit názvy sloupců, upravovat datové typy a podle potřeby provádět další změny.

1.  Přidejte modul Převést do datové sady do kanálu. Tento modul najdete v kategorii **Transformace dat** v návrháři. 

2. Připojte jej k libovolnému modulu, který vyvádí datovou sadu.   

    Dokud jsou data [tabulková](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py), můžete je převést na datovou sadu. To zahrnuje data načtená prostřednictvím [importu dat](import-data.md), data vytvořená pomocí [ručně zadejte data](enter-data-manually.md)nebo datové sady transformované pomocí použít [transformaci](apply-transformation.md).

3.  V rozevíracím seznamu **Akce** označte, zda chcete před uložením datové sady provést jakékoli vyčištění dat:  
  
    - **Žádné**: Použijte data tak, jak jsou.  
  
    - **SetMissingValue**: Nastavte určitou hodnotu na chybějící hodnotu v datové sadě. Výchozí zástupný symbol je znak otazníku (?), ale můžete použít možnost **Vlastní chybějící hodnota** pro zadání jiné hodnoty. Pokud například zadáte **chybějící hodnotu** **Taxi** for Custom , budou všechny instance **služby Taxi** v datové sadě změněny na chybějící hodnotu.
  
    - **ReplaceValues**: Tuto možnost použijte k určení jedné přesné hodnoty, která má být nahrazena jinou přesnou hodnotou. Chybějící hodnoty nebo vlastní hodnoty můžete nahradit nastavením metody **Replace:**

      - **Chybí**: Tuto možnost zvolte, chcete-li nahradit chybějící hodnoty ve vstupní datové sadě. Do **pole Nová hodnota**zadejte hodnotu, kterou chcete chybějící hodnoty nahradit.
      - **Vlastní**: Tuto možnost zvolte, chcete-li nahradit vlastní hodnoty ve vstupní datové sadě. Do **pole Vlastní hodnota**zadejte hodnotu, kterou chcete najít. Pokud například data obsahují `obs` řetězec použitý jako zástupný symbol `obs`pro chybějící hodnoty, zadáte . Do **pole Nová hodnota**zadejte novou hodnotu, kterou chcete nahradit původní řetězec.
  
    Všimněte si, že **ReplaceValues** operace platí pouze pro přesné shody. Například tyto řetězce by neměly `obs.` `obsolete`být ovlivněny: , .  
 
  
5.  Odešlete potrubí.  

## <a name="results"></a>Výsledky

+  Chcete-li výslednou datovou sadu uložit s novým názvem, vyberte na ikoně **Registrovat datovou sadu** na kartě **Výstupy** v pravém panelu modulu.  
  
## <a name="technical-notes"></a>Technické poznámky  

-   Každý modul, který přebírá datovou sadu jako vstup, může také přijímat data v souboru CSV nebo TSV. Před spuštěním kódu modulu jsou vstupy předem zpracovány. Předběžné zpracování je ekvivalentní spuštění modulu Převést na datovou sadu na vstupu.  
  
-   Nelze převést z formátu SVMLight na datovou sadu.  
  
-   Když zadáváte vlastní operaci nahrazení, operace hledání a nahrazení se vztahuje na úplné hodnoty. Částečné shody nejsou povoleny. Můžete například nahradit 3 s -1 nebo 33, ale nemůžete nahradit 3 ve dvoumístném čísle, například 35.  
  
-   Pro operace vlastní nahrazení nahrazení tiše nezdaří, pokud použijete jako náhradu libovolný znak, který neodpovídá aktuální datový typ sloupce.  

  
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
