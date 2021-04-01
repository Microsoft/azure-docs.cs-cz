---
title: Ukázková data v různých umístěních Azure Storage – vědecký proces týmového zpracování dat
description: Ukázková data v kontejnerech objektů blob Azure, SQL Server a tabulkách podregistru, aby se snížila na menší, ale jenom se zástupcem a více spravovatelnými velikostmi.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 878a2a47dfaddf1d3399a1dbd98865332838e148
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321745"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Ukázková data v kontejnerech Azure Blob, na SQL Serveru a v tabulkách Hivu

Následující články popisují, jak vzorkovat data uložená v jednom ze tří různých umístění Azure:

* [**Data kontejneru Azure Blob**](sample-data-blob.md) se navzorkují tak, že se budou stahovat programově a pak se z něho vyvzorkování pomocí ukázkového kódu Pythonu.
* [**Data SQL Server**](sample-data-sql-server.md) jsou poukázkaovaná pomocí programovacího jazyka SQL i Pythonu. 
* [**Data tabulky podregistru**](sample-data-hive.md) jsou poukázkaovaná pomocí dotazů na podregistry.

Tento úkol vzorkování je krok v rámci [vědeckého zpracování týmových dat (TDSP)](./index.yml).

**Proč ukázková data?**

Pokud je datová sada, kterou plánujete analyzovat, rozsáhlá, je obvykle vhodné ji vyvzorkovat, aby se snížila na menší, ale jenom se zástupcem a více spravovatelnými velikostmi. Možnost může zjednodušit porozumění, průzkum a inženýry funkcí. Tato role vzorkování v procesu Cortana Analytics umožňuje rychlé vytváření prototypů funkcí pro zpracování dat a modelů strojového učení.