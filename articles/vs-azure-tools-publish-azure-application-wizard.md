---
title: Pomocí sady Visual Studio aplikaci Azure Průvodce publikováním | Microsoft Docs
description: Zjistěte, jak konfigurovat nastavení na různé Visual Studio Průvodci publikováním aplikace Azure
services: visual-studio-online
author: ghogen
manager: douge
assetId: 7d8f1ac9-e439-47e0-a183-0642c4ea1920
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 02d38f49a1bfe490acbcfee95a8a703cf0d7594f
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "31795975"
---
# <a name="using-the-visual-studio-publish-azure-application-wizard"></a>Pomocí sady Visual Studio aplikaci Azure Průvodce publikováním

Až budete vyvíjet webové aplikace v sadě Visual Studio, můžete publikovat tuto aplikaci do cloudové služby Azure pomocí **publikování aplikaci Azure** průvodce.

> [!Note]
> Tento článek je o nasazení do cloudové služby, není pro webové servery. Informace o nasazení do webové stránky najdete v tématu [jak nasadit webovou stránku Azure](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).

## <a name="accessing-the-publish-azure-application-wizard"></a>Přístup k Průvodci publikování aplikaci Azure

Můžete přistupovat Průvodce publikování aplikaci Azure dvěma způsoby v závislosti na typu projektu sady Visual Studio, které máte.

**Pokud máte projekt Azure cloud service:**

1. Vytvořit nebo otevřít projekt Azure cloud service v sadě Visual Studio.

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a v místní nabídce vyberte **publikovat**.

**Pokud máte projekt webové aplikace, který není povolen pro Azure:**

1. Vytvořit nebo otevřít projekt Azure cloud service v sadě Visual Studio.

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na projekt a v místní nabídce vyberte **převést** > **převést na projekt cloudové služby Azure**. 

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na nově vytvořený projekt Azure a v místní nabídce vyberte **publikovat**.

## <a name="sign-in-page"></a>Přihlašovací stránka

![Přihlašovací stránka](./media/vs-azure-tools-publish-azure-application-wizard/sign-in.png)

**Účet** – vyberte účet, nebo vyberte **přidat účet** v rozevíracím seznamu účtu.

**Zvolte předplatné** – zvolte předplatné má použít pro vaše nasazení.

## <a name="settings-page---common-settings-tab"></a>Nastavení stránky – karta společné nastavení

![Společná nastavení](./media/vs-azure-tools-publish-azure-application-wizard/settings-common-settings.png)

**Cloudová služba** -pomocí rozevíracího seznamu, vyberte buď existující cloudové služby, nebo vybrat možnost  **&lt;vytvořit nový >** a vytvořit cloudovou službu. Datové centrum zobrazí v závorkách u každé cloudové služby. Doporučuje se, že data center umístění pro cloudové služby být stejné jako umístění center dat pro účet úložiště (Upřesnit nastavení).

**Prostředí** -vyberte buď **produkční** nebo **pracovní**. Vyberte pracovní prostředí, pokud chcete nasadit aplikaci v testovacím prostředí. 

**Konfigurace sestavení** -vyberte buď **ladění** nebo **verze**.

**Konfigurace služby** -vyberte buď **cloudu** nebo **místní**.

