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
ms.openlocfilehash: 22e91d50227fcb44c7b90478d76379c14161ae05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76718599"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Ukázková data v kontejnerech Azure Blob, na SQL Serveru a v tabulkách Hivu

Následující články popisují, jak vzorkovat data uložená v jednom ze tří různých umístění Azure:

* [**Data kontejneru Azure Blob**](sample-data-blob.md) se navzorkují tak, že se budou stahovat programově a pak se z něho vyvzorkování pomocí ukázkového kódu Pythonu.
* [**Data SQL Server**](sample-data-sql-server.md) jsou poukázkaovaná pomocí programovacího jazyka SQL i Pythonu. 
* [**Data tabulky podregistru**](sample-data-hive.md) jsou poukázkaovaná pomocí dotazů na podregistry.

Tento úkol vzorkování je krok v rámci [vědeckého zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Proč ukázková data?**

Pokud je datová sada, kterou plánujete analyzovat, rozsáhlá, je obvykle vhodné ji vyvzorkovat, aby se snížila na menší, ale jenom se zástupcem a více spravovatelnými velikostmi. Možnost může zjednodušit porozumění, průzkum a inženýry funkcí. Tato role vzorkování v procesu Cortana Analytics umožňuje rychlé vytváření prototypů funkcí pro zpracování dat a modelů strojového učení.

