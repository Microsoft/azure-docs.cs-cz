---
title: Azure Application Insights Snapshot debuggeru upgrade pro aplikace .NET | Dokumentace Microsoftu
description: Jak upgradovat Snapshot Debugger na nejnovější verzi v Azure App Service, nebo prostřednictvím balíčků Nuget
services: application-insights
author: MarioHewardt
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: Mario.Hewardt
ms.reviewer: mbullwin
ms.openlocfilehash: 54b79897ee378cda52106fe704e25c50a4325f38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784146"
---
# <a name="upgrading-the-snapshot-debugger"></a>Upgradovat Snapshot Debugger

Pro nejlepší možné zabezpečení pro vaše data, Microsoft pohybuje od TLS 1.0 a TLS 1.1, což se ukázalo se pak můžou útočníci určené. Pokud používáte starší verzi rozšíření webu, bude vyžadovat upgradu pokračovat v práci. Tento dokument popisuje kroky potřebné k upgradovat Snapshot debugger na nejnovější verzi. Existují dva primární cesty upgradu v závislosti na tom, pokud jste povolili ladicí program snímků pomocí rozšíření webu, nebo pokud jste použili Nuget sady SDK/přidané do vaší aplikace. Obě možnosti upgradu jsou popsány níže. 

## <a name="upgrading-the-site-extension"></a>Upgrade rozšíření webu

Pokud jste povolili ladicí program snímků pomocí rozšíření webu, budete moct snadno upgradovat pomocí následujícího postupu:

1. Přihlaste se k portálu Azure.
2. Přejděte k prostředku, který má služba Application Insights a ladicí program snímků povolené. Například pro webovou aplikaci, přejděte k prostředku služby App Service:

   ![Snímek obrazovky se jednotlivé prostředky App Service s názvem DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Jakmile jste přešli na váš prostředek, klikněte na Application Insights v kartě s přehledem:

   ![Snímek obrazovky tři tlačítka. Je vybráno tlačítko Center s názvem Application Insights](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Otevře se nové okno s aktuálním nastavením. Pokud budete chtít využít tuto příležitost a změnit nastavení, můžete je nechat, jak je. **Použít** ve výchozím nastavení není povolené tlačítko v dolní části okna a je nutné přepnout jednoho z nastavení aktivací tlačítka. Nemáte žádné skutečné nastavení změnit, místo toho můžete změnit nastavení a potom okamžitě ho změnit zpět. Doporučujeme, abyste při přepínání Profiler nastavení a pak vyberete **použít**.

   ![Snímek obrazovky nástroje Konfigurace Application Insights aplikaci služby stránka se zvýrazní červeně tlačítko použít](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. Po kliknutí na **použít**, zobrazí se výzva k potvrzení změn.

    > [!NOTE]
    > Web se restartuje jako součást procesu upgradu.

   ![Snímek obrazovky ze služby App Service použít monitorování řádku. Textové pole zobrazí zprávu: "Můžeme se teď použít změny nastavení vaší aplikace a nainstalujte naše nástroje propojit prostředek Application Insights do webové aplikace. Tato operace restartuje Web. Opravdu chcete pokračovat?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Klikněte na tlačítko **Ano** změny se projeví. Během procesu se zobrazí oznámení, zobrazuje aplikují změny:

   ![Snímek obrazovky se použít změny – aktualizuje se rozšíření zprávu, která se zobrazí v pravém horním rohu](./media/snapshot-debugger-upgrade/updating-extensions.png)

Po dokončení, **"Změny se použijí"** se zobrazí oznámení.

   ![Snímek obrazovky zprávy s oznámením změny se použijí.](./media/snapshot-debugger-upgrade/changes-are-applied.png)

Web se teď upgradoval a je připravený k použití.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Upgrade pomocí sady SDK/Nuget Snapshot Debugger

Pokud aplikace používá verzi `Microsoft.ApplicationInsights.SnapshotCollector` nižší než verze 1.3.1, ji budou muset upgradovat [novější verze](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) pokračovat v práci.
