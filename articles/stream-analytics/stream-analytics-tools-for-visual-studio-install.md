---
title: Nastavení Azure Stream Analytics tools for Visual Studio
description: Tento článek popisuje požadavky na instalaci a jak nastavit Azure Stream Analytics tools for Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: e87fc5b91e9e1d5f4f8449e84b17bcdab9c0b6b2
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39713590"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Instalace nástroje Azure Stream Analytics pro Visual Studio
Nástroje Azure Stream Analytics podporují Visual Studio 2017, 2015 nebo 2013. Tento článek popisuje, jak nainstalovat a odinstalovat nástroje.

Další informace o používání nástroje naleznete v tématu [nástroje Stream Analytics pro Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Instalace
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Stáhněte si [Visual Studio 2017 (15.3 nebo novější)](https://www.visualstudio.com/). Podporují se edice Enterprise (Ultimate nebo Premium), Professional a Community. Edice Express se nepodporuje. 
* Stream Analytics tools jsou součástí **vývoj pro Azure** a **ukládání a zpracování dat** úlohy v sadě Visual Studio 2017. V rámci instalace sady Visual Studio povolte jednu z těchto dvou sad funkcí.

Povolit **ukládání a zpracování dat** pracovního vytížení, jak je znázorněno:

![Vybrané úloze ukládání a zpracování dat](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

Povolit **vývoj pro Azure** pracovního vytížení, jak je znázorněno:

![Je vybrán funkcí vývoj pro Azure](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Instalace sady Visual Studio 2015 nebo Visual Studio 2013 Update 4. Podporují se edice Enterprise (Ultimate nebo Premium), Professional a Community. Edice Express se nepodporuje. 
* Instalace Microsoft Azure SDK pro .NET verze 2.7.1 nebo vyšší pomocí [instalačního programu webové platformy](http://www.microsoft.com/web/downloads/platform.aspx).
* Nainstalujte [nástroje Azure Stream Analytics pro Visual Studio](http://aka.ms/asatoolsvs).

## <a name="update"></a>Aktualizace

### <a name="visual-studio-2017"></a>Visual Studio 2017
Nové připomenutí verze se zobrazí v sadě Visual Studio oznámení. 

### <a name="visual-studio-2013-and-visual-studio-2015"></a>Visual Studio 2013 a Visual Studio 2015
Nainstalované nástroje Stream Analytics pro Visual Studio vyhledat nové verze automaticky. Postupujte podle pokynů v místním okně a nainstalujte nejnovější verzi. 


## <a name="uninstall"></a>Odinstalace

### <a name="visual-studio-2017"></a>Visual Studio 2017
Poklikejte na instalační program sady Visual Studio a vyberte **změnit**. Vymazat **Azure Data Lake a Stream Analytics Tools** zaškrtávací políčko buď z **ukládání a zpracování dat** úlohy nebo **vývoj pro Azure** pracovního vytížení.

### <a name="visual-studio-2013-and-visual-studio-2015"></a>Visual Studio 2013 a Visual Studio 2015
Přejděte do ovládacích panelů a odinstalujte **Microsoft Azure Data Lake a Stream Analytics tools pro Visual Studio**.





