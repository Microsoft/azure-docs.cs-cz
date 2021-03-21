---
title: Požadavky a předpoklady pro použití replikace mezi oblastmi Azure NetApp Filesho svazku | Microsoft Docs
description: V této části najdete popis požadavků a důležitých informací o používání funkce replikace mezi oblastmi Azure NetApp Files.
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
ms.date: 01/20/2021
ms.author: b-juche
ms.openlocfilehash: ed03e20f7a1a24d1a38e023b958959fdc6fdc326
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579341"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>Požadavky a předpoklady pro použití replikace mezi oblastmi 

Všimněte si následujících požadavků a důležitých informací o [používání funkce replikace mezi oblastmi](cross-region-replication-create-peering.md) Azure NetApp Files:  

## <a name="requirements-and-considerations"></a>Požadavky a předpoklady 

* Funkce replikace mezi oblastmi je aktuálně ve verzi Public Preview. Pro přístup k této funkci musíte odeslat žádost pořadníku Azure NetApp Files prostřednictvím stránky pro [odeslání pořadníku replikace mezi oblastmi](https://aka.ms/anfcrrpreviewsignup). Než použijete funkci replikace mezi oblastmi, počkejte na oficiální e-mail s potvrzením od Azure NetApp Files týmu.
* Replikace Azure NetApp Files je k dispozici pouze v určitých dvojicích pevné oblasti. Podívejte se na [páry podporovaných oblastí](cross-region-replication-introduction.md#supported-region-pairs). 
* Svazky SMB jsou podporované společně se svazky NFS. Replikace svazků SMB vyžaduje připojení ke službě Active Directory ve zdrojovém a cílovém účtu NetApp. Cílové připojení AD musí mít přístup k serverům DNS nebo PŘIDÁVAT řadiče domény, které jsou dosažitelné z delegované podsítě v cílové oblasti. Další informace najdete v tématu [požadavky pro připojení ke službě Active Directory](create-active-directory-connections.md#requirements-for-active-directory-connections). 
* Cílový účet musí být v jiné oblasti než v oblasti zdrojového svazku. Můžete také vybrat existující účet NetApp v jiné oblasti.  
* Cílový svazek replikace je jen pro čtení, dokud [převezmete služby při selhání do cílové oblasti](cross-region-replication-manage-disaster-recovery.md#fail-over-to-destination-volume) a povolíte cílový svazek pro čtení a zápis. 
* Replikace Azure NetApp Files v současné době nepodporuje více předplatných; všechny replikace se musí provádět v rámci jednoho předplatného.
* Můžete nastavit maximálně pět svazků pro replikaci v rámci jednoho předplatného na oblast. Můžete otevřít lístek podpory pro vyžádání zvýšení výchozí kvóty pro pět cílových svazků replikace (na předplatné v oblasti). 
* Může to trvat až pět minut, než se rozhraní projeví nově přidaného snímku na zdrojovém svazku.  
* Nepodporují se kaskádové a ventilátory v topologiích a výstupech.
* Konfigurace replikace svazků pro zdrojové svazky vytvořené ze snímku není v tuto chvíli podporována.
* Po nastavení replikace mezi oblastmi vytvoří proces replikace *snímky SnapMirror* , které poskytnou odkazy mezi zdrojovým svazkem a cílovým svazkem. Snímky SnapMirror se automaticky cyklují, když se pro každé přírůstkové přenosy vytvoří nový. Snímky SnapMirror nemůžete odstranit, dokud se neodstraní vztah replikace a svazek. 
* Ruční snímky můžete odstranit ve zdrojovém svazku relace replikace, pokud je relace replikace aktivní nebo přerušená a také po odstranění vztahu replikace. Ruční snímky pro cílový svazek nemůžete odstranit, dokud není přerušený vztah replikace.
* Nelze se vrátit k snímku, který byl proveden před vytvořením cílového svazku replikace.

## <a name="next-steps"></a>Další kroky
* [Vytvoření replikace svazku](cross-region-replication-create-peering.md)
* [Zobrazení stavu vztahu replikace](cross-region-replication-display-health-status.md)
* [Správa zotavení po havárii](cross-region-replication-manage-disaster-recovery.md)
* [Metriky replikace svazků](azure-netapp-files-metrics.md#replication)
* [Odstranění svazků nebo replikací svazků](cross-region-replication-delete.md)
* [Řešení potíží s replikací mezi oblastmi](troubleshoot-cross-region-replication.md)


