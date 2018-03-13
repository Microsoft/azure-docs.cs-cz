---
title: "Ladění služby Azure cloudové služby nebo virtuálního počítače v sadě Visual Studio | Microsoft Docs"
description: "Ladění cloudové služby nebo virtuálního počítače v sadě Visual Studio"
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: ghogen
editor: 
ms.assetid: 945e06e0-2100-41af-b218-72347367ddab
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: 32f8ba565904682b3bcb9860c641bac18705ad5f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="debugging-an-azure-cloud-service-or-virtual-machine-in-visual-studio"></a>Ladění služby Azure cloudové služby nebo virtuálního počítače v sadě Visual Studio

Visual Studio poskytuje různé možnosti pro ladění cloudových služeb Azure a virtuálních počítačů.

## <a name="debug-your-cloud-service-on-your-local-computer"></a>Ladění cloudové služby na místním počítači

Můžete ušetřit čas a peníze pomocí Azure výpočetní emulátor k ladění cloudové služby v místním počítači. Ladění služby místně, před nasazením, můžete zlepšit spolehlivost a výkon bez placení pro dobu výpočtů. Ale některé může dojít k chybám pouze při spuštění cloudové služby v Azure sám sebe. Tyto chyby můžete ladit, pokud povolíte vzdálené ladění při publikování služby a potom připojí ladicí program na instanci role.

Emulátor simuluje službu Azure Compute a běží ve vašem místním prostředí, aby mohli testování a ladění cloudové služby, před nasazením. Emulátor zpracovává životní cyklus instance role a poskytuje přístup k simulované prostředky, například místní úložiště. Při ladění a spouštění služby ze sady Visual Studio, automaticky spustí se emulátor jako pozadí aplikace a pak nasadí do emulátoru služby. Emulátor serveru můžete použít k zobrazení služby, když je spuštěna v místním prostředí. Můžete spustit na plnou verzi nebo expresní verze emulátoru. (Počínaje Azure 2.3, expresní verze emulátoru je výchozí.) V tématu [pomocí expresní emulátor ke spouštění a ladění cloudové služby místně](vs-azure-tools-emulator-express-debug-run.md).

### <a name="to-debug-your-cloud-service-on-your-local-computer"></a>Chcete-li ladit cloudové služby na místním počítači

1. Na řádku nabídek zvolte **ladění**, **spustit ladění** ke spuštění projektu Azure cloud service. Jako alternativu můžete stisknutím klávesy F5. Zobrazí zprávu, která se spouští výpočetní emulátor. Když se spustí v emulátoru, potvrdí ikonu na hlavním panelu ji.

    ![Azure emulátoru na hlavním panelu](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)

2. Zobrazit uživatelské rozhraní pro emulátoru služby výpočty v otevřením místní nabídky Azure ikony v oznamovací oblasti a pak vyberte **zobrazit uživatelské prostředí emulátoru výpočtů**.

    V levém podokně rozhraní ukazuje služby, které jsou aktuálně nasazené na emulátoru služby výpočty v a instance rolí, které běží jednotlivých služeb. Můžete vybrat služby nebo role zobrazíte životního cyklu, protokolování a diagnostické informace v pravém podokně. Pokud v horním okrajem zahrnuté okna umístili fokus, rozšíří se k vyplnění v pravém podokně.

3. Krok prostřednictvím aplikace výběrem příkazy na **ladění** nabídce a nastavení zarážek v kódu. Aktuální stav aplikace aktualizují krocích aplikace v ladicím programu podokna. Když zastavíte ladění, nasazení aplikace, které se odstraní. Pokud vaše aplikace obsahuje webovou roli a nastavíte vlastnost spuštění akce spustit webový prohlížeč, Visual Studio spustí webovou aplikaci v prohlížeči. Pokud změníte počet instancí role v konfiguraci služby, musíte zastavit cloudovou službu a poté znovu spusťte ladění tak, že ladíte tyto nové instance role.

    **Poznámka:** při zastavení spuštěna nebo k ladění služby nejsou zastavena emulátoru služby výpočty místní a emulátoru úložiště. Explicitně je potřeba zastavit z oznamovací oblasti.

## <a name="debug-a-cloud-service-in-azure"></a>Ladění cloudové služby v Azure

