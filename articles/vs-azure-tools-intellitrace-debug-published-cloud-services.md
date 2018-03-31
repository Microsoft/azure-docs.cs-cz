---
title: Ladění publikovaný Azure cloud service pomocí sady Visual Studio a IntelliTrace | Microsoft Docs
description: Zjistěte, jak ladit Cloudová služba se Visual Studio a IntelliTrace
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
ms.workload: na
ms.date: 03/21/2017
ms.author: mikejo
ms.openlocfilehash: 2ca15bd5ffa88d2e8053decf5b81c265b1d9c6e1
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="debugging-a-published-azure-cloud-service-with-visual-studio-and-intellitrace"></a>Ladění publikovaný Azure cloud service sadou Visual Studio a IntelliTrace
S použitím technologie IntelliTrace můžete protokolovat rozsáhlé ladicí informace pro instanci role při spuštění v Azure. Pokud potřebujete najít příčinu problému, můžete protokoly IntelliTrace krokovat kód ze sady Visual Studio, jako kdyby byly spuštěny v Azure. IntelliTrace záznamy ve skutečnosti klíčů provádění kódu a dat prostředí, když vaše aplikace Azure běží jako cloudová služba v Azure a umožňuje přehrát zaznamenaná data ze sady Visual Studio. 

Můžete použít IntelliTrace, pokud máte Visual Studio Enterprise nainstalován a cíle vaší aplikace Azure rozhraní .NET Framework 4 nebo novější verze. IntelliTrace shromažďuje informace o Azure role. Virtuální počítače pro tyto role vždy spouštět 64bitové operační systémy.

Jako alternativu, můžete použít [vzdálené ladění](http://go.microsoft.com/fwlink/p/?LinkId=623041) připojit přímo k Cloudová služba, která běží v Azure.

> [!IMPORTANT]
> IntelliTrace je určený jenom pro účely ladění a by nemělo být použito pro produkční nasazení.
> 

## <a name="configure-an-azure-application-for-intellitrace"></a>Konfigurace aplikací Azure pro IntelliTrace
Pokud chcete povolit IntelliTrace pro aplikaci Azure, musíte vytvořit a publikování aplikace z projektu Visual Studio Azure. Před publikováním do Azure, je nutné nakonfigurovat IntelliTrace pro vaše aplikace Azure. Pokud publikujete aplikaci bez konfigurace IntelliTrace, budete muset znovu publikovat projektu. Další informace najdete v tématu [publikování Azure cloud services projektů pomocí sady Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Až budete připravení nasadit aplikaci Azure, ověřte, že jsou vaše cíle sestavení projektu nastavena na **ladění**.

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a v místní nabídce vyberte **publikovat**.
   
1. V **publikování aplikaci Azure** dialogové okno, vyberte předplatné Azure a vyberte **Další**.

1. V **nastavení** vyberte **Upřesnit nastavení** kartě.

1. Zapnout **povolit IntelliTrace** možnost shromáždit protokoly IntelliTrace pro vaši aplikaci, když je publikován v cloudu.
   
1. Chcete-li přizpůsobit základní konfiguraci IntelliTrace, vyberte **nastavení** vedle **povolit IntelliTrace**.

    ![Odkaz nastavení IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/intellitrace-settings-link.png)
   
1. V **IntelliTrace nastavení** dialogové okno, můžete zadat které události do protokolu, zda se mají shromažďovat informace o volání, které moduly a procesy shromažďování protokolů a kolik místa k přidělení k záznamu. Další informace o IntelliTrace najdete v tématu [ladění pomocí technologie IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).
   
    ![Nastavení IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

V protokolu IntelliTrace je cyklický soubor protokolu maximální velikost zadaná v nastavení IntelliTrace (výchozí velikost je 250 MB). Do souboru v systému souborů virtuálního počítače se shromáždí protokoly IntelliTrace. Pokud budete požadovat protokoly, je snímek prováděné v tomto bodě v čase a stahovat do místního počítače.

Po cloudovou službu systému Azure se publikoval do Azure, můžete určit, pokud bylo povoleno IntelliTrace z uzlu Azure v **Průzkumníka serveru**, jak je znázorněno na následujícím obrázku:

![Průzkumník serveru - IntelliTrace povoleno](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="download-intellitrace-logs-for-a-role-instance"></a>Stažení protokolů IntelliTrace pro instanci role
Pomocí sady Visual Studio, můžete stáhnout protokoly IntelliTrace pro instanci role pomocí následujících kroků:

1. V **Průzkumníka serveru**, rozbalte **cloudové služby** uzel a vyhledejte instanci role, jejichž protokoly, které chcete stáhnout. 

1. Klikněte pravým tlačítkem na instanci role a v místní nabídce s vyberte **zobrazit protokoly IntelliTrace**. 

    ![Zobrazit možnosti nabídky protokoly IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/view-intellitrace-logs.png)

1. Protokoly IntelliTrace se stáhnou do souboru v adresáři v místním počítači. Pokaždé, když, abyste si vyžádali nástroje IntelliTrace v protokolech, se vytvoří nový snímek. Během stahování protokolů, Visual Studio zobrazí průběh operace v **protokol činnosti Azure** okno. Jak je znázorněno na následujícím obrázku, můžete rozbalit položky řádku pro operaci pro zobrazení dalších podrobností.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

Můžete pokračovat v práci v sadě Visual Studio, když jsou stahování protokolů IntelliTrace. Po dokončení stahování protokol otevře v sadě Visual Studio.

> [!NOTE]
> Protokoly IntelliTrace může obsahovat výjimky, které rozhraní generuje a zpracovává později. Kód vnitřní framework generuje tyto výjimky jako součást normální spuštění role, takže může bezpečně ignorovat.
> 
> 

## <a name="next-steps"></a>Další postup
- [Možnosti pro ladění cloudové služby Azure](vs-azure-tools-debugging-cloud-services-overview.md)
- [Publikování Azure cloud service pomocí sady Visual Studio](vs-azure-tools-publishing-a-cloud-service.md)