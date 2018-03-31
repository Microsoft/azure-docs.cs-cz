---
title: Pokyny k instalaci služby Azure Stream Analytics tools pro sadu Visual Studio | Microsoft Docs
description: Pokyny k instalaci služby Azure Stream Analytics tools pro sadu Visual Studio
keywords: Visual studio
documentationcenter: ''
services: stream-analytics
author: su-jie
manager: ''
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/19/2017
ms.author: sujie
ms.openlocfilehash: fe1f2158439b2c04e8232eb6fe3c9faf79641ffe
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="installation-instructions-for-stream-analytics-tools-for-visual-studio"></a>Pokyny k instalaci služby Stream Analytics tools pro sadu Visual Studio
Nástroje služby Azure Stream Analytics teď podporují 2017 Visual Studio, 2015 a 2013. V tomto dokumentu zavedeme postup instalace a odinstalace nástroje.

Další informace o použití [Stream Analytics tools pro Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="install"></a>Instalace
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Stáhněte si [Visual Studio 2017 (15.3 nebo novější)](https://www.visualstudio.com/). Enterprise (Ultimate nebo Premium), Professional a Community jsou podporované tyto edice. Express edition není podporován. 
* Stream Analytics tools jsou součástí **Azure development** a **úložiště dat a zpracování** úlohy v aplikaci Visual Studio 2017. Povolte jednu z těchto dvou úloh jako součást instalace Visual Studia.

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





