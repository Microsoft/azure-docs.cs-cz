---
title: Ladění Azure cloudové služby nebo virtuálního počítače v sadě Visual Studio | Dokumentace Microsoftu
description: Ladění cloudové služby nebo virtuálního počítače v sadě Visual Studio
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: douge
editor: ''
ms.assetid: 945e06e0-2100-41af-b218-72347367ddab
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: af961533648ce3a939c25fca32173684255a52a8
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444686"
---
# <a name="debugging-an-azure-cloud-service-or-virtual-machine-in-visual-studio"></a>Ladění Azure cloudové služby nebo virtuálního počítače v sadě Visual Studio

Visual Studio nabízí různé možnosti pro ladění Azure cloud services a virtual machines.

## <a name="debug-your-cloud-service-on-your-local-computer"></a>Ladění cloudové služby na místním počítači

Můžete ušetřit čas a peníze pomocí webu Azure compute emulátoru k ladění cloudové služby na místním počítači. Ladění služby místně, než ho nasadíte, můžete zlepšit výkon a spolehlivost bez nutnosti platit za výpočetní čas. Nicméně některé může dojít k chybám pouze při spuštění cloudové služby v Azure samotný. Tyto chyby můžete ladit, pokud povolíte vzdálené ladění, při publikování služby a potom připojit ladicí modul k instanci role.

Emulátor služby Azure Compute simuluje a běží v místním prostředí, takže můžete testovat a ladit cloudové služby před jejím nasazením. Emulátor zpracovává životního cyklu vaše instance rolí a poskytuje přístup k simulované prostředky, jako jsou místní úložiště. Při ladění nebo spuštění služby ze sady Visual Studio, automaticky spustí se emulátor jako aplikace běžící na pozadí a pak nasadí vaši službu v emulátoru. Chcete-li zobrazit vaši službu při spuštění v místním prostředí můžete použít emulátor. Můžete spustit na plnou verzi nebo verze express emulátoru. (Od verze Azure 2.3, verze express emulátoru je výchozí hodnota.) Zobrazit [pomocí Emulator Express ke spouštění a ladění cloudové služby místně](vs-azure-tools-emulator-express-debug-run.md).

### <a name="to-debug-your-cloud-service-on-your-local-computer"></a>Ladění cloudové služby na místním počítači

1. V panelu nabídky zvolte **ladění**, **spustit ladění** pro spuštění vašeho projektu cloudové služby Azure. Jako alternativu můžete stisknutím klávesy F5. Zobrazí se vám zpráva, která se spouští emulátoru Compute. Po spuštění emulátoru, na ikonu na hlavním panelu systému potvrdí ji.

    ![Emulátor Azure na hlavním panelu systému](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)

2. Zobrazení uživatelského rozhraní pro emulátor služby výpočty tak, že otevřete místní nabídku pro Azure ikonu v oznamovací oblasti a pak vyberte **zobrazit Uživatelském prostředí emulátoru výpočtů**.

    V levém podokně uživatelském rozhraní zobrazeny služby, které jsou aktuálně nasazené na emulátor služby výpočty a instance rolí, které každá služba je spuštěna. Můžete vybrat služby nebo role k zobrazení životní cyklus, protokolování a diagnostické informace v pravém podokně. Je-li zaměřit na horní okraj zahrnuté okna, roztáhne a vyplní v pravém podokně.

3. Krok přes aplikaci tak, že vyberete příkazy na **ladění** nabídky a nastavení zarážek v kódu. Krocích v ladicím programu aplikace se zobrazí aktuální stav aplikace aktualizují podokna. Při zastavení ladění, nasazení aplikace, které se odstraní. Pokud vaše aplikace obsahuje webové role a nastavíte vlastnost po spuštění akce spuštění webového prohlížeče, Visual Studio spustí webovou aplikaci v prohlížeči. Pokud změníte počet instancí role v konfiguraci služby, musíte zastavit cloudovou službu a pak znovu spusťte ladění, takže můžete ladit tyto nové instance role.

    **Poznámka:** při zastavení spuštění nebo ladění služby nejsou zastavena místním výpočetním emulátoru a emulátoru úložiště. Musíte explicitně zastavit z oznamovací oblasti.

