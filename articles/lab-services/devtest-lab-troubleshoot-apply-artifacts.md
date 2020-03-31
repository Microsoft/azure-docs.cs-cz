---
title: Řešení problémů s artefakty v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit problémy, ke kterým dochází při použití artefaktů ve virtuálním počítači Azure DevTest Labs.
services: devtest-lab
documentationcenter: na
author: spelluru
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: fc5051667100a2ebaa01b7815f825fadd766b08f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456986"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Řešení problémů při použití artefaktů ve virtuálním počítači Azure DevTest Labs
Použití artefaktů na virtuálním počítači může selhat z různých důvodů. Tento článek vás provede některými způsoby, které vám pomohou identifikovat možné příčiny.

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, můžete kontaktovat odborníky Azure DevTest Labs (DTL) na [fórech MSDN Azure a Přetečení zásobníku](https://azure.microsoft.com/support/forums/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat podporu.   

> [!NOTE]
> Tento článek se vztahuje na virtuální počítače se systémem Windows i jiné počítače než Windows. Zatímco existují určité rozdíly, budou explicitně volány v tomto článku.

## <a name="quick-troubleshooting-steps"></a>Rychlé kroky řešení potíží
Zkontrolujte, zda je spuštěný virtuální virtuální hotel. DevTest Labs vyžaduje, aby byl virtuální počítač spuštěný a aby byl agent [virtuálních strojů Microsoft Azure (Agent virtuálního počítače)](../virtual-machines/extensions/agent-windows.md) nainstalovaný a připravený.

> [!TIP]
> Na **webu Azure Portal**přejděte na stránku **Spravovat artefakty** pro virtuální počítač a zjistěte, jestli je virtuální počítač připravený k použití artefaktů. Zobrazí se zpráva v horní části této stránky. 
> 
> Pomocí **Azure PowerShell**, zkontrolujte příznak **canApplyArtifacts**, který je vrácena pouze při rozbalte na operaci GET. Podívejte se na následující příklad příkazu:

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
Řešení potíží s virtuálními počítačemi vytvořenými pomocí devTest Labs a modelu nasazení Správce prostředků můžete řešit pomocí jedné z následujících metod:

- **Azure portal** – skvělé, pokud potřebujete rychle získat vizuální náznak toho, co může být příčinou problému.
- **Azure PowerShell** – pokud se vám nelíbí výzva k Prostředí PowerShell, rychle se dotazujte na prostředky DevTest Labs pomocí rutin Azure PowerShell.

> [!TIP]
> Další informace o kontrole provádění artefaktů v rámci virtuálního počítači najdete [v tématu Diagnostika selhání artefaktů v testovacím prostředí](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="symptoms-causes-and-potential-resolutions"></a>Příznaky, příčiny a potenciální rozlišení 

### <a name="artifact-appears-to-hang"></a>Zdá se, že artefakt visí   
Artefakt se zobrazí zablokuje, dokud předdefinovaný časový limit vyprší a artefakt je označen jako **Failed**.

Když se zdá, že artefakt zablokuje, nejprve určit, kde je přilepená. Artefakt může být blokován v některém z následujících kroků během provádění:

- **Během počáteční žádosti**. DevTest Labs vytvoří šablonu Azure Resource Manager požádat o použití rozšíření vlastní skript (CSE). Proto na pozadí je aktivována nasazení skupiny prostředků. Když dojde k chybě na této úrovni, získáte podrobnosti v **protokolech aktivit skupiny** prostředků pro daný virtuální ms.  
    - K protokolu aktivit se dostanete z navigačního panelu stránky virtuálního zařízení testovacího prostředí. Když ji vyberete, zobrazí se položka pro **použití artefaktů na virtuální počítač** (pokud byla operace použít artefakty spuštěna přímo) nebo Přidat nebo upravit virtuální **počítače** (pokud byla operace použití artefaktů součástí procesu vytváření virtuálních počítačů).
    - Vyhledejte chyby v těchto položkách. Někdy chyba nebude odpovídajícím způsobem označena a budete muset prozkoumat každou položku.
    - Při zkoumání podrobností každé položky, ujistěte se, že zkontrolujte obsah datové části JSON. V dolní části dokumentu se může zobrazit chyba.
- **Při pokusu o spuštění artefaktu**. Mohlo by to být kvůli problémům se sítí nebo úložištěm. Podrobnosti naleznete v příslušné části dále v tomto článku. To se může stát také kvůli způsobu, jakým je skript vytvořen. Například:
    - Skript prostředí PowerShell má **povinné parametry**, ale jeden nepodaří předat hodnotu, protože umožňujete uživateli nechat prázdné, nebo proto, že nemáte výchozí hodnotu pro vlastnost v souboru definice artefaktfile.json. Skript bude zavěšit, protože čeká na vstup uživatele.
    - Skript prostředí PowerShell **vyžaduje vstup uživatele** jako součást provádění. Skripty musí být zapsány do práce tiše bez nutnosti zásahu uživatele.
- **Agent virtuálního montovny trvá dlouho, než bude připravený**. Při prvním spuštění virtuálního aplikace nebo při prvním nainstalovaném rozšíření vlastního skriptu, které slouží požadavku na použití artefaktů, může virtuální jazyk vyžadovat buď upgrade agenta virtuálního zařízení, nebo čekání na inicializaci agenta virtuálního zařízení. Může být služby, na kterých závisí agent virtuálního počítače, které trvá dlouhou dobu inicializovat. V takových případech najdete [v tématu Azure Virtual Machine Agent přehled](../virtual-machines/extensions/agent-windows.md) pro další řešení potíží.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-script"></a>Ověření, zda se artefakt zdá být zavěšený kvůli skriptu

1. Přihlaste se k dotyčnému virtuálnímu počítači.
2. Zkopírujte skript místně ve virtuálním počítači nebo `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`ho vyhledejte ve virtuálním počítači pod . Je to místo, kde jsou stahovány skripty artefaktů.
3. Pomocí příkazového řádku se zvýšenými oprávněními spusťte skript místně a zajistěte stejné hodnoty parametrů, které byly použity k vyvolání problému.
4. Zjistěte, zda skript trpí nežádoucím chováním. Pokud ano, požádejte o aktualizaci artefaktu (pokud je z veřejného úložiště); nebo proveďte opravy sami (pokud se jedná o váš soukromý repo).

> [!TIP]
> Můžete opravit problémy s artefakty hostovanými v našem [veřejném repo a](https://github.com/Azure/azure-devtestlab) odeslat změny k našemu přezkoumání a schválení. Viz oddíl **Příspěvky** v [dokumentu README.md.](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md)
> 
> Informace o psaní vlastních artefaktů naleznete [v AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) dokumentu.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-vm-agent"></a>Ověření, jestli se artefakt zobrazí jako zablokování kvůli agentovi virtuálního počítače:
1. Přihlaste se k dotyčnému virtuálnímu počítači.
2. Pomocí Průzkumníka souborů přejděte na **C:\WindowsAzure\logs**.
3. Vyhledejte a otevřete soubor **WaAppAgent.log**.
4. Vyhledejte položky, které ukazují, kdy se spustí agent virtuálního zařízení a kdy je dokončení inicializace (to znamená, že první prezenční signál je odeslán). Upřednostňuj novější položky nebo konkrétně ty, které se pohybují kolem časového období, pro které se problém emituje.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    V tomto příkladu uvidíte, že čas spuštění agenta virtuálního soudu trvalo 10 minut a 20 sekund, protože byl odeslán prezenční signál. Příčinou v tomto případě byla služba OOBE, která trvala dlouhou dobu.

> [!TIP]
> Obecné informace o rozšířeních Azure najdete v tématu [rozšíření a funkce virtuálních strojů Azure](../virtual-machines/extensions/overview.md).

## <a name="storage-errors"></a>Chyby úložiště
DevTest Labs vyžaduje přístup k účtu úložiště testovacího prostředí, který je vytvořen do mezipaměti artefakty. Když DevTest Labs použije artefakt, bude číst konfiguraci artefaktu a jeho soubory z nakonfigurovaných úložišť. Ve výchozím nastavení devTest Labs konfiguruje přístup k **veřejné úložiště artefaktů**.

V závislosti na konfiguraci virtuálního počítače nemusí mít přímý přístup k tomuto opětovnému pojistce. Proto podle návrhu DevTest Labs ukládá artefakty v účtu úložiště, který je vytvořen při první inicializování testovacího prostředí.

Pokud je přístup k tomuto účtu úložiště blokován jakýmkoli způsobem, jako když je provoz blokován z virtuálního počítače do služby Azure Storage, může se zobrazit chyba podobná té následující:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

Výše uvedená chyba by se objevila v části **Zpráva nasazení** na stránce S **výsledky artefaktů** v části **Správa artefaktů**. Zobrazí se také v **protokolech aktivit** ve skupině prostředků daného virtuálního počítače.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Jak zajistit, aby se neblokovala komunikace se službou Azure Storage:

- **Zkontrolujte přidané skupiny zabezpečení sítě (NSG).** It may be that a subscription policy was added where NSGs are automatically configured in all virtual networks. To by také ovlivnit výchozí virtuální sítě testovacího prostředí, pokud se používá, nebo jiné virtuální sítě nakonfigurované ve vašem testovacím prostředí, který se používá pro vytváření virtuálních počítačů.
- **Zkontrolujte výchozí účet úložiště testovacího prostředí** (to znamená, že první účet úložiště vytvořený při vytvoření testovacího prostředí, jehož název\<obvykle\>začíná písmenem "a" a končí vícemístným číslem, které je název laboratoře #).
    1. Přejděte do skupiny prostředků testovacího prostředí.
    2. Vyhledejte prostředek **typu účet úložiště**, jehož název odpovídá konvenci.
    3. Přejděte na stránku účtu úložiště s názvem **Brány firewall a virtuální sítě**.
    4. Ujistěte se, že je nastavena na **všechny sítě**. Pokud je vybraná možnost **Vybrané sítě,** ujistěte se, že virtuální sítě testovacího prostředí použité k vytvoření virtuálních počítačů jsou přidány do seznamu.

Další podrobné řešení potíží najdete v [tématu Konfigurace firewallů azure storage a virtuálních sítí](../storage/common/storage-network-security.md).

> [!TIP]
> **Ověřte pravidla skupiny zabezpečení sítě**. Pomocí [ověření toku IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) zkontrolujte, zda pravidlo ve skupině zabezpečení sítě blokuje provoz do nebo z virtuálního počítače. Můžete také zkontrolovat účinná pravidla skupiny zabezpečení a zajistit, aby existovalo pravidlo příchozího **povolení** k nesenožení zabezpečení. Další informace najdete [v tématu Použití účinných pravidel zabezpečení k řešení potíží s tokem provozu virtuálních montovací](../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="other-sources-of-error"></a>Jiné zdroje chyb
Existují i jiné méně časté možné zdroje chyb. Ujistěte se, že vyhodnotit každý, aby zjistili, zda se vztahuje na váš případ. Zde je jeden z nich: 

- **Platnost osobního přístupového tokenu pro privátní repo**. Po vypršení platnosti artefaktu nebude uveden a všechny skripty, které odkazují na artefakty z úložiště s vypršením platnosti tokenu soukromého přístupu se nezdaří odpovídajícím způsobem.

## <a name="next-steps"></a>Další kroky
Pokud nedošlo k žádné z těchto chyb a stále nelze použít artefakty, můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.

