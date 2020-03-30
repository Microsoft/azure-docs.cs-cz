---
title: Konfigurace GPU pro Virtuální plochu Windows – Azure
description: Jak povolit vykreslování a kódování s akcelerací GPU ve virtuální ploše Windows.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 941a1ff23668a3202028e8b693b57d902095b3b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78384819"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Konfigurace akcelerace grafických procesorů (GPU) pro virtuální plochu windows

Virtuální plocha Windows podporuje vykreslování a kódování s akcelerací GPU pro lepší výkon a škálovatelnost aplikací. Akcelerace GPU je obzvláště důležitá pro aplikace náročné na grafiku.

Podle pokynů v tomto článku vytvořte virtuální počítač Azure optimalizovaný pro GPU, přidejte ho do fondu hostitelů a nakonfigurujte ho tak, aby používal akceleraci GPU pro vykreslování a kódování. Tento článek předpokládá, že již máte nakonfigurovaný klienta virtuální plochy Windows.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Výběr velikosti virtuálního počítače Azure optimalizovaného pro GPU

Azure nabízí řadu [velikostí virtuálních strojů optimalizovaných pro GPU](/azure/virtual-machines/windows/sizes-gpu). Správná volba pro hostitelský fond závisí na řadě faktorů, včetně konkrétníúlohy aplikace, požadované kvality uživatelského prostředí a nákladů. Obecně platí, že větší a schopnější gpu nabízejí lepší uživatelské prostředí při dané hustotě uživatele.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Vytvoření fondu hostitelů, zřízení virtuálního počítače a konfigurace skupiny aplikací

