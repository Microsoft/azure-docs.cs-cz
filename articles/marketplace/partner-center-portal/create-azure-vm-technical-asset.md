---
title: Vytvoření technických prostředků virtuálního počítače Azure
description: Naučte se vytvářet a konfigurovat technické prostředky pro nabídku virtuálního počítače pro Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: dannyevers
ms.author: mingshen
ms.date: 04/13/2020
ms.openlocfilehash: aee2016b422dcf447dbae047c8a77ae93bf79ab7
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107151"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Vytvoření technických prostředků virtuálního počítače Azure

Tento článek popisuje, jak vytvořit a nakonfigurovat technické prostředky pro nabídku virtuálního počítače pro Azure Marketplace. Virtuální počítač obsahuje dvě součásti: virtuální pevné disky s operačním systémem (VHD) a volitelné přidružené datové disky VHD:

* **Virtuální pevný disk operačního systému** – obsahuje operační systém a řešení, které se nasadí s vaší nabídkou. Proces přípravy VHD se liší v závislosti na tom, jestli se jedná o virtuální počítač založený na platformě Linux, Windows nebo vlastním.
* **Datové disky VHD** – vyhrazené a trvalé úložiště pro virtuální počítač. Nepoužívejte virtuální pevný disk operačního systému (například jednotku C:) k ukládání trvalých informací.

Image virtuálního počítače obsahuje jeden disk s operačním systémem a až 16 datových disků. Použijte jeden virtuální pevný disk na datový disk, a to i v případě, že je disk prázdný.

> [!NOTE]
> Bez ohledu na to, jaký operační systém používáte, přidejte jenom minimální počet datových disků, které řešení potřebuje. Zákazníci nemohou odebrat disky, které jsou součástí bitové kopie v době nasazení, ale mohou vždy přidávat disky během nebo po nasazení.

> [!IMPORTANT]
> Každá image virtuálního počítače v plánu musí mít stejný počet datových disků.

## <a name="fundamental-technical-knowledge"></a>Základní technické poznatky

Návrh, sestavování a testování těchto assetů trvá čas a vyžaduje technické znalosti platformy Azure a technologií, které slouží k sestavování této nabídky. Kromě vaší domény řešení by váš technický tým měl mít znalosti o těchto technologiích společnosti Microsoft:

