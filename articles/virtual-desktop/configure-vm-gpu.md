---
title: Konfigurace pro Windows virtuální plochy Preview – Azure GPU
description: Postup při povolování – hardwarově akcelerovanou vykreslování a kódování v náhledu virtuální plochy Windows.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: a6a67c89253a1b16f9266d7917655d1b1104022e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159567"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-preview"></a>Konfigurace grafických akcelerace procesor (GPU) pro Windows Virtual Desktop Preview

Virtuální Desktop Preview Windows podporuje – hardwarově akcelerovanou vykreslování a kódování aplikace lepší výkon a škálovatelnost. Akcelerace GPU je zvlášť zásadní pro aplikace náročné na grafiku.

Postupujte podle pokynů v tomto článku vytvořte virtuální počítač Azure GPU optimalizované, přidat do fondu hostitele a nakonfigurovat ji používat akcelerace GPU pro kódování a vykreslování. Tento článek předpokládá, že už máte nakonfigurované tenanta virtuální plochy Windows.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Vyberte velikost virtuálního počítače Azure optimalizované z hlediska GPU

Azure nabízí celou řadu [GPU optimalizované velikosti virtuálních počítačů](/azure/virtual-machines/windows/sizes-gpu). Tou správnou volbou pro hostitele fondu závisí na řadě faktorů, včetně úloh konkrétní aplikace, požadovanou kvalitu uživatelské prostředí a náklady. Obecně platí větší a podporuje více grafickými procesory nabízí lepší výkon hustotě daného uživatele.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Vytvoření fondu hostitele, zřízení virtuálního počítače a konfigurace skupiny aplikací