## <a name="debug-a-cloud-service-in-azure"></a>Ladění cloudové služby v Azure

Ladění cloudové služby ze vzdáleného počítače, je nutné povolit tuto funkci explicitně při nasazení cloudové služby tak, aby požadované na virtuálních počítačích, na kterých běží vaše instance rolí jsou nainstalovány služby (například msvsmon.exe). Pokud jste nepovolili, vzdálené ladění, při publikování služby, budete muset znovu publikovat službu pomocí vzdáleného ladění povoleno.

Pokud povolíte vzdálené ladění pro cloudovou službu, není je důvodem sníženého výkonu nebo účtovat další poplatky. Nepoužívejte vzdáleného ladění na služby v produkčním prostředí, protože klienti, kteří používají službu může být nepříznivě ovlivněn.

> [!NOTE]
> Když publikujete do cloudového ze sady Visual Studio, můžete povolit **IntelliTrace** pro všechny role v dané služby, které jsou cíleny na rozhraní .NET Framework 4 nebo .NET Framework 4.5. S použitím **IntelliTrace**, můžete zkoumat události, ke kterým došlo v minulosti v instanci role a reprodukovat kontextu v čase. Zobrazit [ladění publikované cloudové služby pomocí nástroje IntelliTrace a sady Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016) a [pomocí IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx).

### <a name="to-enable-remote-debugging-for-a-cloud-service"></a>Jak aktivovat vzdálené ladění pro cloudovou službu

1. Otevřete místní nabídku pro projekt Azure a pak vyberte **publikovat**.

2. Vyberte **pracovní** prostředí a **ladění** konfigurace.

    Toto je jenom. Můžete se rozhodnout spustit testovací prostředí v produkčním prostředí. Pokud povolíte vzdálené ladění v produkčním prostředí ale může ovlivnit nepříznivě uživatele. Můžete vybrat konfiguraci vydané verze, ale konfiguraci ladění, umožňuje ladění snazší.

    ![Vyberte konfiguraci ladění](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)

