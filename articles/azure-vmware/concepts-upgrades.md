---
title: Koncepty – aktualizace a upgrady v privátním cloudu
description: Přečtěte si o klíčových procesech upgradu a funkcích v řešení Azure VMware.
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 6fb65a0e192fbf1f054857223b3fdaf58fc6a903
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809645"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Aktualizace a upgrady privátního cloudu řešení Azure VMware

Jednou z klíčových výhod privátních cloudů řešení Azure VMware je to, že platforma je zachována za vás. Údržba platformy zahrnuje automatizované aktualizace pro validované softwarové sady VMware, což pomáhá zajistit, abyste používali nejnovější verzi ověřeného soukromého cloudového softwaru řešení Azure VMware.

Konkrétně privátní cloud řešení Azure VMware zahrnuje:

- Vyhrazené holé uzly serveru zřízené pomocí VMware ESXi hypervisoru 
- Server vCenter pro správu ESXi a síti vSAN 
- Software definované sítě VMware NSX-T pro virtuální počítače s vSphere úlohami  
- VMware síti vSAN DataStore pro virtuální počítače úloh vSphere  
- HCX VMware pro mobilitu úloh  

Kromě těchto součástí zahrnuje privátní cloud řešení Azure VMware prostředky ve službě Azure Underlay, která je nutná pro připojení a provozování privátního cloudu. Řešení Azure VMware nepřetržitě monitoruje stav obou komponent Underlay i VMware. Když řešení Azure VMware zjistí selhání, provede akci, aby opravila komponenty, které selhaly. 

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

Budete upozorněni před a po použití oprav pro vaše privátní cloudy. Budeme s vámi také spolupracovat, abyste naplánovali časový interval pro správu a údržbu před instalací aktualizací nebo upgradů do vašeho privátního cloudu. 

## <a name="vmware-appliance-backup"></a>Zálohování zařízení VMware 

Kromě toho, že řešení Azure VMware má k disloze tyto komponenty VMware, převezme tyto informace:

- vCenter Server 
- Správce NSX – T 

V případě selhání může řešení Azure VMware je obnovit ze zálohy konfigurace. 

Další informace o verzích softwaru VMware najdete v [článku koncept privátních cloudů a clusterů](concepts-private-clouds-clusters.md) a v [nejčastějších dotazech](faq.md).

## <a name="next-steps"></a>Další kroky

Dalším krokem je [Vytvoření privátního cloudu](tutorial-create-private-cloud.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
