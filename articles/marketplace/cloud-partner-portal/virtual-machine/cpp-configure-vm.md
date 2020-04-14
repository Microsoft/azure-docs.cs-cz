---
title: Konfigurace virtuálního počítače hostovaného Microsoft Azure pro Azure Marketplace
description: Vysvětluje, jak velikost, aktualizaci a generalizaci virtuálního počítače hostovaného v Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: f20beced7d977668d12c06375ceb8a2554c6d335
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273422"
---
# <a name="configure-the-azure-hosted-vm"></a>Konfigurace virtuálního počítače hostovaného v Azure

> [!IMPORTANT]
> dubna 2020 začneme s přesouváním nabídky virtuálních strojů Azure do Partnerského centra. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Vytvoření technických prostředků virtuálního počítače Azure](https://aka.ms/AzureVMTechAsset) a spravujte migrované nabídky.

Tento článek vysvětluje, jak velikost, aktualizaci a generalizaci virtuálního počítače (VM) hostovaného v Azure.  Tyto kroky jsou nezbytné k přípravě virtuálního počítače k nasazení z Azure Marketplace.

## <a name="sizing-the-vhds"></a>Dimenzování virtuálních diod

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Pokud jste vybrali jeden z virtuálních počítačů předem nakonfigurovaných s operačním systémem (a volitelně další služby), pak jste už vybrali standardní velikost virtuálního počítače Azure, jak je popsáno na [kartě Virtuální počítač skuf .](./cpp-skus-tab.md)  Spuštění řešení s předkonfigurovaným osem je doporučený přístup.  Pokud však instalujete operační systém ručně, musíte velikost primárního virtuálního pevného disku v bitové kopii virtuálního počítače:

- Pro Windows by měl být operační systém VHD vytvořen jako 127-128 GB pevného formátu VHD. 
- Pro Linux by měl být tento virtuální pevný disk vytvořen jako virtuální pevný disk s kapacitou 30–50 GB.

Pokud je fyzická velikost menší než 127-128 GB, virtuální pevný disk by měl být řídké. Základní bitové kopie systému Windows a SERVERU SQL Server již splňují tyto požadavky, proto neměňte formát ani velikost získaného virtuálního pevného disku. 

Datové disky mohou být až 1 TB. Při rozhodování o jejich velikosti si uvědomte, že zákazníci nemohou změnit velikost virtuálních dispozičních discích v rámci bitové kopie v době nasazení. Vdisky datových disků by měly být vytvořeny jako virtuální pevné visuty. Měly by být také řídké. Datové disky mohou být zpočátku prázdné nebo mohou obsahovat data.


## <a name="install-the-most-current-updates"></a>Instalace nejaktuálnějších aktualizací

Základní image virtuálních aplikací operačního systému obsahují nejnovější aktualizace až do data publikování. Před publikováním vytvořeného virtuálního pevného disku operačního systému se ujistěte, že aktualizujete operační systém a všechny nainstalované služby pomocí všech nejnovějších oprav zabezpečení a údržby.

V systému Windows Server 2016 spusťte příkaz **Vyhledat aktualizace.**  V opačném případě u starších verzí systému Windows naleznete v tématu [Jak získat aktualizaci prostřednictvím služby Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Služba Windows Update automaticky nainstaluje nejnovější důležité a důležité aktualizace zabezpečení.

Pro linuxové distribuce jsou aktualizace běžně stahovány a instalovány prostřednictvím nástroje příkazového řádku nebo grafického nástroje.  Například Ubuntu Linux poskytuje příkaz [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) a nástroj [Správce aktualizací](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) pro aktualizaci operačního systému.


## <a name="perform-additional-security-checks"></a>Provedení dalších kontrol zabezpečení

Měli byste udržovat vysokou úroveň zabezpečení pro image řešení na Azure Marketplace.  Následující článek obsahuje kontrolní seznam konfigurací zabezpečení a postupů, které vám pomohou v tomto účelu: [Doporučení zabezpečení pro image Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Některá z těchto doporučení jsou specifická pro bitové kopie založené na Linuxu, ale většina se vztahuje na libovolnou image virtuálního počítače. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Provádění vlastní konfigurace a naplánovaných úloh

Pokud je potřeba další konfigurace, doporučeným přístupem je použití naplánované úlohy, která se spustí při spuštění, k provádění konečných změn virtuálního počítače po jeho nasazení.  Zvažte také následující doporučení:
- Pokud se jedná o úlohu run-once, doporučujeme, aby se úloha po úspěšném dokončení odstranila sama.
- Konfigurace by neměly spoléhat na jiné jednotky než C nebo D, protože pouze tyto dvě jednotky, které jsou vždy zaručeno, že existují. Jednotka C je disk operačního systému a jednotka D je dočasný místní disk.

Další informace o přizpůsobení Linuxu najdete v [tématu Rozšíření virtuálních strojů a funkce pro Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Zobecnění obrázku

Všechny image na Azure Marketplace musí být opakovaně použitelné obecným způsobem. K dosažení této opakované použitelnosti musí být operační systém VHD *zobecněn*, což je operace, která odebere všechny identifikátory specifické pro instance a softwarové ovladače z virtuálního počítače.

### <a name="windows"></a>Windows

Disky operačního systému Windows jsou zobecněny [pomocí nástroje sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Pokud následně aktualizujete nebo překonfigurujete operační systém, je nutné znovu spustit sysprep. 

> [!WARNING]
>  Vzhledem k tomu, že aktualizace může běžet automaticky, po spuštění sysprep, měli byste vypnout virtuální ho, dokud se nasadí.  Tímto vypnutím zabráníte tomu, aby následné aktualizace nedělaly změny v systému VHD nebo nainstalované služby specifické pro instance.

Další informace o spuštění sysprepu naleznete v [tématu Kroky k generalizaci virtuálního pevného disku](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)

### <a name="linux"></a>Linux

Následující dvoustupňový proces zobecní virtuální počítač s Linuxem a znovu jej nasadí jako samostatný virtuální počítač. Tyto dva kroky jsou jen základy procesu. Další informace o těchto dvou krocích a důvodech, proč je nutné provést, naleznete v [tématu Vytvoření bitové kopie virtuálního počítače nebo virtuálního počítače](../../../virtual-machines/linux/capture-image.md). Pro účely vytvoření virtuálního pevného disku pro vaši nabídku Azure Marketplace můžete zastavit, když se dostanete do části "Vytvoření virtuálního počítače ze zachycené image".

#### <a name="remove-the-azure-linux-agent"></a>Odebrání agenta Azure Linuxu
1.  Připojte se k virtuálnímu počítači s Linuxem pomocí klienta SSH.
2.  V okně SSH zadejte následující příkaz: <br/>
    `sudo waagent -deprovision+user`
3.  Chcete-li pokračovat, zadejte. `y` (Parametr můžete `-force` přidat k předchozímu příkazu, vyhněte se tomuto kroku potvrzení.)
4.  Po dokončení příkazu `exit` zadejte zavření klienta SSH.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Zachycení obrazu
1.  Přejděte na portál Azure, vyberte skupinu prostředků (RG) a zrušte přidělení virtuálního počítače.
2.  Váš virtuální pevný disk je nyní generalizovaný a pomocí tohoto virtuálního počítače můžete vytvořit nový virtuální počítač.


## <a name="create-one-or-more-copies"></a>Vytvoření jedné nebo více kopií

Vytváření kopií virtuálního počítače je často užitečné pro zálohování, testování, přizpůsobené převzetí služeb při selhání nebo vyrovnávání zatížení, nabízet různé konfigurace řešení a tak dále. Informace o tom, jak duplikovat a stáhnout primární virtuální pevný disk, chcete-li vytvořit nespravovaný klon, naleznete v tématu:

- Virtuální počítač s [Linuxem: Stažení virtuálního pevného disku Linuxu z Azure](../../../virtual-machines/linux/download-vhd.md)
- Virtuální počítač s Windows: [Stažení virtuálního pevného disku s Windows z Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Další kroky

Po zobecnění virtuálního počítače, byla odebrána a jste vytvořili bitovou kopii virtuálního počítače, jste připraveni [nasadit virtuální počítač z virtuálního pevného disku](./cpp-deploy-vm-vhd.md).
