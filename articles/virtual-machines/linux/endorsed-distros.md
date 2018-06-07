---
title: Schválené distribucích systému Linux | Microsoft Docs
description: Další informace o Linux na distribuce schválené pro Azure, včetně pokynů pro Ubuntu, CentOS, Oracle a SUSE.
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
ms.openlocfilehash: c2233ab32314655c510c8c884641293ae3325ef5
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809756"
---
# <a name="linux-on-distributions-endorsed-by-azure"></a>Linux na distribuce schválené pro Azure
Partneři poskytovat Linux bitové kopie v Azure Marketplace. Pracujeme s různé komunit Linux přidat i více typů schválené distribučního seznamu. Do té doby pro distribuce, které nejsou k dispozici z Marketplace, můžete vždy zahrnout vlastní Linux podle pokynů v tématu [vytvoření a nahrání virtuálního pevného disku obsahující operační systém Linux](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Podporované distribuce a verze
Následující tabulka uvádí Linuxových distribucích a verzích, které jsou podporovány v Azure. Odkazovat na [podporu pro Linux bitové kopie v Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) podrobné informace o podpoře pro systémy Linux a technologií s otevřeným zdrojem v Azure.

Linux integrační služby (LIS) ovladače pro Hyper-V a Azure jsou moduly jádra, které Microsoft přispívá přímo do nadřazeného jádra systému Linux.  Některé ovladače LIS jsou součástí distribuční jádra ve výchozím nastavení. Starší distribuce, které jsou založeny na Red Hat Enterprise (RHEL) / CentOS jsou k dispozici jako samostatný soubor ke stažení na [Linux integrační služby verzi 4.2 pro Hyper-V a Azure](https://www.microsoft.com/en-us/download/details.aspx?id=55106). V tématu [Linux jádra požadavky](create-upload-generic.md#linux-kernel-requirements) Další informace o ovladačích LIS.

Azure Linux Agent je již předem nainstalován pro Azure Marketplace Image a je obvykle dostupná z úložiště balíčků pro distribuci. Zdrojový kód najdete na [Githubu](https://github.com/azure/walinuxagent).

  
| Distribuce | Verze | Ovladače | Agent |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3 + 7.0 + |CentOS 6.3: [LIS stáhnout](https://www.microsoft.com/en-us/download/details.aspx?id=55106)<p>CentOS 6.4 +: V jádra |Balíček: V [úložišti](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) v části "WALinuxAgent" <br/>Zdrojový kód: [Githubu](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |Jádra systému |Zdrojový kód: [Githubu](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9 +, 8.2 + |Jádra systému |Balíček: V úložišti v části "příkaz waagent" <br/>Zdrojový kód: [Githubu](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |Jádra systému |Balíček: V úložišti v části "WALinuxAgent" <br/>Zdrojový kód: [Githubu](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7 +, 7.1 + |Jádra systému |Balíček: V úložišti v části "WALinuxAgent" <br/>Zdrojový kód: [Githubu](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES pro SAP<br>11 SP4<br>12 SP1 +|Jádra systému |Balíček:<p> pro 11 v [cloudu: nástroje](https://build.opensuse.org/project/show/Cloud:Tools) úložišti<br>pro 12 součástí modulu "Veřejného cloudu" v části "python agenta azure"<br/>Zdrojový kód: [Githubu](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE přestupného 42.2 + |Jádra systému |Balíček: V [cloudu: nástroje](https://build.opensuse.org/project/show/Cloud:Tools) úložišti v části "python agenta azure" <br/>Zdrojový kód: [Githubu](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04 +  **<sup>1</sup>** |Jádra systému |Balíček: V úložišti v části "walinuxagent" <br/>Zdrojový kód: [Githubu](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>**  Ubuntu 12.04 podpory v Azure naleznete [EOL oznámení](https://azure.microsoft.com/blog/ubuntu-12-04-precise-pangolin-nearing-end-of-life/).


## <a name="partners"></a>Partneři

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Z webu CoreOS:

*CoreOS je určená pro zabezpečení, konzistence a spolehlivosti. Místo instalace balíčků prostřednictvím yum nebo výstižný, používá jádro operačního systému Linux kontejnery ke správě vašim službám na vyšší úrovni abstrakce. V rámci kontejneru, který se dá spustit na jeden nebo více počítačů CoreOS balíčku kódu jedné služby a všechny závislosti.*

### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ je nezávislé poradě a služby společnosti, který se specializuje na vývoji a implementaci řešení pro odborníky v oblasti s použitím bezplatný software. Jako počáteční open-source odborníky obsahuje Credativ mezinárodní rozpoznávání s řadu IT oddělení, které používají jejich podporu. Ve spojení se společností Microsoft Credativ aktuálně připravuje odpovídající Debian bitové kopie pro Debian 8 (Klára) a Debian před 7 (Wheezy). Obě Image jsou navrženy speciálně pro spouštění v Azure a se dají snadno spravovat prostřednictvím platformy. Credativ se také podporují dlouhodobé údržby a aktualizace Debian bitových kopií pro Azure prostřednictvím centra podpory jeho otevřený zdroj.

### <a name="oracle"></a>Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Strategie pro Oracle je nabízí široké portfolio řešení pro veřejné a privátní cloudy. Strategie poskytuje zákazníkům možnost volby a flexibilitu v tom, jak nasadit software Oracle v cloudech Oracle a ostatních cloudů. Partnerství Oraclu s Microsoftem umožňuje zákazníkům nasadit software Oracle ve veřejných a privátních cloudech Microsoftu s jistotou certifikace a podpory od Oraclu.  Oracle na závazků a investice do řešení Oracle veřejného a privátního cloudu se nezmění.

### <a name="red-hat"></a>Red Hat
[http://www.redhat.com/en/partners/strategic-alliance/microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Na světě přední poskytovatel řešení s otevřeným zdrojem, Red Hat pomáhá více než 90 % žádnou 500 společností vyřešit obchodní problémy, Zarovnat IT a obchodní strategie a příprava pro budoucí technologie. Red Hat tomu poskytování zabezpečeného řešení prostřednictvím otevřete obchodní model a model cenově dostupné, předvídatelný předplatné.

### <a name="suse"></a>SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server v Azure je Principy platforma, která poskytuje vyšší spolehlivost a zabezpečení pro cloud computing. SUSE pro univerzální platformu Linux zajišťuje bezproblémovou integraci se službou Azure cloud services k poskytování snadno spravovatelné cloudové prostředí. Více než 9,200 certifikovaných aplikací z více než 1800 nezávislí dodavatelé softwaru pro SUSE Linux Enterprise Server SUSE zajistí, že úlohy běžící v datovém centru podporované můžou s jistotou nasazené na Azure.

### <a name="canonical"></a>Canonical
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Kanonický inženýrství a otevřete komunity zásad správného řízení jednotka Ubuntu na úspěch v klienta, serveru a cloud computing, což zahrnuje osobní cloudové služby pro spotřebitele. Kanonický na vizi jednotné a bezplatná platforma v Ubuntu, z telefonu do cloudu, poskytuje řadu jednotné rozhraní pro telefon, tablet, TV a plochy. Tato vize díky Ubuntu první volbou pro různé instituce od poskytovatelů veřejného cloudu uvede příjemce electronics a Oblíbené položky mezi jednotlivé technologists.

S vývojáři a engineering centrech po celém světě je jedinečně Canonical nastavený při spolupráci s osoby provádějící hardwaru, poskytovatelů obsahu a vývojářům softwaru, aby Ubuntu řešení na trh pro počítače, servery a kapesních zařízeních.
