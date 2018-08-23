---
title: Pomocí sady Visual Studio Azure Průvodce publikováním aplikace | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat různá nastavení v Visual Studio Průvodci publikováním aplikace Azure
services: visual-studio-online
author: ghogen
manager: douge
assetId: 7d8f1ac9-e439-47e0-a183-0642c4ea1920
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 97d78bd61f7cf2a651fea1ac29e2a952a8f8ced3
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055456"
---
# <a name="using-the-visual-studio-publish-azure-application-wizard"></a>Pomocí sady Visual Studio Azure Průvodce publikováním aplikace

Poté, co při vývoji webové aplikace v sadě Visual Studio, můžete publikovat tuto aplikaci do cloudové služby Azure s použitím **publikování aplikaci Azure** průvodce.

> [!Note]
> Tento článek je o nasazení ke cloudovým službám, ne k webovým stránkám. Informace o nasazení do webové stránky najdete v tématu [postup nasazení na webu Azure](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).

## <a name="accessing-the-publish-azure-application-wizard"></a>Přístup k Průvodci publikování aplikaci Azure

Průvodce publikování aplikaci Azure v závislosti na typu projektu sady Visual Studio, ke kterým máte dvě možnosti, jak můžete přistupovat.

**Pokud máte projektu cloudové služby Azure:**

1. Vytvořte nebo otevřete v sadě Visual Studio projekt cloudové služby Azure.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a v místní nabídce vyberte **publikovat**.

**Pokud máte projekt webové aplikace, který není povolen pro Azure:**

1. Vytvořte nebo otevřete v sadě Visual Studio projekt cloudové služby Azure.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a v místní nabídce vyberte **převést** > **převést na projekt cloudové služby Azure**. 

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na nově vytvořený projekt Azure a v místní nabídce vyberte **publikovat**.

## <a name="sign-in-page"></a>Přihlašovací stránka

![Přihlašovací stránka](./media/vs-azure-tools-publish-azure-application-wizard/sign-in.png)

**Účet** – vyberte účet nebo vyberte **přidat účet** v rozevíracím seznamu účtů.

**Vaše předplatné** – zvolte předplatné, které chcete použít pro vaše nasazení.

## <a name="settings-page---common-settings-tab"></a>Stránka nastavení – karta obecná nastavení

![Společná nastavení](./media/vs-azure-tools-publish-azure-application-wizard/settings-common-settings.png)

**Cloudová služba** – pomocí rozevíracího seznamu vyberte existující cloud service, nebo vyberte  **&lt;vytvořit nový >** a vytvořit cloudovou službu. Datové centrum zobrazuje v závorkách pro každou cloudovou službu. Doporučuje se, že data center umístění pro cloudovou službu být stejné jako umístění centra dat pro účet úložiště (rozšířené nastavení).

**Prostředí** – vyberte buď **produkční** nebo **pracovní**. Zvolte testovací prostředí, pokud chcete nasadit aplikaci v testovacím prostředí. 

**Konfigurace sestavení** – vyberte buď **ladění** nebo **vydání**.

**Konfigurace služby** – vyberte buď **cloudu** nebo **místní**.

