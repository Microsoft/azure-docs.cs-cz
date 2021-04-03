---
title: Azure cache pro možnosti izolace sítě Redis
description: V tomto článku se dozvíte, jak určit nejlepší řešení pro izolaci sítě podle svých potřeb. Projdeme základy používání privátních odkazů Azure, vkládání Azure Virtual Network (VNet) a Azure Firewall pravidla s jejich výhodami a omezeními.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ef284661d44f700cf0b5282efcd2e6f7b94fa3b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621514"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Azure cache pro možnosti izolace sítě Redis 
V tomto článku se dozvíte, jak určit nejlepší řešení pro izolaci sítě podle svých potřeb. Projdeme základy používání privátních odkazů Azure, vkládání Azure Virtual Network (VNet) a Azure Firewall pravidla s jejich výhodami a omezeními.  

## <a name="azure-private-link-public-preview"></a>Privátní odkaz Azure (Public Preview) 
Privátní propojení Azure poskytuje privátní připojení z virtuální sítě k Azure PaaS Services. Zjednodušuje architekturu sítě a zabezpečuje připojení mezi koncovými body v Azure tím, že eliminuje vystavení dat veřejnému Internetu. 

### <a name="advantages"></a>Výhody
* Podporováno pro instance Redis na úrovni Basic, Standard a Premium Azure cache. 
* Pomocí [privátního odkazu Azure](../private-link/private-link-overview.md)se můžete připojit k instanci Azure cache z vaší virtuální sítě prostřednictvím privátního koncového bodu, kterému se přiřadí privátní IP adresa v podsíti v rámci virtuální sítě. V tomto případě jsou instance mezipaměti dostupné jak v rámci virtuální sítě, tak i veřejně.  
* Po vytvoření privátního koncového bodu může být přístup k veřejné síti omezený pomocí `publicNetworkAccess` příznaku. Tento příznak je nastaven jako `Disabled` výchozí, což umožní přístup pouze k privátnímu propojení. Můžete nastavit hodnotu na `Enabled` nebo `Disabled` pomocí žádosti o opravu. Další informace najdete v tématu [Azure cache for Redis s privátním odkazem na Azure (Preview)](cache-private-link.md). 
* Všechny závislosti externích mezipamětí nebudou mít vliv na pravidla NSG virtuální sítě.

### <a name="limitations"></a>Omezení 
* Skupiny zabezpečení sítě (NSG) jsou u privátních koncových bodů zakázané. Pokud jsou však v podsíti jiné prostředky, bude na tyto prostředky platit vynucování NSG.
* Geografická replikace, pravidla brány firewall, podpora konzoly portálu, více koncových bodů na mezipamětní mezipaměť, trvalost na bránu firewall a virtuální sítě vložené do mezipaměti se zatím nepodporují. 
* Aby bylo možné se připojit ke clusterované mezipaměti, je `publicNetworkAccess` nutné nastavit na `Disabled` a může to být pouze jedno připojení privátního koncového bodu.

> [!NOTE]
> Při přidávání privátního koncového bodu do instance mezipaměti bude veškerý provoz Redis přesunut do privátního koncového bodu z důvodu DNS.
> Zajistěte, aby byla předchozí pravidla brány firewall upravena dříve.  
>
>

## <a name="azure-virtual-network-injection"></a>Vkládání Azure Virtual Network 
Virtuální síť je základním stavebním blokem vaší privátní sítě v Azure. Virtuální síť umožňuje mnoha prostředkům Azure zabezpečeně komunikovat mezi sebou, internetem a místními sítěmi. Virtuální síť je jako tradiční síť, kterou byste měli používat ve svém vlastním datovém centru, ale s výhodami infrastruktury, škálování, dostupnosti a izolace Azure. 

### <a name="advantages"></a>Výhody
* Když je u instance Azure cache for Redis nakonfigurovaná virtuální síť, není veřejně adresovatelná a je dostupná jenom z virtuálních počítačů a aplikací v rámci virtuální sítě.  
* Když je síť VNet kombinována s omezenými NSG zásadami, pomáhá snížit riziko exfiltrace dat. 
* Nasazení virtuální sítě poskytuje lepší zabezpečení a izolaci pro mezipaměť Azure pro Redis a podsítě, zásady řízení přístupu a další funkce pro další omezení přístupu. 
* Geografická replikace je podporovaná. 

### <a name="limitations"></a>Omezení
* Virtuální sítě vložené do mezipaměti jsou dostupné jenom pro službu Azure cache Premium pro Redis. 
* Pokud používáte mezipaměť s vloženou virtuální sítí, budete muset otevřít virtuální síť, abyste mohli ukládat závislosti do mezipaměti, jako jsou například seznamy CRL/PKI, integrace, Azure Storage, Azure Monitor a další.  


## <a name="azure-firewall-rules"></a>Pravidla Azure Firewall
[Azure firewall](../firewall/overview.md) je spravovaná cloudová služba zabezpečení sítě, která chrání vaše prostředky virtuální sítě Azure. Jedná se o plně stavovou bránu firewall jako službu s integrovanou vysokou dostupností a neomezenou škálovatelností cloudu. Můžete centrálně vytvářet, vynucovat a protokolovat zásady připojení k aplikacím a sítím napříč různými předplatnými a virtuálními sítěmi.  

### <a name="advantages"></a>Výhody
* Když jsou pravidla brány firewall nakonfigurovaná, můžou se k ní připojit jenom připojení klientů ze zadaných rozsahů IP adres. Připojení z mezipaměti Azure pro systémy monitorování Redis jsou vždycky povolená, i když jsou nakonfigurovaná pravidla brány firewall. Povolují se taky pravidla NSG, která definujete.  

### <a name="limitations"></a>Omezení
* Pravidla brány firewall se dají použít ve spojení s vloženými mezipamětí virtuální sítě, ale aktuálně se nejedná o privátní koncové body. 


## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak nakonfigurovat [mezipaměť s vloženou virtuální sítí pro instanci služby Premium Azure cache pro Redis](cache-how-to-premium-vnet.md).  
* Naučte se konfigurovat [pravidla brány firewall pro všechny úrovně Redis v mezipaměti Azure](cache-configure.md#firewall). 
* Naučte se [Konfigurovat privátní koncové body pro všechny Redis úrovně Azure cache](cache-private-link.md).