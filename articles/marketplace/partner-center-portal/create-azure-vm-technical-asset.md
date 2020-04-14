---
title: Vytvoření technických prostředků virtuálního počítače Azure
description: Zjistěte, jak vytvořit a nakonfigurovat technické prostředky pro nabídku virtuálního počítače (VM) pro Azure Marketplace.
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: eb885d0e965579b1ab2d66395f9f96eab0845bae
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266497"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Vytvoření technických prostředků virtuálního počítače Azure

> [!IMPORTANT]
> Přesouváme správu nabídek virtuálních strojů Azure z portálu partnerů cloudu do Centra partnerů. Dokud se vaše nabídky nemigrují, postupujte podle pokynů v části [Vytvoření technických prostředků pro nabídku virtuálních strojů pro portál partnerů cloudu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets) pro správu nabídek.

Tento článek popisuje, jak vytvořit a nakonfigurovat technické prostředky pro nabídku virtuálního počítače (VM) pro Azure Marketplace. Virtuální počítač obsahuje dvě součásti: virtuální pevný disk operačního systému (VHD) a volitelné přidružené datové disky VHD:

* **Operační systém VHD** – Obsahuje operační systém a řešení, které se nasazuje s vaší nabídkou. Proces přípravy virtuálního pevného disku se liší v závislosti na tom, zda se jedná o virtuální počítač založený na Linuxu, Windows nebo na vlastním základě.
* **Virtuální pevné disky datové disky** – vyhrazené trvalé úložiště pro virtuální počítače. Nepoužívejte operační systém VHD (například jednotka C:) k ukládání trvalých informací.

Bitová kopie virtuálního počítače obsahuje jeden disk operačního systému a až 16 datových disků. Použijte jeden virtuální disk Na datový disk, i když je disk prázdný.

> [!NOTE]
> Bez ohledu na to, který operační systém používáte, přidejte pouze minimální počet datových disků potřebných pro řešení. Zákazníci nemohou odebrat disky, které jsou součástí bitové kopie v době nasazení, ale mohou vždy přidat disky během nebo po nasazení.

> [!IMPORTANT]
> Každá image virtuálního počítače v plánu musí mít stejný počet datových disků.

## <a name="fundamental-technical-knowledge"></a>Základní technické znalosti

Navrhování, vytváření a testování těchto prostředků vyžaduje čas a vyžaduje technické znalosti platformy Azure i technologií používaných k sestavení nabídky. Kromě domény řešení by váš technický tým měl mít znalosti o následujících technologiích společnosti Microsoft:

