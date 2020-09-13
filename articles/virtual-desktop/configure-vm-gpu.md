---
title: Konfigurace GPU pro virtuální počítače s Windows – Azure
description: Jak povolit urychlené vykreslování a kódování GPU na virtuálním počítači s Windows
author: gundarev
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 33b8d3f62ef45c6078f10535c6376f611472f5a2
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441744"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Konfigurace akcelerace grafického procesoru (GPU) pro Windows Virtual Desktop

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/configure-vm-gpu-2019.md).

Virtuální počítač s Windows podporuje vykreslování a kódování GPU pro lepší výkon a škálovatelnost aplikace. Akcelerace GPU je zvláště důležitá pro aplikace náročné na grafiku.

Podle pokynů v tomto článku vytvořte virtuální počítač Azure optimalizovaný pro GPU, přidejte ho do fondu hostitelů a nakonfigurujte ho tak, aby používal akceleraci GPU pro vykreslování a kódování. V tomto článku se předpokládá, že už máte nakonfigurovaného tenanta virtuálních klientů s Windows.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Vyberte velikost virtuálního počítače Azure optimalizované grafickým procesorem.

Azure nabízí řadu [velikostí virtuálních počítačů](/azure/virtual-machines/windows/sizes-gpu), které jsou optimalizované pro GPU. Správná volba pro fond hostitelů závisí na mnoha faktorech, včetně konkrétních aplikačních úloh, požadované kvality uživatelského prostředí a nákladů. Celkově větší a více schopných GPU nabízí lepší uživatelské prostředí při dané hustotě uživatelů.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Vytvoření fondu hostitelů, zřízení virtuálního počítače a konfigurace skupiny aplikací

