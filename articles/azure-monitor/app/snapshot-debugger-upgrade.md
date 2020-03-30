---
title: Upgrade ladicího programu snímků přehledů aplikací Azure
description: Jak upgradovat ladicí program snímků pro aplikace .NET na nejnovější verzi ve službě Azure App Services nebo prostřednictvím balíčků Nuget
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671388"
---
# <a name="upgrading-the-snapshot-debugger"></a>Inovace ladicího programu snímků

Aby bylo zajištěno co nejlepší zabezpečení vašich dat, společnost Microsoft se vzdaluje od služeb TLS 1.0 a TLS 1.1, u kterých se ukázalo, že jsou zranitelné vůči určeným útočníkům. Pokud používáte starší verzi rozšíření webu, bude to vyžadovat upgrade, aby bylo pokračovat v práci. Tento dokument popisuje kroky potřebné k upgradu ladicího programu snímek na nejnovější verzi. Existují dvě primární cesty upgradu v závislosti na tom, zda jste povolili ladicí program snímek pomocí rozšíření webu nebo pokud jste použili SDK/Nuget přidán do vaší aplikace. Obě cesty upgradu jsou popsány níže. 

## <a name="upgrading-the-site-extension"></a>Inovace rozšíření webu

> [!IMPORTANT]
> Starší verze Application Insights používaly privátní rozšíření webu s názvem _Rozšíření Application Insights pro Službu Azure App Service_. Aktuální prostředí Application Insights je povoleno nastavením nastavení aplikace tak, aby se rozsvítilo předinstalované rozšíření webu.
> Chcete-li se vyhnout konfliktům, které mohou způsobit, že váš web přestane fungovat, je důležité nejprve odstranit rozšíření soukromého webu. Viz krok 4 níže.

Pokud jste povolili ladicí program snímek pomocí rozšíření webu, můžete upgradovat pomocí následujícího postupu:

1. Přihlaste se k portálu Azure.
2. Přejděte na prostředek, který má application insights a snapshot ladicí program povolen. Například pro webovou aplikaci přejděte na prostředek služby App Service:

   ![Snímek obrazovky s jednotlivými prostředky služby App Service s názvem DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Po přechodu na zdroj klikněte na okno Rozšíření a počkejte, až se seznam rozšíření naplní:

   ![Snímek obrazovky s rozšířeními služby App Service zobrazující rozšíření Application Insights pro službu Azure App Service](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Pokud je nainstalovaná jakákoli verze _rozšíření Application Insights pro službu Azure App Service,_ vyberte ji a klikněte na Odstranit. Potvrďte **Ano,** chcete-li rozšíření odstranit a před přechodem k dalšímu kroku počkejte na dokončení odstranění.

   ![Snímek obrazovky s rozšířeními služby App Service zobrazující rozšíření Application Insights pro službu Azure App Service se zvýrazněným tlačítkem Odstranit](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Přejděte do okna Přehled vašeho prostředku a klikněte na Přehledy aplikací:

   ![Snímek obrazovky se třemi tlačítky Je vybráno středové tlačítko s názvem Application Insights](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Pokud je to poprvé, co jste si prohlíželi okno Application Insights pro tuto službu aplikací, budete vyzváni k zapnutí Application Insights. Vyberte **Možnost Zapnout přehledy aplikací**.
 
   ![Snímek obrazovky s prvním prostředím pro okno Application Insights se zvýrazněným tlačítkem Zapnout přehledy aplikací](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. Zobrazí se aktuální nastavení Přehledy aplikací. Pokud nechcete využít příležitosti ke změně nastavení, můžete je nechat tak, jak jsou. Tlačítko **Použít** v dolní části okna není ve výchozím nastavení povoleno a budete muset přepnout jedno z nastavení pro aktivaci tlačítka. Nemusíte měnit žádné skutečné nastavení, spíše můžete změnit nastavení a okamžitě jej změnit zpět. Doporučujeme přepínat nastavení Profiler a pak vybrat **použít**.

   ![Snímek obrazovky stránky Konfigurace služby Application Insights App Service se zvýrazněným červeným tlačítkem Použít](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. Po klepnutí na tlačítko **Použít**budete vyzváni k potvrzení změn.

    > [!NOTE]
    > Web bude restartován jako součást procesu upgradu.

   ![Snímek obrazovky s výzvou k použití monitorování služby App Service V textovém poli se zobrazí zpráva: "Teď použijeme změny v nastavení vaší aplikace a nainstalujeme naše nástroje, které propojí váš prostředek Application Insights s webovou aplikací. Tím se web restartuje. Chcete pokračovat?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Klepnutím na tlačítko **Ano** aplikujte změny a počkejte na dokončení procesu.

Web byl nyní upgradován a je připraven k použití.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Upgrade ladicího programu snímků pomocí sady SDK/Nuget

Pokud aplikace používá verzi `Microsoft.ApplicationInsights.SnapshotCollector` pod verzí 1.3.1, bude nutné ji upgradovat na [novější verzi,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) aby bylo možné pokračovat v práci.
