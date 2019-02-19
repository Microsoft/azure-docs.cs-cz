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
ms.openlocfilehash: 673f4935dce28b30c10e6abf4c7d22e00c1dd73a
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343906"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Instalace nástroje Azure Stream Analytics pro Visual Studio
Nástroje Azure Stream Analytics podporují sadu Visual Studio 2017, 2015 a 2013. Tento článek popisuje, jak nainstalovat a odinstalovat nástroje.

Další informace o používání nástroje naleznete v tématu [nástroje Stream Analytics pro Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Instalace
### <a name="recommended-visual-studio-2019-and-2017"></a>Doporučené: Visual Studio 2019 a 2017
* Stáhněte si [Visual Studio 2019 (ve verzi Preview 2 nebo vyšší) a Visual Studio 2017 (15.3 nebo novější)](https://www.visualstudio.com/). Podporují se edice Enterprise (Ultimate nebo Premium), Professional a Community. Edice Express se nepodporuje. Visual Studio 2017 na počítači Mac není podporována. 
* Stream Analytics tools jsou součástí **vývoj pro Azure** a **ukládání a zpracování dat** úlohy v sadě Visual Studio 2017. V rámci instalace sady Visual Studio povolte jednu z těchto dvou sad funkcí.

Povolit **ukládání a zpracování dat** pracovního vytížení, jak je znázorněno:

![Vybrané úloze ukládání a zpracování dat](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

Povolit **vývoj pro Azure** pracovního vytížení, jak je znázorněno:

![Je vybrán funkcí vývoj pro Azure](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)

* V nabídce Nástroje zvolte **rozšíření a aktualizace**. Najít Azure Data Lake a Stream Analytics tools nainstalovaná rozšíření a klikněte na **aktualizace** nainstalovat nejnovější rozšíření. 

![Rozšíření sady Visual Studio a aktualizace](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-extensions-updates.png)

### <a name="visual-studio-2015-2013"></a>Visual Studio 2015, 2013
* Instalace sady Visual Studio 2015 nebo Visual Studio 2013 Update 4. Podporují se edice Enterprise (Ultimate nebo Premium), Professional a Community. Edice Express se nepodporuje. 
* Instalace Microsoft Azure SDK pro .NET verze 2.7.1 nebo vyšší pomocí [instalačního programu webové platformy](https://www.microsoft.com/web/downloads/platform.aspx).
* Nainstalujte [nástroje Azure Stream Analytics pro Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Aktualizace

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 a 2017
Nové připomenutí verze se zobrazí v sadě Visual Studio oznámení.

![Visual Studio novou verzí připomenutí](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-new-version-reminder-vs-tools.png)

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 a 2013
Nainstalované nástroje Stream Analytics pro Visual Studio vyhledat nové verze automaticky. Postupujte podle pokynů v místním okně a nainstalujte nejnovější verzi. 


## <a name="uninstall"></a>Odinstalace

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 a 2017
Poklikejte na instalační program sady Visual Studio a vyberte **změnit**. Vymazat **Azure Data Lake a Stream Analytics Tools** zaškrtávací políčko buď z **ukládání a zpracování dat** úlohy nebo **vývoj pro Azure** pracovního vytížení.

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 a 2013
Přejděte do ovládacích panelů a odinstalujte **Microsoft Azure Data Lake a Stream Analytics tools pro Visual Studio**.





