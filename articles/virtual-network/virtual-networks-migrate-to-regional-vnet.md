---
title: Migrace virtuální sítě Azure (klasické) ze skupiny vztahů do oblasti | Dokumentace Microsoftu
description: Zjistěte, jak migrovat virtuální síť (klasická) ze skupiny vztahů do oblasti.
services: virtual-network
documentationcenter: na
author: genlin
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
ms.openlocfilehash: d3bb93d12a217e6d9066d037ff92f071b6139ab3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648631"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migrace virtuální sítě (klasické) ze skupiny vztahů do oblasti

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a classic](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tento článek se věnuje použití klasického modelu nasazení. Společnost Microsoft doporučuje, aby většina nových nasazení používala model nasazení Resource Manager.

Skupiny vztahů Ujistěte se, že prostředky vytvořené v rámci stejné skupiny vztahů jsou fyzicky hostitelem serverů umístěných blízko sebe, povolení těchto prostředků komunikovat rychleji. Skupiny vztahů byly v minulosti, požadavek na vytvoření virtuální sítě (classic). V té době může správce síťové služby, který spravované virtuální sítě (klasické) funguje jenom v rámci sady fyzických serverech nebo jednotky škálování. Vylepšení architektury kybernetická oboru správy sítě v oblasti.

V důsledku těchto vylepšení architektury jsou skupiny vztahů už nedoporučuje, nebo požadované pro virtuální sítě (klasické). Používání skupin vztahů pro virtuální sítě (classic) je nahrazena oblastech. Virtuální sítě (classic), které jsou spojeny s oblastmi, se nazývají regionálních virtuálních sítích.

Doporučujeme, že skupiny vztahů nepoužíváte obecně. Kromě požadavek na virtuální síť skupiny vztahů byly také důležité používat k zajištění prostředky, jako jsou výpočetní výkon (classic) a úložiště (classic), byly umístěny blízko sebe. Nicméně s architekturou aktuální síť Azure, tyto požadavky na umístění už nejsou potřebné.

> [!IMPORTANT]
> I když je technicky vzato stále možné vytvořit virtuální síť, která souvisí se skupinou vztahů, neexistuje žádný pádný důvod, proč tomu. Mnoho funkcí virtuální sítě, jako jsou skupiny zabezpečení sítě jsou k dispozici pouze při použití regionální virtuální síť a nejsou k dispozici pro virtuální sítě, přidružených skupin vztahů.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Upravit soubor konfigurace sítě

1. Exportujte soubor konfigurace sítě. Zjistěte, jak exportovat soubor konfigurace sítě pomocí Powershellu nebo rozhraní příkazového řádku Azure (CLI) 1.0, najdete v článku [konfigurace virtuální sítě pomocí souboru konfigurace sítě](virtual-networks-using-network-configuration-file.md#export).
2. Upravte konfigurační soubor sítě nahrazení **AffinityGroup** s **umístění**. Zadejte Azure [oblasti](https://azure.microsoft.com/regions) pro **umístění**.
   
   > [!NOTE]
   > **Umístění** je oblast, který jste zadali pro skupinu vztahů, která souvisí s vaší virtuální sítě (classic). Pokud se vaše virtuální síť (classic) přidružené ke skupině vztahů, který je umístěný v oblasti západní USA, při migraci, třeba vaše **umístění** musí odkazovat na západní USA. 
   > 
   > 
   
    Upravte následující řádky v konfiguračním souboru sítě, nahraďte vlastními hodnotami: 
   
    **Původní hodnota:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **Nová hodnota:** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. Uložte změny a [importovat](virtual-networks-using-network-configuration-file.md#import) konfigurace sítě do Azure.

> [!NOTE]
> Tato migrace nezpůsobí žádné výpadky služeb.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Co dělat, když máte virtuální počítač (klasický) ve skupině vztahů
Virtuální počítače (classic), které jsou aktuálně ve skupině vztahů, není potřeba odebrat ze skupiny vztahů. Po nasazení virtuálního počítače je nasazený na jednotce škálování jednoho. Skupiny vztahů můžete omezit sadu dostupných velikostí virtuálních počítačů pro nová nasazení virtuálního počítače, ale všechny existující virtuální počítač, který je nasazený je již omezena na sadu velikostí virtuálních počítačů dostupných v jednotce škálování, ve kterém je virtuální počítač nasazený. Protože virtuální počítač je již nasazený na jednotce škálování, odebrání virtuálního počítače ze skupiny vztahů nemá žádný vliv na virtuálním počítači.
