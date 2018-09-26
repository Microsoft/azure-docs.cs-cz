---
title: Řešení potíží s přidělením cloudové služby | Dokumentace Microsoftu
description: Řešení potíží s přidělením při nasazení Cloud Services v Azure
services: azure-service-management, cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 22888c76b27d287a8d7fb0f0f1f0a0d39d92375d
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092948"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Řešení potíží s přidělením při nasazení Cloud Services v Azure
## <a name="summary"></a>Souhrn
Při nasazení instancí pro Cloudovou službu nebo přidat nový web nebo instancí rolí pracovního procesu, Microsoft Azure přiděluje výpočetní prostředky. Zobrazí chyby může někdy při provádění těchto operací ještě před dosažením limity předplatného Azure. Tento článek popisuje některé běžné chyby v přidělení příčiny a navrhne možnou nápravu. Informace může být užitečné také při plánování nasazení služby.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Na pozadí – jak funguje přidělení
Servery v datových centrech Azure jsou rozdělené do clusterů. Dojde k pokusu o nové žádosti o přidělení službu cloudu v několika clusterech. Když je první instance nasazená na cloudové služby (na pracovní nebo produkční), která Cloudová služba získá připnuté na clusteru. Žádné další nasazení pro cloudovou službu k tomu dochází ve stejném clusteru. V tomto článku budeme odkazovat na tuto jako "připnuté na clusteru". Diagram 1 níže ukazuje případ normální rozdělení, které dojde k pokusu o v několika clusterech; Obrázek 2 znázorňuje velikost písmen přidělení, která má připnuté na clusteru 2, protože se jedná, kde se hostuje stávající CS_1 cloudové služby.

![Diagram přidělení](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Proč dojde k selhání přidělení
Při požadavku na přidělení je připnutá na cluster, je vyšší riziko selhání Získejte zdarma zdroje informací, protože fond k dispozici zdrojů je omezená na clusteru. Kromě toho pokud požadavek na přidělení je připnutá na cluster, ale tento cluster nepodporuje typ prostředku, který jste požadovali, vaši žádost se nezdaří i v případě, že cluster má volný prostředek. Obrázek 3 dole znázorňuje tento případ, ve kterém se připojené přidělení nezdaří, protože pouze Release candidate clusteru nemá žádné volné prostředky. Obrázek 4 ukazuje případ, kde připojené přidělení se nezdaří, protože pouze Release candidate cluster nepodporuje požadovanou velikost virtuálního počítače, i v případě, že cluster má volné prostředky.

![Došlo k chybě přidělení připnutých](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Řešení potíží s přidělením pro cloudové služby
### <a name="error-message"></a>Chybová zpráva
Může zobrazit následující chybová zpráva:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Běžné problémy
Zde jsou obvyklé scénáře přidělení, které způsobují požadavek na přidělení na připnout na jednom clusteru.

* Nasazuje pro přípravný Slot – cloudové služby je nasazení ve slotu buď, pak celý cloudové služby je připnutá na konkrétním clusteru.  To znamená, že pokud už v produkčním slotu existuje nasazení, dá se nové pracovní nasazení ve stejném clusteru přidělit jenom do tohoto produkčního slotu. Pokud cluster se už brzy kapacitu, žádost může selhat.
* Škálování – přidávání nových instancí do existující cloudovou službu, musíte přidělit ve stejném clusteru.  Malé požadavky na škálování se většinou podaří přidělit, ale neplatí to vždy. Pokud cluster se už brzy kapacitu, žádost může selhat.
* Skupina vztahů - nové nasazení do prázdné cloudové služby je možné přidělit podle prostředků infrastruktury v libovolný cluster. v dané oblasti, pokud do skupiny vztahů je připnutá cloudové služby. Nasazení do stejné skupiny vztahů se pokusí vytvořit na stejném clusteru. Pokud cluster se už brzy kapacitu, žádost může selhat.
* Skupina vztahů vNet - starší virtuální sítě bylo vázané na skupiny vztahů, místo oblasti a cloudové služby v těchto virtuálních sítích by připnuté na clusteru pro skupinu vztahů. Nasazení pro tento typ virtuální sítě se pokusí vytvořit v připojených clusteru. Pokud cluster se už brzy kapacitu, žádost může selhat.

## <a name="solutions"></a>Řešení
1. Opětovné nasazení do nové cloudové služby – toto řešení je pravděpodobně nejvíce úspěšná, protože umožňuje platformy zvolit ze všech clusterů v dané oblasti.

   * Nasazení úlohy na novou cloudovou službu  
   * Aktualizace záznamu nebo záznam CNAME tak, aby odkazoval provoz na novou cloudovou službu
   * Jakmile žádný provoz prochází na starý web, můžete odstranit staré cloudovou službu. Toto řešení by měl mít za následek nulovými výpadky.
2. Odstraňte produkční a přípravné sloty – toto řešení se zachovat existující název DNS, ale způsobí, že aplikace.

   * Odstraňte produkční a přípravné sloty existující cloudové služby tak, aby cloudové služby je prázdný a pak
   * Vytvořte nové nasazení do stávající cloudové služby. Tím se znovu pokusí o přidělení na všech clusterech v oblasti. Ujistěte se, že Cloudová služba se neváže na skupinu vztahů.
3. Vyhrazená IP adresa - toto řešení se zachovat stávající IP adresy, ale způsobí, že aplikace.  

   * Vytvořit vyhrazenou IP adresu pro vaše stávající nasazení pomocí Powershellu

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Postupujte podle uvedeného a ujistěte se, pokud chcete zadat novou vyhrazenou IP adresu do služby CSCFG #2.
4. Odeberte skupinu vztahů pro nová nasazení – skupiny vztahů už se nedoporučuje. Postupujte podle kroků pro #1 výše a nasaďte novou cloudovou službu. Ujistěte se Cloudová služba není ve skupině vztahů.
5. Převést na regionální virtuální síť – viz [migrace ze skupiny vztahů do regionální virtuální síť (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
