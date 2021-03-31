---
title: Výstup služby Azure synapse Analytics z Azure Stream Analytics
description: Tento článek popisuje Azure synapse Analytics jako výstup pro Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 7e85df8ae67624a253a9fb617629d7355109c210
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019597"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Výstup služby Azure synapse Analytics z Azure Stream Analytics

[Azure synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) je bezlimitní analytická služba, která přináší dohromady podnikové datové sklady a analýzu velkých objemů dat. 

Úlohy Azure Stream Analytics můžou ve službě Azure synapse Analytics nastavovat výstup do vyhrazené tabulky fondu SQL a můžou zpracovat propustnost až do 200 MB/s. Tato funkce podporuje nejvíce náročné analýzy v reálném čase a zpracování dat za provozu pro úlohy, jako je vytváření sestav a řídicí panel.  

Aby bylo možné přidat jako výstup do úlohy Stream Analytics, musí existovat vyhrazená tabulka fondu SQL. Schéma tabulky musí odpovídat polím a jejich typům ve výstupu vaší úlohy. 

Pokud chcete jako výstup použít Azure synapse, musíte zajistit, aby byl účet úložiště nakonfigurovaný. Přejděte do nastavení účtu úložiště a nakonfigurujte účet úložiště. Povolují se jenom typy účtů úložiště, které podporují tabulky: obecné účely v2 a obecné účely v1. Vyberte pouze úroveň Standard. Úroveň Premium se nepodporuje.

## <a name="output-configuration"></a>Konfigurace výstupu

V následující tabulce jsou uvedené názvy vlastností a jejich popisy pro vytváření výstupu Azure synapse Analytics.

|Název vlastnosti|Description|
|-|-|
|Alias pro výstup |Popisný název, který se používá v dotazech k směrování výstupu dotazu do této databáze. |
|databáze |vyhrazený název fondu SQL, kam posíláte výstup. |
|Název serveru |Název serveru Azure synapse.  |
|Uživatelské jméno |Uživatelské jméno, které má přístup pro zápis do databáze. Stream Analytics podporuje pouze ověřování SQL. |
|Heslo |Heslo pro připojení k databázi. |
|Tabulka  | Název tabulky, do které se zapisuje výstup V názvu tabulky se rozlišují velká a malá písmena. Schéma této tabulky by mělo přesně odpovídat počtu polí a jejich typům vygenerovaných výstupem úlohy.|

## <a name="next-steps"></a>Další kroky

* [Použití spravovaných identit pro přístup k Azure SQL Database nebo ke službě Azure synapse Analytics z úlohy Azure Stream Analytics (Preview)](sql-database-output-managed-identity.md)
* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)