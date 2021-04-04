---
title: Nastavení Azure Stream Analyticsch nástrojů pro Visual Studio
description: Tento článek popisuje požadavky na instalaci a postup nastavení Azure Stream Analytics nástrojů pro sadu Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/22/2018
ms.openlocfilehash: 0077ac8465e8f785e772b384f26e0edc6874a1a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018696"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Nainstalovat Azure Stream Analytics nástroje pro Visual Studio

Sady Visual Studio 2019 a Visual Studio 2017 podporují nástroje Azure Data Lake a Stream Analytics. Tento článek popisuje, jak nainstalovat a odinstalovat nástroje.

Další informace o používání nástrojů najdete v tématu [rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Instalace

Nástroje pro podporu Visual Studio Enterprise (Ultimate/Premium), Professional a Community Edition. Express Edition a Visual Studio pro Mac je nepodporují.

Doporučujeme Visual Studio 2019.

### <a name="install-for-visual-studio-2019-and-2017"></a>Instalace pro Visual Studio 2019 a 2017<a name="recommended-visual-studio-2019-and-2017"></a>

Nástroje Azure Data Lake a Stream Analytics jsou součástí **vývoje Azure** a úloh **úložiště a zpracování dat** . Během instalace povolte buď jednu z těchto dvou úloh. Pokud je aplikace Visual Studio už nainstalovaná, vyberte **nástroje**  >  pro přidání úloh do nástrojů **získat nástroje a funkce** .

Stáhněte si [Visual studio 2019 (Preview 2 nebo vyšší) nebo Visual Studio 2017 (15,3 nebo vyšší)](https://www.visualstudio.com/) a postupujte podle pokynů k instalaci.

Vyberte úlohu **ukládání a zpracování dat** , jak je znázorněno níže:

![Je vybraná úloha ukládání a zpracování dat.](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Vyberte úlohu **vývoje Azure** , jak je znázorněno níže:

![Je vybraná úloha vývoj pro Azure.](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

Po přidání úlohy aktualizujte nástroje. Tento postup se týká sady Visual Studio 2019:

1. Vyberte **rozšíření**  >  **Spravovat rozšíření**.

1. V nabídce **Spravovat rozšíření** vyberte **aktualizace** a zvolte **Azure Data Lake a Stream Analytics nástroje**.

1. Pokud chcete nainstalovat nejnovější rozšíření, vyberte **aktualizovat** .

![Rozšíření a aktualizace sady Visual Studio](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Instalace pro Visual Studio 2015 a 2013<a name="visual-studio-2015-2013"></a>

Nástroje pro podporu Visual Studio Enterprise (Ultimate/Premium), Professional a Community Edition. Edice Express je nepodporuje.

* Nainstalujte Visual Studio 2015 nebo Visual Studio 2013 Update 4.
* Nainstalujte Microsoft Azure SDK pro .NET verze 2.7.1 nebo novější pomocí [instalačního programu webové platformy](https://www.microsoft.com/web/downloads/platform.aspx).
* Nainstalovat [Microsoft Azure Data Lake a Stream Analytics nástroje pro Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Update<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

V případě sady Visual Studio 2019 a Visual Studio 2017 se nové připomenutí verze zobrazí jako oznámení sady Visual Studio.

Pro Visual Studio 2015 a Visual Studio 2013 nástroje automaticky kontrolují nové verze. Postupujte podle pokynů a nainstalujte nejnovější verzi.

## <a name="uninstall"></a>Odinstalovat

Nástroje Azure Data Lake a Stream Analytics můžete odinstalovat. V případě sady Visual Studio 2019 nebo Visual Studio 2017 vyberte **nástroje**  >  **získat nástroje a funkce**. V **úpravách** zrušte výběr **Azure Data Lake a Stream Analyticsch nástrojů**. Zobrazuje se v rámci úlohy **úložiště dat a zpracování** nebo úlohy **vývoje Azure** .

Chcete-li provést odinstalaci ze sady Visual Studio 2015 nebo Visual Studio 2013, použijte **ovládací panel**  >  **programy a funkce**. Odinstalujte **Microsoft Azure Data Lake a Stream Analytics nástrojů pro Visual Studio**.
