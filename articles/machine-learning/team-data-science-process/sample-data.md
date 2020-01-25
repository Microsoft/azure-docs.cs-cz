---
title: Ukázková data v různých umístěních Azure Storage – vědecký proces týmového zpracování dat
description: Ukázková data v Azure kontejnery, SQL Server, objektů blob a tabulky snížit na velikost menší, ale reprezentativní a lépe zvládnutelné Hive.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718599"
---
# <a name="heading"></a>Ukázková data v kontejnerech objektů blob v Azure, SQL Server a v tabulkách Hivu

Následující články popisují, jak ukázková data, která je uložena v jednom ze tří různých umístěních Azure:

* [**Datový kontejner objektů blob v Azure** ](sample-data-blob.md) vzorkovat stahování prostřednictvím kódu programu a potom vzorkování pomocí vzorového kódu Pythonu.
* [**Data systému SQL Server** ](sample-data-sql-server.md) je shromažďovat vzorky pomocí SQL a programovací jazyk Python. 
* [**Data v tabulce Hive** ](sample-data-hive.md) je shromažďovat vzorky pomocí dotazů Hive.

Tato úloha vzorkování je krok [vědecké zpracování týmových dat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Proč ukázkových dat?**

Pokud je velké datové sady, které chcete analyzovat, je obvykle vhodné na nižší dat ke snížení velikosti menší, ale reprezentativní a lépe zvládnutelné. Možnost může zjednodušit porozumění, průzkum a inženýry funkcí. Tato role vzorkování v procesu Cortana Analytics umožňuje rychlé vytváření prototypů funkcí pro zpracování dat a modelů strojového učení.