3. Obvyklým postupem ale vybrat **povolit vzdálený ladicí program pro všechny role** zaškrtávací políčko na **Upřesnit nastavení** kartu.

    ![Konfigurace ladění](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### <a name="to-attach-the-debugger-to-a-cloud-service-in-azure"></a>Připojení ladicího programu do cloudové služby v Azure

1. V Průzkumníku serveru rozbalte uzel pro cloudovou službu.

2. Otevřete místní nabídku pro danou roli nebo instanci role, ke kterému chcete připojit a pak vyberte **připojit ladicí program**.

    Pokud ladíte roli, ladicí program sady Visual Studio připojí k každou instanci této role. Ladicí program přeruší na zarážku pro první instanci role, který spouští tento řádek kódu a splňuje všechny podmínky této zarážky. Pokud ladíte instance, ladicí program připojí jenom pro tuto instanci a zalomení na zarážku pouze v případě této konkrétní instance spouští tento řádek kódu a splňuje podmínky k zarážce.

    ![Připojit ladicí program](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)

3. Po ladicí program připojí k instanci, ladění obvyklým způsobem. Ladicí program automaticky připojí k procesu vhodného hostitele pro vaši roli. V závislosti na tom, co je role ladicí program připojí k w3wp.exe, WaWorkerHost.exe nebo WaIISHost.exe. Pokud chcete ověřit proces, ke kterému je připojen ladicí program, rozbalte uzel instance v Průzkumníku serveru. Zobrazit [infrastruktura rolí Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) Další informace o Azure zpracují.

    ![Vyberte typ kódu – dialogové okno](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

4. Pokud chcete identifikovat procesy, ke kterým je připojen ladicí program, otevřete procesy dialogové okno, v řádku nabídek, výběrem ladění, Windows, procesy. (Klávesnice: Ctrl + Alt + Z) Chcete-li odpojit konkrétní proces, otevřete místní nabídku a vyberte **odpojení procesu**. Nebo, vyhledejte uzel instance v Průzkumníku serveru, vyhledejte proces, otevřete místní nabídku a vyberte **odpojení procesu**.

    ![Ladění procesů](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

> [!WARNING]
> Vyhněte se dlouho zastaví na zarážkách při vzdáleném ladění. Azure považuje proces, který se zastaví po dobu delší než několik minut, než jako reagovat a zastaví odesílání provozu do této instance. Chcete-li zrušit příliš dlouho, odpojí se od procesu msvsmon.exe.

Chcete-li odpojit ladicí program od všech procesů v instanci nebo roli, otevřete místní nabídku pro danou roli nebo instanci, kterou ladíte a pak vyberte **odpojit ladicí program**.

## <a name="limitations-of-remote-debugging-in-azure"></a>Omezení pro vzdálené ladění v Azure

Z Azure SDK 2.3 vzdálené ladění má následující omezení:

* Vzdálené ladění povoleno, nelze publikovat cloudovou službu, ve kterém všechny role má více než 25 instancí.
* Ladicí program používá porty 30400 k 30424, 31400 k 31424 a 32400 k 32424. Pokud se pokusíte použít některou z těchto portů, nebude možné publikovat službu a jednu z následujících chybových zpráv se zobrazí v protokolu aktivit Azure:

  * Chyba při ověřování souboru .cscfg proti souboru .csdef.
    Vyhrazený port rozsahu "rozsah" pro koncový bod Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector role "role" se překrývá s už definovaný port nebo rozsah.
  * Přidělení selhalo. Zkuste to znovu později, zkuste zmenšit velikost virtuálního počítače nebo počet instancí role nebo vyzkoušejte nasazení v jiné oblasti.

## <a name="debugging-azure-virtual-machines"></a>Ladění virtuálních počítačů Azure

Můžete ladit programy, které běží na virtuálních počítačích Azure s použitím Průzkumníka serveru v sadě Visual Studio. Když povolíte vzdálené ladění na virtuálním počítači Azure, Azure nainstaluje vzdáleného ladění rozšíření na virtuálním počítači. Pak můžete připojit k procesům ve virtuálním počítači a ladit běžným způsobem.

> [!NOTE]
> Virtuálních počítačích vytvořených pomocí zásobníku správce prostředků Azure můžete vzdáleně ladit pomocí Průzkumníku cloudu v sadě Visual Studio 2015. Další informace najdete v tématu [Správa prostředků Azure pomocí Průzkumníka cloudu](http://go.microsoft.com/fwlink/?LinkId=623031).

### <a name="to-debug-an-azure-virtual-machine"></a>Chcete-li ladit virtuálního počítače Azure

1. V Průzkumníku serveru rozbalte uzel virtuální počítače a vyberte uzel virtuální počítač, který chcete ladit.

2. Otevřete kontextovou nabídku a vyberte **povolit ladění**. Když se zobrazí výzva, pokud jste si jisti, zda chcete povolit ladění na virtuálním počítači, vyberte **Ano**.

    Vzdálené ladění rozšíření Azure nainstaluje na virtuálním počítači pro povolení ladění.

    ![Virtuální počítač povolit ladění příkaz](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Protokol aktivit Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

3. Po dokončení instalace vzdáleného ladění rozšíření, otevřete místní nabídku virtuálního počítače a vyberte **připojit ladicí program...**

    Azure načte seznam procesů na virtuálním počítači a zobrazí je v připojit k procesu – dialogové okno.

    ![Připojení příkazu ladicího programu](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

4. V **připojit k procesu** dialogu **vyberte** omezit výsledky seznamu zobrazíte jen typy kódu, který chcete ladit. Můžete ladit 32bitové nebo 64bitové spravovaného kódu a nativního kódu.

    ![Vyberte typ kódu – dialogové okno](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

5. Vyberte procesy, které chcete ladit na virtuálním počítači a potom vyberte **připojit**. Můžete například procesu w3wp.exe kdybys chtěla ladění webových aplikací na virtuálním počítači. Zobrazit [ladění jednoho nebo více procesů v sadě Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) a [infrastruktura rolí Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) Další informace.

## <a name="create-a-web-project-and-a-virtual-machine-for-debugging"></a>Vytvoření webového projektu a virtuálního počítače pro ladění

Před publikováním projektu Azure se vám může být užitečné pro testování v prostředí s omezením, který podporuje ladění a testování scénářů a kde ji můžete nainstalovat, testování a monitorování aplikací. Jedním ze způsobů spuštění těchto testů je vzdáleně ladit aplikace na virtuálním počítači.

Projekty aplikace Visual Studio ASP.NET nabízejí možnost vytvoření virtuálního počítače po ruce, můžete použít pro testování aplikací. Virtuální počítač obsahuje běžně potřebných koncové body, jako je PowerShell, vzdálených ploch a WebDeploy.

### <a name="to-create-a-web-project-and-a-virtual-machine-for-debugging"></a>Vytvoření webového projektu a virtuálního počítače pro ladění

1. V sadě Visual Studio vytvořte novou webovou aplikaci ASP.NET.

2. V dialogovém okně Nový projekt ASP.NET, v části Azure zvolte **virtuální počítač** v rozevíracím seznamu. Nechte **vytvořit vzdálené prostředky** zaškrtnuté políčko. Vyberte **OK** pokračovat.

    **Vytvoření virtuálního počítače v Azure** zobrazí se dialogové okno.

    ![Vytvořit dialogové okno projektu ASP.NET pro web](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **Poznámka:** budete vyzváni k přihlášení ke svému účtu Azure, pokud jste ještě nejste přihlášení.

3. Vyberte různá nastavení pro virtuální počítač a pak vyberte **OK**. Zobrazit [virtuálních počítačů](http://go.microsoft.com/fwlink/?LinkId=623033) Další informace.

    Název, který zadáte pro název DNS bude název virtuálního počítače.

    ![Vytvoření virtuálního počítače v Azure dialogové okno](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    Azure vytvoří virtuální počítač a zřizuje a konfiguruje koncových bodů, jako je vzdálené plochy a nasazení webu

4. Po konfiguraci virtuálního počítače v Průzkumníku serveru vyberte uzlu virtuálního počítače.

5. Otevřete kontextovou nabídku a vyberte **povolit ladění**. Když se zobrazí výzva, pokud jste si jisti, zda chcete povolit ladění na virtuálním počítači, vyberte **Ano**.

    Vzdálené ladění rozšíření Azure nainstaluje do virtuálního počítače pro povolení ladění.

    ![Virtuální počítač povolit ladění příkaz](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Protokol aktivit Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

6. Publikování projektu, jak je uvedeno v [postupy: nasazení webového projektu pomocí publikování jedním kliknutím v sadě Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx). Vzhledem k tomu, který chcete ladit na virtuálním počítači, na **nastavení** stránku **Publikovat Web** průvodce, vyberte **ladění** jako konfiguraci. Tím zajistíte, že kód symboly jsou k dispozici při ladění.

    ![Nastavení publikování](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)

7. V **možností publikování souboru**vyberte **odebrat další soubory v cílovém umístění** Pokud projekt již byl nasazen na dřívější čas.

8. Po dokončení publikuje projektu, v místní nabídce virtuálního počítače v Průzkumníku serveru vyberte **připojit ladicí program...**

    Azure načte seznam procesů na virtuálním počítači a zobrazí je v připojit k procesu – dialogové okno.

    ![Připojení příkazu ladicího programu](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

9. V **připojit k procesu** dialogu **vyberte** omezit výsledky seznamu zobrazíte jen typy kódu, který chcete ladit. Můžete ladit 32bitové nebo 64bitové spravovaného kódu a nativního kódu.

    ![Vyberte typ kódu – dialogové okno](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

10. Vyberte procesy, které chcete ladit na virtuálním počítači a potom vyberte **připojit**. Můžete například procesu w3wp.exe kdybys chtěla ladění webových aplikací na virtuálním počítači. Zobrazit [ladění jednoho nebo více procesů v sadě Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) Další informace.

## <a name="next-steps"></a>Další postup

* Použití **Intellitrace** ke shromažďování protokolů událostí a volání ze serveru verze. Zobrazit [ladění publikované cloudové služby pomocí nástroje IntelliTrace a sady Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016).

* Použití **Azure Diagnostics** můžete protokolovat podrobné informace z spouštění kódu v rámci rolí, zda role běží ve vývojovém prostředí nebo v Azure. Zobrazit [shromažďování dat protokolování pomocí diagnostiky Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).
