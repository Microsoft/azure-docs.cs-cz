---
title: Konfigurace virtuálního počítače hostovaného Microsoft Azure pro Azure Marketplace
description: Vysvětluje, jak velikost, aktualizace a generalizace virtuálních počítačů hostovaných v Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 1270dff0bcb8de117247a454ab9c144250cfb17c
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880349"
---
# <a name="configure-the-azure-hosted-vm"></a>Konfigurace virtuálního počítače hostovaného pro Azure

Tento článek vysvětluje, jak můžete měnit, aktualizovat a zobecnit virtuální počítač hostovaný v Azure.  Tyto kroky jsou nezbytné pro přípravu virtuálního počítače k nasazení z Azure Marketplace.


## <a name="sizing-the-vhds"></a>Změna velikosti virtuálních pevných disků

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Pokud jste vybrali jeden z virtuálních počítačů, které jsou předem nakonfigurované s operačním systémem (a volitelně i další služby), pak jste už si vybrali standardní velikost virtuálního počítače Azure, jak je popsáno na [kartě SKU virtuálních počítačů](./cpp-skus-tab.md).  Doporučený postup je spuštění řešení s předem nakonfigurovaným operačním systémem.  Pokud ale instalujete operační systém ručně, musíte ve své imagi virtuálního počítače nastavit velikost primárního virtuálního pevného disku:

- V případě systému Windows by měl být virtuální pevný disk operačního systému vytvořen jako VHD s pevným formátem 127-128 GB. 
- Pro Linux by se měl tento virtuální pevný disk vytvořit jako VHD s pevným formátem 30-50 GB.

Pokud je fyzická velikost menší než 127-128 GB, měl by být virtuální pevný disk zhuštěný. Základní image Windows a SQL Server, které už tyto požadavky splňují, neměňte formát nebo velikost získaného virtuálního pevného disku. 

Datové disky můžou být velké až 1 TB. Při rozhodování o velikosti si pamatujte, že zákazníci nemůžou v době nasazování měnit velikost virtuálních pevných disků v rámci bitové kopie. Virtuální pevné disky datového disku by se měly vytvářet jako virtuální pevné disky s pevným formátem. Měly by také být zhuštěné. Datové disky můžou být zpočátku prázdné nebo obsahovat data.


## <a name="install-the-most-current-updates"></a>Nainstalovat nejaktuálnější aktualizace

Základní image virtuálních počítačů s operačním systémem obsahují nejnovější aktualizace až do jejich publikovaných dat. Před publikováním virtuálního pevného disku operačního systému, který jste vytvořili, se ujistěte, že aktualizujete operační systém a všechny nainstalované služby se všemi nejnovějšími opravami zabezpečení a údržby.

