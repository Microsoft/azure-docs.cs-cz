---
title: Konverzace student výchozí konfigurace - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Další informace o výchozí konfigurace student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 56e2140b83bf1c5722a459c14f31b2b4b0ba6b15
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343220"
---
# <a name="default-values-and-boundaries"></a>Výchozí hodnoty a hranice

Tento dokument popisuje výchozí konfigurace student konverzace a hranice služby klíče.

## <a name="default-configuration"></a>Výchozí konfigurace

Parametr | Výchozí hodnota
--- | --- 
Výchozí hodnota časového limitu relace | 30 minut

## <a name="boundaries"></a>Hranice

Parametr | Omezení
--- | --- 
Vytváření rozhraní API, zavolá maximální HTTP za měsíc | 5M
Vytváření rozhraní API, maximální HTTP volání za sekundu | 25
Relace rozhraní API, maximální HTTP volání za měsíc | 500 000
Relace rozhraní API, maximální počet volání HTTP za sekundu | 10
Maximální počet vlastní (bez programový) entit na aplikaci. | V tématu [LEOŠ hranice doc](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries); v praxi, skutečný počet může být mírně nižší
Maximální počet předdefinovaných entit na aplikaci. | V tématu [LEOŠ hranice dokumentů](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries)
Maximální počet entit (celkově) na aplikaci. | 100
Maximální počet akcí na aplikaci. | 32
Maximální počet train dialogová okna na aplikaci. | 1000
Maximální počet uživatelů se změní na train dialogové okno | 100
Maximální počet dialogová okna protokolu na aplikaci. | Žádné předem nastavené omezení, ale dialogová okna protokolu jsou uchovány pouze po stanovenou dobu než budou odstraněny.  Navíc zobrazí rozhraní student konverzace 100 dialogová okna protokolu současně. 
Maximální počet aplikací na uživatele | Žádné předem nastavené omezení
Maximální počet po sobě jdoucích akce bez čekání | 5 (*)

(*) Po 5 sekvenční akce bez čekání všechny akce bez čekání jsou maskována a Student konverzace vybere mezi akce k dispozici čekání.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Začínáme s konverzace student](./quickstart.md)
