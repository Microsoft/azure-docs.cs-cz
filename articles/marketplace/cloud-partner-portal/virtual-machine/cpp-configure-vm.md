---
title: Konfigurace virtuálních počítačů hostovaných v Azure společnosti Microsoft pro Azure Marketplace | Dokumentace Microsoftu
description: Vysvětluje, jak upravit velikost, aktualizovat a generalizace virtuálního počítače hostované v Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c1db8c99b1bd3f9bbb768572ca1f5f7a4e1e0de4
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639632"
---
# <a name="configure-the-azure-hosted-vm"></a>Konfigurace virtuálních počítačů hostovaných v Azure

Tento článek vysvětluje, jak upravit velikost, aktualizovat a generalizace virtuálního počítače (VM) hostované v Azure.  Tyto kroky jsou nezbytné pro přípravu vašeho virtuálního počítače jde nasadit z webu Azure Marketplace.


## <a name="sizing-the-vhds"></a>Změna velikosti virtuálních pevných disků

<!--TD: Check if the following assertion is true. I didn't understand the original content. --> Pokud jste vybrali některé virtuální počítače předem nakonfigurované s operačním systémem (a volitelně další služby), pak již mít vyberou standardní velikost virtuálního počítače Azure, jak je popsáno v [skladové položky virtuálních počítačů kartu](./cpp-skus-tab.md).  Toto je doporučený postup.  Ale operační systém při instalaci ručně, pak jste musí velikost primární virtuální pevný disk ve vaší imagi virtuálního počítače:

- Pro Windows, operačního systému virtuálního pevného disku by měl být vytvořen jako 127-128 GB virtuální pevný disk pevného formátu. 
- Pro Linux, tento virtuální pevný disk by měl být vytvořen jako 30 – 50 GB virtuální pevný disk pevného formátu.

Pokud je fyzická velikost menší než 127-128 GB, měl by být virtuální pevný disk zhuštěný. Základní Image Windows a SQL Server k dispozici tyto požadavky už splňují, takže neměňte formát ani velikost získaného virtuálního pevného disku. 

Datové disky můžou být velké až 1 TB. Při rozhodování o velikosti disku, nezapomeňte, že zákazníci nemůžou měnit velikost virtuálních pevných disků v imagi v době nasazování. Datový disk virtuální pevné disky musí být vytvořené jako pevného formátu virtuálních pevných disků. Měly by být zhuštěné. Datové disky může zpočátku být prázdný nebo obsahovat data.


## <a name="install-the-most-current-updates"></a>Nainstalujte nejnovější aktualizace

Základní Image operačního systému virtuálních počítačů obsahují nejnovější aktualizace až do jejich publikovaná data. Před publikováním operační systém virtuálního pevného disku, které jste vytvořili, ujistěte se všechny nejnovější zabezpečení a údržba opravy aktualizace operačního systému a všechny nainstalované služby.

