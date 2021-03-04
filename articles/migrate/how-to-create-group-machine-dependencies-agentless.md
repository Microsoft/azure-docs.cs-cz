---
title: Nastavení analýzy závislostí bez agentů v serveru Azure Migrate Assessment
description: Nastavte analýzu závislostí bez agentů v Azure Migrate Server Assessment.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: c3aa2aea764af8469152b007e60427724fea398a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045849"
---
# <a name="analyze-server-dependencies-agentless"></a>Analyzovat závislosti serveru (bez agenta)

Tento článek popisuje, jak nastavit analýzu závislostí bez agentů pomocí Azure Migrate: posouzení serveru. [Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat a pochopit závislosti mezi servery pro účely posouzení a migrace do Azure.

> [!IMPORTANT]
> Analýza závislostí bez agentů je momentálně ve verzi Preview pro servery, které běží ve vašem prostředí VMware, zjištěné nástrojem Azure Migrate: Nástroj pro vyhodnocení serveru.
> Tuto verzi Preview pokrývá zákaznická podpora a je možné ji použít pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Aktuální omezení

- V zobrazení analýzy závislostí momentálně nelze přidat nebo odebrat server ze skupiny.
- Mapování závislostí pro skupinu serverů není aktuálně k dispozici.
- V Azure Migrate projektu lze shromažďování dat závislostí Povolit souběžně pro servery 1000. Můžete analyzovat větší počet serverů pomocí sekvenceování v dávkách serverů 1000.

## <a name="before-you-start"></a>Než začnete

- Ujistěte se, že jste [vytvořili Azure Migrate projekt](./create-manage-projects.md) pomocí Azure Migrate: Nástroj pro vyhodnocení serveru byl do něj přidán.
- Zkontrolujte [požadavky VMware](migrate-support-matrix-vmware.md#vmware-requirements) a proveďte analýzu závislostí.
- Před nastavením zařízení zkontrolujte [požadavky na zařízení](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) .
- Než povolíte analýzu závislostí na serverech, [Zkontrolujte požadavky na analýzu závislostí](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) .

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Nasazení a konfigurace zařízení Azure Migrate

1. [Projděte si](migrate-appliance.md#appliance---vmware) požadavky na nasazení zařízení Azure Migrate.
2. Zkontrolujte adresy URL Azure, které bude zařízení potřebovat pro přístup k [veřejným](migrate-appliance.md#public-cloud-urls) a [státním cloudům](migrate-appliance.md#government-cloud-urls).
3. [Zkontrolujte data](migrate-appliance.md#collected-data---vmware) , která zařízení shromažďuje během zjišťování a posouzení.
4. [Poznamenejte si](migrate-support-matrix-vmware.md#port-access-requirements) požadavky na přístup k portu pro dané zařízení.
5. [Nasaďte zařízení Azure Migrate](how-to-set-up-appliance-vmware.md) a spusťte zjišťování. Chcete-li nasadit zařízení, Stáhněte a importujte šablonu vajíček do VMware a vytvořte na svém vCenter Server Server se systémem. Po nasazení zařízení je potřeba ho zaregistrovat v projektu Azure Migrate a nakonfigurovat ho tak, aby zahájil zjišťování.
6. Při konfiguraci zařízení musíte v nástroji Configuration Manager pro zařízení zadat následující:
    - Podrobnosti vCenter Server, ke kterým se chcete připojit
    - vCenter Server rozsah přihlašovacích údajů, aby se zjistily servery ve vašem prostředí VMware.
    - Přihlašovací údaje serveru, které můžou být přihlašovacími údaji k doméně/Windows (ne k doméně)/Linux (nedoménám). [Přečtěte si další informace](add-server-credentials.md) o tom, jak zadat přihlašovací údaje a jak je zpracovat.

## <a name="verify-permissions"></a>Ověření oprávnění

- Pro zjišťování a hodnocení musíte [vytvořit účet vCenter Server jen pro čtení](./tutorial-discover-vmware.md#prepare-vmware) . Účet jen pro čtení potřebuje oprávnění povolená pro **Virtual Machines**  >  **operace hostů**, aby bylo možné komunikovat se servery za účelem shromažďování dat o závislostech.
- Potřebujete uživatelský účet, aby mohl server Assessment získat přístup k serveru a shromažďovat data o závislostech. [Přečtěte si](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) o požadavcích na účet pro servery s Windows a Linux.

### <a name="add-credentials-and-initiate-discovery"></a>Přidat pověření a iniciovat zjišťování

1. Otevřete Správce konfigurace zařízení, proveďte kontrolu požadovaných součástí a registraci zařízení.
2. Přejděte na panel **Správa přihlašovacích údajů a zdrojů zjišťování** .
1.  V **kroku 1: zadání přihlašovacích údajů vCenter Server** klikněte na **Přidat přihlašovací údaje** a zadejte přihlašovací údaje pro účet vCenter Server, který zařízení použije ke zjišťování serverů běžících na vCenter Server.
1. V **kroku 2: zadejte vCenter Server podrobnosti**, klikněte na **Přidat zdroj zjišťování** a vyberte popisný název přihlašovacích údajů z rozevíracího seznamu, zadejte **IP adresu nebo plně kvalifikovaný název domény** vCenter Serverho :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="panelu instance 3 v nástroji Configuration Manager pro vCenter Server podrobnosti"::: .
1. V **kroku 3: zadání přihlašovacích údajů serveru pro inventarizaci softwaru, analýzu závislostí bez agentů a zjišťování instancí SQL Server a databází**, klikněte na **Přidat přihlašovací údaje** a zadejte více přihlašovacích údajů serveru pro zahájení inventáře softwaru.
1. Kliknutím na **Spustit zjišťování Zahajte** vCenter Server zjišťování.

 Po dokončení zjišťování vCenter Server zařízení zahájí zjišťování nainstalovaných aplikací, rolí a funkcí (inventář softwaru). Během inventarizace softwaru budou přidané přihlašovací údaje k serverům iterovat na servery a ověřeny pro analýzu závislostí bez agentů. Pro servery z portálu můžete povolit analýzu závislostí bez agentů. Pro povolení analýzy závislostí bez agentů je možné vybrat pouze servery, na kterých je ověřování úspěšné.

## <a name="start-dependency-discovery"></a>Spustit zjišťování závislosti

Vyberte servery, na kterých chcete povolit zjišťování závislostí.

1. V **Azure Migrate: vyhodnocování serveru** klikněte na **zjištěné servery**.
2. Vyberte **název zařízení** , jehož zjišťování chcete zkontrolovat.
1. Stav ověření serverů můžete zobrazit ve sloupci **závislosti (bez agenta)** .
1. Klikněte na rozevírací seznam **Analýza závislostí** .
1. Klikněte na **Přidat servery**.
1. Na stránce **Přidat servery** vyberte servery, na kterých chcete povolit analýzu závislostí. Mapování závislostí můžete povolit pouze na serverech, na kterých bylo ověření úspěšné. Další cyklus ověřování spustí 24 hodin po posledním časovém razítku ověření.
1. Po výběru serverů klikněte na **Přidat servery**.

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="Spustit analýzu závislostí":::

Po povolení analýzy závislostí na serverech můžete vizualizovat závislosti přibližně po šesti hodinách. Pokud chcete současně povolit více serverů pro analýzu závislostí, můžete k tomu použít [PowerShell](#start-or-stop-dependency-analysis-using-powershell) .

## <a name="visualize-dependencies"></a>Vizualizace závislostí

1. V **Azure Migrate: vyhodnocování serveru** klikněte na **zjištěné servery**.
1. Vyberte **název zařízení** , jehož zjišťování chcete zkontrolovat.
1. Vyhledejte server, jehož závislosti chcete zkontrolovat.
1. Ve sloupci **závislosti (bez agenta)** klikněte na **Zobrazit závislosti.**
1. Změňte časové období, pro které chcete zobrazit mapu pomocí rozevíracího seznamu doba **Trvání** .
1. Rozbalte skupinu **klientů** , aby vybrala seznam serverů se závislostí na vybraném serveru.
1. Rozbalte skupinu **portů** a uveďte servery, které mají závislost z vybraného serveru.
1. Pokud chcete přejít k zobrazení mapy kteréhokoli závislého serveru, klikněte na název serveru > **Načíst mapu serveru** 
 :::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="rozbalte uzel port serveru a mapa zátěžového serveru"::: .
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="Rozbalit skupinu klientů":::

8. Rozbalte vybraný server pro zobrazení podrobností na úrovni procesu pro každou závislost.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="Rozbalte možnost Server a zobrazte procesy.":::

> [!NOTE]
> Informace o procesu pro závislost není vždy k dispozici. Pokud není k dispozici, závislost je znázorněna s procesem označeným jako "Neznámý proces".

## <a name="export-dependency-data"></a>Exportovat data závislostí

1. V **Azure Migrate: vyhodnocování serveru** klikněte na **zjištěné servery**.
2. Klikněte na rozevírací seznam **Analýza závislostí** .
3. Klikněte na **exportovat závislosti aplikací**.
4. Na stránce **exportovat závislosti aplikací** vyberte název zařízení, který zjišťuje požadované servery.
5. Vyberte čas spuštění a čas ukončení. Všimněte si, že data si můžete stáhnout jenom za posledních 30 dní.
6. Klikněte na **exportovat závislost**.

Data závislosti se exportují a stahují ve formátu CSV. Stažený soubor obsahuje data závislostí napříč všemi servery, které jsou povolené pro analýzu závislostí. 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="Exportovat závislosti":::

### <a name="dependency-information"></a>Informace o závislostech

Každý řádek exportovaného sdíleného svazku clusteru odpovídá závislosti zjištěné v určeném časovém intervalu.

Následující tabulka shrnuje pole v exportovaném souboru CSV. Všimněte si, že pole název serveru, aplikace a proces jsou vyplněna pouze pro servery, které mají povolenou analýzu závislostí bez agenta.

**Název pole** | **Podrobnosti**
--- | --- 
Timeslot | Timeslot, během kterého byla závislost zjištěna. <br/> Data závislostí jsou v současnosti zachycena za 6 hodinových slotů.
Název zdrojového serveru | Název zdrojového serveru 
Zdrojová aplikace | Název aplikace na zdrojovém serveru  
Zdrojový proces | Název procesu na zdrojovém serveru  
Název cílového serveru | Název cílového serveru
Cílová IP adresa | IP adresa cílového serveru
Cílová aplikace | Název aplikace na cílovém serveru
Cílový proces | Název procesu na cílovém serveru  
Cílový port | Číslo portu na cílovém serveru

## <a name="stop-dependency-discovery"></a>Zastavit zjišťování závislosti

Vyberte servery, na kterých chcete zastavit zjišťování závislostí.

1. V **Azure Migrate: vyhodnocování serveru** klikněte na **zjištěné servery**.
1. Vyberte **název zařízení** , jehož zjišťování chcete zkontrolovat.
1. Klikněte na rozevírací seznam **Analýza závislostí** .
1. Klikněte na **odebrat servery**.
1. Na stránce **odebrat servery** vyberte server, u kterého chcete zastavit analýzu závislostí.
1. Po výběru serverů klikněte na **odebrat servery**.

Pokud chcete závislost zastavit současně na více serverech, můžete k tomu použít [PowerShell](#start-or-stop-dependency-analysis-using-powershell) .

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>Spuštění nebo zastavení analýzy závislostí pomocí prostředí PowerShell

Stáhněte modul PowerShell z [Azure PowerShellho úložiště ukázek](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) na GitHubu.

### <a name="log-in-to-azure"></a>Přihlaste se k Azure.

1. Přihlaste se k předplatnému Azure pomocí rutiny Connect-AzAccount.

    ```PowerShell
    Connect-AzAccount
    ```
    Pokud používáte Azure Government, použijte následující příkaz.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Vyberte předplatné, ve kterém jste vytvořili Azure Migrate projekt. 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Import staženého modulu AzMig_Dependencies PowerShell

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Povolit nebo zakázat shromažďování dat závislostí

1. Pomocí následujících příkazů Získejte seznam zjištěných serverů v projektu Azure Migrate. V následujícím příkladu je název projektu FabrikamDemoProject a skupina prostředků, do které patří, je FabrikamDemoRG. Seznam serverů bude uložen v FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    V souboru můžete zobrazit zobrazovaný název serveru, aktuální stav kolekce závislostí a ID ARM všech zjištěných serverů. 

2. Pokud chcete povolit nebo zakázat závislosti, vytvořte vstupní soubor CSV. Soubor musí obsahovat sloupec s hlavičkou "ARM ID". Všechny další hlavičky v souboru CSV budou ignorovány. Sdílený svazek clusteru můžete vytvořit pomocí souboru vygenerovaného v předchozím kroku. Vytvořte kopii souboru uchovávající servery, na kterých chcete povolit nebo zakázat závislosti. 

    V následujícím příkladu se aktivuje analýza závislostí na seznamu serverů ve vstupním souboru FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    V následujícím příkladu se v seznamu serverů ve vstupním souboru FabrikamDemo_VMs_Disable.csv zakáže analýza závislostí.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Vizualizovat síťová připojení v Power BI

Azure Migrate nabízí šablonu Power BI, kterou můžete použít k vizualizaci síťových připojení mnoha serverů najednou a filtrovat podle procesů a serverů. Chcete-li vizualizovat, načtěte Power BI s daty závislosti podle následujících pokynů.

1. Stáhněte modul PowerShell a šablonu Power BI z [Azure PowerShellho úložiště ukázek](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) na GitHubu.

2. Přihlaste se k Azure pomocí následujících pokynů: 
    - Přihlaste se k předplatnému Azure pomocí rutiny Connect-AzAccount.

        ```PowerShell
        Connect-AzAccount
        ```

    - Pokud používáte Azure Government, použijte následující příkaz.

        ```PowerShell
        Connect-AzAccount -EnvironmentName AzureUSGovernment
        ```

    - Vyberte předplatné, ve kterém jste vytvořili Azure Migrate projekt.

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. Import staženého modulu AzMig_Dependencies PowerShell

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Spusťte následující příkaz. Tento příkaz stáhne data závislostí do sdíleného svazku clusteru a zpracuje je, aby vygenerovala seznam jedinečných závislostí, které lze použít pro vizualizaci v Power BI. V příkladu pod názvem projektu je FabrikamDemoProject a skupina prostředků, do které patří, je FabrikamDemoRG. Závislosti budou staženy pro servery zjištěné nástrojem FabrikamAppliance. Jedinečné závislosti budou uloženy v FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. Otevřít staženou šablonu Power BI

6. Načte stažená data závislosti v Power BI.
    - Otevřete šablonu v Power BI.
    - Na panelu nástrojů klikněte na **načíst data** . 
    - Z běžných zdrojů dat vyberte **text/CSV** .
    - Vyberte stažený soubor se závislostmi.
    - Klikněte na **načíst**.
    - Zobrazí se tabulka s názvem souboru CSV. Tabulku můžete zobrazit na panelu pole na pravé straně. Přejmenujte ji na AzMig_Dependencies
    - Na panelu nástrojů klikněte na aktualizovat.

    V grafu síťových připojení a názvu zdrojového serveru, název cílového serveru, název zdrojového procesu, průřezy názvu cílového procesu by se měly dosvítit s importovanými daty.

7. Vizualizujte mapování síťových připojení podle serverů a procesů. Uložte soubor.

## <a name="next-steps"></a>Další kroky

[Servery skupiny](how-to-create-a-group.md) pro posouzení.