---
title: Instalační program služby Azure Stream Analytics tools pro sadu Visual Studio
description: Tento článek popisuje požadavky na instalaci a jak nastavit Azure Stream Analytics tools pro sadu Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/19/2017
ms.openlocfilehash: 511658fc0e2b480987455007dac5f55cd7850feb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Instalace nástrojů Azure Stream Analytics tools for Visual Studio
Nástroje služby Azure Stream Analytics teď podporují 2017 Visual Studio, 2015 a 2013. Tento dokument popisuje, jak k instalaci a odinstalaci nástroje.

Další informace o používání nástrojů najdete v tématu [Stream Analytics tools pro Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="install"></a>Instalace
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Stáhněte si [Visual Studio 2017 (15.3 nebo novější)](https://www.visualstudio.com/). Enterprise (Ultimate nebo Premium), Professional a Community jsou podporované tyto edice. Express edition není podporován. 
* Stream Analytics tools jsou součástí **Azure development** a **úložiště dat a zpracování** úlohy v aplikaci Visual Studio 2017. V rámci instalace sady Visual Studio povolte jednu z těchto dvou sad funkcí.

Povolit **úložiště dat a zpracování** zatížení, jak je znázorněno:

![Úlohami datového úložiště a zpracování](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)

Povolit **Azure development** zatížení, jak je znázorněno:

![Vývoj pro Azure zatížení](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Nainstalujte Visual Studio 2015 nebo Visual Studio 2013 Update 4. Enterprise (Ultimate nebo Premium), Professional a Community jsou podporované tyto edice. Express edition není podporován. 
* Nainstalovat sadu Microsoft Azure SDK pro .NET verze 2.7.1 nebo vyšší pomocí [instalačního programu webové platformy](http://www.microsoft.com/web/downloads/platform.aspx).
* Nainstalujte [Azure Stream Analytics tools pro Visual Studio](http://aka.ms/asatoolsvs).



## <a name="update"></a>Aktualizace

### <a name="visual-studio-2017"></a>Visual Studio 2017
Nové verze upozornění se zobrazí v sadě Visual Studio oznámení. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Nainstalované Stream Analytics tools pro Visual Studio vyhledávat nové verze automaticky. Postupujte podle pokynů v místním okně, nainstalujte nejnovější verzi. 


## <a name="uninstall"></a>Odinstalace

### <a name="visual-studio-2017"></a>Visual Studio 2017
Klikněte dvakrát na instalační program sady Visual Studio a vyberte **upravit**. Vymazat **Azure Data Lake a Stream Analytics Tools** políčko buď z **úložiště dat a zpracování** zatížení nebo **Azure development** zatížení.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Přejděte do ovládacích panelů a odinstalovat **Microsoft Azure Data Lake a Stream Analytics tools pro Visual Studio**.





