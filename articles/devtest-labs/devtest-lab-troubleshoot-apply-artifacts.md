---
title: Řešení potíží s artefakty v Azure DevTest Labs | Microsoft Docs
description: Naučte se řešit problémy, ke kterým dochází při aplikování artefaktů ve Azure DevTest Labsm virtuálním počítači.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a89b675a1b3bf134b98e09c7278f0eccb594c325
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483189"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Řešení potíží při aplikování artefaktů ve Azure DevTest Labsm virtuálním počítači
Použití artefaktů na virtuálním počítači může z různých důvodů selhat. Tento článek vás provede některými metodami, které vám pomůžou identifikovat možné příčiny.

Pokud potřebujete další informace v jakémkoli bodě tohoto článku, můžete se obrátit na odborníky na Azure DevTest Labs (oddálení) na [webu MSDN Azure a Stack Overflow fóra](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu.   

> [!NOTE]
> Tento článek se týká virtuálních počítačů s Windows i jiným systémem než Windows. I když existují určité rozdíly, budou v tomto článku explicitně vyvolány.

## <a name="quick-troubleshooting-steps"></a>Rychlé kroky pro řešení potíží
Ověřte, že je virtuální počítač spuštěný. DevTest Labs vyžaduje, aby byl virtuální počítač spuštěný a aby byl nainstalovaný a připravený [Agent virtuálního počítače s Microsoft Azure (VM Agent)](../virtual-machines/extensions/agent-windows.md) .

> [!TIP]
> V **Azure Portal** přejděte na stránku **Spravovat ARTEFAKTY** pro virtuální počítač a zjistěte, jestli je virtuální počítač připravený na použití artefaktů. V horní části stránky se zobrazí zpráva. 
> 
> Pomocí **Azure PowerShell** zkontrolujte příznak **canApplyArtifacts**, který se vrátí, jenom když rozbalíte operaci get. Podívejte se na následující vzorový příkaz:

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>Způsoby řešení potíží 
Můžete řešit potíže s virtuálními počítači vytvořenými pomocí DevTest Labs a modelu nasazení Správce prostředků pomocí jedné z následujících metod:

- **Azure Portal** – Skvělé, pokud potřebujete rychle získat vizuální nápovědu, co může být příčinou problému.
- **Azure PowerShell** – Pokud jste si netroufáte na příkazovém řádku PowerShellu, rychle Dotazujte prostředky DevTest Labs pomocí rutin Azure PowerShell.

> [!TIP]
> Další informace o tom, jak zkontrolovat spuštění artefaktů v rámci virtuálního počítače, najdete v tématu [Diagnostika selhání artefaktů v testovacím prostředí](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="symptoms-causes-and-potential-resolutions"></a>Příznaky, příčiny a možná řešení 

### <a name="artifact-appears-to-stop-responding"></a>Artefakt se zdá, že přestane reagovat.

Artefakt se zdá, že přestane reagovat, dokud nevyprší předem definovaný časový limit a artefakt je označený jako **neúspěšný**.

Když se zdá, že artefakt přestane reagovat, nejprve určete, kde je zablokované. Artefakt může být během provádění zablokovaný v některém z následujících kroků:

- **Během počátečního požadavku**. DevTest Labs vytvoří šablonu Azure Resource Manager pro vyžádání použití rozšíření vlastních skriptů (CSE). Proto se na pozadí aktivuje nasazení skupiny prostředků. Když dojde k chybě na této úrovni, získáte podrobnosti v **protokolech aktivit** skupiny prostředků pro daný virtuální počítač.  
    - K protokolu aktivit můžete přistupovat z navigačního panelu stránky testovacího virtuálního počítače. Když vyberete tuto možnost, zobrazí se položka pro **aplikování artefaktů na virtuální počítač** (Pokud se operace použít artefakty aktivovala přímo) nebo **přidá nebo upraví virtuální počítače** (Pokud operace aplikování artefaktů byla součástí procesu vytváření virtuálního počítače).
    - Vyhledejte chyby pod těmito položkami. V některých případech se chyba nebude označit příznakem odpovídajícím způsobem a bude nutné prozkoumat každou položku.
    - Při zkoumání podrobností každého záznamu nezapomeňte zkontrolovat obsah datové části JSON. V dolní části dokumentu se může zobrazit chyba.
- **Při pokusu o spuštění artefaktu**. Důvodem může být problémy se sítí nebo úložištěm. Podrobnosti najdete v příslušné části dále v tomto článku. Může k tomu také dojít z důvodu způsobu, jakým je skript vytvořen. Například:
    - Skript PowerShellu má **povinné parametry**, ale jednomu z nich se nepovede zadat jeho hodnotu, buď proto, že uživateli povolíte jeho prázdné pole, nebo protože nemáte výchozí hodnotu pro vlastnost v artifactfile.jsv definičním souboru. Skript přestane reagovat, protože čeká na vstup uživatele.
    - Skript PowerShellu **vyžaduje vstup uživatele** jako součást provádění. Skripty musí být zapsány pro tichou práci bez nutnosti zásahu uživatele.
- **Agent virtuálního počítače bude mít dobu potřebnou k přípravě**. Při prvním spuštění virtuálního počítače nebo při první instalaci rozšíření vlastních skriptů k obsluze žádosti o použití artefaktů může virtuální počítač vyžadovat buď upgrade agenta virtuálního počítače, nebo počkat na inicializaci agenta virtuálního počítače. Může se jednat o služby, na kterých agent virtuálního počítače závisí na tom, že se při inicializaci trvá dlouhou dobu. V takových případech najdete další informace o řešení potíží v tématu [Přehled agenta virtuálních počítačů Azure](../virtual-machines/extensions/agent-windows.md) .

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-script"></a>Ověření, zda se artefakt zdá, že přestane reagovat z důvodu skriptu

1. Přihlaste se k danému virtuálnímu počítači.
2. Zkopírujte skript místně na virtuálním počítači nebo ho vyhledejte na virtuálním počítači v části `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>` . Je to místo, kde se stáhnou skripty artefaktů.
3. Pomocí příkazového řádku se zvýšenými oprávněními spusťte skript místně a zadejte stejné hodnoty parametrů, které se použijí k tomu, aby mohl problém způsobovat.
4. Určete, jestli se skript nevyskytne při jakémkoli nežádoucím chování. Pokud ano, požádejte o aktualizaci artefaktu (Pokud se jedná o veřejné úložiště); nebo proveďte opravy sami (Pokud je z vašeho privátního úložiště).

> [!TIP]
> Můžete opravit problémy s artefakty hostovanými v našem [veřejném úložišti](https://github.com/Azure/azure-devtestlab) a odeslat změny pro naši kontrolu a schválení. Podívejte se na část **příspěvky** v dokumentu [Readme.MD](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md) .
> 
> Informace o vytváření vlastních artefaktů naleznete v tématu [AUTHORING.MD](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) Document.

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-vm-agent"></a>Chcete-li ověřit, zda artefakt přestane reagovat z důvodu agenta virtuálního počítače:
1. Přihlaste se k danému virtuálnímu počítači.
2. Pomocí Průzkumníka souborů přejděte na **C:\WindowsAzure\logs**.
3. Vyhledejte a otevřete soubor **WaAppAgent. log**.
4. Vyhledejte položky, které se zobrazí, když se spustí agent virtuálního počítače a kdy se inicializuje inicializace (to znamená, že se pošle první prezenční signál). Dejte přednost novějším položkám nebo konkrétně těm v časovém období, pro které se k problému setkáte.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    V tomto příkladu vidíte, že čas spuštění agenta virtuálního počítače trval 10 minut a 20 sekund, protože byl odeslán prezenční signál. Příčinou tohoto případu bylo, že spuštění služby OOBE trvá dlouhou dobu.

> [!TIP]
> Obecné informace o rozšířeních Azure najdete v tématu [rozšíření a funkce virtuálních počítačů Azure](../virtual-machines/extensions/overview.md).

## <a name="storage-errors"></a>Chyby úložiště
DevTest Labs vyžaduje přístup k účtu úložiště testovacího prostředí vytvořenému pro ukládání artefaktů do mezipaměti. Když DevTest Labs použije artefakt, načte konfiguraci artefaktu a jeho soubory z nakonfigurovaných úložišť. Ve výchozím nastavení DevTest Labs nakonfiguruje přístup k **úložišti artefaktů veřejného**.

V závislosti na tom, jak je virtuální počítač nakonfigurovaný, nemusí mít přímý přístup k tomuto úložišti. Proto DevTest Labs podle návrhu ukládá artefakty do mezipaměti účtu úložiště, který je vytvořen při první inicializaci testovacího prostředí.

Pokud se přístup k tomuto účtu úložiště zablokuje jakýmkoli způsobem, jako když je kvůli zablokování provozu z virtuálního počítače do služby Azure Storage, může se zobrazit chybová zpráva podobná následující:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

Výše uvedená chyba se zobrazí v části **zpráva nasazení** na stránce **výsledky artefaktu** v části **Spravovat artefakty**. Zobrazí se také v **protokolech aktivit** v rámci skupiny prostředků v daném virtuálním počítači.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Aby se zajistilo, že komunikace se službou Azure Storage není blokovaná:

- **Kontrolovat přidané skupiny zabezpečení sítě (NSG)**. Je možné, že se přidala zásada předplatného, kde skupin zabezpečení sítě se automaticky nakonfigurují ve všech virtuálních sítích. Má vliv také na výchozí virtuální síť testovacího prostředí, pokud se používá, nebo na jinou virtuální síť nakonfigurovanou v testovacím prostředí, která se používá pro vytváření virtuálních počítačů.
- **Ověřte výchozí účet úložiště testovacího prostředí** (tj. první účet úložiště vytvořený při vytvoření testovacího prostředí, jehož název obvykle začíná písmenem "a" a končí číslicí \<labname\> #).
    1. Přejděte do skupiny prostředků testovacího prostředí.
    2. Vyhledejte prostředek typu **účet úložiště**, jehož název odpovídá konvenci.
    3. Přejděte na stránku účtu úložiště s názvem **brány firewall a virtuální sítě**.
    4. Ujistěte se, že je nastavená na **všechny sítě**. Pokud je vybraná možnost **vybrané sítě** , zajistěte, aby se do seznamu přidaly virtuální sítě testovacího prostředí použité k vytvoření virtuálních počítačů.

Další podrobné řešení potíží najdete v tématu [konfigurace Azure Storage bran firewall a virtuálních sítí](../storage/common/storage-network-security.md).

> [!TIP]
> **Ověřte pravidla skupiny zabezpečení sítě**. Pomocí [ověření toku protokolu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) ověříte, že pravidlo ve skupině zabezpečení sítě blokuje provoz do nebo z virtuálního počítače. Můžete také zkontrolovat efektivní pravidla skupiny zabezpečení, abyste zajistili, že příchozí pravidlo **Povolit** NSG existuje. Další informace najdete v tématu [použití platných pravidel zabezpečení k řešení potíží s tokem provozu virtuálních počítačů](../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="other-sources-of-error"></a>Další zdroje chyby
Existují další méně časté možné zdroje chyb. Nezapomeňte je vyhodnotit, abyste viděli, jestli platí pro váš případ. Tady je jeden z nich: 

- **Osobní přístupový token pro soukromé úložiště vypršel**. Po vypršení platnosti artefakt nebude uveden a žádné skripty, které odkazují na artefakty z úložiště s tokenem Private Access s vypršenou platností, odpovídajícím způsobem selžou.

## <a name="next-steps"></a>Další kroky
Pokud nedošlo k žádné z těchto chyb a stále nemůžete použít artefakty, můžete začlenit incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
