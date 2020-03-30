---
title: Přehled virtuálních počítačů
titleSuffix: Azure VMware Solution by CloudSimple
description: Seznamte se s virtuálními počítači CloudSimple a jejich výhodami.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 436a5ab6fb286ce206f981487b6a29d774ecd1a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024904"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple virtuální počítače – přehled

CloudSimple umožňuje spravovat virtuální počítače VMware (VM) z portálu Azure.  Cluster nebo fond prostředků z vašeho clusteru vSphere se spravuje prostřednictvím Azure mapováním na vaše předplatné.

Chcete-li vytvořit virtuální počítač CloudSimple z Azure, musí existovat šablona virtuálního počítače na vašem privátního cloudu vCenter.  Šablona se používá k přizpůsobení operačního systému a aplikací.  Šablona virtuálního počítače může být posílena ke splnění zásad zabezpečení rozlehlé sítě.  Šablonu můžete použít k vytvoření virtuálních počítačů a pak je spotřebovávat z webu Azure Portal pomocí samoobslužného modelu.

## <a name="benefits"></a>Výhody

CloudSimple virtuální počítače z portálu Azure poskytují samoobslužný mechanismus pro uživatele k vytváření a správě virtuálních počítačů VMware.

* Vytvoření virtuálního počítače CloudSimple v privátním cloudu vCenter
* Správa vlastností virtuálního počítače
  * Přidání nebo odebrání disků
  * Přidání nebo odebrání nic
* Napájení virtuálního počítače CloudSimple
  * Zapnutí a vypnutí
  * Resetování virtuálního počítače
* Odstranění virtuálního počítače

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [využívat virtuální počítače VMware v Azure](quickstart-create-vmware-virtual-machine.md)
* Přečtěte si, jak [mapovat předplatné Azure](azure-subscription-mapping.md)
