---
title: Přehled zásad uchovávání dat – Azure SQL Edge
description: Další informace o zásadách uchovávání dat v Azure SQL Edge
keywords: SQL Edge, uchovávání dat
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: bb059a946c03f41e5b65944eec67070f84ee6b08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90976342"
---
# <a name="data-retention-overview"></a>Přehled uchovávání dat

Shromažďování a ukládání dat z připojených zařízení IoT je důležité pro zajištění a získání provozních a obchodních přehledů. Ale vzhledem k objemu dat, která pocházejí z těchto zařízení, je důležité, aby organizace pečlivě naplánovaly množství dat, která chtějí uchovávat, a v jakém členitosti. I když je zachování všech dat ve všech členitosti žádoucí, není vždy praktické. Objem dat, která je možné uchovat, se navíc omezuje na množství úložiště dostupného na zařízeních IoT nebo Edge. 

Správci databáze Azure SQL Edge mohou definovat zásady uchovávání dat v databázi SQL Edge a jejích podkladových tabulkách. Po definování zásad uchovávání dat se spustí úloha systému na pozadí pro vyprázdnění zastaralých (starých) dat z tabulek uživatelů. 

> [!Note]
> Data po vyprázdnění z tabulky nelze obnovit. Jediným možným způsobem obnovení vyčištěných dat je obnovení databáze ze starší zálohy.

Rychlá zprovoznění:

- [Povolit a zakázat zásady uchovávání dat](data-retention-enable-disable.md)
- [Správa historických dat pomocí zásad uchovávání informací](data-retention-cleanup.md)

## <a name="how-data-retention-works"></a>Jak funguje uchovávání dat

Chcete-li konfigurovat uchovávání dat, můžete použít příkazy DDL. Další informace získáte, když [povolíte nebo zakážete zásady uchovávání dat](data-retention-enable-disable.md). Pro automatické odstranění zastaralých záznamů je nutné nejprve povolit uchovávání dat jak pro databázi, tak pro tabulky, které chcete v této databázi vyprázdnit. 

Po dokončení konfigurace uchovávání dat pro tabulku se spustí úloha na pozadí, která identifikuje zastaralé záznamy v tabulce a odstraní tyto záznamy. Pokud z nějakého důvodu není automatické čištění úloh spuštěno nebo není možné s odstraněním pokračovat, je možné pro tyto tabulky provést ruční operaci čištění. Další informace o automatických a ručních vyčištěních najdete v tématu [automatické a ruční vyčištění](data-retention-cleanup.md).

## <a name="limitations-and-restrictions"></a>Omezení a omezení

- Uchovávání dat, pokud je povoleno, je automaticky zakázáno, pokud je databáze obnovena z úplného zálohování nebo je znovu připojena. 
- Pro dočasnou tabulku historie nelze povolit uchovávání dat.
- Colomn filtru uchovávání dat nelze změnit. Chcete-li změnit sloupec, zakažte uchovávání dat v tabulce.  

## <a name="next-steps"></a>Další kroky

- [Machine Learning a uměle inteligentní informace s ONNXem v SQL Edge](onnx-overview.md).
- [Vytvoření kompletního řešení IoT pomocí SQL Edge pomocí IoT Edge](tutorial-deploy-azure-resources.md).
- [Streamování dat ve službě Azure SQL Edge](stream-data.md)
