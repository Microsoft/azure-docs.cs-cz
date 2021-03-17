---
title: 'Použít transformaci: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul použít transformaci v Azure Machine Learning k úpravě vstupní datové sady na základě dříve vypočítané transformace.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/26/2020
ms.openlocfilehash: a5db3935ae445ee7dcf8129eb1d4c75fcb64302f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003901"
---
# <a name="apply-transformation-module"></a>Použít transformační modul

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Pomocí tohoto modulu můžete upravit vstupní datovou sadu založenou na dříve vypočítané transformaci. Tento modul je nezbytný v případě, že potřebujete aktualizovat transformace v odvozených kanálech.

Pokud jste například použili z skóre k normalizování školicích dat pomocí modulu **normalizing data** Module, měli byste použít hodnotu z-skóre, která byla vypočítána pro školení v rámci fáze bodování. V Azure Machine Learning můžete metodu normalizace Uložit jako transformaci a pak pomocí **použít transformaci** použít pro vstupní data skóre před vyhodnocením.

## <a name="how-to-save-transformations"></a>Uložení transformací

Návrhář umožňuje uložit transformace dat jako datové **sady** , abyste je mohli použít v jiných kanálech.

1. Vyberte modul transformace dat, který byl úspěšně spuštěn.

1. Vyberte kartu **výstupy + protokoly** .

1. Najděte výstup transformace a vyberte **datovou sadu registru** , kterou chcete uložit jako modul v kategorii **datových sad** v paletě modulu.

## <a name="how-to-use-apply-transformation"></a>Použití funkce použít transformaci  
  
1. Přidejte do svého kanálu modul **použít transformaci** . Tento modul najdete v části **model bodování & vyhodnocení** v paletě modulu. 
  
1. V paletě modulu Najděte uloženou transformaci, kterou chcete použít v **datových sadách** .

1. Připojte výstup uložené transformace k levému vstupnímu portu modulu **použít transformaci** .

    Datová sada musí mít přesně stejné schéma (počet sloupců, názvy sloupců, datové typy) jako datovou sadu, pro kterou byla transformace poprvé navržena.  
  
1. Připojte výstup objektu požadovaného modulu ke správnému vstupnímu portu modulu **použít transformaci** .
  
1. Chcete-li použít transformaci na novou datovou sadu, odešlete kanál.  

> [!IMPORTANT]
> Aby bylo zajištěno, že aktualizovaná transformace v školicích kanálech je také proveditelná v kanálech pro odvození, je nutné postupovat podle následujících kroků pokaždé, když je v školicím kanálu aktualizována transformace:
> 1. V kanálu školení Zaregistrujte výstup pro [transformaci vybrat sloupce](select-columns-transform.md) jako datovou sadu.
> ![Registrovat datovou sadu výstupu modulu](media/module/select-columns-transform-register-dataset.png)
> 1. V kanálu odvození odeberte modul **td** a nahraďte ho registrovanou datovou sadou v předchozím kroku.
> ![Nahradit modul TD](media/module/replace-tranformation-directory.png)

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 