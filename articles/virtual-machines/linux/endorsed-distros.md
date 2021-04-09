---
title: Distribuce systému Linux schválená v Azure
description: Přečtěte si informace o systému Linux v distribucích schválených Azure, včetně pokynů pro Ubuntu, CentOS, Oracle a SUSE.
services: virtual-machines
documentationcenter: ''
author: danielsollondon
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: conceptual
ms.date: 01/03/2021
ms.author: guybo
ms.openlocfilehash: 091a06fd608763ac7265670733890e93bf71dde1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547403"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Schválené distribuce Linux v Azure

Partneři poskytují image Linux v Azure Marketplace. Microsoft spolupracuje s různými komunitami Linux za účelem přidání ještě více typů do schváleného distribučního seznamu. U distribucí, které nejsou k dispozici na webu Marketplace, můžete kdykoli využít vlastní Linux podle pokynů v části [Vytvoření a nahrání virtuálního pevného disku obsahujícího operační systém Linux](./create-upload-generic.md).

## <a name="supported-distributions-and-versions"></a>Podporované distribuce a verze

V následující tabulce jsou uvedené distribuce a verze systému Linux podporované v Azure. Další informace najdete v tématu [Podpora imagí pro Linux v Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure).

Ovladače pro Linux Integration Services (LIS) pro Hyper-V a Azure jsou moduly jádra, které Microsoft přispívá přímo k jádru nadřazeného systému Linux. Některé ovladače v aplikaci LIS jsou ve výchozím nastavení součástí jádra distribuce. Starší distribuce založené na Red Hat Enterprise (RHEL)/CentOS jsou k dispozici jako samostatné stažení na [platformě Linux Integration Services verze 4,2 pro Hyper-V a Azure](https://www.microsoft.com/download/details.aspx?id=55106). Další informace najdete v tématu [požadavky na jádro systému Linux](create-upload-generic.md#linux-kernel-requirements).

Agent Azure Linux je již předinstalovaný na Azure Marketplace imagí a je obvykle k dispozici z úložiště balíčků distribuce. Zdrojový kód najdete na [GitHubu](https://github.com/azure/walinuxagent).

| Distribuce | Verze | Ovladače | Agent |
| --- | --- | --- | --- |
| CentOS podle neautorizovaných vln softwaru |CentOS 6. x, 7. x, 8. x |CentOS 6,3: [stažení v lis](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +: v jádru |Balíček: v [úložišti](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) v části "WALinuxAgent" <br/>Zdrojový kód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)<p> CoreOS je teď [konec životnosti](https://coreos.com/os/eol/) od 26. května 2020. |Již není k dispozici | | |
| Debian podle credativ |8. x, 9. x, 10. x |V jádru |Balíček: v úložišti v části "waagent" <br/>Zdrojový kód: [GitHub](https://github.com/Azure/WALinuxAgent) |
|Flatcar Container Linux od Kinvolk| Pro, stabilní, beta verze| V jádru | WA-Linux-agent je už nainstalovaný v/usr/share/OEM/bin/waagent |
| Oracle Linux Oracle |6.x, 7.x, 8.x |V jádru |Balíček: v úložišti v části "WALinuxAgent" <br/>Zdrojový kód: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| [Red Hat Enterprise Linux se Red Hat](../workloads/redhat/overview.md) |6.x, 7.x, 8.x |V jádru |Balíček: v úložišti v části "WALinuxAgent" <br/>Zdrojový kód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise podle SUSE |SLES/SLES pro SAP 11. x, 12. x, 15. x <br/> [Životní cyklus SUSE veřejné cloudové image](https://www.suse.com/c/suse-public-cloud-image-life-cycle/) |V jádru |Balíček<p> 11 v [cloudu: úložiště nástrojů](https://build.opensuse.org/project/show/Cloud:Tools)<br>pro 12 zahrnuté v modulu "veřejný cloud" v části "Python-Azure-Agent"<br/>Zdrojový kód: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE podle SUSE |openSUSE Leap 15.x |V jádru |Balíček: v [cloudu: úložiště nástrojů](https://build.opensuse.org/project/show/Cloud:Tools) v části Python – Azure-Agent <br/>Zdrojový kód: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu podle kanonického |Server Ubuntu a verze pro. 16. x, 18. x, 20. x<p>Informace o rozšířené podpoře pro Ubuntu 12,04 a 14,04 najdete tady: [Ubuntu rozšířená údržba zabezpečení](https://www.ubuntu.com/esm). |V jádru |Balíček: v úložišti v části "walinuxagent" <br/>Zdrojový kód: [GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="image-update-cadence"></a>Tempo aktualizace image

Azure vyžaduje, aby vydavatelé distribucí v systému Linux pravidelně aktualizovali své image v Azure Marketplace s nejnovějšími opravami a opravami zabezpečení, a to na čtvrtletním nebo rychlejším tempo. Aktualizované image na webu Marketplace jsou pro zákazníky dostupné automaticky jako nové verze SKU image. Další informace o tom, jak najít image pro Linux: [vyhledání imagí virtuálních počítačů se systémem Linux v Azure Marketplace](./cli-ps-findimage.md).

## <a name="azure-tuned-kernels"></a>Jádra Azure – vyladěné jádro

Azure úzce spolupracuje s různými schválenými distribucí pro Linux k optimalizaci imagí, které publikovali do Azure Marketplace. Jedním z aspektů této spolupráce je vývoj "vyladěných" jader pro Linux, které jsou optimalizované pro platformu Azure a dodávané jako plně podporované součásti distribuce systému Linux. Azure-Tuned jádra zahrnují nové funkce a vylepšení výkonu a rychlejší (obvykle čtvrtletní) tempo ve srovnání s výchozími nebo obecnými jádry, které jsou dostupné z distribuce.

Ve většině případů zjistíte, že tyto jádra jsou předem nainstalované na výchozích imagí v Azure Marketplace tak, aby zákazníci ihned získali výhody těchto optimalizovaných jader. Další informace o těchto Azure-Tuned jádra najdete v následujících odkazech:

- [CentOS Azure-Tuned jádro – dostupné přes CentOS Virtualization SIG](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
- [Debian Cloud kernel – k dispozici s imagemi Debian 10 a Debian 9 v Azure](https://wiki.debian.org/Cloud/MicrosoftAzure)
- [Jádro Azure-Tuned SLES](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
- [Jádro Azure-Tuned Ubuntu](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)
- [Flatcar Container Linux pro](https://azuremarketplace.microsoft.com/marketplace/apps/kinvolk.flatcar_pro)

## <a name="partners"></a>Partneři

### <a name="coreos"></a>CoreOS

CoreOS je naplánováno na [konec životnosti](https://coreos.com/os/eol/) do 26. května 2020.
Microsoft má dva (2) kanály migrace pro CoreOS uživatele.

- Flatcar podle Kinvolk (viz položka "Flatcar Container Linux by Kinvolk".)
- [Fedora Core OS](https://docs.fedoraproject.org/en-US/fedora-coreos/provisioning-azure/) (zákazníci musí nahrávat svoji vlastní image. Tady je [dokumentace k migraci](https://docs.fedoraproject.org/en-US/fedora-coreos/migrate-cl/)).

### <a name="credativ"></a>credativ

[https://www.credativ.de/en/portfolio/support/open-source-support-center/](https://www.credativ.de/en/portfolio/support/open-source-support-center/)

credativ je nezávislá společnost pro poradenské a služby, která se specializuje na vývoj a implementaci profesionálních řešení pomocí bezplatného softwaru. Credativ nabízí mezinárodní rozpoznávání s mnoha IT odděleními, které využívají jejich podporu, jako špičkové open source specialisty. V kombinaci s Microsoftem se credativ připravují image Debian. Image jsou speciálně navržené pro běh v Azure a je možné je snadno spravovat přes platformu. credativ bude také podporovat dlouhodobou údržbu a aktualizaci imagí Debian pro Azure prostřednictvím svých Open Source Center Support Center.

### <a name="kinvolk"></a>Kinvolk
[https://www.kinvolk.io/flatcar-container-linux/](https://www.kinvolk.io/flatcar-container-linux/)

Kinvolk je společnost za Flatcar kontejnerem Linux, která pokračuje v původní CoreOSé vizi pro použití v kontejnerových aplikacích pro minimální, neměnné a automatické aktualizace Foundation. Flatcar jako minimální distribuce obsahuje jenom ty balíčky, které se vyžadují pro nasazení kontejnerů. Neměnné systémy souborů garantuje konzistenci a zabezpečení, zatímco funkce Automatické aktualizace umožňují, aby byly vždy aktuální s nejnovějšími opravami zabezpečení. 

Flatcar Container Linux je zálohovaný globálním týmem Kinvolk specialistů na systémy Linux a kontejnerů, kteří nabízejí volitelné obchodní předplatné podpory zahrnující nepřetržitou reakci, zabezpečení a technické výstrahy a exkluzivní image optimalizované pro Azure, včetně dlouhodobého kanálu podpory.


### <a name="oracle"></a>Oracle

[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Pomocí strategie Oracle můžete nabízet široké portfolio řešení pro veřejné a privátní cloudy. Strategie nabízí zákazníkům možnost volby a flexibilitu při nasazování softwaru Oracle v cloudech Oracle a dalších cloudech. Partnerství Oracle s Microsoftem umožňuje zákazníkům nasadit software Oracle ve veřejných a privátních cloudech Microsoftu s jistotou certifikace a podpory od Oracle.  Závazek Oracle a investice do řešení veřejného a privátního cloudu Oracle se nemění.

### <a name="red-hat"></a>Red Hat

[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Špičkovým poskytovatelem open source řešení, Red Hat pomáhá více než 90% společností Fortune 500 řešit obchodní výzvy, zarovnávat své IT a obchodní strategie a připravovat se na budoucnost technologie. Red Hat dosahuje zajištěním zabezpečených řešení prostřednictvím otevřeného obchodního modelu a cenově dostupného a předvídatelného modelu předplatného.

### <a name="suse"></a>SUSE

[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server v Azure je prověřená platforma, která poskytuje vyšší spolehlivost a zabezpečení cloud computingu. Všestranná platforma pro Linux SUSE se bezproblémově integruje s Azure Cloud Services, aby poskytovala snadno spravovatelné cloudové prostředí. S více než 9 200 certifikovanými aplikacemi od více než 1 800 nezávislých dodavatelů softwaru pro SUSE Linux Enterprise Server SUSE zajistí, aby zatížení běžící v datovém centru bylo možné bez obav nasadit v Azure.

### <a name="canonical"></a>Canonical

[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Kanonický technický a otevřený Ubuntuá jednotka zásad správného řízení komunity v klientech, serverech a cloud computingu, která zahrnuje osobní cloudové služby pro zákazníky. V kanonickém výhledu sjednocené a bezplatné platformy v Ubuntu, od telefonu po Cloud, poskytuje řadu souvislých rozhraní pro telefony, tablety, televizor a Desktop. Tato vize Ubuntu první volbu pro různé instituce od poskytovatelů veřejných cloudů vedoucím společnosti pro uživatele a oblíbené položky mezi jednotlivými technologists.

Díky vývojářům a technickým centrům po celém světě je kanonické řešení jednoznačně umístěno k partnerovi s tvůrci hardwaru, poskytovateli obsahu a vývojářům softwaru, aby mohli nabízet Ubuntu řešení pro počítače, servery a kapesní zařízení.