**Povolení vzdálené plochy pro všechny role** – tuto možnost vyberte, pokud chcete, aby mohli vzdáleně připojit ke službě. Tato možnost slouží především pro řešení potíží. Další informace najdete v tématu [povolit připojení ke vzdálené ploše pro roli v cloudových službách Azure pomocí sady Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

**Povolit nasazení webu pro všechny webové role** – vyberte tuto možnost, chcete-li povolit nasazení webové služby. Je také nutné vybrat **povolit vzdálenou plochu pro všechny role** možnost tuto funkci používat. Další informace najdete v tématu [publikování cloudové služby pomocí sady Visual Studio](vs-azure-tools-publishing-a-cloud-service.md).

## <a name="settings-page---advanced-settings-tab"></a>Stránka nastavení – karta Upřesnit nastavení

![Upřesnit nastavení](./media/vs-azure-tools-publish-azure-application-wizard/settings-advanced-settings.png)

**Popisek nasazení** – buď přijměte výchozí název nebo zadejte název podle vašeho výběru. Chcete-li připojí data k označení nasazení, ponechte políčko zaškrtnuté. 

**Účet úložiště** – vyberte účet úložiště pro toto nasazení **&lt;vytvořit nový > vytvořit účet úložiště. Datové centrum zobrazuje v závorkách pro každý účet úložiště. Doporučuje se, že pro účet úložiště s umístěním datového centra je stejné jako umístění pro System center data pro cloudovou službu (nastavení).

Účet služby Azure storage ukládá balíček pro nasazení aplikace. Po nasazení aplikace je balíček odebrat z účtu úložiště.

**Odstranit nasazení při selhání** – tuto možnost použijte k nasazení odstranit, pokud při publikování došlo k chybám. To mělo být zaškrtnuté políčko, pokud chcete udržovat konstantní virtuální IP adresu pro cloudovou službu.

**Aktualizace nasazení** – tuto možnost vyberte, pokud chcete nasadit pouze aktualizované součásti. Tento typ nasazení může být rychlejší než úplné nasazení. To by měly být porovnány, pokud chcete udržovat konstantní virtuální IP adresu pro cloudovou službu. 

**Nasazení aktualizace - nastavení** -tento dialog umožňuje dále určit, jak chcete, aby role, které chcete aktualizovat. Pokud se rozhodnete **přírůstkové aktualizace**, každou instanci vaší aplikace se aktualizuje jednu po druhé, tak, aby aplikace byla vždy dostupná. Pokud se rozhodnete **Souběžná aktualizace**, všechny instance aplikace jsou aktualizovány ve stejnou dobu. Aktualizuje se současně je rychlejší, ale vaše služba nemusí být k dispozici během procesu aktualizace.

![Nastavení nasazení](./media/vs-azure-tools-publish-azure-application-wizard/deployment-settings.png)

**Povolení technologie IntelliTrace** – určete, zda chcete povolit IntelliTrace. Pomocí nástroje IntelliTrace můžete protokolovat podrobné ladicí informace pro instanci role při spuštění v Azure. Pokud je potřeba najít příčinu problému, můžete protokoly IntelliTrace ke krokování kódu ze sady Visual Studio, jako kdyby byly spuštěné v Azure. Další informace o používání nástroje IntelliTrace naleznete v tématu [ladění publikované Azure cloudové služby pomocí IntelliTrace a sady Visual Studio](./vs-azure-tools-intellitrace-debug-published-cloud-services.md).

**Povolit profilaci** – určete, zda chcete povolit profilaci výkonu. Profiler sady Visual Studio umožňuje získat podrobné analýzy výpočetní aspektů vykonávání cloudové služby. Další informace o používání profileru sady Visual Studio najdete v tématu [testování výkonu cloudové služby Azure](./vs-azure-tools-performance-profiling-cloud-services.md).

**Povolit vzdálený ladicí program pro všechny role** – určete, zda chcete povolit vzdálené ladění. Další informace o ladění cloudové služby pomocí sady Visual Studio najdete v tématu [ladění Azure cloudové služby nebo virtuálního počítače v sadě Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md).

## <a name="diagnostics-settings-page"></a>Stránka nastavení diagnostiky

![Nastavení diagnostiky](./media/vs-azure-tools-publish-azure-application-wizard/diagnostic-settings.png)

Diagnostika umožňuje řešení cloudové služby Azure (nebo virtuální počítač Azure). Informace o diagnostice najdete v tématu [konfiguraci diagnostiky pro Azure Cloud Services a Virtual Machines](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). Informace o Application Insights najdete v tématu [co je Application Insights?](./application-insights/app-insights-overview.md).

## <a name="summary-page"></a>Stránka souhrnu

![Souhrn](./media/vs-azure-tools-publish-azure-application-wizard/summary.png)

**Cílový profil** – můžete vytvořit profil publikování z nastavení, které jste zvolili. Například může vytvořit jeden profil pro testovací prostředí a druhý k produkci. Chcete-li tento profil uložit, zvolte **Uložit** ikonu. Průvodce vytvoří profil a uloží ho do projektu sady Visual Studio. Chcete-li změnit název profilu, otevřete **cílový profil** seznamu a klikněte na tlačítko  **&lt;spravovat... &gt;**.

   > [!Note]
   > Profil publikování se zobrazí v Průzkumníku řešení v sadě Visual Studio a nastavení profilu se zapisují do souboru s příponou .azurePubxml. Nastavení se ukládají jako atributy značky XML.

## <a name="publishing-your-application"></a>Publikování aplikace

Jakmile nakonfigurujete všechna nastavení pro váš projekt nasazení, vyberte **publikovat** v dolní části dialogového okna. Můžete sledovat stav procesu v **výstup** okna v sadě Visual Studio.

## <a name="next-steps"></a>Další postup

- [Migrace a publikovat webovou aplikaci do cloudové služby Azure ze sady Visual Studio](./vs-azure-tools-migrate-publish-web-app-to-cloud-service.md)

- [Zjistěte, jak publikovat cloudovou službu Azure pomocí sady Visual Studio](./vs-azure-tools-publishing-a-cloud-service.md)

- [Ladění publikované Azure cloudové služby pomocí IntelliTrace a sady Visual Studio](./vs-azure-tools-intellitrace-debug-published-cloud-services.md)

- [Testování výkonu cloudové služby Azure](./vs-azure-tools-performance-profiling-cloud-services.md)

- [Konfiguruje se Diagnostika pro Azure Cloud Services a Virtual Machines](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

- [Co je Application Insights?](./application-insights/app-insights-overview.md)