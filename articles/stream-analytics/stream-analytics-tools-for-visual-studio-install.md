---
title: Nastavení Azure Stream Analytics nástrojů pro Visual Studio
description: Tento článek popisuje požadavky na instalaci a jak nastavit Azure Stream Analytics tools pro sadu Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 94ed603990859d12f709e4a6121e3736221cf10a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651174"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Instalace nástrojů Azure Stream Analytics tools for Visual Studio
Nástroje služby Azure Stream Analytics podporovat 2017 Visual Studio, 2015 a 2013. Tento článek popisuje, jak k instalaci a odinstalaci nástroje.

Další informace o používání nástrojů najdete v tématu [Stream Analytics tools pro Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="install"></a>Instalace
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Stáhněte si [Visual Studio 2017 (15.3 nebo novější)](https://www.visualstudio.com/). Enterprise (Ultimate nebo Premium), Professional a Community jsou podporované tyto edice. Express edition není podporován. 
* Stream Analytics tools jsou součástí **Azure development** a **úložiště dat a zpracování** úlohy v aplikaci Visual Studio 2017. V rámci instalace sady Visual Studio povolte jednu z těchto dvou sad funkcí.

Povolit **úložiště dat a zpracování** zatížení, jak je znázorněno:

![Je vybraný zatížení úložiště a zpracování dat](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

Povolit **Azure development** zatížení, jak je znázorněno:

![Je vybraný pracovního vytížení Azure vývoj](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Nainstalujte Visual Studio 2015 nebo Visual Studio 2013 Update 4. Enterprise (Ultimate nebo Premium), Professional a Community jsou podporované tyto edice. Express edition není podporován. 
* Nainstalovat sadu Microsoft Azure SDK pro .NET verze 2.7.1 nebo vyšší pomocí [instalačního programu webové platformy](http://www.microsoft.com/web/downloads/platform.aspx).
* Nainstalujte [Azure Stream Analytics tools pro Visual Studio](http://aka.ms/asatoolsvs).

## <a name="update"></a>Aktualizace

### <a name="visual-studio-2017"></a>Visual Studio 2017
Nové verze upozornění se zobrazí v sadě Visual Studio oznámení. 

### <a name="visual-studio-2013-and-visual-studio-2015"></a>Visual Studio 2013 a Visual Studio 2015
Nainstalované Stream Analytics tools pro Visual Studio vyhledávat nové verze automaticky. Postupujte podle pokynů v místním okně, nainstalujte nejnovější verzi. 


## <a name="uninstall"></a>Odinstalace

### <a name="visual-studio-2017"></a>Visual Studio 2017
Klikněte dvakrát na instalační program sady Visual Studio a vyberte **upravit**. Vymazat **Azure Data Lake a Stream Analytics Tools** políčko buď z **úložiště dat a zpracování** zatížení nebo **Azure development** zatížení.

### <a name="visual-studio-2013-and-visual-studio-2015"></a>Visual Studio 2013 a Visual Studio 2015
Přejděte do ovládacích panelů a odinstalovat **Microsoft Azure Data Lake a Stream Analytics tools pro Visual Studio**.





