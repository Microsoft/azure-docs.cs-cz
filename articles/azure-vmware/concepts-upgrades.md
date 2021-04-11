---
title: Koncepty – aktualizace a upgrady v privátním cloudu
description: Přečtěte si o klíčových procesech upgradu a funkcích v řešení Azure VMware.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 9810de40944f70a4efb7ec81d17868ffdf256c7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586144"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Aktualizace a upgrady privátního cloudu řešení Azure VMware

Jednou z výhod privátních cloudů řešení Azure VMware je platforma zachováváme za vás. Údržba zahrnuje automatizované aktualizace sady prostředků ověřovaného VMware, které vám pomůžou zajistit, abyste používali nejnovější verzi privátního cloudového softwaru řešení Azure VMware.

Konkrétně privátní cloud řešení Azure VMware zahrnuje:

- Vyhrazené holé uzly serveru zřízené pomocí VMware ESXi hypervisoru 
- Server vCenter pro správu ESXi a síti vSAN 
- Software definované sítě VMware NSX-T pro virtuální počítače s vSphere úlohami  
- VMware síti vSAN DataStore pro virtuální počítače úloh vSphere  
- HCX VMware pro mobilitu úloh  

Privátní cloud řešení Azure VMware zahrnuje i prostředky v Azure Underlay, které se vyžadují pro připojení a provozování privátního cloudu. Řešení Azure VMware nepřetržitě monitoruje stav obou komponent Underlay i VMware. Když řešení Azure VMware zjistí selhání, provede akci, aby opravila komponenty, které selhaly. 

## <a name="what-components-get-updated"></a>Které součásti se aktualizují?   

Řešení Azure VMware aktualizuje následující komponenty VMware: 

- vCenter Server a ESXi spuštěné na holéch uzlech serveru 
- Síti vSAN 
- NSX-T 

Řešení Azure VMware také aktualizuje software v Underlay, jako jsou ovladače, software na síťových přepínačích a firmware na holých uzlech. 

## <a name="types-of-updates"></a>Typy aktualizací

Řešení Azure VMware používá následující typy aktualizací pro komponenty VMware:

- Opravy: opravy zabezpečení a opravy chyb vydané VMware. 
- Aktualizace: Aktualizace dílčí verze jedné nebo více komponent VMware. 
- Upgrady: Aktualizace hlavní verze jedné nebo více komponent VMware.

Budete upozorněni před a po použití oprav do privátních cloudů. Kromě toho budeme s vámi naplánovali časový interval pro správu a údržbu před instalací aktualizací nebo upgradů do vašeho privátního cloudu. 

## <a name="vmware-appliance-backup"></a>Zálohování zařízení VMware 

Řešení Azure VMware také provádí zálohu konfigurace následujících komponent VMware:

- vCenter Server 
- Správce NSX – T 

V případě selhání může řešení Azure VMware tyto součásti obnovit ze zálohy konfigurace. 

## <a name="vmware-software-versions"></a>Verze softwaru VMware
[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s klíčovými procesy upgradu a funkcemi v řešení Azure VMware, budete možná chtít získat informace o:

- [Postup vytvoření privátního cloudu](tutorial-create-private-cloud.md).
- [Jak povolit prostředek řešení Azure VMware](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