Pro Windows Server 2016, spusťte **vyhledávat aktualizace** příkazu.  V opačném případě pro starší verze systému Windows, naleznete v tématu [jak získat aktualizace pomocí služby Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Windows update automaticky nainstaluje nejnovější kritické a důležité aktualizace.

Pro distribuce Linuxu aktualizace se běžně stahují a instalují přes nástroje příkazového řádku nebo grafické nástroje.  Například, Ubuntu Linux poskytuje [apt-get](http://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) příkazu a [Update Manager](http://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) nástroje pro aktualizaci operačního systému.


## <a name="perform-additional-security-checks"></a>Provedení dalších kontrol zabezpečení

Je vhodné ponechat vysokou úroveň zabezpečení pro vaše řešení imagí v Tržišti Azure Marketplace.  V následujícím článku obsahuje kontrolní seznam konfigurace zabezpečení a postupy, které vám pomohou v tomto cíli: [doporučení zabezpečení pro Image Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Některé z těchto doporučení jsou specifická pro Image založené na Linuxu, ale většina platí pro všechny image virtuálního počítače. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Vlastní konfigurace a naplánovaných úloh

V případě potřeby další konfigurace je doporučený postup použití naplánovanou úlohu, která se spouští při spuštění počítače a provede všechny finální změny k virtuálnímu počítači po jeho nasazení.  Také zvažte tato doporučení:
- Pokud je jedno spuštění úlohy, doporučuje se, že úloha odstraní po úspěšném dokončení.
- Konfigurace by neměl spoléhat na jednotky jiné než C a D, protože pouze tyto dvě jednotky, který je vždycky zaručená. Jednotka C je disk s operačním systémem a jednotky D je dočasný místní disk.

Další informace o přizpůsobení na Linuxu najdete v tématu [funkcí a rozšíření virtuálních počítačů pro Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Zobecnění image

Všechny Image v Tržišti Azure Marketplace musí být obecně opakovaně použitelné. K dosažení této opětovné použití operačního systému virtuálního pevného disku musí být *zobecněný*, operace, která odebere všechny instance konkrétní identifikátory a softwarových ovladačů z virtuálního počítače.

### <a name="windows"></a>Windows

Disky s operačním systémem Windows jsou zobecněn pomocí [nástroje sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Pokud následně aktualizovat nebo změnit konfiguraci operačního systému, je třeba znovu spustit nástroj sysprep. 

> [!WARNING]
>  Protože aktualizace může běžet automaticky, až spustíte sysprep, měli byste vypnout virtuální počítač, dokud je nasazená.  Toto vypnutí zabrání následné aktualizace v provádění změn instancí operačního systému virtuálního pevného disku nebo služby nainstalovány.

Další informace o spuštění příkazu sysprep najdete v článku [kroky pro generalizaci virtuálního pevného disku] ()https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#steps-to-generalize-a-vhd)

### <a name="linux"></a>Linux

Následující dvoustupňový proces generalizace virtuálního počítače s Linuxem a znovu ji nasadíte jako samostatný virtuální počítač.  Další informace najdete v tématu [jak vytvořit image virtuálního počítače nebo virtuální pevný disk](../../../virtual-machines/linux/capture-image.md). 

#### <a name="remove-the-azure-linux-agent"></a>Odebrat agenta Azure Linux
1.  Připojte se k virtuálním počítačům s Linuxem pomocí klienta SSH.
2.  V okně SSH zadejte následující příkaz: <br/>
    `sudo waagent -deprovision+user`
3.  Typ `y` pokračujte. (Můžete přidat `-force` parametr k příkazu předchozí Vyhněte se tento krok potvrzení.)
4.  Po dokončení příkazu zadejte `exit` zavřete klienta SSH.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Zachycení bitové kopie
1.  Přejděte na web Azure Portal, vyberte skupinu prostředků (RG) a zrušení přidělení virtuálního počítače.
2.  Je nyní zobecněný virtuální pevný disk a vytvoříte nový virtuální počítač s použitím tohoto virtuálního pevného disku.


## <a name="create-one-or-more-copies"></a>Vytvořte jeden nebo více kopií

Vytvoření kopie virtuálního počítače je často užitečné pro zálohování, testování, přizpůsobené převzetí služeb při selhání nebo Vyrovnávání zatížení, nabízí různé konfigurace řešení, a tak dále. Informace o tom, jak duplikovat a stáhnout primárního virtuálního pevného disku, aby nespravované klonování najdete tady:

- Virtuální počítač s Linuxem: [stáhnout VHD s Linuxem z Azure](../../../virtual-machines/linux/download-vhd.md)
- Virtuální počítač Windows: [stáhnout Windows virtuálního pevného disku z Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Další postup

Po dokončení konfigurace virtuálního počítače, jste připraveni [nasazení virtuálního počítače z virtuálního pevného disku](./cpp-deploy-vm-vhd.md).