* Základní znalosti [služeb Azure](https://azure.microsoft.com/services/)
* Jak [navrhovat a navrhovat aplikace Azure](https://azure.microsoft.com/solutions/architecture/)
* Pracovní znalosti virtuálních [počítačů Azure](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) a Azure [Networking](https://azure.microsoft.com/services/?filter=networking)
* Pracovní znalosti [Správce prostředků Azure](https://azure.microsoft.com/features/resource-manager/)
* Pracovní znalosti [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Navrhované nástroje – volitelné

Zvažte použití jednoho z následujících skriptovacích prostředí, které vám pomůže spravovat virtuální virtuální měna a virtuální disponály:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://code.visualstudio.com/)

Kromě toho zvažte přidání následujících nástrojů do vývojového prostředí:

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Rozšíření: [Nástroje Azure Správce prostředků](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Rozšíření: [Zkrášlení](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Rozšíření: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Zkontrolujte dostupné nástroje na stránce [Nástroje pro vývojáře Azure](https://azure.microsoft.com/product-categories/developer-tools/) a pokud používáte Visual Studio, visual studio [Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Vytvoření image virtuálního virtuálního aplikace pomocí schválené základny

> [!NOTE]
> Pokud chcete vytvořit technické prostředky virtuálního počítače pomocí image, kterou jste vytvořili ve vlastních prostorách, přejděte na [Vytvoření virtuálního počítače pomocí vlastní image](#create-a-vm-using-your-own-image).

Tato část popisuje různé aspekty používání schválené základny, jako je například použití protokolu RDP (Remote Desktop Protocol), výběr velikosti virtuálního počítače, instalace nejnovějších aktualizací systému Windows a zobecnění bitové kopie virtuálního pevného disku.

Následující části se zaměřují především na virtuální počítačna základě systému Windows. Další informace o vytváření virtuálních disdrátů založených na Linuxu najdete v [tématu Linux o distribucích schválených Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

> [!WARNING]
> Podle pokynů v tomto tématu pomocí Azure vytvořte virtuální počítač obsahující předem nakonfigurovaný, schválený operační systém. Pokud to není kompatibilní s vaším řešením, je možné vytvořit a nakonfigurovat místní virtuální počítač pomocí schváleného operačního systému. Potom můžete nakonfigurovat a připravit ji k nahrání, jak je popsáno v [článku Příprava virtuálního pevného disku s Windows nebo VHDX pro nahrání do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base"></a>Výběr schválené základny

Jako základnu vyberte operační systém Windows nebo Linux.

#### <a name="windows"></a>Windows

Virtuální pevný disk operačního systému pro bitovou kopii virtuálního počítače se systémem Windows musí být založený na základní bitové kopii schválené Azure, která obsahuje Windows Server nebo SQL Server. Chcete-li začít, vytvořte virtuální počítač z jedné z následujících ibi z webu Azure Portal:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (enterprise, standard, web)
* [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (enterprise, standard, web)

> [!NOTE]
> Pokud používáte aktuální portál Azure nebo Azure PowerShell, pak se schvalují image Windows Serveru publikované 8.

#### <a name="linux"></a>Linux

Azure nabízí celou řadu schválených distribucí Linuxu. Aktuální seznam najdete v [tématu Linux o distribucích schválených Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-in-the-azure-portal"></a>Vytvoření virtuálního počítače na webu Azure Portal

Následujícím postupem vytvořte základní image virtuálního počítače na [webu Azure Portal](https://ms.portal.azure.com/):

1. Přihlaste se k [portálu Azure](https://ms.portal.azure.com/) pomocí účtu Microsoft přidruženého k předplatnému Azure, které chcete použít k publikování nabídky virtuálních počítačích.
2. Vytvořte novou skupinu prostředků a zadejte **název skupiny prostředků**, **Odběr**a **umístění skupiny prostředků**. Podrobnosti naleznete v [tématu Správa prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Vyberte **Virtuální počítače** vlevo, chcete-li zobrazit stránku Podrobnosti o virtuálních počítačích.
4. Výběrem **možnosti + přidat** otevřete **prostředí vytvoření virtuálního počítače**.
5. Vyberte obrázek z rozevíracího seznamu nebo klikněte na **Procházet všechny veřejné a soukromé obrázky** a vyhledejte nebo procházejte všechny dostupné image virtuálních strojů.
6. Vyberte velikost virtuálního počítače, který chcete nasadit, pomocí následujících doporučení:
    * Pokud plánujete vyvíjet virtuální pevný disk místně, na velikosti nezáleží. Zvažte použití jednoho z menších virtuálních zařízení.
    * Pokud máte v plánu rozvíjet image v Azure, zvažte použití jedné z doporučených velikostí virtuálních počítačů pro vybranou bitovou kopii.

7. V části **Disky** rozbalte část **Upřesnit** a nastavte možnost **Použít spravované disky** na **ne**.
8. Zadejte další požadované podrobnosti k vytvoření virtuálního účtu.
9. Chcete-li zkontrolovat své volby, vyberte **Zkontrolovat + vytvořit.** Když se zobrazí zpráva **Ověření předáno,** vyberte **vytvořit**.

Azure začne zřizování virtuálního počítače, který jste zadali. Jeho průběh můžete sledovat výběrem karty **Virtuální počítače** vlevo. Po vytvoření se stav změní na **Spuštěno**.

Pokud narazíte na potíže s vytvářením nového virtuálního pevného disku založeného na Azure, přečtěte si [téma Běžné problémy při vytváření virtuálního pevného disku (časté otázky).](https://aka.ms/VHDcreationIssues)

### <a name="connect-to-your-azure-vm"></a>Připojení k virtuálnímu počítači Azure

Tato část vysvětluje, jak se připojit k virtuálnímu počítači, který jste vytvořili v Azure, a přihlásit se k němu. Po úspěšném připojení můžete s virtuálním serverem pracovat, jako byste byli místně přihlášeni k hostitelskému serveru.

#### <a name="connect-to-a-windows-based-vm"></a>Připojení k virtuálnímu virtuálnímu virtuálnímu mněmu založenému na Windows

Pomocí klienta vzdálené plochy se můžete připojit k virtuálnímu počítači založenému na Windows hostovaném v Azure. Většina verzí systému Windows nativně obsahuje podporu protokolu vzdálené plochy (RDP). Pro ostatní operační systémy naleznete další informace o klientech v [klientech vzdálené plochy](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Tento článek podrobně popisuje, jak používat integrovanou podporu protokolu RDP systému Windows pro připojení k virtuálnímu počítači: [Jak se připojit a přihlásit k virtuálnímu počítači Azure se systémem Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Během procesu se mohou dostat upozornění zabezpečení. Například upozornění jako "Soubor RDP pochází od neznámého vydavatele" nebo "Pověření uživatele nelze ověřit." Je bezpečné ignorovat tato upozornění.

#### <a name="connect-to-a-linux-based-vm"></a>Připojení k virtuálnímu počítači založenému na Linuxu

Chcete-li se připojit k virtuálnímu počítači založenému na Linuxu, potřebujete klienta zabezpečeného shellového protokolu (SSH). Následující kroky používají bezplatný terminál [PuTTY](https://www.ssh.com/ssh/putty/) SHH.

1. Přejděte na [portál Azure](https://ms.portal.azure.com/).
2. Vyhledejte a vyberte **virtuální počítače**.
3. Vyberte virtuální ms, ke kterému se chcete připojit.
4. Spusťte virtuální ho, pokud už není spuštěný.
5. Vyberte název virtuálního virtuálního soudu a otevřete jeho stránku **Přehled.**
6. Všimněte si veřejné IP adresy a názvu DNS virtuálního počítače (pokud tyto hodnoty nejsou nastaveny, musíte [vytvořit síťové rozhraní](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)).
7. Otevřete aplikaci PuTTY.
8. V dialogovém okně Konfigurace PuTTY zadejte IP adresu nebo název DNS virtuálního počítače.

    :::image type="content" source="media/avm-putty.png" alt-text="Ilustruje nastavení terminálu PuTTY. jsou zvýrazněna pole Název hostitele nebo IP adresa a Port.":::

9. Výběrem **možnosti Otevřít** otevřete terminál PuTTY.
10. Po zobrazení výzvy zadejte název účtu a heslo účtu virtuálního počítače s Linuxem.

Pokud máte problémy s připojením, podívejte se do dokumentace pro vašeho klienta SSH. Například [kapitola 10: Běžné chybové zprávy](https://www.ssh.com/ssh/putty/putty-manuals).

Podrobnosti, včetně toho, jak přidat plochu do zřízeného virtuálního počítače s Linuxem, najdete v [tématu Instalace a konfigurace vzdálené plochy pro připojení k virtuálnímu počítači SIP v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop).

## <a name="create-a-vm-using-your-own-image"></a>Vytvoření virtuálního počítače pomocí vlastní image

Tato část popisuje, jak vytvořit a nasadit image virtuálního počítače (VM) poskytované uživatelem. Můžete to provést poskytnutím operačního systému a datového disku v hd image z virtuálního pevného disku (VHD) nasazeného v Azure.

> [!NOTE]
> Chcete-li volitelně použít schválenou základní bitovou kopii, postupujte podle pokynů v [části Vytvoření bitové kopie virtuálního aplikace pomocí schválené základny](#create-a-vm-image-using-an-approved-base).

1. Nahrajte své obrázky do účtu Azure Storage.
2. Nasazení image virtuálního mísy.
3. Zachyťte image virtuálního aplikace.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Nahrání obrázků do účtu úložiště Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Nahrajte své virtuální pevné disky a virtuální disky datového disku z generalizovaného operačního systému do svého účtu úložiště Azure.

### <a name="deploy-your-image"></a>Nasazení bitové kopie

Vytvořte si image pomocí portálu Azure nebo Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Nasazení s využitím webu Azure Portal

1. Na domovské stránce vyberte **Vytvořit prostředek**, vyhledejte "Nasazení šablony" a vyberte **Vytvořit**.
2. V editoru zvolte **Vytvořit vlastní šablonu**.

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Ilustruje stránku Vlastní nasazení.":::

3. Vložte tuto [šablonu JSON](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template) do editoru a vyberte **Uložit**.
4. Zadejte hodnoty parametrů pro zobrazené vlastní stránky vlastností **nasazení.**

    | Parametr | Popis |
    | ------------ | ------------- |
    | Název účtu uživatelského úložiště | Obsah z buňky 2 |
    | Název kontejneru úložiště uživatelů | Název účtu úložiště, kde se nachází generalizovaný virtuální pevný disk |
    | Název DNS pro veřejnou IP adresu | Veřejný název DNS IP. Definujte název DNS pro veřejnou IP adresu na webu Azure Portal po nasazení nabídky. |
    | Uživatelské jméno správce | Uživatelské jméno účtu správce pro nový virtuální virtuální účet |
    | Heslo správce | Heslo účtu správce pro nový virtuální účet |
    | OS Type | Operační systém VM: Windows nebo Linux |
    | ID předplatného | Identifikátor vybraného předplatného |
    | Umístění | Zeměpisná poloha nasazení |
    | Velikost virtuálního počítače | [Velikost virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), například Standard_A2 |
    | Název veřejné IP adresy | Název vaší veřejné IP adresy |
    | Název virtuálního počítače | Název nového virtuálního soudu |
    | Název virtuální sítě | Název virtuální sítě používané virtuálním počítačem |
    | Název nepřenosné_nekvi | Název karty síťového rozhraní s virtuální sítí |
    | Adresa URL virtuálního disku | Úplná adresa URL virtuálního pevného disku operačního systému |
    |  |  |

5. Po zadání těchto hodnot vyberte **možnost Koupit**.

Azure začne nasazovat. Vytvoří nový virtuální virtuální účet se zadaným nespravovaným virtuálním pevným diskem v cestě k účtu zadaného úložiště. Průběh na webu Azure Portal můžete sledovat výběrem **virtuálních počítačů** na levé straně portálu. Když se vytvoří virtuální hod, stav se změní ze spuštění na spuštěno.

#### <a name="deploy-using-azure-powershell"></a>Nasazení s využitím Azure PowerShellu

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>Zachycení image virtuálního mísa

Použijte následující pokyny, které odpovídají vašemu přístupu:

* Azure PowerShell: [Jak vytvořit nespravovanou image virtuálního počítače z virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* Azure CLI: [Jak vytvořit image virtuálního počítače nebo virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API: [Virtuální počítače – digitalizace](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>Konfigurace virtuálního počítače

Tato část popisuje, jak velikost, aktualizaci a generalizaci virtuálního počítače Azure. Tyto kroky jsou nezbytné k přípravě virtuálního počítače k nasazení na Azure Marketplace.

### <a name="sizing-the-vhds"></a>Dimenzování virtuálních diod

Pokud jste vybrali jeden z virtuálních počítačů předem nakonfigurovaných s operačním systémem (a volitelně další služby), už jste vybrali standardní velikost virtuálních počítačových služeb Azure. Spuštění řešení s předkonfigurovaným osem je doporučený přístup. Pokud však instalujete operační systém ručně, musíte velikost primárního virtuálního pevného disku v bitové kopii virtuálního počítače:

* Pro Windows by měl být virtuální pevný disk operačního systému vytvořen jako vhd v pevném formátu 127–128 GB.
* Pro Linux by měl být tento virtuální pevný disk vytvořen jako virtuální pevný disk s kapacitou 30–50 GB.

Pokud je fyzická velikost menší než 127–128 GB, měl by být virtuální pevný disk rozšiřitelný (řídký/dynamický). Základní bitové kopie systému Windows a SERVERU SQL Server, které jsou k dispozici, již tyto požadavky splňují, takže neměňte formát ani velikost virtuálního pevného disku.

Datové disky mohou být až 1 TB. Při rozhodování o velikosti mějte na paměti, že zákazníci nemohou změnit velikost virtuálních dispon ů v rámci bitové kopie v době nasazení. Vdisky datových disků by měly být vytvořeny jako virtuální pevné visuty. Měly by být také rozšiřitelné (řídké /dynamické). Datové disky mohou být zpočátku prázdné nebo mohou obsahovat data.

### <a name="install-the-most-current-updates"></a>Instalace nejaktuálnějších aktualizací

Základní image virtuálních aplikací operačního systému musí obsahovat nejnovější aktualizace až do data publikování. Před publikováním vytvořeného virtuálního pevného disku operačního systému se ujistěte, že aktualizujete operační systém a všechny nainstalované služby pomocí všech nejnovějších oprav zabezpečení a údržby.

V systému Windows Server spusťte příkaz **Vyhledat aktualizace.**

Pro linuxové distribuce jsou aktualizace běžně stahovány a instalovány prostřednictvím nástroje příkazového řádku nebo grafického nástroje. Například Ubuntu Linux poskytuje příkaz [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) a nástroj [Správce aktualizací](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) pro aktualizaci operačního systému.

### <a name="perform-additional-security-checks"></a>Provedení dalších kontrol zabezpečení

Udržujte vysokou úroveň zabezpečení pro image řešení na Azure Marketplace. Následující článek obsahuje kontrolní seznam konfigurací zabezpečení a postupů, které vám pomohou: [Doporučení zabezpečení pro image Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Některá z těchto doporučení jsou specifická pro bitové kopie založené na Linuxu, ale většina se vztahuje na libovolnou image virtuálního počítače.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Provádění vlastní konfigurace a naplánovaných úloh

Pokud je potřeba další konfigurace, použijte naplánovanou úlohu, která se spustí při spuštění, k provádění konečných změn virtuálního počítače po jeho nasazení. Zvažte také následující doporučení:

* Pokud se jedná o úlohu spouštění jednou, měla by se po úspěšném dokončení odstranit sama.
* Konfigurace by neměly spoléhat na jiné jednotky než C nebo D, protože pouze tyto dvě jednotky jsou vždy zaručena existovat (jednotka C je disk operačního systému a jednotka D je dočasný místní disk).

Další informace o přizpůsobení Linuxu najdete v [tématu Rozšíření virtuálních strojů a funkce pro Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Zobecnění obrázku

Všechny image na Azure Marketplace musí být opakovaně použitelné obecným způsobem. K dosažení tohoto cíle musí být zobecněn operační systém VHD, operace, která odebere všechny identifikátory specifické pro instanci a softwarové ovladače z virtuálního počítače.

### <a name="windows"></a>Windows

Disky operačního systému Windows jsou zobecněny [pomocí nástroje sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Pokud následně aktualizujete nebo překonfigurujete operační systém, je nutné znovu spustit sysprep.

> [!WARNING]
> Vzhledem k tomu, že aktualizace může spustit automaticky, po spuštění sysprep, vypněte virtuální ho, dokud jeho nasazení. Tímto vypnutím zabráníte tomu, aby následné aktualizace nedělaly změny operačního systému nebo nainstalované služby specifické pro instance. Další informace o spuštění sysprepu naleznete v [tématu Kroky k generalizaci virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="linux"></a>Linux

Následující proces zobecnizuje virtuální počítač s Linuxem a znovu jej nasadí jako samostatný virtuální počítač. Podrobnosti najdete v [tématu Jak vytvořit bitovou kopii virtuálního počítače nebo virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). Můžete zastavit, když se dostanete do sekce "Vytvořit virtuální hod ze zachycené image".

1. **Odebrání agenta Azure Linuxu**

    1. Připojte se k virtuálnímu počítači s Linuxem pomocí klienta SSH.
    2. V okně SSH zadejte následující `sudo waagent -deprovision+user`příkaz: .
    3. Chcete-li pokračovat, zadejte **y** (můžete přidat parametr **-force** k předchozímu příkazu, abyste se vyhnuli kroku potvrzení).
    d. Po dokončení příkazu zadejte **Exit** zavřete klienta SSH.

2. **Zastavení virtuálního počítače**

    1. Na webu Azure Portal vyberte skupinu prostředků (RG) a zrušte přidělení virtuálního počítače.
    2. Váš virtuální pevný disk je teď zobecněný a pomocí tohoto virtuálního počítače můžete vytvořit nový virtuální počítač.

## <a name="next-steps"></a>Další kroky

Pokud jste zjistili potíže s vytvořením nového virtuálního pevného disku založeného na Azure, přečtěte [si téma Běžné problémy při vytváření virtuálního pevného disku](https://aka.ms/AzureVM_VHDCreationFAQ).

V opačném případě:

* [Certifikujte image virtuálního počítače](https://aks.ms/CertifyVMimage) vysvětluje, jak otestovat a odeslat image virtuálního počítače pro certifikaci Azure Marketplace, včetně toho, kde získat *nástroj Certifikace Test Tool pro Azure Certified* a jak ho použít k certifikaci image virtuálního počítače.
