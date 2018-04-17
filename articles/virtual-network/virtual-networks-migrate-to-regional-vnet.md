---
title: Migrovat virtuální sítě Azure (klasický) ze skupiny vztahů v oblasti | Microsoft Docs
description: Zjistěte, jak migrovat virtuální sítě (klasické) v oblasti ze skupiny vztahů.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: b8b4fbdbf92e49a0287cd6c440513213279d24d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migrovat virtuální sítě (klasické) ze skupiny vztahů v oblasti

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tento článek se věnuje použití klasického modelu nasazení. Společnost Microsoft doporučuje, aby většina nových nasazení používala model nasazení Resource Manager.

Skupiny vztahů Ujistěte se, že jsou prostředky vytvořené v rámci stejné skupině vztahů fyzicky hostované servery, které jsou blízko sebe, povolení tyto prostředky ke komunikaci rychlejší. V minulosti skupiny vztahů byly požadavek pro vytvoření virtuální sítě (klasické). V té době službu správce sítě, který spravovat virtuální sítě (klasické) může fungovat pouze v rámci sady fyzických serverech nebo jednotky škálování. Architektury vylepšení zvýšili rozsah správy sítě v oblasti.

V důsledku těchto vylepšení architektury jsou skupiny vztahů už doporučené, nebo požadované pro virtuální sítě (klasické). Pomocí skupiny vztahů pro virtuální sítě (klasické) je nahrazena oblasti. Virtuální sítě (klasické), které jsou spojeny s oblastí se nazývají regionálních virtuálních sítí.

Doporučujeme, že skupiny vztahů nepoužíváte obecně. Kromě zajištění dostatečného požadavek na virtuální síť skupiny vztahů byly také důležité používat k zajištění prostředky, například výpočetní (klasické) a úložiště (klasické), byly umístěny vedle sebe. Však s architekturou aktuální síť Azure a tyto požadavky na umístění již nejsou potřebné.

> [!IMPORTANT]
> Přestože je technicky stále možné vytvořit virtuální síť, který je přidružený skupině vztahů, neexistuje žádný přesvědčivý důvod k tomu. Mnoho funkcí virtuální sítě, jako jsou skupiny zabezpečení sítě, jsou k dispozici pouze při použití regionální virtuální síť a nejsou k dispozici pro virtuální sítě, které jsou přidružené skupiny vztahů.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Upravovat soubor konfigurace sítě

1. Exportujte konfigurační soubor sítě. Informace o tom, jak exportovat soubor konfigurace sítě pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure (CLI) 1.0, najdete v části [konfigurace virtuální sítě pomocí konfiguračního souboru sítě](virtual-networks-using-network-configuration-file.md#export).
2. Upravovat soubor konfigurace sítě, nahraďte **AffinityGroup** s **umístění**. Zadejte Azure [oblast](https://azure.microsoft.com/regions) pro **umístění**.
   
   > [!NOTE]
   > **Umístění** je oblast, která jste zadali pro skupinu vztahů, která souvisí s vaší virtuální sítě (klasické). Pokud virtuální sítě (klasické) souvisí s skupinu vztahů, který je umístěný v západní USA, když provádíte migraci, například vaše **umístění** musí odkazovat na západní USA. 
   > 
   > 
   
    Upravte následující řádky v konfiguračním souboru sítě, nahraďte hodnoty vlastními: 
   
    **Původní hodnota:** \<VirtualNetworkSitename = "VNetUSWest" AffinityGroup = "VNetDemoAG"\> 
   
    **Nová hodnota:** \<VirtualNetworkSitename = "VNetUSWest" umístění = "Západní USA"\>
3. Uložte změny a [importovat](virtual-networks-using-network-configuration-file.md#import) konfigurace sítě Azure.

> [!NOTE]
> Tato migrace nezpůsobí žádné výpadky k vašim službám.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Co dělat, když máte virtuální počítač (klasický) ve skupině vztahů
Virtuální počítače (klasické) jsou aktuálně ve skupině vztahů, není potřeba odebrat ze skupiny vztahů. Po nasazení virtuálního počítače se nasadí do jednotky škálování jednoho. Skupiny vztahů můžete omezit sadu dostupných velikostí virtuálních počítačů pro nové nasazení virtuálního počítače, ale už existující virtuální počítač, který je nasazen omezené sadu velikosti virtuálních počítačů, které jsou k dispozici v jednotce škálování, ve kterém je virtuální počítač nasazen. Protože virtuální počítač je už nasazená na jednotce škálování, odebrání virtuálního počítače ze skupiny vztahů nemá žádný vliv na virtuálním počítači.