* Základní porozumění [službám Azure](https://azure.microsoft.com/services/)
* [Návrh a architekt aplikací Azure](https://azure.microsoft.com/solutions/architecture/)
* Praktické znalosti [Virtual Machines Azure](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) a [sítí Azure](https://azure.microsoft.com/services/?filter=networking)
* Praktické znalosti [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* Praktické znalosti formátu [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Navrhované nástroje – volitelné

Zvažte použití jednoho z následujících skriptovacích prostředí, které vám pomůžou spravovat virtuální počítače a virtuální pevné disky:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://code.visualstudio.com/)

Kromě toho zvažte přidání následujících nástrojů do vývojového prostředí:

* [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Rozšíření: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Přípona: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Přípona: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Projděte si dostupné nástroje na stránce [Azure vývojářské nástroje](https://azure.microsoft.com/product-categories/developer-tools/) a pokud používáte Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Vytvoření image virtuálního počítače pomocí schválené základní třídy

> [!NOTE]
> Pokud chcete vytvořit technické prostředky virtuálních počítačů pomocí Image, kterou jste vytvořili v místním prostředí, přečtěte si o [Vytvoření virtuálního počítače pomocí vlastní image](#create-a-vm-using-your-own-image).

Tato část popisuje různé aspekty používání schválené základny, jako je použití protokol RDP (Remote Desktop Protocol) (RDP), výběr velikosti virtuálního počítače, instalace nejnovějších aktualizací Windows a generalizace image VHD.

Následující části se zaměřují hlavně na virtuální pevné disky založené na Windows. Další informace o vytváření VHD na discích se systémem Linux najdete v tématu [Linux v distribucích, které jsou schváleny v Azure](../../virtual-machines/linux/endorsed-distros.md).

> [!WARNING]
> Podle pokynů v tomto tématu použijte Azure k vytvoření virtuálního počítače, který obsahuje předem nakonfigurovaný, schválený operační systém. Pokud to není kompatibilní s vaším řešením, je možné vytvořit a nakonfigurovat místní virtuální počítač pomocí schváleného operačního systému. Pak ji můžete nakonfigurovat a připravit pro nahrání, jak je popsáno v tématu [Příprava virtuálního pevného disku (VHD) Windows nebo VHDX pro nahrání do Azure](../../virtual-machines/windows/prepare-for-upload-vhd-image.md).

### <a name="select-an-approved-base"></a>Vybrat schválený základ

Jako základ vyberte buď operační systém Windows, nebo Linux.

#### <a name="windows"></a>Windows

Virtuální pevný disk operačního systému pro vaši image virtuálního počítače se systémem Windows musí být založený na základní imagi schválené pro Azure, která obsahuje Windows Server nebo SQL Server. Začněte tím, že vytvoříte virtuální počítač z jedné z následujících imagí z Azure Portal:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2012 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enterprise, Standard, Web)
* [SQL Server 2012 SP2](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enterprise, Standard, Web)

> [!NOTE]
> Pokud používáte aktuální Azure Portal nebo Azure PowerShell, schvalují se image Windows serveru vydané 8. září 2014 a novějšími.

#### <a name="linux"></a>Linux

Azure nabízí řadu schválených distribucí Linux. Aktuální seznam najdete v tématu [Linux v distribucích, které jsou schváleny v Azure](../../virtual-machines/linux/endorsed-distros.md).

### <a name="create-vm-in-the-azure-portal"></a>Vytvoření virtuálního počítače v Azure Portal

Pomocí těchto kroků vytvořte v [Azure Portal](https://ms.portal.azure.com/)základní image virtuálního počítače:

1. Přihlaste se k [Azure Portal](https://ms.portal.azure.com/) pomocí účet Microsoft přidruženého k předplatnému Azure, které chcete použít k publikování nabídky virtuálních počítačů.
2. Vytvořte novou skupinu prostředků a zadejte **název skupiny prostředků**, **předplatné**a **umístění skupiny prostředků**. Podrobnosti najdete v tématu [Správa prostředků](../../azure-resource-manager/resource-group-portal.md).
3. Na levé straně vyberte **virtuální počítače** , aby se zobrazila stránka s podrobnostmi o virtuálních počítačích.
4. Výběrem **+ Přidat** otevřete **prostředí vytvořit virtuální počítač**.
5. V rozevíracím seznamu vyberte obrázek nebo kliknutím na **Procházet všechny veřejné a soukromé image** vyhledejte nebo procházejte všechny dostupné image virtuálních počítačů.
6. Vyberte velikost virtuálního počítače, který chcete nasadit, a to pomocí následujících doporučení:
    * Pokud plánujete vývoj virtuálního pevného disku v místním prostředí, nezáleží na velikosti. Zvažte použití jednoho z menších virtuálních počítačů.
    * Pokud plánujete vývoj image v Azure, zvažte použití jedné z doporučených velikostí virtuálních počítačů pro vybranou bitovou kopii.

7. V části **disky** rozbalte oddíl **Upřesnit** a nastavte možnost **použít spravované disky** na **ne**.
8. Zadejte další požadované podrobnosti pro vytvoření virtuálního počítače.
9. Vyberte **zkontrolovat + vytvořit** a zkontrolujte své volby. Když se zobrazí zpráva s **potvrzením ověření** , vyberte **vytvořit**.

Azure zahájí zřizování virtuálního počítače, který jste zadali. Svůj průběh můžete sledovat tak, že na levé straně vyberete kartu **Virtual Machines** . Po vytvoření se stav změní na **spuštěno**.

Pokud narazíte na potíže při vytváření nového virtuálního pevného disku založeného na Azure, přečtěte si téma [běžné problémy při vytváření virtuálního pevného disku (FAQ)](common-issues-during-vhd-creation.md).

### <a name="connect-to-your-azure-vm"></a>Připojení k VIRTUÁLNÍmu počítači Azure

Tato část vysvětluje, jak se připojit k virtuálnímu počítači, který jste vytvořili v Azure, a přihlásit se k němu. Po úspěšném připojení můžete s virtuálním počítačem pracovat jako v případě, že jste byli místně přihlášení k hostitelskému serveru.

#### <a name="connect-to-a-windows-based-vm"></a>Připojení k virtuálnímu počítači se systémem Windows

Použijte klienta vzdálené plochy pro připojení k virtuálnímu počítači se systémem Windows hostovanému v Azure. Většina verzí Windows nativně obsahuje podporu protokolu RDP (Remote Desktop Protocol). Pro jiné operační systémy můžete najít další informace o klientech v [klientech vzdálené plochy](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Tento článek podrobně popisuje, jak použít integrovanou podporu Windows RDP pro připojení k vašemu VIRTUÁLNÍmu počítači: [jak se připojit a přihlásit k virtuálnímu počítači Azure s Windows](../../virtual-machines/windows/connect-logon.md).

> [!TIP]
> Během procesu můžete obdržet upozornění zabezpečení. Například upozornění, například "soubor. RDP pochází z neznámého vydavatele" nebo "vaše uživatelská pověření nelze ověřit". Tato upozornění je bezpečné ignorovat.

#### <a name="connect-to-a-linux-based-vm"></a>Připojení k virtuálnímu počítači se systémem Linux

K připojení k virtuálnímu počítači se systémem Linux budete potřebovat klienta SSH (Secure Shell Protocol). V následujících krocích se používá [bezplatná](https://www.ssh.com/ssh/putty/) koncová SSH.

1. Přejít na [Azure Portal](https://ms.portal.azure.com/).
2. Vyhledejte a vyberte **virtuální počítače**.
3. Vyberte virtuální počítač, ke kterému se chcete připojit.
4. Spusťte virtuální počítač, pokud ještě není spuštěný.
5. Vyberte název virtuálního počítače, pro který chcete otevřít stránku s **přehledem** .
6. Poznamenejte si veřejnou IP adresu a název DNS vašeho virtuálního počítače (pokud tyto hodnoty nejsou nastavené, musíte [vytvořit síťové rozhraní](../../virtual-network/virtual-network-network-interface.md#create-a-network-interface))).
7. Otevřete aplikaci pro výstup.
8. V dialogovém okně Konfigurace výstupu zadejte IP adresu nebo název DNS vašeho virtuálního počítače.

    :::image type="content" source="media/avm-putty.png" alt-text="Ukazuje nastavení terminálu pro výstup. Název hostitele nebo IP adresa a pole portů jsou zvýrazněné.":::

9. Vyberte **otevřít** a otevřete terminál k výstupu.
10. Po zobrazení výzvy zadejte název účtu a heslo účtu virtuálního počítače se systémem Linux.

Pokud máte problémy s připojením, přečtěte si dokumentaci k vašemu klientovi SSH. Například [Kapitola 10: běžné chybové zprávy](https://www.ssh.com/ssh/putty/putty-manuals).

Podrobnosti, včetně postupu přidání plochy do zřízeného virtuálního počítače se systémem Linux, najdete v tématu [instalace a konfigurace vzdálené plochy pro připojení k virtuálnímu počítači se systémem Linux v Azure](../../virtual-machines/linux/use-remote-desktop.md).

## <a name="create-a-vm-using-your-own-image"></a>Vytvoření virtuálního počítače pomocí vlastní image

Tato část popisuje, jak vytvořit a nasadit image virtuálního počítače (VM) zadaného uživatelem. To můžete provést tak, že zadáte image virtuálního pevného disku operačního systému a datového disku z virtuálního pevného disku nasazeného v Azure.

> [!NOTE]
> Pokud chcete volitelně použít schválenou základní bitovou kopii, postupujte podle pokynů v části [Vytvoření image virtuálního počítače pomocí schválené základní](#create-a-vm-image-using-an-approved-base)třídy.

1. Nahrajte obrázky do účtu Azure Storage.
2. Nasaďte image virtuálního počítače.
3. Zachyťte image virtuálního počítače.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Nahrání imagí do účtu služby Azure Storage

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Nahrajte zobecněný virtuální pevný disk s operačním systémem a virtuální pevné disky s daty do svého účtu služby Azure Storage.

### <a name="deploy-your-image"></a>Nasazení image

Vytvořte bitovou kopii pomocí Azure Portal nebo Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Nasazení s využitím webu Azure Portal

1. Na domovské stránce vyberte **vytvořit prostředek**, vyhledejte "nasazení šablony" a vyberte **vytvořit**.
2. **V editoru vyberte vytvořit vlastní šablonu**.

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Znázorňuje stránku vlastního nasazení.":::

3. Vložte tuto [šablonu JSON](../partner-center-portal/azure-vm-image-certification.md) do editoru a vyberte **Uložit**.
4. Zadejte hodnoty parametrů pro zobrazené stránky vlastností **vlastního nasazení** .

    | Parametr | Popis |
    | ------------ | ------------- |
    | Název účtu úložiště uživatele | Obsah z buňky 2 |
    | Název kontejneru úložiště uživatele | Název účtu úložiště, kde se nachází zobecněný virtuální pevný disk |
    | Název DNS pro veřejnou IP adresu | Název DNS veřejné IP adresy. Po nasazení nabídky zadejte název DNS pro veřejnou IP adresu v Azure Portal. |
    | Uživatelské jméno správce | Uživatelské jméno účtu správce pro nový virtuální počítač |
    | Heslo správce | Heslo účtu správce pro nový virtuální počítač |
    | OS Type | Operační systém virtuálního počítače: Windows nebo Linux |
    | ID předplatného | Identifikátor vybraného předplatného |
    | Umístění | Zeměpisná poloha nasazení |
    | Velikost virtuálního počítače | [Velikost virtuálního počítače Azure](../../virtual-machines/windows/sizes.md), například Standard_A2 |
    | Název veřejné IP adresy | Název vaší veřejné IP adresy |
    | Název virtuálního počítače | Název nového virtuálního počítače |
    | Název Virtual Network | Název virtuální sítě používané virtuálním počítačem |
    | Název síťové karty | Název síťové karty, na které běží virtuální síť |
    | ADRESA URL VIRTUÁLNÍHO PEVNÉHO DISKU | Úplná adresa URL virtuálního pevného disku s operačním systémem |
    |  |  |

5. Po poskytnutí těchto hodnot vyberte **koupit**.

Azure spustí nasazení. Vytvoří nový virtuální počítač se zadaným nespravovaným virtuálním pevným diskem v zadané cestě k účtu úložiště. Průběh můžete sledovat v Azure Portal výběrem **Virtual Machines** na levé straně portálu. Po vytvoření virtuálního počítače se stav změní z počáteční na spuštěno.

#### <a name="deploy-using-azure-powershell"></a>Nasazení s využitím Azure PowerShellu

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>Zachycení image virtuálního počítače

Použijte následující pokyny, které odpovídají vašemu přístupu:

* Azure PowerShell: [Vytvoření image nespravovaného virtuálního počítače z virtuálního počítače Azure](../../virtual-machines/windows/capture-image-resource.md)
* Azure CLI: [Vytvoření image virtuálního počítače nebo virtuálního pevného disku](../../virtual-machines/linux/capture-image.md)
* Rozhraní API: [Virtual Machines-Capture](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>Konfigurace virtuálního počítače

Tato část popisuje, jak změnit velikost, aktualizovat a zobecnit virtuální počítač Azure. Tyto kroky jsou nezbytné k přípravě nasazení virtuálního počítače na Azure Marketplace.

### <a name="sizing-the-vhds"></a>Změna velikosti virtuálních pevných disků

Pokud jste vybrali některý z virtuálních počítačů, které jsou předem nakonfigurované s operačním systémem (a volitelně i další služby), už jste si vybrali standardní velikost virtuálního počítače Azure. Doporučený postup je spuštění řešení s předem nakonfigurovaným operačním systémem. Pokud ale instalujete operační systém ručně, musíte ve své imagi virtuálního počítače nastavit velikost primárního virtuálního pevného disku:

* V případě systému Windows by se měl virtuální pevný disk operačního systému vytvořit jako VHD s pevným formátem, který je 127 – 128 GB.
* Pro Linux by měl být tento virtuální pevný disk vytvořen jako 30 – 50 GB pevného formátu VHD.

Pokud je fyzická velikost menší než 127 – 128 GB, měl by být virtuální pevný disk rozšiřitelný (zhuštěný/dynamický). Základní bitové kopie systému Windows a SQL Server, které jsou již v souladu s těmito požadavky, neměňte formát nebo velikost virtuálního pevného disku.

Datové disky můžou být velké až 1 TB. Při rozhodování o velikosti si pamatujte, že zákazníci nemůžou měnit velikost VHD v rámci bitové kopie v době nasazení. Virtuální pevné disky datového disku by se měly vytvářet jako virtuální pevné disky s pevným formátem. Měly by být také rozšiřitelné (zhuštěné/dynamické). Datové disky můžou být zpočátku prázdné nebo obsahovat data.

### <a name="install-the-most-current-updates"></a>Nainstalovat nejaktuálnější aktualizace

Základní image virtuálních počítačů s operačním systémem musí obsahovat nejnovější aktualizace až do data publikování. Před publikováním virtuálního pevného disku s operačním systémem, který jste vytvořili, se ujistěte, že aktualizujete operační systém a všechny nainstalované služby se všemi nejnovějšími opravami zabezpečení a údržby.

Pro Windows Server spusťte příkaz **Check for Updates** .

U distribucí pro Linux se aktualizace běžně stahují a instalují prostřednictvím nástroje příkazového řádku nebo grafického nástroje. Například Ubuntu Linux poskytuje příkaz [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) a nástroj [správce aktualizací](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) pro aktualizaci operačního systému.

### <a name="perform-additional-security-checks"></a>Provedení dalších kontrol zabezpečení

Udržujte si vysokou úroveň zabezpečení pro obrázky vašich řešení v Azure Marketplace. Následující článek poskytuje kontrolní seznam konfigurací a postupů zabezpečení, které vám pomůžou: [doporučení zabezpečení pro Azure Marketplace image](../../security/security-recommendations-azure-marketplace-images.md). Některá z těchto doporučení jsou specifická pro image založené na systému Linux, ale většina se vztahují na všechny image virtuálních počítačů.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Provádění vlastních konfigurací a naplánovaných úloh

Pokud je potřeba další konfigurace, použijte naplánovanou úlohu, která se spustí při spuštění, aby se všechny závěrečné změny virtuálního počítače daly provést po jeho nasazení. Vezměte v úvahu také následující doporučení:

* Pokud se jedná o úlohu spuštění jednou, je nutné úlohu po úspěšném dokončení odstranit.
* Konfigurace by se neměla spoléhat na jiné jednotky než C nebo D, protože existují jenom tyto dvě jednotky (jednotka C je disk s operačním systémem a jednotka D je dočasný místní disk).

Další informace o úpravách pro Linux najdete v tématu [rozšíření a funkce virtuálních počítačů pro Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="generalize-the-image"></a>Generalizace bitové kopie

Všechny obrázky v Azure Marketplace musí být obecně znovu použitelné. K tomu je potřeba, aby byl virtuální pevný disk operačního systému zobecněný, operace, která odebere všechny identifikátory jednotlivých instancí a softwarové ovladače z virtuálního počítače.

### <a name="windows"></a>Windows

Disky s operačním systémem Windows jsou zobecněny pomocí [nástroje Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Pokud následně aktualizujete nebo znovu nakonfigurujete operační systém, musíte znovu spustit nástroj Sysprep.

> [!WARNING]
> Vzhledem k tomu, že se aktualizace můžou spouštět automaticky po spuštění nástroje Sysprep, vypněte virtuální počítač, dokud nebude nasazený. Toto vypnutí zabrání následným aktualizacím v provádění změn specifických pro konkrétní instance v operačním systému nebo nainstalovaných službách. Další informace o spuštění nástroje Sysprep najdete v tématu [postup generalizace VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="linux"></a>Linux

Následující proces generalizuje virtuální počítač Linux a znovu ho nasadí jako samostatný virtuální počítač. Podrobnosti najdete v tématu [Postup vytvoření image virtuálního počítače nebo virtuálního pevného disku](../../virtual-machines/linux/capture-image.md). Až se dostanete do části "Vytvoření virtuálního počítače ze zaznamenané bitové kopie", můžete zastavit.

1. **Odebrat agenta Azure Linux**

    1. Připojte se k VIRTUÁLNÍmu počítači se systémem Linux pomocí klienta SSH.
    2. V okně SSH zadejte následující příkaz: `sudo waagent -deprovision+user` .
    3. Zadejte **Y** pro pokračování (můžete přidat parametr **-Force** k předchozímu příkazu, abyste se vyhnuli potvrzovacímu kroku).
    d. Po dokončení příkazu zadejte **příkaz exit** a zavřete tak klienta ssh.

2. **Zastavení virtuálního počítače**

    1. V Azure Portal vyberte skupinu prostředků (RG) a zrušte přidělení virtuálního počítače.
    2. Virtuální pevný disk je teď zobecněný a můžete vytvořit nový virtuální počítač pomocí tohoto virtuálního pevného disku.

## <a name="next-steps"></a>Další kroky

Pokud jste narazili na potíže s vytvářením nového virtuálního pevného disku založeného na Azure, přečtěte si téma [běžné problémy při vytváření VHD](common-issues-during-vhd-creation.md).

V opačném případě:

* Certifikace [vaší image virtuálního počítače](get-sas-uri.md) vysvětluje, jak otestovat a odeslat image virtuálního počítače pro Azure Marketplace certifikace, včetně toho, kde získat nástroj *pro testování certifikace pro nástroj pro certifikaci Azure* a jak ho použít k certifikaci vaší image virtuálního počítače.