V případě systému Windows Server 2016 spusťte příkaz **Check for Updates** .  V opačném případě se v případě starších verzí Windows Podívejte [na téma Jak získat aktualizaci prostřednictvím web Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Windows Update nainstaluje automaticky nejnovější kritické a důležité aktualizace zabezpečení.

U distribucí pro Linux se aktualizace běžně stahují a instalují prostřednictvím nástroje příkazového řádku nebo grafického nástroje.  Například Ubuntu Linux poskytuje příkaz [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) a nástroj [správce aktualizací](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) pro aktualizaci operačního systému.


## <a name="perform-additional-security-checks"></a>Provedení dalších kontrol zabezpečení

Měli byste udržovat vysokou úroveň zabezpečení pro obrázky vašeho řešení v Azure Marketplace.  Následující článek poskytuje kontrolní seznam konfigurací a postupů zabezpečení, které vám pomohou v tomto cíli: [Doporučení zabezpečení pro Azure Marketplace image](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)  Některá z těchto doporučení jsou specifická pro image založené na systému Linux, ale většina se vztahují na všechny image virtuálních počítačů. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Provádění vlastních konfigurací a naplánovaných úloh

Pokud je potřeba další konfigurace, doporučuje se použít naplánovanou úlohu, která se spustí při spuštění, aby se všechny závěrečné změny virtuálního počítače daly provést po jeho nasazení.  Vezměte v úvahu také následující doporučení:
- Pokud se jedná o úlohu spuštění jednou, doporučuje se, aby se úloha po úspěšném dokončení odstranila.
- Konfigurace by se neměla spoléhat na jiné jednotky než C nebo D, protože existují jenom tyto dvě jednotky, které jsou vždycky zaručené. Jednotka C je disk s operačním systémem a jednotka D je dočasný místní disk.

Další informace o úpravách pro Linux najdete v tématu [rozšíření a funkce virtuálních počítačů pro Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Zobecnění image

Všechny obrázky v Azure Marketplace musí být obecně znovu použitelné. Pro dosažení této opětovné použitelnosti musí být virtuální pevný disk operačníhosystému zobecněný, operace, která odebere všechny identifikátory a ovladače softwaru z virtuálního počítače.

### <a name="windows"></a>Windows

Disky s operačním systémem Windows jsou zobecněny pomocí [nástroje Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Pokud následně aktualizujete nebo znovu nakonfigurujete operační systém, musíte znovu spustit nástroj Sysprep. 

> [!WARNING]
>  Vzhledem k tomu, že aktualizace mohou být spouštěny automaticky, po spuštění nástroje Sysprep byste měli virtuální počítač vypnout, dokud nebude nasazen.  Toto vypnutí zabrání následným aktualizacím v provádění změn specifických pro konkrétní instance v operačním systému VHD nebo nainstalovaných službách.

Další informace o spuštění nástroje Sysprep najdete v tématu [postup generalizace VHD](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) .

### <a name="linux"></a>Linux

Následující dvoustupňový proces generalizuje virtuální počítač Linux a znovu ho nasadí jako samostatný virtuální počítač. Tyto dva kroky jsou pouze základními kroky procesu. Další informace o těchto dvou krocích a o tom, proč je potřeba provést, najdete v tématu [Postup vytvoření image virtuálního počítače nebo virtuálního pevného disku](../../../virtual-machines/linux/capture-image.md). Pro účely vytvoření virtuálního pevného disku pro nabídku Azure Marketplace můžete zastavit po dosažení části Vytvoření virtuálního počítače ze zaznamenané bitové kopie.

#### <a name="remove-the-azure-linux-agent"></a>Odebrat agenta Azure Linux
1.  Připojte se k VIRTUÁLNÍmu počítači se systémem Linux pomocí klienta SSH.
2.  V okně SSH zadejte následující příkaz: <br/>
    `sudo waagent -deprovision+user`
3.  Chcete `y` -li pokračovat, zadejte. (Můžete přidat `-force` parametr do předchozího příkazu, abyste se vyhnuli tomuto kroku potvrzení.)
4.  Po dokončení příkazu zadejte `exit` příkaz pro uzavření klienta ssh.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Zachytit bitovou kopii
1.  Přejít na Azure Portal, vyberte svou skupinu prostředků (RG) a zrušte přidělení virtuálního počítače.
2.  Virtuální pevný disk je teď zobecněný a můžete vytvořit nový virtuální počítač pomocí tohoto virtuálního pevného disku.


## <a name="create-one-or-more-copies"></a>Vytvořit jednu nebo více kopií

Vytváření kopií virtuálních počítačů je často užitečné pro zálohování, testování, přizpůsobení převzetí služeb při selhání nebo vyrovnávání zatížení, které nabízí různé konfigurace řešení a tak dále. Informace o tom, jak duplikovat a stáhnout primární VHD, najdete v tématu:

- Virtuální počítač Linux: [Stažení virtuálního pevného disku se systémem Linux z Azure](../../../virtual-machines/linux/download-vhd.md)
- Virtuální počítač s Windows: [Stažení virtuálního pevného disku s Windows z Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Další postup

Po zobecnění se virtuální počítač oddělí a vytvořili jste image virtuálního počítače, jste připraveni [nasadit virtuální počítač z virtuálního pevného disku](./cpp-deploy-vm-vhd.md).
