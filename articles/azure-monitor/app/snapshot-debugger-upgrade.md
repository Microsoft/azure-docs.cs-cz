---
title: Upgrade služby Azure Application Insights Snapshot Debugger pro aplikace .NET | Microsoft Docs
description: Postup upgradu Snapshot Debugger na nejnovější verzi v Azure App Services nebo prostřednictvím balíčků NuGet
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: MarioHewardt
ms.author: marioh
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e2b21b7cbb6b04da0c93e73c0cacb8a05c338bde
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899838"
---
# <a name="upgrading-the-snapshot-debugger"></a>Upgrade Snapshot Debugger

Abychom zajistili nejlepší možnou bezpečnost vašich dat, Microsoft se přesouvá od TLS 1,0 a TLS 1,1, které se ukázaly jako zranitelné vůči stanoveným útokům. Pokud používáte starší verzi rozšíření webu, bude vyžadovat upgrade, aby pokračoval v práci. Tento dokument popisuje kroky potřebné k upgradu ladicího programu snímků na nejnovější verzi. Existují dva primární cesty upgradu v závislosti na tom, jestli jste povolili Snapshot Debugger pomocí rozšíření webu nebo když jste do své aplikace použili sadu SDK/NuGet, kterou jste přidali. Obě cesty upgradu jsou popsány níže. 

## <a name="upgrading-the-site-extension"></a>Upgrade rozšíření webu

Pokud jste povolili program Snapshot debugger pomocí rozšíření lokality, můžete snadno upgradovat pomocí následujícího postupu:

1. Přihlaste se k portálu Azure.
2. Přejděte k prostředku, který má povolenou Application Insights a Snapshot Debugger. Například pro webovou aplikaci přejděte na prostředek App Service:

   ![Snímek obrazovky s jednotlivými App Service prostředky s názvem DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Po přechodu na prostředek klikněte v okně Přehled na Application Insights:

   ![Snímek obrazovky se třemi tlačítky Je vybrané prostřední tlačítko s názvem Application Insights.](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Otevře se nové okno s aktuálním nastavením. Pokud nechcete mít možnost změnit nastavení, můžete je nechat jako. Tlačítko **použít** v dolní části okna není ve výchozím nastavení povolené a vy budete muset zapnout tlačítko, abyste aktivovali jedno z nastavení. Nemusíte měnit žádná skutečná nastavení, místo toho můžete změnit nastavení a potom ho hned znovu změnit. Doporučujeme, abyste převedli nastavení profileru a pak vybrali **použít**.

   ![Snímek obrazovky konfigurační stránky Application Insights App Service se zvýrazněným tlačítkem použít v červené](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. Po kliknutí na **použít**se zobrazí výzva k potvrzení změn.

    > [!NOTE]
    > Lokalita bude restartována v rámci procesu upgradu.

   ![Snímek obrazovky s výzvou pro monitorování použití App Service V textovém poli se zobrazí zpráva: "v nastavení aplikace nyní použijeme změny a nainstalujeme naše nástroje, které propojí váš Application Insights prostředek s webovou aplikací. Tím se server restartuje. Chcete pokračovat? "](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Kliknutím na **Ano** změny aplikujte. Během procesu se zobrazí oznámení o tom, že se změny aplikují:

   ![Snímek obrazovky s použitými změnami – aktualizace rozšíření, která se zobrazí v pravém horním rohu](./media/snapshot-debugger-upgrade/updating-extensions.png)

Po dokončení se zobrazí oznámení **"změny se použijí"** .

   ![Snímek obrazovky s informacemi o změnách použitých ve zprávě](./media/snapshot-debugger-upgrade/changes-are-applied.png)

Lokalita je nyní upgradována a je připravena k použití.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Upgrade Snapshot Debugger pomocí sady SDK/NuGet

Pokud aplikace používá `Microsoft.ApplicationInsights.SnapshotCollector` verze nižší než 1.3.1, bude nutné ji upgradovat na [novější verzi](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) , aby pokračovala v práci.
