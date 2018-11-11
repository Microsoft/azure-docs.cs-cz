---
title: Doporučených distribucích systému Linux | Dokumentace Microsoftu
description: Další informace o systému Linux v distribucích schválených pro Azure, včetně pokynů pro Ubuntu, CentOS, Oracle a SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: szark
ms.openlocfilehash: b39ae6b5546e9504b9f37504c486c941ab034399
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249995"
---
# <a name="linux-on-distributions-endorsed-by-azure"></a>Linux v distribucích schválených pro Azure
Partneři poskytují imagí Linuxu v Tržišti Azure Marketplace. Pracujeme na tom s různými komunitami Linux přidejte ještě více rozšířili se schválenou sadou distribučního seznamu. Do té doby pro distribuce, které nejsou k dispozici z webu Marketplace, můžete vždy použít vlastní Linux podle pokynů v tématu [vytvoření a nahrání virtuálního pevného disku obsahujícího operační systém Linux](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Podporované distribuce a verze
V následující tabulce jsou uvedeny v Linuxových distribucích a verzích, které jsou podporovány v Azure. Odkazovat na [podporu pro Linuxové Image v Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) podrobné informace o podpoře pro Linux a opensourcové technologie v Azure.

Ovladače služby Linux Integration Services (LIS) pro Hyper-V a Azure jsou jádra moduly, které Microsoft přispívá přímo do nadřazeného jádro Linuxu.  Některé ovladače služby LIS jsou ve výchozím nastavení součástí distribuce jádra. Starší distribuce, které jsou založeny na Red Hat Enterprise (RHEL) jsou dostupné jako samostatný soubor ke stažení na CentOS [Linux Integration Services verze 4.2 pro Hyper-V a Azure](https://www.microsoft.com/en-us/download/details.aspx?id=55106). Zobrazit [požadavky jádro Linuxu](create-upload-generic.md#linux-kernel-requirements) Další informace o ovladače služby LIS.

Azure Linux Agent je už předem nainstalované na Image Azure Marketplace a je obvykle k dispozici z úložiště balíčků vaší distribuce. Zdrojový kód můžete najít na [Githubu](https://github.com/azure/walinuxagent).

  
| Distribuce | Verze | Ovladače | Agent |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3 + 7.0 + |CentOS 6.3: [LIS stáhnout](https://www.microsoft.com/en-us/download/details.aspx?id=55106)<p>CentOS 6.4 +: V jádra |Balíček: V [úložiště](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) v části "WALinuxAgent" <br/>Zdrojový kód: [Githubu](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |Jádra systému |Zdrojový kód: [Githubu](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9 +, 8.2 + |Jádra systému |Balíček: V úložišti v části "waagent" <br/>Zdrojový kód: [Githubu](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |Jádra systému |Balíček: V úložišti v části "WALinuxAgent" <br/>Zdrojový kód: [Githubu](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7 + 7.1 + |Jádra systému |Balíček: V úložišti v části "WALinuxAgent" <br/>Zdrojový kód: [Githubu](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES pro SAP<br>11 SP4<br>12 SP1 +<br>15|Jádra systému |Balíček:<p> pro 11 v [cloudu: nástroje](https://build.opensuse.org/project/show/Cloud:Tools) úložiště<br>12 součástí modulu "Veřejný Cloud" v části "python agenta azure"<br/>Zdrojový kód: [Githubu](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE přestupné 42.2 + |Jádra systému |Balíček: V [cloudu: nástroje](https://build.opensuse.org/project/show/Cloud:Tools) úložiště v části "python agenta azure" <br/>Zdrojový kód: [Githubu](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04 +  **<sup>1</sup>** |Jádra systému |Balíček: V úložišti v části "walinuxagent" <br/>Zdrojový kód: [Githubu](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>**  podporu pro Ubuntu 12.04 v Azure najdete [konce řádku oznámení](https://azure.microsoft.com/blog/ubuntu-12-04-precise-pangolin-nearing-end-of-life/).


## <a name="partners"></a>Partneři

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Z webu CoreOS:

*CoreOS je určená pro zabezpečení, konzistence a spolehlivost. Místo instalace balíčků přes yum nebo apt, CoreOS používá kontejnery Linuxu pro správu svých služeb na vyšší úrovni abstrakce. Kód jediné služby a všechny závislosti jsou sbaleny do kontejneru, který může běžet na jeden nebo více počítačích CoreOS.*

### <a name="credativ"></a>credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ je nezávislé konzultaci s a služby společnosti, která se specializuje na vývoji a implementaci profesionální řešení pomocí bezplatného softwaru. Jako přední open source specialisty má Credativ mezinárodní rozpoznávání s řadu IT oddělení, které používají jejich podporu. Ve spojení s Microsoftem Credativ aktuálně připravuje odpovídající Debian Image Debian 8 (Jessie) a Debian před 7 (Wheezy). Obě bitové kopie jsou navrženy speciálně pro použití v Azure a je možné snadno spravovat prostřednictvím platformy. Credativ bude podporovat také dlouhodobou údržbu a aktualizace Image Debian pro Azure prostřednictvím jeho otevřít zdroj Support Center.

### <a name="oracle"></a>Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Strategie Oracle je nabídnout širokého portfolia řešení pro veřejných a privátních cloudů. Tato strategie dává zákazníkům možnost volby a flexibilitu v tom, jak nasadit software Oracle během používání Oracle cloudů i v jiných cloudech. Partnerství Oraclu s Microsoftem umožňuje zákazníkům nasadit software Oracle ve veřejných a privátních cloudech Microsoftu s jistotou certifikace a podpory od Oraclu.  Závazek a investice do řešení veřejného a soukromého cloudu Oracle Oracle je beze změny.

### <a name="red-hat"></a>Red Hat
[http://www.redhat.com/en/partners/strategic-alliance/microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Přední poskytovatel na světě open source řešení Red Hat pomáhá více než 90 % společností z žebříčku Fortune 500 řešit obchodní problémy, mapování scénářů IT a obchodní strategie a připraví na budoucnost technologie. Red Hat to provádí poskytování zabezpečeného řešení prostřednictvím open obchodní model a model pomocí cenově dostupné a předvídatelná předplatného.

### <a name="suse"></a>SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server v Azure je prověřené platformě, která vám poskytuje spolehlivost a zabezpečení pro cloud computingu. SUSE na univerzální platformě Linux se hladce integrují s cloudovými službami Azure umožní snadno spravovatelné cloudovém prostředí. S více než 9,200 certifikovaných aplikací od 1800 nezávislých dodavatelů softwaru pro SUSE Linux Enterprise serveru SUSE zajišťuje, úlohy běžící v podporovaných v datovém centru, můžou bez problémů dokážete nasadit v Azure.

### <a name="canonical"></a>Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Canonical inženýrství a zásad správného řízení komunity open jednotka pro Ubuntu úspěch v klientovi, serveru a cloud computingu, což zahrnuje osobní cloudových služeb pro spotřebitele. Od firmy canonical vize platformy jednotný, bez v Ubuntu, z telefonu do cloudu, poskytuje řadu přeměnit rozhraní pro telefony, tablety, TV a desktop. Tuto vizi říká díky první volbu pro různé instituce od poskytovatelů veřejných cloudů tvůrci spotřební elektroniku a Oblíbené položky mezi jednotlivé technologists Ubuntu.

S vývojáři a technologie výrobních centrech po celém světě Canonical jednoznačně umístěn při spolupráci s tvůrci hardwaru, poskytovatele obsahu a vývojářům uveďte Ubuntu řešení na trh pro počítače, servery a kapesních zařízeních.
