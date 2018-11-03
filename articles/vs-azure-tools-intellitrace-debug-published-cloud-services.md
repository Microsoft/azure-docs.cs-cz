---
title: Ladění publikování Azure cloudové služby pomocí IntelliTrace a sady Visual Studio | Dokumentace Microsoftu
description: Zjistěte, jak ladění cloudové služby pomocí IntelliTrace a sady Visual Studio
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 5e6662fc-b917-43ea-bf2b-4f2fc3d213dc
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 03/21/2017
ms.author: mikejo
ms.openlocfilehash: df361d1721e3a437294a21129acb0ad8c2093630
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969423"
---
# <a name="debugging-a-published-azure-cloud-service-with-visual-studio-and-intellitrace"></a>Ladění publikované Azure cloudové služby pomocí IntelliTrace a sady Visual Studio
Pomocí nástroje IntelliTrace můžete protokolovat podrobné ladicí informace pro instanci role při spuštění v Azure. Pokud je potřeba najít příčinu problému, můžete protokoly IntelliTrace ke krokování kódu ze sady Visual Studio, jako kdyby byly spuštěné v Azure. Nástroj IntelliTrace zaznamenává v důsledku toho klíčů provádění kódu a dat prostředí, když vaše aplikace Azure běží jako cloudová služba v Azure a umožňuje přehrát zaznamenaná data ze sady Visual Studio. 

Můžete použít nástroj IntelliTrace, pokud máte nainstalované Visual Studio Enterprise a vaše aplikace Azure cílí rozhraní .NET Framework 4 nebo novější. Nástroj IntelliTrace shromažďuje informace o Azure role. Virtuální počítače pro tyto role ke spuštění vždy 64bitové operační systémy.

Jako alternativu můžete použít [vzdálené ladění](http://go.microsoft.com/fwlink/p/?LinkId=623041) připojit přímo do cloudové služby, na kterém běží v Azure.

> [!IMPORTANT]
> Nástroj IntelliTrace je určená pro účely ladění pouze a neměl by se používat pro produkční nasazení.
> 

## <a name="configure-an-azure-application-for-intellitrace"></a>Konfigurace aplikace Azure pro technologii IntelliTrace
K povolení technologie IntelliTrace pro aplikaci Azure, musíte vytvořit a publikovat aplikace z projektu sady Visual Studio v Azure. Před publikováním na Azure, je nutné nakonfigurovat nástroj IntelliTrace pro vaše aplikace Azure. Pokud publikujete aplikaci bez nástroje IntelliTrace, budete muset znovu publikujte projekt. Další informace najdete v tématu [publikování Azure cloud services projektů s použitím sady Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Až budete připravení nasadit vaše aplikace Azure, ověřte, že váš projekt cílí sestavení jsou nastaveny na **ladění**.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a v místní nabídce vyberte **publikovat**.
   
1. V **publikování aplikaci Azure** dialogového okna, vyberte předplatné Azure a vyberte **Další**.

1. V **nastavení** stránky, vyberte **Upřesnit nastavení** kartu.

1. Zapnout **povolit IntelliTrace** možnost shromažďovat protokoly IntelliTrace pro vaši aplikaci, když je publikován v cloudu.
   
1. Chcete-li přizpůsobit základní konfigurace technologie IntelliTrace, vyberte **nastavení** vedle **povolit IntelliTrace**.

    ![Odkaz nastavení IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/intellitrace-settings-link.png)
   
1. V **nastavení IntelliTrace** dialogového okna, můžete zadat události do protokolu, jestli se má shromažďovat informace o volání, které moduly a procesy pro shromažďování protokolů pro a tom, kolik místa k přidělení na záznam. Další informace o IntelliTrace naleznete v tématu [ladění pomocí nástroje IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).
   
    ![Nastavení technologie IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

Protokol nástroje IntelliTrace je cyklická soubor protokolu o maximální velikosti zadaného v nastavení nástroje IntelliTrace (výchozí velikost je 250 MB). Do souboru v systému souborů virtuálního počítače jsou shromážděné protokoly IntelliTrace. Pokud si vyžádáte protokoly, snímku je přijata od tohoto okamžiku v čase a stáhnou do místního počítače.

Po publikování cloudové služby Azure do Azure, můžete určit, pokud nástroj IntelliTrace je povolen v Azure uzlu **Průzkumníka serveru**, jak je znázorněno na následujícím obrázku:

![Průzkumník serveru – IntelliTrace povoleno](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="download-intellitrace-logs-for-a-role-instance"></a>Stáhnout protokoly IntelliTrace pro instanci role
Pomocí sady Visual Studio, si můžete stáhnout protokoly IntelliTrace pro instanci role pomocí následujících kroků:

1. V **Průzkumníka serveru**, rozbalte **Cloud Services** uzel a vyhledejte instance role, jehož protokoly chcete stáhnout. 

1. Klikněte pravým tlačítkem na instanci role a v místní nabídce s, vyberte **zobrazit protokoly IntelliTrace**. 

    ![Zobrazit možnost nabídky protokoly IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/view-intellitrace-logs.png)

1. Protokoly IntelliTrace se stáhnou do souboru v adresáři v místním počítači. Protokoluje pokaždé, když, abyste si vyžádali nástroje IntelliTrace, je vytvořen nový snímek. Během stahování protokolů, Visual Studio zobrazí průběh operace **protokolu aktivit Azure** okna. Jak je znázorněno na následujícím obrázku, můžete rozbalit položku řádku pro operace, která se zobrazí podrobnější údaje.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

Můžete pokračovat v práci v sadě Visual Studio při stahování protokolu IntelliTrace. Po dokončení stahování protokolu se otevře v sadě Visual Studio.

> [!NOTE]
> Protokoly nástroje IntelliTrace může obsahovat výjimky, které rozhraní framework generuje a zpracovává později. Interní rámec kód generuje tyto výjimky v rámci normálního spouštění role, takže může bezpečně ignorovat.
> 
> 

## <a name="next-steps"></a>Další postup
- [Možnosti pro ladění cloudových služeb Azure](vs-azure-tools-debugging-cloud-services-overview.md)
- [Publikování cloudové služby Azure pomocí sady Visual Studio](vs-azure-tools-publishing-a-cloud-service.md)