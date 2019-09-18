---
title: Migrace virtuální sítě Azure (Classic) ze skupiny vztahů do oblasti | Microsoft Docs
description: Naučte se migrovat virtuální síť (Classic) ze skupiny vztahů do oblasti.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: d33d9ec4eadeaa3a082103f1ad699e2fc3010e3b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058401"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migrace virtuální sítě (Classic) ze skupiny vztahů do oblasti

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a klasický](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Tento článek se věnuje použití klasického modelu nasazení. Microsoft doporučuje, aby většina nových nasazení používala model nasazení Správce prostředků.

Skupiny vztahů zajistí, aby prostředky vytvořené v rámci stejné skupiny vztahů byly fyzicky hostovány servery, které jsou blízko sebe, a umožňují těmto prostředkům komunikovat rychleji. V minulosti byly skupiny vztahů požadovány pro vytváření virtuálních sítí (Classic). Služba správce sítě, kterou spravované virtuální sítě (Classic), může v tuto chvíli fungovat jenom v rámci sady fyzických serverů nebo jednotek škálování. Vylepšení architektury zvýšily rozsah správy sítě na určitou oblast.

V důsledku těchto vylepšení architektury se skupiny vztahů už nedoporučují, nebo se vyžadují pro virtuální sítě (Classic). Použití skupin vztahů pro virtuální sítě (Classic) je nahrazeno oblastmi. Virtuální sítě (klasické), které jsou přidružené k oblastem, se nazývají oblastní virtuální sítě.

Doporučujeme, abyste skupiny vztahů všeobecně nepoužívali. Mimo požadavek virtuální sítě byly skupiny vztahů také důležité k tomu, aby se zajistilo, že prostředky, jako jsou výpočetní prostředí (Classic) a úložiště (Classic), jsou umístěné blízko sebe. U aktuální síťové architektury Azure se ale tyto požadavky na umístění už nepotřebují.

> [!IMPORTANT]
> I když je ještě technicky možné vytvořit virtuální síť, která je přidružená ke skupině vztahů, neexistuje žádný přesvědčivý důvod k tomu. Mnoho funkcí virtuální sítě, jako jsou například skupiny zabezpečení sítě, je k dispozici pouze při použití regionální virtuální sítě a nejsou k dispozici pro virtuální sítě, které jsou spojeny se skupinami vztahů.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Úprava konfiguračního souboru sítě

1. Exportujte konfigurační soubor sítě. Informace o tom, jak exportovat konfigurační soubor sítě pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure (CLI) 1,0, najdete v tématu [Konfigurace virtuální sítě pomocí konfiguračního souboru sítě](virtual-networks-using-network-configuration-file.md#export).
2. Upravte konfigurační soubor sítě a nahraďte **AffinityGroup** **místem**. Určete [oblast](https://azure.microsoft.com/regions) Azure pro **umístění**.
   
   > [!NOTE]
   > **Umístění** je oblast, kterou jste zadali pro skupinu vztahů, která je přidružená k vaší virtuální síti (Classic). Například pokud je vaše virtuální síť (Classic) přidružená ke skupině vztahů, která je umístěná v Západní USA, při migraci musí vaše **umístění** ukazovat na západní USA. 
   > 
   > 
   
    Upravte následující řádky v souboru konfigurace sítě a nahraďte hodnoty vlastními: 
   
    **Stará hodnota:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **Nová hodnota:** \<VirtualNetworkSitename = "VNetUSWest" Location = "Západní USA"\>
3. Uložte změny a [importujte](virtual-networks-using-network-configuration-file.md#import) konfiguraci sítě do Azure.

> [!NOTE]
> Tato migrace nezpůsobí výpadkům vašich služeb žádné výpadky.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Co dělat, když máte virtuální počítač (Classic) ve skupině vztahů
Virtuální počítače (Classic), které jsou aktuálně ve skupině vztahů, není nutné odebírat ze skupiny vztahů. Po nasazení virtuálního počítače se virtuální počítač nasadí do jedné jednotky škálování. Skupiny vztahů můžou omezit sadu dostupných velikostí virtuálních počítačů pro nové nasazení virtuálních počítačů, ale existující nasazený virtuální počítač je už omezený na sadu velikostí virtuálních počítačů dostupných v jednotce škálování, ve které je virtuální počítač nasazený. Vzhledem k tomu, že virtuální počítač je už nasazený do jednotky škálování, nemá odebrání virtuálního počítače ze skupiny vztahů žádný vliv na virtuální počítač.
