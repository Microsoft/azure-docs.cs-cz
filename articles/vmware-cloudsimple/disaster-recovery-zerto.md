---
title: Řešení Azure VMware (AVS) – použití privátního cloudu služby AVS jako havárie pro místní úlohy
description: Popisuje, jak nastavit privátní cloud služby AVS jako lokalitu pro zotavení po havárii pro místní úlohy VMware.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1f34c4fb89d28a001a4af4d21879403a9ac5e860
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024700"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Nastavení privátního cloudu pro funkci AVS jako lokality pro zotavení po havárii pro místní úlohy VMware

Váš privátní cloud služby AVS můžete nastavit jako lokalitu pro obnovení místních aplikací, aby se zajistila Kontinuita podnikových prostředí v případě havárie. Řešení obnovení vychází z Zerto virtuální replikace jako platforma pro replikaci a orchestraci. Kritickou infrastrukturu a virtuální počítače aplikací je možné replikovat nepřetržitě z místního serveru vCenter do vašeho privátního cloudu služby AVS. Privátní cloud služby AVS můžete použít pro testování převzetí služeb při selhání a zajištění dostupnosti aplikace během havárie. Podobný přístup můžete nastavit jako primární lokalitu, která je chráněná lokalitou pro obnovení v jiném umístění.

> [!NOTE]
> Pokyny k určení velikosti prostředí pro zotavení po havárii najdete v tématu [požadavky na změnu velikosti dokumentu Zerto pro virtuální replikaci Zerto](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) .

Řešení AVS:

* Eliminuje nutnost nastavit datacentrum specificky pro zotavení po havárii (DR).
* Umožňuje využívat umístění Azure, kde je nasazená službou AVS pro celosvětově geografickou odolnost.
* Nabízí možnost snížit náklady na nasazení a celkové náklady na vlastnictví pro zotavení po havárii.

Řešení vyžaduje:

* Instalace, konfigurace a Správa Zerto v privátním cloudu služby AVS
* Pokud je privátní cloud služby AVS chráněným webem, zadejte vlastní licence pro Zerto. Můžete spárovat Zerto běžící na webu služby AVS pomocí místní lokality pro licencování.

Následující obrázek ukazuje architekturu pro řešení Zerto.

![Architektura](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Jak nasadit řešení

Následující části popisují, jak nasadit řešení zotavení po havárii pomocí virtuální replikace Zerto v privátním cloudu služby AVS.

1. [Požadavky](#prerequisites)
2. [Volitelná konfigurace privátního cloudu služby AVS](#optional-configuration-on-your-avs-private-cloud)
3. [Nastavení ZVM a VRA v privátním cloudu služby AVS](#set-up-zvm-and-vra-on-your-avs-private-cloud)
4. [Nastavení skupiny virtuálních ochran Zerto](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Požadavky

Pokud chcete povolit virtuální replikaci Zerto z místního prostředí do vašeho privátního cloudu služby AVS, proveďte následující požadavky.

1. [Nastavte připojení VPN typu Site-to-site mezi vaší místní sítí a Vaším privátním cloudem služby AVS](set-up-vpn.md).
2. [Nastavte vyhledávání DNS tak, aby komponenty správy privátního cloudu služby AVS byly předávány na servery DNS privátního cloudu služby AVS](on-premises-dns-setup.md). Pokud chcete povolit předávání vyhledávání DNS, vytvořte záznam zóny přesměrování na místním serveru DNS pro `*.AVS.io` na servery DNS se službou AVS.
3. Nastavte vyhledávání DNS tak, aby místní součásti vCenter byly předávány na místní servery DNS. Servery DNS musí být dosažitelné z privátního cloudu služby AVS prostřednictvím sítě VPN typu Site-to-site. Pokud potřebujete pomoc, odešlete [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), která poskytne následující informace. 

    * Název místní domény DNS
    * IP adresy místních serverů DNS

4. Nainstalujte Windows Server do privátního cloudu služby AVS. Server se používá k instalaci Virtual Manageru Zerto.
5. [Eskalace vašich oprávnění](escalate-private-cloud-privileges.md)ke službě AVS
6. Vytvořte nového uživatele v rámci vašeho privátního cloudu služby AVS s rolí správce, který se použije jako účet služby pro Virtual Manager Zerto.

### <a name="optional-configuration-on-your-avs-private-cloud"></a>Volitelná konfigurace privátního cloudu služby AVS

1. Vytvořte jeden nebo víc fondů prostředků v rámci vašeho privátního cloudu služby AVS a použijte je jako cílové fondy zdrojů pro virtuální počítače z místního prostředí.
2. Vytvořte ve vaší službě AVS privátní cloud vCenter jednu nebo víc složek, které se použijí jako cílové složky pro virtuální počítače z místního prostředí.
3. Vytvořte sítě VLAN pro síť s podporou převzetí služeb při selhání a nastavte pravidla brány firewall. Otevřete [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) pro pomoc.
4. Vytvoření distribuovaných skupin portů pro síť s podporou převzetí služeb při selhání a testování sítě pro testování převzetí služeb virtuálních počítačů
5. Nainstalujte [servery DHCP a DNS](dns-dhcp-setup.md) nebo použijte řadič domény služby Active Directory v prostředí privátního cloudu služby AVS.

### <a name="set-up-zvm-and-vra-on-your-avs-private-cloud"></a>Nastavení ZVM a VRA v privátním cloudu služby AVS

1. Nainstalujte Virtual Manager Zerto (ZVM) na Windows Server v privátním cloudu služby AVS.
2. Přihlaste se k ZVM pomocí účtu služby vytvořeného v předchozích krocích.
3. Nastavte licencování pro Virtual Manager Zerto.
4. Nainstalujte zařízení Zerto Virtual Replication (VRA) na hostitele ESXi vašeho privátního cloudu v rámci služby AVS.
5. Párování privátního cloudu služby AVS v ZVM s vašimi místními ZVM.

### <a name="set-up-zerto-virtual-protection-group"></a>Nastavení skupiny virtuálních ochran Zerto

1. Vytvořte novou skupinu virtuální ochrany (VPG) a zadejte prioritu pro VPG.
2. Vyberte virtuální počítače, které vyžadují ochranu pro provozní kontinuitu, a pokud je to potřeba, upravte pořadí spouštění.
3. Vyberte lokalitu pro obnovení jako privátní cloud služby AVS a výchozí server pro obnovení jako cluster služby AVS Private Cloud nebo skupinu prostředků, kterou jste vytvořili. V privátním cloudu služby AVS vyberte **vsanDatastore** pro úložiště dat obnovení.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Možnost hostitele můžete přizpůsobit pro jednotlivé virtuální počítače v rámci možnosti nastavení virtuálního počítače.

4. Podle potřeby upravte možnosti úložiště.
5. Zadejte sítě pro obnovení, které se mají použít pro síť s podporou převzetí služeb při selhání, a testovací síť převzetí služeb při selhání jako skupiny distribuovaných portů vytvořené dříve a podle potřeby upravte
6. V případě potřeby upravte nastavení sítě pro jednotlivé virtuální počítače a vytvořte VPG.
7. Po dokončení replikace test převzetí služeb při selhání.

## <a name="reference"></a>Referenční informace

[Dokumentace k Zerto](https://www.zerto.com/myzerto/technical-documentation/)