Vytvořte nový fond hostitelů pomocí virtuálního počítače o vybrané velikosti. Pokyny najdete [v tématu Kurz: Vytvoření fondu hostitelů s Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Windows Virtual Desktop podporuje vykreslování a kódování s akcelerací GPU v následujících operačních systémech:

* Windows 10 verze 1511 nebo novější
* Windows Server 2016 nebo novější

Musíte také nakonfigurovat skupinu aplikací nebo použít výchozí skupinu aplikací pro stolní počítače (s názvem "Skupina aplikací pro stolní počítače"), která se automaticky vytvoří při vytváření nového fondu hostitelů. Pokyny najdete [v tématu Výuka: Správa skupin aplikací pro Windows Virtual Desktop](/azure/virtual-desktop/manage-app-groups).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instalace podporovaných grafických ovladačů ve virtuálním počítači

Chcete-li využít funkce GPU virtuálních počítačů řady Azure N ve virtuální ploše Windows, je nutné nainstalovat příslušné grafické ovladače. Postupujte podle pokynů na [podporované operační systémy a ovladače](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers) k instalaci ovladačů od příslušného dodavatele grafiky, a to buď ručně nebo pomocí rozšíření virtuálního počítače Azure.

Pro Virtuální plochu Windows jsou podporované jenom ovladače distribuované službou Azure. Navíc pro virtuální počítače Azure s grafickými procesory NVIDIA jsou pro virtuální plochu Windows podporované jenom [ovladače NVIDIA GRID.](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)

Po instalaci ovladače je vyžadováno restartování virtuálního počítače. Pomocí ověřovacích kroků uvedených ve výše uvedených pokynech ověřte, zda byly grafické ovladače úspěšně nainstalovány.

## <a name="configure-gpu-accelerated-app-rendering"></a>Konfigurace vykreslování aplikací s akcelerací GPU

Ve výchozím nastavení jsou aplikace a plochy spuštěné v konfiguracích s více relacemi vykresleny pomocí procesoru a nevyužívají dostupné grafické procesory pro vykreslování. Konfigurace zásad skupiny pro hostitele relace pro povolení vykreslování akcelerovaného GPU:

1. Připojte se k ploše virtuálního počítače pomocí účtu s oprávněními místního správce.
2. Otevřete nabídku Start a zadáním příkazu gpedit.msc otevřete Editor zásad skupiny.
3. Přejděte strom do pole **Šablony pro správu konfigurace** > počítače**Součásti služby** > **Vzdálená plocha** > **služby** > **Vzdálená plocha Hostitel** > **vzdálené relace Vzdálené relace Environment**.
4. Vyberte **zásadu: Použijte výchozí hardwarový grafický adaptér pro všechny relace služby Vzdálená plocha** a nastavte tuto zásadu na **Povoleno,** aby bylo možné povolit vykreslování GPU ve vzdálené relaci.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Konfigurace kódování rámce akcelerovaného gpu

Vzdálená plocha kóduje všechny grafiky vykreslené aplikacemi a plochami (ať už vykreslené pomocí grafického procesoru nebo cpu) pro přenos klientům vzdálené plochy. Ve výchozím nastavení vzdálená plocha nevyužívá dostupné grafické procesory pro toto kódování. Nakonfigurujte zásady skupiny pro hostitele relace tak, aby umožňovaly kódování rámce akcelerované grafickým procesorem. Pokračování výše uvedených kroků:

1. Vyberte zásadu **Priorita grafického režimu H.264/AVC 444 pro připojení ke vzdálené ploše** a nastavte tuto zásadu na **Povoleno** tak, aby ve vzdálené relaci vynucova kodek H.264/AVC 444.
2. Vyberte zásadu **Konfigurace hardwarového kódování H.264/AVC pro připojení ke vzdálené ploše** a nastavte tuto zásadu na **Povoleno,** aby bylo ve vzdálené relaci povoleno hardwarové kódování pro AVC/H.264.

    >[!NOTE]
    >V systému Windows Server 2016 nastavte možnost **Preferovat kódování hardwaru AVC** na **vždy pokus**.

3. Nyní, když byly zásady skupiny upraveny, vynuťte aktualizaci zásad skupiny. Otevřete příkazový řádek a zadejte:

    ```batch
    gpupdate.exe /force
    ```

4. Odhlaste se z relace vzdálené plochy.

## <a name="verify-gpu-accelerated-app-rendering"></a>Ověření vykreslování aplikací s akcelerací GPU

Chcete-li ověřit, zda aplikace používají grafický procesor pro vykreslování, vyzkoušejte některou z následujících možností:

* Pro virtuální počítače Azure s grafickým procesorem NVIDIA použijte `nvidia-smi` nástroj popsaný v části Ověření instalace [ovladače](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) ke kontrole využití GPU při spouštění aplikací.
* V podporovaných verzích operačního systému můžete pomocí Správce úloh zkontrolovat využití GPU. Vyberte GPU na kartě "Výkon", abyste zjistili, zda aplikace používají GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Ověření kódování rámce akcelerované houštinou gpu

Chcete-li ověřit, zda vzdálená plocha používá kódování s akcelerací GPU:

1. Připojte se ke ploše virtuálního počítače pomocí klienta Virtuální desktop windows.
2. Spusťte Prohlížeč událostí a přejděte na následující uzel: **Protokoly** > aplikací a služeb**microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **Provozní**
3. Chcete-li zjistit, zda se používá kódování akcelerované GPU, vyhledejte ID události 170. Pokud se zobrazí "Hardwarový kodér AVC povolen: 1", použije se kódování GPU.
4. Chcete-li zjistit, zda se používá režim AVC 444, vyhledejte ID události 162. Pokud vidíte "AVC K dispozici: 1 Počáteční profil: 2048", pak AVC 444 se používá.

## <a name="next-steps"></a>Další kroky

Tyto pokyny by vás měly zprovoznit s akceleracegpu na jednom hostitelském virtuálním počítači relace. Některé další důležité informace pro povolení akcelerace GPU ve větším hostitelském fondu:

* Zvažte použití [rozšíření virtuálního zařízení](/azure/virtual-machines/extensions/overview) pro zjednodušení instalace a aktualizací ovladačů napříč řadou virtuálních aplikací. Použijte [rozšíření ovladače GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) pro virtuální zařízení s grafickými procesory NVIDIA a použijte rozšíření ovladače GPU AMD (již brzy) pro virtuální zařízení s grafickými procesory AMD.
* Zvažte použití zásad skupiny služby Active Directory ke zjednodušení konfigurace zásad skupiny na příčce několika virtuálních počítačů. Informace o nasazení zásad skupiny v doméně služby Active Directory naleznete v [tématu Práce s objekty zásad skupiny](https://go.microsoft.com/fwlink/p/?LinkId=620889).
