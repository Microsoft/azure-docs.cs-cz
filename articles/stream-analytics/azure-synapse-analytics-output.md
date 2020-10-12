---
title: Výstup služby Azure synapse Analytics z Azure Stream Analytics
description: Tento článek popisuje Azure synapse Analytics jako výstup pro Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 04c315f593b90204faaeaec562c18e9e4be301d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90881902"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Výstup služby Azure synapse Analytics z Azure Stream Analytics

[Azure synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) (dříve SQL Data Warehouse) je bezlimitní analytická služba, která přináší dohromady podnikové datové sklady a analýzu velkých objemů dat. 

Úlohy Azure Stream Analytics můžou ve službě Azure synapse Analytics nastavovat výstup do tabulky fondů SQL a můžou zpracovat míry propustnosti až do 200 MB/s. Tato funkce podporuje nejvíce náročné analýzy v reálném čase a zpracování dat za provozu pro úlohy, jako je vytváření sestav a řídicí panel.  

Aby bylo možné přidat jako výstup do úlohy Stream Analytics, musí existovat tabulka fondu SQL. Schéma tabulky musí odpovídat polím a jejich typům ve výstupu vaší úlohy. 

Pokud chcete jako výstup použít Azure synapse, musíte zajistit, aby byl účet úložiště nakonfigurovaný. Přejděte do nastavení účtu úložiště a nakonfigurujte účet úložiště. Povolují se jenom typy účtů úložiště, které podporují tabulky: obecné účely v2 a obecné účely v1. Vyberte pouze úroveň Standard. Úroveň Premium se nepodporuje.

## <a name="output-configuration"></a>Konfigurace výstupu

V následující tabulce jsou uvedené názvy vlastností a jejich popisy pro vytváření výstupu Azure synapse Analytics.

|Název vlastnosti|Description|
|-|-|
|Alias pro výstup |Popisný název, který se používá v dotazech k směrování výstupu dotazu do této databáze. |
|databáze |Název fondu SQL, kam posíláte výstup. |
|Název serveru |Název serveru Azure synapse.  |
|Uživatelské jméno |Uživatelské jméno, které má přístup pro zápis do databáze. Stream Analytics podporuje pouze ověřování SQL. |
|Heslo |Heslo pro připojení k databázi. |
|Tabulka  | Název tabulky, do které se zapisuje výstup V názvu tabulky se rozlišují velká a malá písmena. Schéma této tabulky by mělo přesně odpovídat počtu polí a jejich typům vygenerovaných výstupem úlohy.|

## <a name="next-steps"></a>Další kroky

* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí Azure CLI](quick-create-azure-cli.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí šablony ARM](quick-create-azure-resource-manager.md)
* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics v Visual Studio Code](quick-create-visual-studio-code.md)