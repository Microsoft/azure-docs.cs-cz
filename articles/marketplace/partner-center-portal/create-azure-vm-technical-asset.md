---
title: Vytváření technických prostředků pro nabídku Azure Marketplace virtuálních počítačů
description: Naučte se vytvářet a konfigurovat technické prostředky pro nabídku virtuálního počítače pro Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: ec043c97da8056fee4af8f6ead84f4df05c0f304
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079016"
---
# <a name="create-technical-assets-for-an-azure-marketplace-virtual-machine-offer"></a>Vytváření technických prostředků pro nabídku Azure Marketplace virtuálních počítačů

Když publikujete image virtuálních počítačů do Azure Marketplace, tým Azure ověří image virtuálního počítače, aby zajistila, že se jedná o možnosti spouštění, zabezpečení a kompatibility Azure. Pokud některý z testů s vysokou kvalitou selže, publikování se nezdaří a zobrazí se zpráva obsahující chybu a možné [kroky k nápravě](https://docs.microsoft.com/azure/marketplace/partner-center-portal/vm-certification-issues-solutions).

Tento článek popisuje, jak vytvořit a nakonfigurovat technické prostředky pro nabídku virtuálního počítače pro Azure Marketplace. Virtuální počítač obsahuje dvě součásti: virtuální pevné disky s operačním systémem (VHD) a volitelné přidružené datové disky VHD:

- **Virtuální pevný disk operačního systému**: obsahuje operační systém a řešení, které se nasadí s vaší nabídkou. Proces přípravy VHD se liší v závislosti na tom, jestli se jedná o virtuální počítač založený na platformě Linux, Windows nebo na vlastním virtuálním počítači.

- **Virtuální pevné disky datových disků**: vyhrazené a trvalé úložiště pro virtuální počítač. Nepoužívejte virtuální pevný disk operačního systému (například jednotku C:) k ukládání trvalých informací.

Image virtuálního počítače obsahuje jeden disk s operačním systémem a až 16 datových disků. Použijte jeden virtuální pevný disk na datový disk, a to i v případě, že je disk prázdný.

> [!NOTE]
> Bez ohledu na to, jaký operační systém používáte, přidejte jenom minimální počet datových disků, které řešení potřebuje. Zákazníci nemohou odebrat disky, které jsou součástí bitové kopie v době nasazení, ale mohou vždy přidávat disky během nebo po nasazení.

> [!IMPORTANT]
> Každá image virtuálního počítače v plánu musí mít stejný počet datových disků.

## <a name="fundamental-technical-knowledge"></a>Základní technické poznatky

Návrh, sestavování a testování těchto assetů trvá čas a vyžaduje technické znalosti platformy Azure a technologií, které slouží k sestavování této nabídky. Kromě vaší domény řešení by váš technický tým měl mít znalosti o těchto technologiích společnosti Microsoft:

- Základní porozumění [službám Azure](https://azure.microsoft.com/services/)
- [Návrh a architekt aplikací Azure](https://azure.microsoft.com/solutions/architecture/)
- Praktické znalosti o sítích [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage)a [Azure](https://azure.microsoft.com/services/?filter=networking)
- Praktické znalosti [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Praktické znalosti formátu [JSON](https://www.json.org/)

### <a name="optional-suggested-tools"></a>Volitelné navrhované nástroje

Zvažte použití jednoho z následujících skriptovacích prostředí, které vám pomůžou spravovat virtuální počítače a virtuální pevné disky:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://code.visualstudio.com/)

Kromě toho zvažte přidání následujících nástrojů do vývojového prostředí:

- [Průzkumník služby Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-vm-image-using-an-approved-base"></a>Vytvoření image virtuálního počítače pomocí schválené základní třídy

Pokud chcete vytvořit technické prostředky virtuálních počítačů pomocí Image, kterou jste vytvořili v místním prostředí, přečtěte si téma [Vytvoření image virtuálního počítače pomocí níže schválené základní](#create-a-vm-image-using-an-approved-base) třídy.

Tato část popisuje různé aspekty používání schválené základny, jako je použití protokol RDP (Remote Desktop Protocol) (RDP), výběr velikosti virtuálního počítače, instalace nejnovějších aktualizací Windows a generalizace image VHD.

Postupujte podle pokynů v tomto článku a použijte Azure k vytvoření virtuálního počítače, který obsahuje předem nakonfigurovaný, schválený operační systém. Pokud to není kompatibilní s vaším řešením, je možné vytvořit a nakonfigurovat místní virtuální počítač pomocí schváleného operačního systému. Pak ji můžete nakonfigurovat a připravit pro nahrání, jak je popsáno v tématu [Příprava virtuálního pevného disku (VHD) Windows nebo VHDX pro nahrání do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base-image"></a>Výběr schválené základní image

Jako základ vyberte buď operační systém Windows, nebo Linux.

Virtuální pevný disk operačního systému pro vaši image virtuálního počítače musí být založený na základní imagi schválené pro Azure, která obsahuje Windows Server nebo SQL Server.

Když odešlete žádost o opětovné publikování image s aktualizacemi, může testovací případ na částečný počet selhat. V takovém případě nebude vaše image schválená.

K této chybě dojde, pokud jste použili základní image, která patří jinému vydavateli a aktualizovali jste bitovou kopii. V takovém případě nebudete moci publikovat vaši image.

Pokud chcete tento problém vyřešit, načtěte nejnovější image z Azure Marketplace a proveďte v této imagi změny. Pro zobrazení schválených základních imagí, kde můžete vyhledat svou image, se podívejte na tyto informace:

#### <a name="windows"></a>Windows

- Windows Server ([2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 R2 datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
- SQL Server 2019 ([Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [Standard](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [Web](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview))
- SQL Server 2014 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))
- SQL Server 2012 SP2 ([Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))

#### <a name="linux"></a>Linux

Azure nabízí řadu schválených distribucí Linux. Aktuální seznam najdete v tématu [Linux v distribucích, které jsou schváleny v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-on-the-azure-portal"></a>Vytvoření virtuálního počítače na Azure Portal

Pomocí těchto kroků vytvořte na [Azure Portal](https://ms.portal.azure.com/)základní image virtuálního počítače:

1. Přihlaste se k [Azure Portal](https://ms.portal.azure.com/) pomocí účet Microsoft přidruženého k předplatnému Azure, které chcete použít k publikování nabídky virtuálních počítačů.
2. Vytvořte novou skupinu prostředků a zadejte **název skupiny prostředků**, **předplatné**a **umístění skupiny prostředků**. Podrobnosti najdete v tématu [Správa prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    :::image type="content" source="media/vm/create-resource-group.png" alt-text="Zobrazuje začátek vytváření skupiny prostředků.":::

3. Vyberte **virtuální počítače** na levém navigačním panelu, aby se zobrazila stránka s podrobnostmi o virtuálních počítačích.
4. Výběrem **+ Přidat** otevřete **prostředí vytvořit virtuální počítač**.
5. Vyberte obrázek z rozevíracího seznamu nebo vyberte **Procházet všechny veřejné a soukromé image** , abyste mohli vyhledávat nebo procházet všechny dostupné image virtuálních počítačů. Příklad:

    :::image type="content" source="media/vm/create-resource-group-example.png" alt-text="Zobrazuje začátek vytváření skupiny prostředků.":::

6. Vyberte velikost virtuálního počítače, který chcete nasadit, a to pomocí následujících doporučení:
    1. Pokud plánujete vývoj virtuálního pevného disku v místním prostředí, nezáleží na velikosti. Zvažte použití jednoho z menších virtuálních počítačů.
    2. Pokud plánujete vývoj image v Azure, zvažte použití jedné z doporučených velikostí virtuálních počítačů pro vybranou bitovou kopii.

    :::image type="content" source="media/vm/create-virtual-machine.png" alt-text="Zobrazuje začátek vytváření skupiny prostředků.":::

7. V části **disky** rozbalte oddíl **Upřesnit** a nastavte možnost **použít spravované disky** na **ne**.

    :::image type="content" source="media/vm/use-managed-disks.png" alt-text="Zobrazuje začátek vytváření skupiny prostředků.":::

8. Zadejte další požadované podrobnosti pro vytvoření virtuálního počítače.
9. Vyberte **zkontrolovat + vytvořit** a zkontrolujte své volby. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně**, vyberte **Vytvořit**.

Azure zahájí zřizování virtuálního počítače, který jste zadali. Svůj průběh můžete sledovat tak, že na levé straně vyberete kartu **Virtual Machines** . Po vytvoření se stav změní na **spuštěno**.

### <a name="create-a-generation-2-vm-on-the-azure-portal"></a>Vytvoření virtuálního počítače 2. generace na Azure Portal

Vytvořte virtuální počítač 2. generace (Gen2) v Azure Portal.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).
2. Vyberte **Vytvořit prostředek**.
3. Na levé straně vyberte **Zobrazit vše** z Azure Marketplace.
4. Vyberte bitovou kopii, která podporuje Gen2.
5. Vyberte **Vytvořit**.
6. Na kartě **Upřesnit** v části **generování virtuálního počítače** vyberte možnost **Obecné 2** .
7. Na kartě **základy** klikněte v části **Podrobnosti instance**na **Velikost** a otevřete okno **Vybrat velikost virtuálního počítače** .
8. Vyberte doporučenou velikost [podporovaného virtuálního počítače](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2#generation-2-vm-sizes) a velikosti 2. generace.
9. Pokud chcete dokončit vytváření virtuálního počítače, Projděte si [tok vytváření Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) .

    :::image type="content" source="media/vm/vm-generation.png" alt-text="Zobrazuje začátek vytváření skupiny prostředků.":::

## <a name="connect-to-your-azure-vm"></a>Připojení k VIRTUÁLNÍmu počítači Azure

V této části se dozvíte, jak se připojit a přihlásit k virtuálnímu počítači, který jste vytvořili v Azure. Po úspěšném připojení můžete s virtuálním počítačem pracovat jako v případě, že jste byli místně přihlášení k hostitelskému serveru.

### <a name="connect-to-a-windows-based-vm"></a>Připojení k virtuálnímu počítači se systémem Windows

Použijte klienta vzdálené plochy pro připojení k virtuálnímu počítači se systémem Windows hostovanému v Azure. Většina verzí Windows nativně obsahuje podporu protokolu RDP (Remote Desktop Protocol). Pro jiné operační systémy můžete najít další informace o klientech v [klientech vzdálené plochy](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Tento článek podrobně popisuje, jak použít integrovanou podporu Windows RDP pro připojení k vašemu VIRTUÁLNÍmu počítači: [jak se připojit a přihlásit k virtuálnímu počítači Azure s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Během procesu můžete obdržet upozornění zabezpečení. Například upozornění, například "soubor. RDP pochází z neznámého vydavatele" nebo "vaše uživatelská pověření nelze ověřit". Tato upozornění je bezpečné ignorovat.

### <a name="connect-to-a-linux-based-vm"></a>Připojení k virtuálnímu počítači se systémem Linux

K připojení k virtuálnímu počítači se systémem Linux budete potřebovat klienta SSH (Secure Shell Protocol). V následujících krocích se používá [bezplatná terminálu](https://www.ssh.com/ssh/putty/) SSH.

1. Přejděte na web [Azure Portal](https://ms.portal.azure.com/).
2. Vyhledejte a vyberte virtuální počítače.
3. Vyberte virtuální počítač, ke kterému se chcete připojit.
4. Spusťte virtuální počítač, pokud ještě není spuštěný.
5. Vyberte název virtuálního počítače, pro který chcete otevřít stránku s přehledem.
6. Poznamenejte si veřejnou IP adresu a název DNS vašeho virtuálního počítače (pokud tyto hodnoty nejsou nastavené, musíte [vytvořit síťové rozhraní](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface).
7. Otevřete aplikaci pro výstup.
8. V dialogovém okně Konfigurace výstupu zadejte IP adresu nebo název DNS vašeho virtuálního počítače.

    :::image type="content" source="media/vm/putty-configuration.png" alt-text="Zobrazuje začátek vytváření skupiny prostředků.":::

9. Vyberte **otevřít** a otevřete terminál k výstupu.
10. Po zobrazení výzvy zadejte název účtu a heslo účtu virtuálního počítače se systémem Linux.

## <a name="configure-the-virtual-machine"></a>Konfigurace virtuálního počítače

Tato část popisuje, jak změnit velikost, aktualizovat a zobecnit virtuální počítač Azure. Tyto kroky jsou nezbytné k přípravě nasazení virtuálního počítače na Azure Marketplace.

### <a name="sizing-the-vhds"></a>Změna velikosti virtuálních pevných disků

Následující pravidla platí pro omezení velikosti disku s operačním systémem. Když odešlete jakoukoli žádost, ujistěte se, že velikost disku operačního systému je v rámci omezení pro Linux nebo Windows.

| Operační systém | Doporučená velikost VHD |
| --- | --- |
| Linux | 30 až 1023 GB |
| Windows | 30 až 250 GB |

Vzhledem k tomu, že virtuální počítače umožňují přístup k základnímu operačnímu systému, zajistěte, aby byl virtuální pevný disk dostatečně velký. Vzhledem k tomu, že disky nejsou rozšiřitelné bez výpadků, použijte velikost disku v rozmezí 30 až 50 &nbsp; GB.

| Velikost virtuálního pevného disku | Skutečná obsazená velikost | Řešení |
| --- | --- | --- |
| >500 TB | neuvedeno | Obraťte se na tým podpory se schválením výjimky. |
| 250-500 TB | >200 GB se liší od velikosti objektu BLOB | Obraťte se na tým podpory se schválením výjimky. |

### <a name="install-the-most-current-updates"></a>Nainstalovat nejaktuálnější aktualizace

Základní image virtuálních počítačů s operačním systémem musí obsahovat nejnovější aktualizace až do data publikování. Před publikováním virtuálního pevného disku s operačním systémem, který jste vytvořili, se ujistěte, že aktualizujete operační systém a všechny nainstalované služby se všemi nejnovějšími opravami zabezpečení a údržby.

- Pro Windows Server spusťte příkaz Check for Updates.
- U distribucí pro Linux se aktualizace běžně stahují a instalují prostřednictvím nástroje příkazového řádku nebo grafického nástroje. Například Ubuntu Linux poskytuje příkaz [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) a nástroj [správce aktualizací](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) pro aktualizaci operačního systému.

#### <a name="perform-additional-security-checks"></a>Provedení dalších kontrol zabezpečení

Udržujte si vysokou úroveň zabezpečení pro obrázky vašich řešení v Azure Marketplace. Kontrolní seznam konfigurací a postupů zabezpečení najdete v tématu [doporučení zabezpečení pro Azure Marketplace image](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images). Některá z těchto doporučení jsou specifická pro image založené na systému Linux, ale většina se vztahují na všechny image virtuálních počítačů.

#### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Provádění vlastních konfigurací a naplánovaných úloh

Pokud potřebujete další konfiguraci, použijte naplánovanou úlohu, která se spustí při spuštění, aby se konečné změny virtuálního počítače provedly po jeho nasazení. Zvažte také následující body:

- Pokud se jedná o úlohu spuštění jednou, je nutné úlohu po úspěšném dokončení odstranit.
- Konfigurace by se neměla spoléhat na jiné jednotky než C nebo D, protože existují jenom tyto dvě jednotky (jednotka C je disk s operačním systémem a jednotka D je dočasný místní disk).

Další informace o úpravách pro Linux najdete v tématu [rozšíření a funkce virtuálních počítačů pro Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).

## <a name="generalize-the-image"></a>Generalizace bitové kopie

Všechny obrázky v Azure Marketplace musí být obecně znovu použitelné. K tomu je potřeba, aby byl virtuální pevný disk operačního systému zobecněný, operace, která odebere všechny identifikátory jednotlivých instancí a softwarové ovladače z virtuálního počítače.

### <a name="for-windows"></a>Pro Windows

Disky s operačním systémem Windows jsou zobecněny pomocí nástroje [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Pokud později aktualizujete nebo znovu nakonfigurujete operační systém, musíte znovu spustit nástroj Sysprep.

> [!WARNING]
> Po spuštění nástroje Sysprep vypněte virtuální počítač, dokud nebude nasazen, protože aktualizace mohou být spouštěny automaticky. Toto vypnutí zabrání následným aktualizacím v provádění změn specifických pro konkrétní instance v operačním systému nebo nainstalovaných službách. Další informace o spuštění nástroje Sysprep najdete v tématu [postup generalizace VHD](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Pro Linux

Následující proces generalizuje virtuální počítač Linux a znovu ho nasadí jako samostatný virtuální počítač. Podrobnosti najdete v tématu [Postup vytvoření image virtuálního počítače nebo virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image). V případě, že se dostanete k části s názvem vytvoření virtuálního počítače ze zaznamenané bitové kopie, můžete zastavit.

1. Odebrat agenta Azure Linux

    1. Připojení k VIRTUÁLNÍmu počítači se systémem Linux pomocí klienta SSH
    2. V okně SSH zadejte následující příkaz: `sudo waagent –deprovision+user` .
    3. Zadejte Y pro pokračování (můžete přidat parametr-Force k předchozímu příkazu, abyste se vyhnuli potvrzovacímu kroku).
    4. Po dokončení příkazu zadejte příkaz exit a zavřete tak klienta SSH.

2. Zastavení virtuálního počítače

    1. V Azure Portal vyberte skupinu prostředků (RG) a zrušte přidělení virtuálního počítače.
    2. Virtuální pevný disk je teď zobecněný a můžete vytvořit nový virtuální počítač pomocí tohoto virtuálního pevného disku.

## <a name="next-steps"></a>Další kroky

- Pokud jste narazili na potíže s vytvářením nového virtuálního pevného disku založeného na Azure, přečtěte si téma [běžné problémy při vytváření VHD](common-issues-during-vhd-creation.md).
- Pokud ne, [testovací virtuální počítač (VM) nasazený z VHD](azure-vm-image-certification.md) vysvětluje, jak otestovat a odeslat image virtuálního počítače pro Azure Marketplace certifikace, včetně toho, kde získat nástroj certifikace pro testování pro Azure Certified a jak ho použít k certifikaci vaší image virtuálního počítače.
