---
title: Nastavení nástrojů Azure Stream Analytics pro Visual Studio
description: Tento článek popisuje požadavky na instalaci a jak nastavit nástroje Azure Stream Analytics pro Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: fa3990d2239da71fb27f4c9a06699f758fcfe7a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354369"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Instalace nástrojů Azure Stream Analytics pro Visual Studio

Visual Studio 2019 a Visual Studio 2017 podporují nástroje Azure Data Lake a Stream Analytics. Tento článek popisuje, jak nainstalovat a odinstalovat nástroje.

Další informace o používání nástrojů najdete v [tématu Úvodní příručka: Vytvoření úlohy Azure Stream Analytics pomocí Visual Studia](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Instalace

Nástroje podporují edice Visual Studio Enterprise (Ultimate/Premium), Professional a Community. Express edition a Visual Studio pro Mac je nepodporují.

Doporučujeme Visual Studio 2019.

### <a name="install-for-visual-studio-2019-and-2017"></a>Instalace pro Visual Studio 2019 a 2017<a name="recommended-visual-studio-2019-and-2017"></a>

Nástroje Azure Data Lake a Stream Analytics jsou součástí úloh **azure developmentu** a **úložiště dat a zpracování.** Povolte jednu z těchto dvou úloh během instalace. Pokud je Visual Studio už nainstalované, vyberte **Nástroje** > **získat nástroje a funkce** a přidejte úlohy.

Stáhněte si [Visual Studio 2019 (náhled 2 nebo vyšší) nebo Visual Studio 2017 (15.3 nebo vyšší)](https://www.visualstudio.com/) a postupujte podle pokynů k instalaci.

Vyberte úlohu **ukládání a zpracování dat,** jak je znázorněno:

![Je vybrána úloha pro ukládání a zpracování dat.](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Vyberte **zatížení vývoje Azure,** jak je znázorněno:

![Je vybráno vývojové zatížení Azure](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

Po přidání pracovního vytížení aktualizujte nástroje. Tento postup odkazuje na Visual Studio 2019:

1. Vyberte **možnost Spravovat** > **rozšíření**.

1. V **spravovat rozšíření**vyberte **Aktualizace** a zvolte Nástroje Azure Data Lake a **Stream Analytics Tools**.

1. Chcete-li nainstalovat nejnovější rozšíření, vyberte **možnost Aktualizovat.**

![Rozšíření a aktualizace sady Visual Studio](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Instalace pro Visual Studio 2015 a 2013<a name="visual-studio-2015-2013"></a>

Nástroje podporují edice Visual Studio Enterprise (Ultimate/Premium), Professional a Community. Express edition je nepodporuje.

* Nainstalujte Visual Studio 2015 nebo Visual Studio 2013 Update 4.
* Nainstalujte sadu Microsoft Azure SDK pro rozhraní .NET verze 2.7.1 nebo vyšší pomocí [instalačního programu webové platformy](https://www.microsoft.com/web/downloads/platform.aspx).
* Nainstalujte [nástroje Microsoft Azure Data Lake a Stream Analytics pro Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Aktualizace<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

Pro Visual Studio 2019 a Visual Studio 2017 se jako oznámení visual studia zobrazí připomenutí nové verze.

Pro Visual Studio 2015 a Visual Studio 2013 nástroje automaticky zkontrolujte nové verze. Podle pokynů nainstalujte nejnovější verzi.

## <a name="uninstall"></a>Odinstalace

Můžete odinstalovat Nástroje Azure Data Lake a Stream Analytics. Pro Visual Studio 2019 nebo Visual Studio 2017 vyberte **Nástroje** > **získat nástroje a funkce**. V **pozměnění zrušte**výběr **nástrojů Azure Data Lake a Stream Analytics**. Zobrazí se pod úlohou **úložiště a zpracování dat** nebo zatížení vývoje **Azure.**

Chcete-li odinstalovat z Visual Studia 2015 nebo Visual Studia 2013, přejděte na **Ovládací panely** > **Programy a funkce**. Odinstalujte **nástroje Microsoft Azure Data Lake a Stream Analytics pro Visual Studio**.
