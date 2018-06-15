---
title: Řešení potíží s klasický nasazení virtuálního počítače s Windows | Microsoft Docs
description: Řešení problémů nasazení classic, když vytvoříte nový virtuální počítač Windows v Azure
services: virtual-machines-windows
documentationcenter: ''
author: JiangChen79
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 9f01d237-ba39-4c32-b72d-18f5f505d43a
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2016
ms.author: cjiang
ms.openlocfilehash: 5a1ad33bd8c287910458d26a96858e85c689fb16
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2017
ms.locfileid: "24056637"
---
# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-windows-virtual-machine-in-azure"></a>Řešení problémů nasazení classic s vytvoření nového virtuálního počítače Windows v Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-selectors-include.md)]

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Resource Manager verzi v tomto článku naleznete v části [zde](../../virtual-machines-windows-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Protokoly auditu shromáždit
Pokud chcete spustit Poradce při potížích, shromážděte protokoly auditu k identifikaci chyby související s problém.

Na portálu Azure klikněte na tlačítko **Procházet** > **virtuální počítače** > *virtuálního počítače Windows* > **nastavení** > **protokoly auditu**.

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**/ Y:** Pokud operační systém je Windows zobecněn a je nahrál nebo zachytit pomocí nastavení zobecněný, pak nebudou všechny chyby. Podobně pokud je operačním systémem Windows specializovaný a je nahrál nebo zachytit pomocí specializované nastavení a potom nebudou všechny chyby.

**Odešlete chyby:**

**N<sup>1</sup>:** Pokud operačního systému Windows zobecněn, a nahraje jako specializovaný, budete mít zřizování vypršení časového limitu se zablokuje na obrazovce při prvním zapnutí virtuálního počítače.

**N<sup>2</sup>:** Pokud operačního systému Windows specializuje a nahraje jako zobecněn, budete mít zřizování chyba selhání se virtuální počítač na obrazovce při prvním zapnutí zablokuje, protože nový virtuální počítač je spuštěn s původní název počítače, uživatelské jméno a heslo.

**Řešení:**

Obě tyto chyby vyřešíte nahrajte původní virtuální pevný disk, k dispozici místní, stejné nastavení jako pro operační systém (zobecněn/specializuje). Pokud chcete nahrát jako zobecněn, nezapomeňte nejprve spustit nástroj sysprep. V tématu [vytvoření a nahrání virtuálního pevného disku serveru Windows Azure](createupload-vhd.md) Další informace.

**Zaznamenat chyby:**

**N<sup>3</sup>:** Pokud operačního systému Windows zobecněn, a se zaznamená jako specializovaný, zobrazí se zřizování vypršení časového limitu vzhledem k tomu, že původní virtuální počítač je nepoužitelný, jako je označena jako zobecněn.

**N<sup>4</sup>:** Pokud operačního systému Windows specializuje a z něj zachytí jako zobecněn, zřizování selhání chyba se zobrazí, protože nový virtuální počítač je spuštěn s původní název počítače, uživatelské jméno a heslo. Navíc původní virtuální počítač je nepoužitelný, protože je označeno jako specializované.

**Řešení:**

Chcete-li obě tyto chyby, odstraňte aktuální image z portálu, a [kopii z aktuální virtuální pevné disky](capture-image.md) stejné nastavení jako pro operační systém (zobecněn/specializuje).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problém: Vlastní nebo Galerie / marketplace obrázku; došlo k chybě přidělení
Tato chyba nastane v situacích, odeslání novou žádost o virtuální počítač do clusteru, který buď nemá dostupné volné místo pro uložení žádosti, nebo nepodporuje požadovanou velikost virtuálního počítače. Není možné kombinovat různé řadu virtuálních počítačů v rámci stejné cloudové služby. Takže pokud chcete vytvořit nový virtuální počítač je jiný než co cloudové služby může podporovat velikost, výpočetní požadavek nezdaří.

V závislosti na omezení cloudové služby, kterou použijete k vytvoření nového virtuálního počítače může dojít k chybě způsobené dvou situacích.

**Příčina 1:** Cloudová služba je ukotvena do clusteru s podporou konkrétní nebo je propojená do skupiny vztahů a proto připnutý do clusteru s podporou konkrétní návrh. Proto nový prostředek výpočetní požadavků v tom, že skupina vztahů se zkoušejí ve stejném clusteru, kde jsou hostované existující prostředky. Ale stejného clusteru nemusí buď podporují požadovaná velikost virtuálního počítače nebo mít není dostatek volného místa, což vede k chybě přidělení. To platí, jestli jsou vytvořeny nové prostředky prostřednictvím novou cloudovou službu nebo stávající cloudovou službu.

**Řešení 1:**

* Vytvořte novou cloudovou službu a přidružit ji k oblasti nebo na základě oblast virtuální sítě.
* Vytvořte nový virtuální počítač v rámci nové cloudové služby.
  Pokud dojde k chybě při pokusu o vytvoření novou cloudovou službu, opakujte později nebo změnit oblast pro cloudové služby.

> [!IMPORTANT]
> Pokud byly pokusu o vytvoření nového virtuálního počítače ve stávající cloudovou službu, ale nelze a bylo nutné vytvořit novou cloudovou službu pro nový virtuální počítač, můžete sloučit všechny virtuální počítače v rámci stejné cloudové služby. Uděláte to tak, odstraňte virtuální počítače ve stávající cloudovou službu a jejich kopii z jejich disků ve novou cloudovou službu. Je důležité si pamatovat, že novou cloudovou službu bude mít nový název a VIP, tak budete muset aktualizovat tyto pro všechny závislosti, které používají tyto informace pro stávající cloudovou službu.
> 
> 

**Příčina 2:** Cloudová služba se přidružené virtuální síť, která souvisí se do skupiny vztahů, takže je připnutá do clusteru s podporou konkrétní návrh. Všechny nové výpočetních prostředků požadavků v tom, že skupina vztahů se proto zkoušejí ve stejném clusteru, kde jsou hostované existující prostředky. Ale stejného clusteru nemusí buď podporují požadovaná velikost virtuálního počítače nebo mít není dostatek volného místa, což vede k chybě přidělení. To platí, jestli jsou vytvořeny nové prostředky prostřednictvím novou cloudovou službu nebo stávající cloudovou službu.

**Řešení 2:**

* Vytvořte nový regionální virtuální síť.
* Vytvořte nový virtuální počítač v nové virtuální sítě.
* [Připojit existující virtuální síť](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) do nové virtuální sítě. Další informace [regionálních virtuálních sítí](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). Alternativně můžete [migrace na základě vztahů skupiny virtuální sítě na regionální virtuální síť](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)a pak vytvořte nový virtuální počítač.

## <a name="next-steps"></a>Další kroky
Pokud dojde k potížím při spuštění zastaveného virtuálního počítače Windows nebo přizpůsobit existující virtuální počítač Windows v Azure, najdete v části [nasazení classic potíží s restartováním nebo změnou velikosti existující virtuální počítač Windows v Azure](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md).