Vytvořte nový fond hostitelů pomocí virtuálního počítače zvolené velikosti. Pokyny najdete v tématu [kurz: Vytvoření fondu hostitelů pomocí Azure Portal](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Virtuální počítač s Windows podporuje vykreslování a kódování GPU v následujících operačních systémech:

* Windows 10 verze 1511 nebo novější
* Windows Server 2016 nebo novější

Musíte taky nakonfigurovat skupinu aplikací nebo použít výchozí skupinu desktopových aplikací (nazvanou "skupina desktopových aplikací"), která se automaticky vytvoří při vytváření nového fondu hostitelů. Pokyny najdete v tématu [kurz: Správa skupin aplikací pro virtuální počítač s Windows](/azure/virtual-desktop/manage-app-groups).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instalace podporovaných grafických ovladačů ve vašem virtuálním počítači

Pokud chcete využít výhod schopností GPU virtuálních počítačů Azure N-Series na virtuálním počítači s Windows, musíte nainstalovat příslušné ovladače grafiky. Podle pokynů v části [podporované operační systémy a ovladače](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers) nainstalujte ovladače od příslušného dodavatele grafiky, a to buď ručně, nebo pomocí rozšíření virtuálního počítače Azure.

Pro virtuální počítače s Windows se podporují jenom ovladače distribuované pomocí Azure. U virtuálních počítačů Azure s grafickými procesory NVIDIA se navíc podporují jenom [ovladače NVIDIA gridu](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) pro virtuální počítače s Windows.

Po instalaci ovladače se vyžaduje restartování virtuálního počítače. Pomocí kroků pro ověření výše uvedených pokynů potvrďte, že ovladače grafiky byly úspěšně nainstalovány.

## <a name="configure-gpu-accelerated-app-rendering"></a>Konfigurace vykreslování aplikace akcelerované GPU

Ve výchozím nastavení se aplikace a počítače běžící v konfiguracích s více relacemi vykreslují s využitím procesoru a nevyužívají k vykreslování dostupné GPU. Nakonfigurujte Zásady skupiny pro hostitele relací, aby bylo možné povolit vykreslování akcelerované GPU:

1. Připojte se k ploše virtuálního počítače pomocí účtu s oprávněními místního správce.
2. Otevřete nabídku Start a zadáním příkazu gpedit. msc otevřete Editor Zásady skupiny.
3. Přejděte ke stromu **Konfigurace počítače**  >  **šablony pro správu**  >  **součásti systému Windows**  >  **Vzdálená plocha**  >  **hostitel relace vzdálené plochy**  >  **prostředí vzdálené**plochy.
4. Vyberte zásady **použití hardwarových grafických adaptérů pro všechny relace vzdálené plochy** a nastavte tuto zásadu na **povoleno** , pokud chcete povolit vykreslování GPU ve vzdálené relaci.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Konfigurace kódování rámce akcelerovaného GPU

Vzdálená plocha zakóduje všechny grafiky vygenerované aplikacemi a plochami (ať už vygenerované pomocí GPU nebo s využitím procesoru) pro přenos do klientů vzdálené plochy. Pokud se část obrazovky často aktualizuje, Tato část obrazovky se zakóduje pomocí kodeku videa (H. 264/AVC). Ve výchozím nastavení Vzdálená plocha nevyužívá k tomuto kódování dostupné GPU. Nakonfigurujte Zásady skupiny pro hostitele relací, aby bylo možné povolit kódování rámce GPU. Pokračuje se výše uvedenými kroky:

>[!NOTE]
>Pro virtuální počítače řady NVv4-series není k dispozici kódování rámců podporující GPU.

1. Vyberte zásady **Konfigurace H. 264/AVC hardwarového kódování pro připojení ke vzdálené ploše** a nastavte tuto zásadu na **povoleno** , pokud chcete povolit kódování hardwaru pro AVC/H. 264 ve vzdálené relaci.

    >[!NOTE]
    >V systému Windows Server 2016 nastavte možnost **PREFEROVAT AVC hardwarové kódování** , aby se **vždy pokoušelo o pokus**.

2. Teď, když jste upravili zásady skupiny, vynuťte aktualizaci zásad skupiny. Otevřete příkazový řádek a zadejte:

    ```cmd
    gpupdate.exe /force
    ```

3. Odhlaste se z relace vzdálené plochy.

## <a name="configure-fullscreen-video-encoding"></a>Nakonfigurovat kódování videa na celé obrazovce

Pokud často používáte aplikace, které vytváří vysoce snímkový obsah, jako jsou 3D modelování, CAD a video aplikace, můžete pro vzdálenou relaci Povolit kódování na celé obrazovce. Profil videa na celé obrazovce poskytuje vyšší kmitočet snímků a lepší uživatelské prostředí pro tyto aplikace na úkor šířky pásma sítě a hostitele relací a prostředků klientů. Pro kódování videa na celé obrazovce se doporučuje používat kódování rámců GPU-Accelerated. Nakonfigurujte Zásady skupiny pro hostitele relace, aby se povolilo kódování videa na celé obrazovce. Pokračuje se výše uvedenými kroky:

1. **Pro připojení ke vzdálené ploše vyberte možnost nastavit prioritu zásad v grafickém režimu H. 264/avc 444** a nastavte tuto zásadu na **povoleno** , aby ve vzdálené relaci vynutila kodek H. 264/AVC 444.
2. Teď, když jste upravili zásady skupiny, vynuťte aktualizaci zásad skupiny. Otevřete příkazový řádek a zadejte:

    ```cmd
    gpupdate.exe /force
    ```

3. Odhlaste se z relace vzdálené plochy.
## <a name="verify-gpu-accelerated-app-rendering"></a>Ověření vykreslování aplikace akcelerované GPU

Pokud chcete ověřit, jestli aplikace používají GPU k vykreslování, zkuste použít některou z těchto možností:

* Pro virtuální počítače Azure s grafickým procesorem NVIDIA použijte `nvidia-smi` nástroj, jak je popsáno v tématu [ověření instalace ovladače](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) a kontrola využití GPU při spouštění aplikací.
* V podporovaných verzích operačních systémů můžete použít Správce úloh ke kontrole využití GPU. Vyberte GPU na kartě výkon, abyste viděli, jestli aplikace využívají GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Ověřit kódování rámce akcelerované GPU

Ověření, že Vzdálená plocha používá kódování GPU-akcelerované:

1. Připojte se k ploše virtuálního počítače pomocí klienta virtuální plochy Windows.
2. Spusťte prohlížeč událostí a přejděte do následujícího uzlu: **protokoly aplikací a služeb**  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **Operational**
3. Pokud chcete zjistit, jestli se používá kódování GPU, hledejte událost s ID 170. Pokud se zobrazí zpráva "je povolen hardwarový kodér AVC: 1", použije se kódování GPU.

## <a name="verify-fullscreen-video-encoding"></a>Ověřit kódování videa na celé obrazovce

Ověření, že Vzdálená plocha používá celé toto video kódování:

1. Připojte se k ploše virtuálního počítače pomocí klienta virtuální plochy Windows.
2. Spusťte prohlížeč událostí a přejděte do následujícího uzlu: **protokoly aplikací a služeb**  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **Operational**
3. Pokud chcete zjistit, jestli se používá kódování videa na celé obrazovce, vyhledejte událost s ID 162. Pokud se zobrazí možnost AVC je k dispozici: 1 počáteční profil: 2048, použije se AVC 444.

## <a name="next-steps"></a>Další kroky

Tyto pokyny by měly být v provozu s akcelerací GPU na jednom hostiteli relace (jeden virtuální počítač). Některé další předpoklady pro povolení akcelerace GPU napříč větším fondem hostitelů:

* Zvažte použití [rozšíření virtuálního počítače](/azure/virtual-machines/extensions/overview) pro zjednodušení instalace a aktualizace ovladačů v rámci několika virtuálních počítačů. Pro virtuální počítače s grafickými procesory NVIDIA použijte [rozšíření ovladače GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) a použijte [rozšíření ovladače AMD GPU](/azure/virtual-machines/extensions/hpccompute-amd-gpu-windows) pro virtuální počítače s grafickými procesory AMD.
* Zvažte použití služby Active Directory Zásady skupiny ke zjednodušení konfigurace zásad skupiny pro celou řadu virtuálních počítačů. Informace o nasazení Zásady skupiny v doméně služby Active Directory naleznete v tématu [Working with zásady skupiny Objects](https://go.microsoft.com/fwlink/p/?LinkId=620889).
