---
title: Řešení Azure VMware podle CloudSimple – použití privátního cloudu jako havárie pro místní úlohy
description: Popisuje, jak nastavit privátní cloud CloudSimple jako lokalitu pro zotavení po havárii pro místní úlohy VMware.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5dd1f157984cdb300cc3b375d71654e5da54d1ae
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898126"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Nastavení privátního cloudu CloudSimple jako lokality pro zotavení po havárii pro místní úlohy VMware

Privátní cloud CloudSimple je možné nastavit jako lokalitu pro obnovení místních aplikací, aby poskytoval provozní kontinuitu pro případ havárie. Řešení obnovení vychází z Zerto virtuální replikace jako platforma pro replikaci a orchestraci. Kritickou infrastrukturu a virtuální počítače aplikací je možné replikovat nepřetržitě z místního serveru vCenter do privátního cloudu. Privátní Cloud můžete použít pro testování převzetí služeb při selhání a zajištění dostupnosti aplikace během havárie. Podobný přístup se dá nastavit jako primární lokalita, která je chráněná lokalitou pro obnovení v jiném umístění.

> [!NOTE]
> Pokyny k určení velikosti prostředí pro zotavení po havárii najdete v tématu [požadavky na změnu velikosti dokumentu Zerto pro virtuální replikaci Zerto](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) .

Řešení CloudSimple:

* Eliminuje nutnost nastavit datacentrum specificky pro zotavení po havárii (DR).
* Umožňuje využívat umístění Azure, kde je CloudSimple nasazená pro celosvětově geografickou odolnost.
* Nabízí možnost snížit náklady na nasazení a celkové náklady na vlastnictví pro zotavení po havárii.

Řešení vyžaduje:

* Instalace, konfigurace a Správa Zerto v privátním cloudu.
* Zadejte vlastní licence pro Zerto, pokud je privátní cloud chráněným webem. Můžete spárovat Zerto běžící na webu CloudSimple s vaší místní lokalitou pro licencování.

Následující obrázek ukazuje architekturu pro řešení Zerto.

![Architektura](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Jak nasadit řešení

Následující části popisují, jak nasadit řešení zotavení po havárii pomocí virtuální replikace Zerto ve vašem privátním cloudu.

1. [Požadavky](#prerequisites)
2. [Volitelná konfigurace privátního cloudu CloudSimple](#optional-configuration-on-your-private-cloud)
3. [Nastavení ZVM a VRA v privátním cloudu CloudSimple](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Nastavení skupiny virtuálních ochran Zerto](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Požadavky

Pokud chcete povolit virtuální replikaci Zerto z místního prostředí do svého privátního cloudu, proveďte následující požadavky.

1. [Nastavte připojení VPN typu Site-to-site mezi vaší místní sítí a privátním cloudem CloudSimple](set-up-vpn.md).
2. [Nastavte vyhledávání DNS tak, aby vaše komponenty správy privátního cloudu byly předávány na servery DNS privátního cloudu](on-premises-dns-setup.md).  Pokud chcete povolit předávání vyhledávání DNS, vytvořte záznam zóny přesměrování na místním serveru DNS pro `*.cloudsimple.io` CloudSimple servery DNS.
3. Nastavte vyhledávání DNS tak, aby místní součásti vCenter byly předávány na místní servery DNS.  Servery DNS musí být dosažitelné z privátního cloudu CloudSimple prostřednictvím sítě Site-to-Site VPN. Pokud potřebujete pomoc, odešlete [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), která poskytne následující informace.  

    * Název místní domény DNS
    * IP adresy místních serverů DNS

4. Nainstalujte Windows Server do privátního cloudu. Server se používá k instalaci Virtual Manageru Zerto.
5. [Eskalace vašich CloudSimple oprávnění](escalate-private-cloud-privileges.md)
6. Vytvořte nového uživatele v privátním cloudu vCenter s rolí správce, který se použije jako účet služby pro Virtual Manager Zerto.

### <a name="optional-configuration-on-your-private-cloud"></a>Volitelná konfigurace v privátním cloudu

1. Vytvořte jeden nebo víc fondů prostředků v rámci vašeho privátního cloudu vCenter a použijte je jako cílové fondy zdrojů pro virtuální počítače z místního prostředí.
2. Vytvořte ve svém privátním cloudu vCenter jednu nebo více složek pro použití jako cílové složky pro virtuální počítače z místního prostředí.
3. Vytvořte sítě VLAN pro síť s podporou převzetí služeb při selhání a nastavte pravidla brány firewall. Otevřete [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) pro pomoc.
4. Vytvoření distribuovaných skupin portů pro síť s podporou převzetí služeb při selhání a testování sítě pro testování převzetí služeb virtuálních počítačů
5. Nainstalujte [DHCP a servery DNS](dns-dhcp-setup.md) nebo ve svém privátním cloudovém prostředí použijte řadič domény služby Active Directory.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>Nastavení ZVM a VRA ve vašem privátním cloudu

1. Nainstalujte Virtual Manager Zerto (ZVM) na Windows Server ve vašem privátním cloudu.
2. Přihlaste se k ZVM pomocí účtu služby vytvořeného v předchozích krocích.
3. Nastavte licencování pro Virtual Manager Zerto.
4. Nainstalujte zařízení Zerto Virtual Replication (VRA) na hostitele ESXi vašeho privátního cloudu.
5. Párování privátního cloudu ZVM s vašimi místními ZVM.

### <a name="set-up-zerto-virtual-protection-group"></a>Nastavení skupiny virtuálních ochran Zerto

1. Vytvořte novou skupinu virtuální ochrany (VPG) a zadejte prioritu pro VPG.
2. Vyberte virtuální počítače, které vyžadují ochranu pro provozní kontinuitu, a pokud je to potřeba, upravte pořadí spouštění.
3. Vyberte lokalitu pro obnovení jako váš privátní cloud a výchozí server pro obnovení jako cluster privátního cloudu nebo skupinu prostředků, kterou jste vytvořili. V privátním cloudu vyberte **vsanDatastore** pro úložiště dat obnovení.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Možnost hostitele můžete přizpůsobit pro jednotlivé virtuální počítače v rámci možnosti nastavení virtuálního počítače.

4. Podle potřeby upravte možnosti úložiště.
5. Zadejte sítě pro obnovení, které se mají použít pro síť s podporou převzetí služeb při selhání, a testovací síť převzetí služeb při selhání jako skupiny distribuovaných portů vytvořené dříve a podle potřeby upravte
6. V případě potřeby upravte nastavení sítě pro jednotlivé virtuální počítače a vytvořte VPG.
7. Po dokončení replikace test převzetí služeb při selhání.

## <a name="reference"></a>Referenční informace

[Dokumentace k Zerto](https://www.zerto.com/myzerto/technical-documentation/)
