---
title: Upgrade služby Azure Application Insights Snapshot Debugger
description: Postup upgradu Snapshot Debugger pro aplikace .NET na nejnovější verzi v Azure App Services nebo prostřednictvím balíčků NuGet
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77671388"
---
# <a name="upgrading-the-snapshot-debugger"></a>Upgrade Snapshot Debugger

Abychom zajistili nejlepší možnou bezpečnost vašich dat, Microsoft se přesouvá od TLS 1,0 a TLS 1,1, které se ukázaly jako zranitelné vůči stanoveným útokům. Pokud používáte starší verzi rozšíření webu, bude vyžadovat upgrade, aby pokračoval v práci. Tento dokument popisuje kroky potřebné k upgradu ladicího programu snímků na nejnovější verzi. Existují dva primární cesty upgradu v závislosti na tom, jestli jste povolili Snapshot Debugger pomocí rozšíření webu nebo když jste do své aplikace použili sadu SDK/NuGet, kterou jste přidali. Obě cesty upgradu jsou popsány níže. 

## <a name="upgrading-the-site-extension"></a>Upgrade rozšíření webu

> [!IMPORTANT]
> Starší verze Application Insights používaly rozšíření privátního webu nazvané _Application Insights Extension pro Azure App Service_. Aktuální prostředí Application Insights je povolené nastavením nastavení aplikace tak, aby se vysvětloval předem instalované rozšíření webu.
> Aby nedocházelo ke konfliktům, což může způsobit, že vaše lokalita přestane fungovat, je důležité nejprve odstranit rozšíření privátního webu. Viz krok 4 níže.

Pokud jste povolili program Snapshot debugger pomocí rozšíření lokality, můžete upgradovat pomocí následujícího postupu:

1. Přihlaste se k portálu Azure.
2. Přejděte k prostředku, který má povolenou Application Insights a Snapshot Debugger. Například pro webovou aplikaci přejděte na prostředek App Service:

   ![Snímek obrazovky s jednotlivými App Service prostředky s názvem DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Po přechodu na prostředek klikněte na okno rozšíření a počkejte, než se doplní seznam rozšíření:

   ![Snímek obrazovky s rozšířeními App Service zobrazující Application Insights rozšíření pro Azure App Service nainstalované](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Pokud je nainstalovaná nějaká verze _Application Insights rozšíření pro Azure App Service_ , vyberte ji a klikněte na Odstranit. Potvrďte volbu **Ano** , pokud chcete odstranit rozšíření a počkat na dokončení odstranění před přechodem k dalšímu kroku.

   ![Snímek obrazovky s rozšířeními App Service znázorňujícími rozšíření Application Insights pro Azure App Service s zvýrazněným tlačítkem odstranit](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Přejděte na okno Přehled prostředku a klikněte na Application Insights:

   ![Snímek obrazovky se třemi tlačítky Je vybrané prostřední tlačítko s názvem Application Insights.](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Pokud se zobrazí okno Application Insights pro tento App Service, zobrazí se výzva, abyste zapnuli Application Insights. Vyberte **zapnout Application Insights**.
 
   ![Snímek obrazovky s prvním prostředím pro okno Application Insights s zvýrazněným tlačítkem zapnout Application Insights](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. Zobrazí se aktuální nastavení Application Insights. Pokud nechcete mít možnost změnit nastavení, můžete je nechat jako. Tlačítko **použít** v dolní části okna není ve výchozím nastavení povolené a vy budete muset zapnout tlačítko, abyste aktivovali jedno z nastavení. Nemusíte měnit žádná skutečná nastavení, místo toho můžete změnit nastavení a potom ho hned znovu změnit. Doporučujeme, abyste převedli nastavení profileru a pak vybrali **použít**.

   ![Snímek obrazovky konfigurační stránky Application Insights App Service se zvýrazněným tlačítkem použít v červené](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. Po kliknutí na **použít**se zobrazí výzva k potvrzení změn.

    > [!NOTE]
    > Lokalita bude restartována v rámci procesu upgradu.

   ![Snímek obrazovky s výzvou pro monitorování použití App Service V textovém poli se zobrazí zpráva: "v nastavení aplikace nyní použijeme změny a nainstalujeme naše nástroje, které propojí váš Application Insights prostředek s webovou aplikací. Tím se server restartuje. Chcete pokračovat? "](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Kliknutím na **Ano** změny aplikujte a počkejte, než se proces dokončí.

Lokalita je nyní upgradována a je připravena k použití.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Upgrade Snapshot Debugger pomocí sady SDK/NuGet

Pokud aplikace používá verzi `Microsoft.ApplicationInsights.SnapshotCollector` nižší verze 1.3.1, bude nutné ji upgradovat na [novější verzi](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) , aby pokračovala v práci.
