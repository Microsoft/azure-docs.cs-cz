---
title: Nasazení Azure SQL Edge pro turbíny ve farmě contoso Wind
description: V tomto kurzu použijete Azure SQL Edge pro detekci probuzení v turbínách ve farmě contoso Wind.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 12/18/2020
ms.openlocfilehash: e966d756744210dc8f6739b96501dd91f0d8d1b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723469"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>Použití Azure SQL Edge k sestavování inteligentnějších obnovitelných prostředků

Tato ukázka Azure SQL Edge je založená na čistě energie ze společnosti Contoso, která využívá farmu větrných dat, která používá SQL DB Edge pro zpracování dat. 

Tato ukázka vám ukáže, jak vyřešit vyvolanou výstrahu v případě, že v zařízení dojde k detekci turbulence větru. Vytvoříte model a nasadíte ho na Edge SQL DB, který opraví zjištěné probuzení a nakonec optimalizuje výstup energie.

Azure SQL Edge – obnovitelné video ukázkové energie na Channel 9:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]

## <a name="setting-up-the-demo-on-your-local-computer"></a>Nastavení ukázky na místním počítači
Git se použije ke zkopírování všech souborů z ukázky do místního počítače. 

1. Nainstalujte Git odsud [.](https://git-scm.com/download)
2. Otevřete příkazový řádek a přejděte do složky, kde se má úložiště stáhnout. 
3. Vydejte příkaz https://github.com/microsoft/sql-server-samples.git .
4. Přejděte do části **"SQL-Server-samples\samples\demos\azure-SQL-Edge-demos\Wind turbín demo"** v umístění, kde je úložiště klonováno.
5. Postupujte podle pokynů v README.md a nastavte ukázkové prostředí a spusťte ukázku.