Vytvoření nového fondu hostitele pomocí virtuálního počítače o velikosti, kterou jste vybrali. Pokyny najdete v tématu [kurzu: Vytvoření fondu hostitele pomocí webu Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Virtuální Desktop Preview Windows podporuje – hardwarově akcelerovanou vykreslování a kódování v následujících operačních systémů:

* Windows 10 verze 1511 nebo novější
* Windows Server 2016 nebo novější

Musíte také konfigurace skupiny aplikací nebo použít výchozí desktopové aplikace pro skupinu (s názvem "Desktop skupiny aplikací"), který se automaticky vytvoří při vytvoření nového fondu hostitele. Pokyny najdete v tématu [kurzu: Správa skupiny aplikací pro Windows Virtual Desktop Preview](/azure/virtual-desktop/manage-app-groups).

>[!NOTE]
>Typ skupiny aplikací "Desktop" Windows Virtual Desktop Preview podporuje jenom pro fondy s podporou grafického procesoru hostitele. Skupiny aplikací typu "Vzdálená aplikace RemoteApp" nejsou podporovány pro fondy s podporou grafického procesoru hostitele.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Instalace ovladačů podporovanou grafickou ve virtuálním počítači

Abyste mohli využívat možnostmi grafického procesoru virtuálních počítačů Azure řady N-series ve verzi Preview virtuální pro plochu Windows, je nutné nainstalovat grafické ovladače NVIDIA. Postupujte podle pokynů na adrese [ovladačů NVIDIA GPU nainstalovat na virtuální počítače řady N-series s Windows](/azure/virtual-machines/windows/n-series-driver-setup) instalace ovladačů, buď ručně nebo pomocí [rozšíření ovladače GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows).

Poznámka: pouze [ovladače NVIDIA GRID](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) distribuuje společnost Azure jsou podporovány pro náhled virtuální plochy Windows.

Po instalaci ovladače je nutné restartovat virtuální počítač. Použijte kroky pro ověření ve výše uvedených pokynů k potvrzení, že byly úspěšně nainstalovány grafické ovladače.

## <a name="configure-gpu-accelerated-app-rendering"></a>Konfigurace aplikace – hardwarově akcelerovanou vykreslování

Ve výchozím nastavení aplikace a klientům na spouštění v konfiguracích s více relací se vykreslují CPU a využití není k dispozici GPU pro vykreslování. Konfigurace zásad skupiny pro hostitele relace umožňující GPU hardwarově urychlené vykreslování:

1. Připojte se k ploše virtuálního počítače pomocí účtu s oprávněními místního správce.
2. Otevřete začátku nabídce a zadejte "gpedit.msc" otevřete Editor zásad skupiny.
3. Přejděte do stromu **konfigurace počítače** > **šablony pro správu** > **součásti Windows**  >   **Vzdálená plocha** > **hostitel relace vzdálené plochy** > **vzdálené relace prostředí**.
4. Vyberte zásadu **hardwaru výchozí grafický adaptér používat pro všechny relace služby Vzdálená plocha** a nastavte tuto zásadu na **povoleno** povolit vykreslování GPU ve vzdálené relaci.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Konfigurace kódování – hardwarově akcelerovanou rámce

Vzdálená plocha kóduje všechny grafiky vykreslen tak, že aplikace a stolních počítačů (ať už vykreslen s GPU nebo CPU) pro přenos do klienty vzdálené plochy. Ve výchozím nastavení vzdálené plochy k dispozici GPU pro toto kódování nevyužívá. Konfigurace zásad skupiny pro hostitele relace umožňující kódování – hardwarově akcelerovanou rámce. Pokračování výše uvedených kroků:

1. Vyberte zásadu **určit prioritu H.264/AVC 444 grafickém režimu pro připojení ke vzdálené ploše** a nastavte tuto zásadu na **povoleno** přinutit H.264/AVC 444 kodek ve vzdálené relaci.
2. Vyberte zásadu **H.264/AVC konfigurace hardwaru kódování pro připojení ke vzdálené ploše** a nastavte tuto zásadu na **povoleno** umožňující kódování hardwaru pro AVC/H.264 ve vzdálené relaci.

    >[!NOTE]
    >Ve Windows serveru 2016, nastavte možnost **raději kódování hardwaru AVC** k **vždy**.

3. Teď, když se upravily zásady skupiny, vynuťte aktualizaci zásad skupiny. Otevřete příkazový řádek a zadejte:

    ```batch
    gpupdate.exe /force
    ```

4. Odhlaste se z relace vzdálené plochy.

## <a name="verify-gpu-accelerated-app-rendering"></a>Ověření aplikace – hardwarově akcelerovanou vykreslování

Pokud chcete ověřit, jestli aplikace využívají GPU pro vykreslování, vyzkoušejte některý z následujících akcí:

* Použití `nvidia-smi` nástroj, jak je popsáno v [ověření instalace ovladače](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) vyhledat využití GPU při spuštění aplikace.
* Na podporovaných verzí operačních systémů můžete použít Správce úloh zkontrolujte využití GPU. Vyberte na kartě "Výkonu" Pokud chcete zobrazit, jestli aplikace využívají GPU GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Kódování – hardwarově akcelerovanou rámce ověření

Chcete-li ověřit, že se pomocí vzdálené plochy – hardwarově akcelerovanou kódování:

1. Připojte se k ploše virtuálního počítače pomocí klienta virtuální plochy Windows.
2. Spusťte Prohlížeč událostí a přejděte na následující uzel: **Protokoly aplikací a služeb** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreTS**  >  **Provozní**
3. Chcete-li zjistit, zda se používá – hardwarově akcelerovanou kódování, vyhledejte událost ID 170. Pokud se zobrazí "kodér hardwaru AVC povoleno: 1" pak GPU kódování se používá.
4. Pokud chcete zjistit, pokud se používá režim AVC 444, vyhledejte událost ID 162. Pokud se zobrazí "AVC dostupné: 1 počáteční profil: 2 048" pak AVC 444 se používá.

## <a name="next-steps"></a>Další postup

Tyto pokyny byste jste pracovat s GPU zrychlení v jedné relaci hostiteli virtuálního počítače. Několik dalších důležitých informací pro povolení GPU akcelerace napříč větší fond hostitele:

* Zvažte použití [rozšíření ovladače GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) pro zjednodušení instalace ovladače a aktualizace v několika virtuálních počítačů.
* Zvažte použití zásad skupiny služby Active Directory pro zjednodušení konfigurace pomocí zásad skupiny v několika virtuálních počítačů. Informace o nasazení zásad skupiny v doméně služby Active Directory najdete v tématu [práce s objekty zásad skupiny](https://go.microsoft.com/fwlink/p/?LinkId=620889).
