---
title: Řešení VMware podle CloudSimple – přehled virtuálních počítačů Azure
description: Seznamte se s CloudSimple virtuálními počítači a jejich výhodami.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0f4967bbd12107bf6a04cb80537d4425c75c5f46
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812493"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Přehled virtuálních počítačů s CloudSimple

CloudSimple umožňuje správu virtuálních počítačů VMware z Azure Portal.  Cluster nebo fond zdrojů z clusteru vSphere se spravuje přes Azure, protože ho namapuje na vaše předplatné.  Virtuální počítač CloudSimple přináší samoobslužnou správu virtuálních počítačů VMware z Azure Portal.  

Aby bylo možné vytvořit virtuální počítač s CloudSimple z Azure, musí existovat šablona virtuálních počítačů ve vašem privátním cloudu vCenter.  Šablona se používá k přizpůsobení operačního systému a aplikací.  Šablona virtuálního počítače se dá posílit, aby splňovala podnikové zásady zabezpečení.  Šablonu můžete použít k vytvoření virtuálních počítačů a jejich využití z Azure Portal pomocí modelu samoobslužné služby.

## <a name="benefits"></a>Výhody

CloudSimple virtuální počítače z Azure Portal poskytují samoobslužný mechanismus pro uživatele, kteří můžou vytvářet a spravovat virtuální počítače VMware.

* Vytvoření virtuálního počítače s CloudSimple v privátním cloudu vCenter
* Spravovat vlastnosti virtuálního počítače
  * Přidat nebo odebrat disky
  * Přidat nebo odebrat síťové karty
* Operace napájení pro virtuální počítač s CloudSimple
  * Zapnutí a vypnutí
  * Resetovat virtuální počítač
* Odstranit virtuální počítač

## <a name="next-steps"></a>Další postup

* Naučte se [využívat virtuální počítače VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Přečtěte si, jak [namapovat předplatné Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)