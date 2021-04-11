---
title: Použití virtuálního počítače s Windows s Azure NetApp Files | Microsoft Docs
description: Poskytuje osvědčené postupy a ukázkové modrotisky na nasazení Windows Virtual desktopu pomocí Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: a765d689307b7f56e5100e75d9f7121e944cea14
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168276"
---
# <a name="benefits-of-using-azure-netapp-files-with-windows-virtual-desktop"></a>Výhody použití Azure NetApp Files s Windows Virtual Desktopem 

Tento článek popisuje osvědčené postupy pro nasazení virtuálního počítače s Windows (WVD) pomocí Azure NetApp Files.

Azure NetApp Files je vysoce výkonná služba File Storage z Azure. Může poskytovat až 450 000 vstupně-výstupních operací za sekundu a latenci, která podporuje extrémně velkou škálu nasazení virtuálních ploch Windows. Šířku pásma můžete upravit a změnit úroveň služby Azure NetApp Files svazků na vyžádání téměř okamžitě, aniž byste museli pozastavit vstup/výstup a přitom zachovat přístup k rovině dat. Tato funkce umožňuje snadno optimalizovat škálování nasazení WVD pro náklady. Můžete také vytvořit prostorově efektivní snímky svazků, aniž by to ovlivnilo výkon svazku. Díky této možnosti můžete vracet jednotlivé [kontejnery profilů uživatelů FSLogix](../virtual-desktop/store-fslogix-profile.md) pomocí kopie z `~snapshot` adresáře nebo okamžitě vrátit celý svazek najednou prostřednictvím funkce vrácení svazku.  Díky až 255 snímků (rotace) pro ochranu svazku před ztrátou dat nebo poškozením můžou správci spoustu pravděpodobnosti vrátit zpátky, co bylo provedeno.

## <a name="sample-blueprints"></a>Ukázkové plány

Následující ukázkové plány znázorňují integraci virtuálního klienta s Windows s Azure NetApp Files. Ve scénáři společného stolního počítače se uživatelé přesměrují do hostitele s více relacemi [](../virtual-desktop/host-pool-load-balancing.md#breadth-first-load-balancing-method), a to pomocí [virtuálních počítačů s více relacemi](../virtual-desktop/windows-10-multisession-faq.yml#what-is-windows-10-enterprise-multi-session)ve fondu. Na druhé straně jsou osobní plochy rezervované pro scénáře, ve kterých má každý uživatel svůj vlastní virtuální počítač.

### <a name="pooled-desktop-scenario"></a>Scénář sdružené plochy

Pro scénář ve fondu [doporučuje](/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations) tým virtuálních počítačů s Windows podle počtu uživatelů vCPU následující pokyny. Všimněte si, že v tomto doporučení není zadaná žádná velikost virtuálního počítače.

|     Typ úlohy     |     Světlý    |     Střední    |     Těžký    |
|-----------------------|--------------|---------------|--------------|
|     Uživatelé na vCPU    |     6        |     4         |     2        |


Toto doporučení potvrzuje LoginVSI test s 500 uživatelem a do každého D16as_V4ho virtuálního počítače se zaznamená přibližně 62 "znalostní báze/středně středně velké uživatele". 

Například při 62 uživatelů na virtuální počítač D16as_V4 může Azure NetApp Files snadno podporovat uživatele 60 000 pro každé prostředí. Testování pro vyhodnocení horní meze D32as_v4 virtuálního počítače probíhá. Pokud má WVD uživatel na vCPU doporučení true pro D32as_v4, více než 120 000 uživatelů by se do 1 000 virtuálních počítačů vešlo ještě před [1 000 broaching omezením sítě IP VNet](./azure-netapp-files-network-topologies.md), jak je znázorněno na následujícím obrázku.  

![Scénář fondu virtuálních ploch Windows pro stolní počítače](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>Scénář osobní plochy 

V případě osobního stolního počítače se na následujícím obrázku zobrazuje doporučení architektury pro obecné účely. Uživatelé jsou namapováni na konkrétní pracovní lusky a každý z nich má pouze pod 1 000 virtuálních počítačů, přičemž místo pro IP adresy, které se šíří z virtuální sítě pro správu. Azure NetApp Files může snadno zpracovávat 900 + osobní plochy na virtuální síť fondu hostitelů s jednou relací, přičemž skutečný počet virtuálních počítačů se rovná 1 000 minus počet hostitelů pro správu, které se nacházejí ve virtuální síti centra. Pokud potřebujete více osobních stolních počítačů, je snadné přidat další lusky (fondy hostitelů a virtuální sítě), jak je znázorněno na následujícím obrázku. 

![Scénář osobní plochy pro virtuální počítače s Windows](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

Při sestavování architektury založené na POD ní přiřazení uživatelů ke správnému poli pod přihlášením je důležité zajistit, aby uživatelé vždy našli své uživatelské profily. 

## <a name="next-steps"></a>Další kroky

- [Architektury řešení s využitím Azure NetApp Files](azure-netapp-files-solution-architectures.md)