K ladění Cloudová služba ze vzdáleného počítače, je nutné povolit tuto funkci explicitně při nasazení cloudové služby tak, aby požadované na virtuálních počítačích, které spustit instance role jsou nainstalovány služby (například msvsmon.exe). Pokud povolíte nepomohly, vzdálené ladění při publikování služby, budete muset znovu publikovat služby s povoleným laděním vzdálené.

Pokud povolíte vzdálené ladění pro cloudovou službu, ho nebude vykazovat snížení výkonu nebo toho vám být účtovány další poplatky. Nepoužívejte vzdáleného ladění na produkční služby, protože klienti, kteří používají službu může být nepříznivě ovlivněn.

> [!NOTE]
> Když publikujete Cloudová služba ze sady Visual Studio, můžete povolit **IntelliTrace** pro všechny role v této službě, které cílí na rozhraní .NET Framework 4 nebo .NET Framework 4.5. Pomocí **IntelliTrace**, můžete prozkoumat události, které nastaly v instanci role v minulosti a reprodukujte kontext z této doby. V tématu [ladění publikované Cloudová služba se IntelliTrace a Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016) a [IntelliTrace pomocí](https://msdn.microsoft.com/library/dd264915.aspx).

### <a name="to-enable-remote-debugging-for-a-cloud-service"></a>Chcete-li povolit vzdálené ladění pro cloudové služby

1. Otevřete místní nabídky projektu Azure a pak vyberte **publikovat**.

2. Vyberte **pracovní** prostředí a **ladění** konfigurace.

    Toto je pouze platí. Můžete se rozhodnout spustit testovací prostředí v produkčním prostředí. Pokud povolíte vzdáleného ladění na produkční prostředí však může ovlivnit nepříznivě uživatele. Můžete zvolit konfigurace verze, ale konfiguraci ladění umožňuje snazší ladění.

    ![Vyberte konfiguraci ladění](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)

3. Postupujte podle kroků obvyklé, ale vybrat **povolení vzdáleného ladicího programu u všech rolí** políčko **Upřesnit nastavení** kartě.

    ![Konfigurace ladění](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### <a name="to-attach-the-debugger-to-a-cloud-service-in-azure"></a>Chcete-li cloudové služby v Azure připojit ladicí program

1. V Průzkumníku serveru rozbalte uzel vaší cloudové služby.

2. Otevřete místní nabídku pro danou roli nebo instanci role, ke kterému chcete připojit a potom vyberte **připojit ladicí program**.

    Pokud ladíte roli, ladicího programu sady Visual Studio se připojí ke každé instanci této role. Na zarážky pro první instanci role, která běží tohoto řádku kódu a splňuje všechny podmínky této zarážek dojde k přerušení ladicího programu. Pokud ladíte instance, připojí ladicí program pouze pro tuto instanci a zalomení na zarážku jenom v případě, že tuto konkrétní instanci běží tohoto řádku kódu a splňuje podmínky se zarážka.

    ![Připojit ladicí program](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)

3. Po ladicí program připojí k instanci, ladění jako obvykle. Ladicí program se automaticky připojí k příslušné hostitelský proces pro vaši roli. V závislosti na tom, co je role připojí ladicí program w3wp.exe, WaWorkerHost.exe nebo WaIISHost.exe. Pokud chcete ověřit procesu, ke kterému je připojen ladicí program, rozbalte uzel instance v Průzkumníku serveru. V tématu [architektura Role Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) Další informace o Azure procesy.

    ![Vyberte typ kód – dialogové okno](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

4. K identifikaci procesů, ke kterým je připojen ladicí program, otevřete procesy dialogové okno, v nabídce panelu Výběr ladění, Windows, procesy. (Klávesové: Ctrl + Alt + Z) Chcete-li odpojit konkrétní proces, otevřete jeho místní nabídky a pak vyberte **odpojit proces**. Nebo, najděte uzel instance v Průzkumníku serveru, najít proces, otevřete jeho místní nabídky a pak vyberte **odpojit proces**.

    ![Ladění procesů](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

> [!WARNING]
> Vyhněte se dlouho se zastavuje na zarážky při vzdáleném ladění. Azure zpracovává proces, který je zastavena po dobu delší než několik minut jako reagovat a zastaví odesílání provozu do této instance. Pokud zastavíte příliš dlouho, msvsmon.exe odpojí z procesu.

Odpojit ladicí program z všechny procesy ve vaší instanci nebo role, otevřete místní nabídku pro danou roli nebo instanci, kterou ladíte a potom vyberte **odpojit ladicí program**.

## <a name="limitations-of-remote-debugging-in-azure"></a>Omezení pro vzdálené ladění v Azure

Z Azure SDK 2.3 vzdálené ladění má následující omezení:

* S vzdálené ladění povoleno, nelze publikovat Cloudová služba, ve kterém žádné role, má víc než 25 instancí.
* Ladicí program používá porty 30400 k 30424, 31400 k 31424 a 32400 k 32424. Pokud se pokusíte použít některou z těchto portů, nebudete moci publikovat službu a jeden z následujících chybových zpráv se objeví v protokolu aktivit pro Azure:

  * Chyba při ověřování souboru .cscfg proti souboru .csdef.
    Vyhrazené rozsahu 'rozsah portů' pro koncový bod Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector role "role" se překrývá s již definovaný port nebo rozsah.
  * Přidělení se nezdařilo. Opakujte akci později, zkuste zmenšit velikost virtuálního počítače nebo počet instancí role nebo vyzkoušejte nasazení v jiné oblasti.

## <a name="debugging-azure-virtual-machines"></a>Ladění virtuální počítače Azure

Můžete ladit programy, které běží na virtuálních počítačích Azure s použitím Průzkumníka serveru v sadě Visual Studio. Když povolíte vzdálené ladění na virtuální počítač Azure, Azure nainstaluje vzdáleného ladění rozšíření ve virtuálním počítači. Pak můžete přiřadit procesy na virtuálním počítači a ladění běžným způsobem.

> [!NOTE]
> Virtuální počítače vytvořené pomocí Azure resource manager zásobníku můžete vzdáleně ladit pomocí Průzkumníku cloudu ve Visual Studiu 2015. Další informace najdete v tématu [Správa prostředků Azure pomocí Průzkumníku cloudu](http://go.microsoft.com/fwlink/?LinkId=623031).

### <a name="to-debug-an-azure-virtual-machine"></a>Chcete-li ladit virtuální počítač Azure

1. V Průzkumníku serveru rozbalte uzel virtuální počítače a vyberte uzel virtuální počítač, který chcete ladit.

2. Otevřete kontextu nabídku a vyberte **povolit ladění**. Když se zobrazí dotaz, pokud jste si jisti, zda chcete povolit ladění na virtuálním počítači, vyberte **Ano**.

    Azure nainstaluje vzdáleného ladění rozšíření ve virtuálním počítači, který chcete povolit ladění.

    ![Virtuální počítač povolit ladění příkaz](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Protokol činnosti Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

3. Po dokončení instalace vzdáleného ladění rozšíření otevřete kontextu nabídku virtuálního počítače a vyberte možnost **připojit ladicí program...**

    Azure získá seznam procesů, které na virtuálním počítači a zobrazí je v připojit k procesu – dialogové okno.

    ![Připojit ladicí program příkaz](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

4. V **připojit k procesu** dialogové okno, vyberte **vyberte** k omezení seznamu výsledky a zobrazit pouze typy kódu, kterou chcete ladit. Můžete ladit 32bitovou nebo 64bitovou spravovaného kódu, nativního kódu nebo obojí.

    ![Vyberte typ kód – dialogové okno](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

5. Vyberte procesů, které chcete ladit na virtuálním počítači a potom vyberte **Attach**. Například můžete zvolit procesu w3wp.exe, pokud chcete ladit webové aplikace na virtuálním počítači. V tématu [ladění jeden nebo více procesy v sadě Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) a [architektura Role Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) Další informace.

## <a name="create-a-web-project-and-a-virtual-machine-for-debugging"></a>Vytvoření webového projektu a virtuální počítač pro ladění

Před publikováním projektu Azure, které může být užitečné k testování v prostředí s omezením podporující ladění a testování scénáře a kde můžete nainstalovat testování a monitorování programy. Jeden způsob, jak tyto testy je vzdáleně ladění aplikace s virtuálním počítačem.

Projekty Visual Studio ASP.NET nabízejí možnost pro vytvoření užitečný virtuální počítač, který můžete použít pro testování aplikace. Virtuální počítač obsahuje běžně potřebné koncové body, jako jsou prostředí PowerShell, vzdálené plochy a WebDeploy.

### <a name="to-create-a-web-project-and-a-virtual-machine-for-debugging"></a>K vytvoření webového projektu a virtuální počítač pro ladění

1. V sadě Visual Studio vytvořte novou webovou aplikaci ASP.NET.

2. V dialogovém okně Nový projekt ASP.NET, v části Azure vyberte **virtuálního počítače** v rozevíracím seznamu. Ponechte **vytvořit vzdálené prostředky** zaškrtnuté políčko. Vyberte **OK** pokračovat.

    **Vytvořit virtuální počítač na platformě Azure** zobrazí se dialogové okno.

    ![ASP.NET webového projektu dialogové okno vytvořit](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **Poznámka:** budete vyzváni k přihlášení k účtu Azure, pokud jste již přihlášeni.

3. Vyberte různá nastavení pro virtuální počítač a pak vyberte **OK**. V tématu [virtuální počítače](http://go.microsoft.com/fwlink/?LinkId=623033) Další informace.

    Název, který zadáte pro název DNS bude název virtuálního počítače.

    ![Vytvoření virtuálního počítače na Azure dialogové okno](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    Azure vytvoří virtuální počítač a pak poskytuje a konfiguruje koncových bodů, jako je vzdálené plochy a nasazení webu

4. Po nakonfigurování virtuálního počítače v Průzkumníku serveru vyberte uzlu virtuálního počítače.

5. Otevřete kontextu nabídku a vyberte **povolit ladění**. Když se zobrazí dotaz, pokud jste si jisti, zda chcete povolit ladění na virtuálním počítači, vyberte **Ano**.

    Azure nainstaluje vzdáleného ladění rozšíření pro virtuální počítač, který chcete povolit ladění.

    ![Virtuální počítač povolit ladění příkaz](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Protokol činnosti Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

6. Publikování projektu, jak je uvedeno v [postupy: nasazení webového projektu pomocí publikování jedním kliknutím v sadě Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx). Vzhledem k tomu, že chcete ladit na virtuálním počítači, na **nastavení** stránky **Publikovat Web** průvodci vyberte **ladění** jako konfiguraci. Tím je zajištěno, že symboly kódu jsou k dispozici při ladění.

    ![Nastavení publikování](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)

7. V **možností publikování souboru**, vyberte **odebrat další soubory v cílovém umístění** Pokud projekt již byl nasazen na dřívější čas.

8. Když na projekt publikuje v místní nabídce virtuálního počítače v Průzkumníku serveru, vyberte **připojit ladicí program...**

    Azure získá seznam procesů, které na virtuálním počítači a zobrazí je v připojit k procesu – dialogové okno.

    ![Připojit ladicí program příkaz](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

9. V **připojit k procesu** dialogové okno, vyberte **vyberte** k omezení seznamu výsledky a zobrazit pouze typy kódu, kterou chcete ladit. Můžete ladit 32bitovou nebo 64bitovou spravovaného kódu, nativního kódu nebo obojí.

    ![Vyberte typ kód – dialogové okno](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

10. Vyberte procesů, které chcete ladit na virtuálním počítači a potom vyberte **Attach**. Například můžete zvolit procesu w3wp.exe, pokud chcete ladit webové aplikace na virtuálním počítači. V tématu [ladění jeden nebo více procesy v sadě Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) Další informace.

## <a name="next-steps"></a>Další postup

* Použití **Intellitrace** ke shromažďování protokolů událostí a volání ze serveru verze. V tématu [ladění publikované Cloudová služba se IntelliTrace a Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016).

* Použití **Azure Diagnostics** protokolovat podrobné informace o spouštění kódu v rámci role, zda role běží ve vývojovém prostředí nebo v Azure. V tématu [shromažďování dat protokolování pomocí Azure Diagnostics](http://go.microsoft.com/fwlink/p/?LinkId=400450).