**Povolení vzdálené plochy pro všechny role** – tuto možnost vyberte, pokud chcete, abyste mohli vzdáleně připojit ke službě. Tato možnost slouží především pro řešení potíží. Další informace najdete v tématu [povolit připojení ke vzdálené ploše pro roli ve službě Azure Cloud Services pomocí sady Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

**Povolit nasazení webu pro všechny webové role** – vyberte tuto možnost, chcete-li povolit nasazení webu pro službu. Je nutné vybrat také **povolení vzdálené plochy u všech rolí** možnost k použití této funkce. Další informace najdete v tématu [publikování cloudové služby pomocí sady Visual Studio](vs-azure-tools-publishing-a-cloud-service.md).

## <a name="settings-page---advanced-settings-tab"></a>Nastavení stránky – karta Upřesnit nastavení

![Upřesňující nastavení](./media/vs-azure-tools-publish-azure-application-wizard/settings-advanced-settings.png)

**Označení nasazení** -přijměte výchozí název, nebo zadejte název vašeho výběru. Datum připojit k označení nasazení, nechte zaškrtnuté políčko. 

**Účet úložiště** – vyberte účet úložiště, který chcete použít pro toto nasazení **&lt;vytvořit nový > vytvořit účet úložiště. Datové centrum zobrazí v závorkách pro každý účet úložiště. Doporučuje se, že umístění center dat pro účet úložiště je stejný jako umístění center dat pro cloudové služby (obecná nastavení).

Účet úložiště Azure ukládá balíček pro nasazení aplikace. Po nasazení aplikace je balíček odebrat z účtu úložiště.

**Odstranění nasazení na selhání** – vyberte tuto možnost nasazení, odstranit, pokud došlo k chybám při publikování. To mělo nezaškrtnuté, pokud chcete zachovat konstantní virtuální IP adresy pro cloudové služby.

**Aktualizace nasazení** – tuto možnost vyberte, pokud chcete nasadit pouze aktualizované součásti. Tento typ nasazení může být rychlejší než úplné nasazení. To je třeba kontrolovat, pokud chcete zachovat konstantní virtuální IP adresy pro cloudové služby. 

**Nasazení aktualizace - nastavení** -toto dialogové okno se používá k dále určit, jakým způsobem chcete role aktualizovat. Pokud se rozhodnete **přírůstkové aktualizace**, každou instanci vaší aplikace se aktualizuje jedna po druhé, tak, aby aplikace je vždy k dispozici. Pokud se rozhodnete **Souběžná aktualizace**, jsou aktualizovány všechny instance aplikace ve stejnou dobu. Souběžné aktualizace je rychlejší, ale vaše služba nemusí být k dispozici během procesu aktualizace.

![Nastavení nasazení](./media/vs-azure-tools-publish-azure-application-wizard/deployment-settings.png)

**Povolit IntelliTrace** -zadat, pokud chcete povolit IntelliTrace. S použitím technologie IntelliTrace můžete protokolovat rozsáhlé ladicí informace pro instanci role při spuštění v Azure. Pokud potřebujete najít příčinu problému, můžete protokoly IntelliTrace krokovat kód ze sady Visual Studio, jako kdyby byly spuštěny v Azure. Další informace o používání IntelliTrace najdete v tématu [ladění publikovaný Azure cloud service sadou Visual Studio a IntelliTrace](./vs-azure-tools-intellitrace-debug-published-cloud-services.md).

**Povolit profilace** -zadat, pokud chcete povolit profilace výkonu. Visual Studio profiler umožňuje získat podrobný analýzu výpočetní aspektů jak cloudové služby běží. Další informace o použití sady Visual Studio profiler najdete v tématu [testování výkonu Azure cloud service](./vs-azure-tools-performance-profiling-cloud-services.md).

**Povolení vzdáleného ladicího programu u všech rolí** -zadat, pokud chcete povolit vzdálené ladění. Další informace o ladění cloudové služby pomocí sady Visual Studio najdete v tématu [ladění Azure cloudové služby nebo virtuálního počítače v sadě Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md).

## <a name="diagnostics-settings-page"></a>Stránka nastavení diagnostiky

![Nastavení diagnostiky](./media/vs-azure-tools-publish-azure-application-wizard/diagnostic-settings.png)

Diagnostika umožňuje řešení cloudové služby Azure (nebo virtuální počítač Azure). Informace o diagnostiky najdete v tématu [konfigurace diagnostiky pro Azure Cloud Services a virtuální počítače](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). Informace o Application Insights najdete v tématu [co je Application Insights?](./application-insights/app-insights-overview.md).

## <a name="summary-page"></a>Stránka souhrnu

![Shrnutí](./media/vs-azure-tools-publish-azure-application-wizard/summary.png)

**Cíl profil** – můžete vytvořit profil publikování z nastavení, které jste zvolili. Například může vytvořit jeden profil pro testovací prostředí a druhý k produkci. Pokud chcete uložit tento profil, vyberte **Uložit** ikonu. Průvodce vytvoří profil a uloží ho do projektu Visual Studia. Chcete-li upravit název profilu, otevřete **cíle profil** seznamu a potom vyberte  **&lt;spravovat... &gt;**.

   > [!Note]
   > Profil publikování se zobrazí v Průzkumníku řešení v sadě Visual Studio a nastavení profilu se zapisují do souboru s příponou .azurePubxml. Nastavení se ukládají jako atributy značek XML.

## <a name="publishing-your-application"></a>Publikování aplikace

Jakmile nakonfigurujete všechna nastavení pro váš projekt nasazení, vyberte **publikovat** v dolní části dialogového okna. Můžete sledovat stav procesu v **výstup** oken v sadě Visual Studio.

## <a name="next-steps"></a>Další postup

- [Migrace a publikovat webovou aplikaci pro cloudové služby Azure ze sady Visual Studio](./vs-azure-tools-migrate-publish-web-app-to-cloud-service.md)

- [Další informace o použití sady Visual Studio k publikování cloudové služby Azure](./vs-azure-tools-publishing-a-cloud-service.md)

- [Ladění publikovaný Azure cloud service sadou Visual Studio a IntelliTrace](./vs-azure-tools-intellitrace-debug-published-cloud-services.md)

- [Testování výkonu Azure cloud service](./vs-azure-tools-performance-profiling-cloud-services.md)

- [Konfigurace diagnostiky pro cloudové služby Azure a virtuálních počítačů](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

- [Co je Application Insights?](./application-insights/app-insights-overview.md)