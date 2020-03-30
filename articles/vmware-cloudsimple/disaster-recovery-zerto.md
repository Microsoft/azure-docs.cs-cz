---
title: Řešení Azure VMware od CloudSimple – použijte privátní cloud jako web pro havárii pro místní úlohy
description: Popisuje, jak nastavit cloudový privátní cloud jako lokalitu pro zotavení po havárii pro místní úlohy VMware.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e019a9229b671be2fb73e758bd39f33657bc2d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083131"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Nastavení cloudového privátního cloudu jako lokality pro zotavení po havárii pro místní úlohy VMware

Váš CloudSimple Private Cloud lze nastavit jako lokalitu pro obnovení pro místní aplikace, které poskytují kontinuitu podnikání v případě havárie. Řešení pro obnovení je založeno na virtuální replikaci Zerto jako platformy replikace a orchestrace. Kritické infrastruktury a virtuálních počítačů aplikací lze replikovat nepřetržitě z místního virtuálního centra do privátního cloudu. Privátní cloud můžete použít pro testování převzetí služeb při selhání a k zajištění dostupnosti vaší aplikace během havárie. Podobný přístup lze sledovat k nastavení privátního cloudu jako primární lokality, která je chráněna lokalitou pro obnovení v jiném umístění.

> [!NOTE]
> Pokyny pro nastavení velikosti prostředí pro zotavení po havárii naleznete v dokumentu Zerto S ohledem na [důležité informace o velikosti pro virtuální replikaci Zerto.](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf)

CloudSimple řešení:

* Eliminuje potřebu nastavit datové centrum speciálně pro zotavení po havárii (DR).
* Umožňuje využít umístění Azure, kde se CloudSimple nasazuje pro celosvětovou geografickou odolnost.
* Poskytuje možnost snížit náklady na nasazení a celkové náklady na vlastnictví zotavení po havárii.

Řešení vyžaduje, abyste:

* Nainstalujte, nakonfigurujte a spravujte Zerto ve svém privátním cloudu.
* Poskytněte si vlastní licence pro Zerto, když je Privátní cloud chráněným webem. Zerto spuštěný na webu CloudSimple můžete spárovat s místním webem pro licencování.

Následující obrázek znázorňuje architekturu řešení Zerto.

![Architektura](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Jak nasadit řešení

Následující části popisují, jak nasadit řešení zotavení po havárii pomocí virtuální replikace Zerto v privátním cloudu.

1. [Požadavky](#prerequisites)
2. [Volitelná konfigurace v privátním cloudu CloudSimple](#optional-configuration-on-your-private-cloud)
3. [Nastavení ZVM a VRA v privátním cloudu CloudSimple](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Nastavit Skupinu virtuální ochrany Zerto](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Požadavky

Chcete-li povolit virtuální replikaci Zerto z místního prostředí do privátního cloudu, vyplňte následující požadavky.

1. [Nastavte připojení VPN site-to-site mezi místní sítí a privátním cloudem CloudSimple](set-up-vpn.md).
2. [Nastavte vyhledávání DNS tak, aby byly komponenty správy privátního cloudu předávány serverům DNS privátního cloudu](on-premises-dns-setup.md).  Chcete-li povolit předávání vyhledávání DNS, vytvořte položku zóny předávání `*.cloudsimple.io` na místním serveru DNS pro servery CloudSimple DNS.
3. Nastavte vyhledávání DNS tak, aby místní součásti vCenter byly předávány místním serverům DNS.  Servery DNS musí být dosažitelné z vašeho cloudu CloudSimple Private Cloud přes síť VPN site-to-site. Pro pomoc odešlete [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)a uveďte následující informace.  

    * Místní název domény DNS
    * Adresy IP místního serveru DNS

4. Nainstalujte server Windows do privátního cloudu. Server se používá k instalaci Zerto Virtual Manager.
5. [Eskalovat vaše CloudSimple oprávnění](escalate-private-cloud-privileges.md).
6. Vytvořte nového uživatele v privátním cloudu vCenter s rolí správce, kterou chcete použít jako účet služby pro virtuální manažerZerto.

### <a name="optional-configuration-on-your-private-cloud"></a>Volitelná konfigurace v privátním cloudu

1. Vytvořte jeden nebo více fondů prostředků v privátním cloudu vCenter, který se použije jako cílové fondy prostředků pro virtuální počítače z místního prostředí.
2. Vytvořte jednu nebo více složek v privátním cloudu vCenter, které se použijí jako cílové složky pro virtuální počítače z místního prostředí.
3. Vytvořte sítě VLAN pro síť s podporou převzetí služeb při selhání a nastavte pravidla brány firewall. Otevřete [žádost o pomoc.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
4. Vytvořte distribuované skupiny portů pro síť s podporou převzetí služeb při selhání a testovací síť pro testování převzetí služeb při selhání virtuálních připojení.
5. Nainstalujte [servery DHCP a DNS](dns-dhcp-setup.md) nebo použijte řadič domény služby Active Directory v prostředí privátního cloudu.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>Nastavení ZVM a VRA v privátním cloudu

1. Nainstalujte Zerto Virtual Manager (ZVM) na server Windows ve vašem privátním cloudu.
2. Přihlaste se k ZVM pomocí účtu služby vytvořeného v předchozích krocích.
3. Nastavte licencování pro virtuální manažerzerto.
4. Nainstalujte zařízení Pro virtuální replikaci Zerto (VRA) na hostitele ESXi vašeho privátního cloudu.
5. Spárujte svůj Privátní cloud ZVM s místním ZVM.

### <a name="set-up-zerto-virtual-protection-group"></a>Nastavit Skupinu virtuální ochrany Zerto

1. Vytvořte novou skupinu virtuální ochrany (VPG) a určete prioritu v rámci vpg.
2. Vyberte virtuální počítače, které vyžadují ochranu pro kontinuitu provozu a v případě potřeby přizpůsobte pořadí spouštění.
3. Vyberte lokalitu pro obnovení jako privátní cloud a výchozí server pro obnovení jako cluster privátního cloudu nebo skupinu prostředků, kterou jste vytvořili. Vyberte **vsanDatastore** pro úložiště dat obnovení v privátním cloudu.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Můžete přizpůsobit možnost hostitele pro jednotlivé virtuální počítače v rámci možnostnastavení nastavení virtuálních počítače.

4. Podle potřeby si můžete přizpůsobit možnosti úložiště.
5. Zadejte sítě pro obnovení, které mají být používány pro síť s podporou převzetí služeb při selhání a testovací síť s podporou převzetí služeb při selhání, jako dříve vytvořené distribuované skupiny portů a podle potřeby přizpůsobte skripty pro obnovení.
6. V případě potřeby přizpůsobte nastavení sítě pro jednotlivé virtuální počítače a vytvořte VPG.
7. Otestujte převzetí služeb při selhání po dokončení replikace.

## <a name="reference"></a>Odkaz

[Zerto dokumentace](https://www.zerto.com/myzerto/technical-documentation/)
