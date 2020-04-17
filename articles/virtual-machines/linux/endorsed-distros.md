---
title: Linuxové distribuce potvrzené v Azure
description: Přečtěte si o Linuxu v distribucích schválených Azure, včetně pokynů pro Ubuntu, CentOS, Oracle a SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: guybo
ms.openlocfilehash: aebb31c825725e8b748fe52bfdf74a79074d6a52
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460490"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Schválené distribuce Linuxu v Azure
Partneři poskytují ibi Linuxu na Azure Marketplace. Spolupracujeme s různými komunitami Linuxu, abychom přidali ještě více příchutí do seznamu endorsed distribution. Do té doby, pro distribuce, které nejsou k dispozici z Marketplace, můžete vždy přinést svůj vlastní Linux podle pokynů na [Vytvořit a nahrát virtuální pevný disk, který obsahuje operační systém Linux](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Podporované distribuce a verze
V následující tabulce jsou uvedeny distribuce a verze Linuxu, které jsou podporované v Azure. Podrobnější informace o podpoře Linuxu a open source technologie v Azure najdete v podpoře [ibi Linuxu v Microsoft Azure.](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)

Ovladače Linux Integration Services (LIS) pro Hyper-V a Azure jsou moduly jádra, které Microsoft přispívá přímo do nadřazeného linuxového jádra.  Některé ovladače LIS jsou ve výchozím nastavení integrovány do jádra distribuce. Starší distribuce založené na Red Hat Enterprise (RHEL)/CentOS jsou k dispozici jako samostatné stažení na [linuxové integrační služby verze 4.2 pro Hyper-V a Azure](https://www.microsoft.com/download/details.aspx?id=55106). Další informace o ovladačích LIS naleznete v [požadavcích na jádro Linuxu.](create-upload-generic.md#linux-kernel-requirements)

Azure Linux Agent je už předinstalovaný na image Azure Marketplace a je obvykle k dispozici z úložiště balíčků distribuce. Zdrojový kód lze nalézt na [GitHubu](https://github.com/azure/walinuxagent).


| Distribuce | Version | Ovladače | Agent |
| --- | --- | --- | --- |
| CentOS |Centos 6,3+, 7,0+, 8,0+ |CentOS 6.3: [LIS ke stažení](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4+: V jádře |Balíček: V [repo](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) pod "WALinuxAgent" <br/>Zdrojový kód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |V jádře |Zdrojový kód: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7,9+, 8,2+, 9, 10 |V jádře |Balení: V repo pod "waagent" <br/>Zdrojový kód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |V jádře |Balíček: V repo pod "WALinuxAgent" <br/>Zdrojový kód: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6,7+, 7,1+, 8,0+ |V jádře |Balíček: V repo pod "WALinuxAgent" <br/>Zdrojový kód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES pro SAP<br>11 SP4<br>12 SP1+<br>15|V jádře |Balíček:<p> pro 11 v [cloudu:Nástroje](https://build.opensuse.org/project/show/Cloud:Tools) odpočívat<br>pro 12 zahrnuty v "Public Cloud" Modul pod "python-azure-agent"<br/>Zdrojový kód: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2+ |V jádře |Balíček: V [cloudu:Nástroje](https://build.opensuse.org/project/show/Cloud:Tools) repo pod "python-azure-agent" <br/>Zdrojový kód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ ** <sup>1</sup>** |V jádře |Balíček: V repo pod "walinuxagent" <br/>Zdrojový kód: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** Informace o rozšířené podpoře pro Ubuntu 12.04 a 14.04 naleznete zde: [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Kadence aktualizace obrázků
Azure vyžaduje, aby vydavatelé schválených distribucí Linuxu pravidelně aktualizovali své image na Azure Marketplace nejnovějšími opravami a opravami zabezpečení, a to ve čtvrtletní nebo rychlejší kadenci. Aktualizované image na Azure Marketplace jsou zákazníkům k dispozici automaticky jako nové verze skladové položky bitové kopie. Další informace o tom, jak najít ibližové obrázky [Linuxu: Najděte ibi virtuálních počítačových virtuálních počítači linuxu na Azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

### <a name="additional-links"></a>Další odkazy
 - [Životní cyklus obrazu veřejného cloudu SUSE](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Jádra vyladěná v Azure

Azure úzce spolupracuje s různými schválenými distribucemi Linuxu a optimalizuje image, které publikují na Azure Marketplace. Jedním aspektem této spolupráce je vývoj "vyladěných" linuxových jader, která jsou optimalizovaná pro platformu Azure a dodávána jako plně podporované součásti distribuce Linuxu. Jádra Vyladěná v Azure obsahují nové funkce a vylepšení výkonu a rychlejší (obvykle čtvrtletní) kadence ve srovnání s výchozím nebo obecným jádrem, která jsou dostupná z distribuce.

Ve většině případů najdete tato jádra předinstalovaná na výchozích ibi na Azure Marketplace, takže zákazníci Azure okamžitě získají výhody těchto optimalizovaných jader. Další informace o těchto jádrech vyladěných v Azure naleznete v následujících odkazech:

 - Kernel centos vyladěné z Azure - k dispozici prostřednictvím CentOS Virtualization SIG - [Více informací](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debian Cloud Kernel - K dispozici s obrazem Debian 10 a Debian 9 "backports" v Azure - [Více informací](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - SLES Azure-Tuned Kernel - [Další informace](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Ubuntu Azure-Tuned Kernel - [Další informace](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>Partneři

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Z webových stránek CoreOS:

*CoreOS je navržen pro zabezpečení, konzistenci a spolehlivost. Namísto instalace balíčků přes yum nebo apt, CoreOS používá linuxové kontejnery pro správu vašich služeb na vyšší úrovni abstrakce. Kód jedné služby a všechny závislosti jsou zabaleny v rámci kontejneru, který lze spustit na jednom nebo více počítačích CoreOS.*

### <a name="credativ"></a>Kredativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ je nezávislá poradenská a poradenská společnost, která se specializuje na vývoj a implementaci profesionálních řešení pomocí svobodného softwaru. Jako přední open-source specialisté má Credativ mezinárodní uznání s mnoha IT odděleními, která využívají jejich podporu. Ve spolupráci s Microsoftem, Credativ v současné době připravuje odpovídající debianí obrazy pro Debian 8 (Jessie) a Debian před 7 (Wheezy). Obě image jsou speciálně navržené pro Azure a lze je snadno spravovat prostřednictvím platformy. Credativ bude také podporovat dlouhodobou údržbu a aktualizaci ibizí Debianu pro Azure prostřednictvím center podpory open source.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Strategií společnosti Oracle je nabídnout široké portfolio řešení pro veřejné i soukromé cloudy. Strategie poskytuje zákazníkům možnost volby a flexibilitu při nasazování softwaru Oracle v cloudech oracle a dalších cloudech. Partnerství společnosti Oracle se společností Microsoft umožňuje zákazníkům nasadit software Oracle ve veřejných i soukromých cloudech společnosti Microsoft s jistotou certifikace a podpory od společnosti Oracle.  Závazek společnosti Oracle a investice do řešení veřejného a soukromého cloudu společnosti Oracle se nemění.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Red Hat, přední světový poskytovatel open source řešení, pomáhá více než 90 % společností z žebříčku Fortune 500 řešit obchodní výzvy, sladit jejich IT a obchodní strategie a připravit se na budoucnost technologií. Red Hat to dělá tím, že poskytuje bezpečná řešení prostřednictvím otevřeného obchodního modelu a cenově dostupného a předvídatelného modelu předplatného.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server v Azure je osvědčená platforma, která poskytuje vynikající spolehlivost a zabezpečení pro cloud computing. Všestranná platforma Linuxu společnosti SUSE se bezproblémově integruje s cloudovými službami Azure a poskytuje snadno spravovatelné cloudové prostředí. Díky více než 9 200 certifikovaným aplikacím od více než 1 800 nezávislých dodavatelů softwaru pro SUSE Linux Enterprise Server zajišťuje SUSE SUSE, že úlohy spuštěné v datovém centru lze s jistotou nasadit v Azure.

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Canonical engineering a open community governance řídí úspěch Ubuntu v klientském, serverovém a cloud computingu, který zahrnuje osobní cloudové služby pro spotřebitele. Canonical vize jednotné, bezplatné platformy v Ubuntu, z telefonu do cloudu, poskytuje rodinu koherentních rozhraní pro telefon, tablet, TV a desktop. Tato vize dělá Ubuntu první volbou pro různé instituce od poskytovatelů veřejných cloudů až po tvůrce spotřební elektroniky a oblíbenou mezi jednotlivými technology.

S vývojáři a inženýrskými centry po celém světě má společnost Canonical jedinečnou pozici pro partnerství s výrobci hardwaru, poskytovateli obsahu a vývojáři softwaru, aby uvede řešení Ubuntu na trh pro počítače, servery a kapesní zařízení.
