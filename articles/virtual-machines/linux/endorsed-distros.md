---
title: Linuxové distribuce schválené pro Azure
description: Přečtěte si informace o systému Linux v distribucích schválených Azure, včetně pokynů pro Ubuntu, CentOS, Oracle a SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: szark
ms.openlocfilehash: 66717222b505b7dd91f7f270cab27ec4ca97038e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036222"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Schválené distribuce Linux v Azure
Partneři poskytují image Linux v Azure Marketplace. Pracujeme s různými komunitami Linux pro přidání ještě více typů do schváleného distribučního seznamu. U distribucí, které nejsou k dispozici na webu Marketplace, můžete kdykoli využít vlastní Linux podle pokynů v části [Vytvoření a nahrání virtuálního pevného disku obsahujícího operační systém Linux](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Podporované distribuce a verze
V následující tabulce jsou uvedené distribuce a verze systému Linux podporované v Azure. Podrobnější informace o podpoře pro Linux a open source technologii v Azure najdete [v tématu Podpora imagí pro Linux v Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) .

Ovladače pro Linux Integration Services (LIS) pro Hyper-V a Azure jsou moduly jádra, které Microsoft přispívá přímo k jádru nadřazeného systému Linux.  Některé ovladače v aplikaci LIS jsou ve výchozím nastavení součástí jádra distribuce. Starší distribuce založené na Red Hat Enterprise (RHEL)/CentOS jsou k dispozici jako samostatné stažení na [platformě Linux Integration Services verze 4,2 pro Hyper-V a Azure](https://www.microsoft.com/download/details.aspx?id=55106). Další informace o ovladačích služby LIS najdete v tématu [požadavky na jádro systému Linux](create-upload-generic.md#linux-kernel-requirements) .

Agent Azure Linux je už předinstalovaný na Azure Marketplace imagí a je typicky dostupný z úložiště balíčku distribuce. Zdrojový kód najdete na [GitHubu](https://github.com/azure/walinuxagent).


| Distribuce | Version | Ovladače | Agent |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3 +, 7.0 +, 8.0 + |CentOS 6,3: [stažení v lis](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +: v jádru |Balíček: v [úložišti](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) v části "WALinuxAgent" <br/>Zdrojový kód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |V jádru |Zdrojový kód: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9 +, 8.2 +, 9, 10 |V jádru |Balíček: v úložišti v části "waagent" <br/>Zdrojový kód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |V jádru |Balíček: v úložišti v části "WALinuxAgent" <br/>Zdrojový kód: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7 +, 7.1 +, 8.0 + |V jádru |Balíček: v úložišti v části "WALinuxAgent" <br/>Zdrojový kód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES pro SAP<br>11 SP4<br>12 SP1+<br>15|V jádru |Balíček:<p> 11 v [cloudu: úložiště nástrojů](https://build.opensuse.org/project/show/Cloud:Tools)<br>pro 12 zahrnuté v modulu "veřejný cloud" v části "Python-Azure-Agent"<br/>Zdrojový kód: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE přestupné 42.2 + |V jádru |Balíček: v [cloudu: úložiště nástrojů](https://build.opensuse.org/project/show/Cloud:Tools) v části Python – Azure-Agent <br/>Zdrojový kód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04 +  **<sup>1</sup>** |V jádru |Balíček: v úložišti v části "walinuxagent" <br/>Zdrojový kód: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** informace o rozšířené podpoře pro Ubuntu 12,04 a 14,04 najdete tady: [Ubuntu rozšířená údržba zabezpečení](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Tempo aktualizace image
Azure vyžaduje, aby vydavatelé distribucí v systému Linux pravidelně aktualizovali své image v Azure Marketplace s nejnovějšími opravami a opravami zabezpečení, a to na čtvrtletním nebo rychlejším tempo. Aktualizované obrázky v Azure Marketplace jsou automaticky dostupné zákazníkům jako nové verze SKU image. Další informace o tom, jak najít image pro Linux: [vyhledání imagí virtuálních počítačů se systémem Linux v Azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

### <a name="additional-links"></a>Další odkazy
 - [Životní cyklus SUSE veřejné cloudové image](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Jádra Azure – vyladěné jádro

Azure úzce spolupracuje s různými schválenými distribucí pro Linux k optimalizaci imagí, které publikovali do Azure Marketplace. Jedním z aspektů této spolupráce je vývoj "vyladěných" jader pro Linux, které jsou optimalizované pro platformu Azure a dodávané jako plně podporované součásti distribuce systému Linux. Jádro optimalizované pro Azure zahrnují nové funkce a vylepšení výkonu a rychlejší (obvykle čtvrtletní) tempo ve srovnání s výchozími nebo obecnými jádry, které jsou dostupné z distribuce.

Ve většině případů najdete tyto jádra předem nainstalované na výchozích imagí v Azure Marketplace, takže zákazníci Azure budou okamžitě využívat výhod těchto optimalizovaných jader. Další informace o těchto jádrech vyladěných pro Azure najdete na následujících odkazech:

 - CentOS vyladěné v Azure – k dispozici prostřednictvím CentOS pro virtualizaci virtualizace – [Další informace](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debian Cloud kernel – k dispozici s imagí Debian 10 a Debian 9 "porty" na Azure – [Další informace](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - SLES – vyladěné jádro Azure – [Další informace](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Ubuntu – vyladěné jádro Azure – [Další informace](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>Partneři

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Na webu CoreOS:

*CoreOS je navržená pro zabezpečení, konzistenci a spolehlivost. Místo instalace balíčků přes Yumu nebo apt CoreOS používá ke správě služeb na vyšší úrovni abstrakce kontejnery Linux. Kód jedné služby a všechny závislosti se zabalí do kontejneru, který se dá spustit na jednom nebo mnoha počítačích s CoreOS.*

### <a name="credativ"></a>credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ je nezávislá společnost pro poradenské a služby, která se specializuje na vývoj a implementaci profesionálních řešení pomocí bezplatného softwaru. Credativ nabízí mezinárodní rozpoznávání s mnoha IT odděleními, které využívají jejich podporu, jako špičkové open source specialisty. V kombinaci s Microsoftem se v credativ aktuálně připravují odpovídající image Debian pro Debian 8 (Jessie) a Debian před 7 (Wheezy). Obě image jsou speciálně navržené pro běh v Azure a je možné je snadno spravovat přes platformu. Credativ bude také podporovat dlouhodobou údržbu a aktualizaci imagí Debian pro Azure prostřednictvím svých Open Source Center Support Center.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Pomocí strategie Oracle můžete nabízet široké portfolio řešení pro veřejné a privátní cloudy. Strategie nabízí zákazníkům možnost volby a flexibilitu při nasazování softwaru Oracle v cloudech Oracle a dalších cloudech. Partnerství Oraclu s Microsoftem umožňuje zákazníkům nasadit software Oracle ve veřejných a privátních cloudech Microsoftu s jistotou certifikace a podpory od Oraclu.  Závazek Oracle a investice do řešení veřejného a privátního cloudu Oracle se nemění.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Špičkovým poskytovatelem open source řešení, Red Hat pomáhá více než 90% společností Fortune 500 řešit obchodní výzvy, zarovnávat své IT a obchodní strategie a připravovat se na budoucnost technologie. Red Hat zajišťuje zabezpečená řešení prostřednictvím otevřeného obchodního modelu a cenově dostupného a předvídatelného modelu předplatného.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server v Azure je prověřená platforma, která poskytuje vyšší spolehlivost a zabezpečení cloud computingu. Všestranná platforma pro Linux SUSE se bezproblémově integruje s Azure Cloud Services, aby poskytovala snadno spravovatelné cloudové prostředí. S více než 9 200 certifikovanými aplikacemi od více než 1 800 nezávislých dodavatelů softwaru pro SUSE Linux Enterprise Server SUSE zajistí, aby zatížení běžící v datovém centru bylo možné bez obav nasadit v Azure.

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Kanonický technický a otevřený Ubuntuá jednotka zásad správného řízení komunity v klientech, serverech a cloud computingu, která zahrnuje osobní cloudové služby pro zákazníky. V kanonickém výhledu sjednocené a bezplatné platformy v Ubuntu, od telefonu po Cloud, poskytuje řadu souvislých rozhraní pro telefony, tablety, televizor a Desktop. Tato vize Ubuntu první volbu pro různé instituce od poskytovatelů veřejných cloudů vedoucím společnosti pro uživatele a oblíbené položky mezi jednotlivými technologists.

Díky vývojářům a technickým centrům po celém světě je kanonické řešení jednoznačně umístěno k partnerovi s tvůrci hardwaru, poskytovateli obsahu a vývojářům softwaru, aby mohli nabízet Ubuntu řešení pro počítače, servery a kapesní zařízení.
