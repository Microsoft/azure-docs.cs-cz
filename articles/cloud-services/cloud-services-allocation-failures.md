---
title: Řešení potíží s přidělením cloudové služby | Microsoft Docs
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
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 33d017d0e09e9b288b0514e85c8865f83a8a2fa1
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2017
ms.locfileid: "23984112"
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Řešení potíží s přidělením při nasazení Cloud Services v Azure
## <a name="summary"></a>Souhrn
Při nasazování instancí cloudové služby nebo přidat nový web nebo instancí rolí pracovního procesu, Microsoft Azure přiřadí výpočetní prostředky. Příležitostně zobrazí chyby při provádění těchto operací ještě před dosažením limity předplatného Azure. Tento článek vysvětluje příčiny některých běžných chyb přidělení a navrhne možné nápravu. Informace mohou být užitečné také při plánování nasazení vašich služeb.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Pozadí – fungování přidělení
Servery v datových centrech Azure jsou rozděleny do clusterů. Žádost o nové cloudové služby přidělení dojde k pokusu o několik clusterů. Při nasazení první instance do cloudové služby (ve buď pracovním nebo produkčním), která Cloudová služba získá připnuli do clusteru. Stane se žádné další nasazení pro cloudové služby ve stejném clusteru. V tomto článku budeme označovat to jako "připnutý do clusteru s podporou". Obrázek 1 níže znázorňuje malá normální přidělení, které dojde k pokusu o několik clusterů; Obrázek 2 znázorňuje malá přidělení, má připnuli k clusteru 2, protože se jedná, je hostitelem existující služby CS_1 cloudu.

![Diagram přidělení](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Proč se stane došlo k chybě přidělení
Při požadavku na přidělení je ukotvena do clusteru, existuje vyšší pravděpodobnost nedaří najít volných prostředků, protože fond zdrojů k dispozici je omezený na clusteru s podporou. Kromě toho pokud požadavek na přidělení umístěn v clusteru, ale tento cluster nepodporuje typ prostředku, který jste požadovali, vaši žádost se nezdaří i v případě, že cluster má volný prostředek. Obrázek 3 níže znázorňuje tento případ, kdy připojené přidělení nezdaří, protože cluster jenom candidate nemá uvolnění prostředků. Obrázek 4 znázorňuje tento případ, kde připojené přidělení nezdaří, protože pouze candidate clusteru nepodporuje požadovaná velikost virtuálního počítače, i když cluster má uvolnění prostředků.

![Došlo k chybě definovaného přidělení](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Řešení potíží s přidělením pro cloudové služby
### <a name="error-message"></a>Chybová zpráva
Mohou se zobrazit tato chybová zpráva:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Běžné problémy
Tady uvádíme běžné scénáře přidělení, které způsobí požadavek přidělení připojit do jednoho clusteru.

* Nasazení do pracovní pozici – cloudové služby má v buď slotu nasazení, pak celý Cloudová služba je připnutá k konkrétní clusteru.  To znamená, že pokud nasazení již existuje v produkčním slotu, pak nové pracovní nasazení lze pouze rozdělit ve stejném clusteru jako produkční slot. Pokud cluster se blíží obsazení, požadavek se pravděpodobně nezdaří.
* Škálování – přidání nové instance do stávající cloudovou službu, musíte přidělit ve stejném clusteru.  Malého škálování požadavků lze obvykle rozdělit, ale ne vždy. Pokud cluster se blíží obsazení, požadavek se pravděpodobně nezdaří.
* Skupina vztahů - nového nasazení do prázdné cloudové služby lze rozdělit podle prostředků infrastruktury v jakémkoliv clusteru v této oblasti, pokud cloudové služby je připojena do skupiny vztahů. Ve stejném clusteru se pokusí nasazení pro stejnou skupinu vztahů. Pokud cluster se blíží obsazení, požadavek se pravděpodobně nezdaří.
* Skupiny vztahů vNet - starší virtuální sítě bylo vázané na skupiny vztahů místo oblasti a cloudové služby v tyto virtuální sítě by připnutý do skupiny vztahů clusteru. Nasazení pro tento typ virtuální sítě se pokusí na definovaného clusteru. Pokud cluster se blíží obsazení, požadavek se pravděpodobně nezdaří.

## <a name="solutions"></a>Řešení
1. Na novou cloudovou službu znovu nasaďte – toto řešení je pravděpodobně být nejvíce úspěšné, protože umožňuje platformy můžete vybrat ze všech clusterů v této oblasti.

   * Nasazení zatížení na novou cloudovou službu  
   * Aktualizujte CNAME nebo záznam směrovaly přenášená data do nové cloudové služby
   * Jakmile nulové provoz prochází k původní lokalitě, můžete odstranit staré cloudové služby. Toto řešení měly ukládat nepřeruší.
2. Odstranit produkční a pracovní sloty – toto řešení se zachovat svůj existující název DNS, ale může způsobit výpadek do vaší aplikace.

   * Odstranit produkční a pracovní sloty stávající cloudovou službu, aby Cloudová služba je prázdná a potom
   * Vytvořte nové nasazení v rámci existující cloudové služby. To se znovu pokusí o přidělení na všech clusterech v oblasti. Zkontrolujte, zda že cloudové služby není vázaný do skupiny vztahů.
3. Vyhrazená IP adresa – toto řešení se zachovat stávající IP adresy, ale může způsobit výpadek do vaší aplikace.  

   * Vytvořit vyhrazenou IP adresu pro vaše stávající nasazení pomocí prostředí Powershell

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Postupujte podle #2 výše, a ujistěte se, pokud chcete zadat novou vyhrazenou IP adresu do služby CSCFG.
4. Odeberte skupinu vztahů pro nová nasazení - už doporučují skupiny vztahů. Postupujte podle kroků pro #1 výše a nasadit novou cloudovou službu. Ujistěte se, Cloudová služba není ve skupině vztahů.
5. Převést na regionální virtuální síť – viz [jak migrovat z skupiny vztahů regionální virtuální síť (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
