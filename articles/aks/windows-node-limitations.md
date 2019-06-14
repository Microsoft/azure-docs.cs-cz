---
title: Omezení pro fondy uzlů Windows Server ve službě Azure Kubernetes Service (AKS)
description: Další informace o známých omezeních při spuštění fondy uzlů Windows Server a úlohy aplikací ve službě Azure Kubernetes Service (AKS)
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: twhitney
ms.openlocfilehash: 12fb9dc67e8afae3dcb9ade97dd61ab438e0fac5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475400"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Aktuální omezení pro fondy uzlů Windows Server a úlohy aplikací ve službě Azure Kubernetes Service (AKS)

Ve službě Azure Kubernetes Service (AKS), můžete vytvořit fond uzlů, na kterém běží Windows Server jako hostovaný operační systém na uzlech. Tyto uzly mohou spouštět nativní aplikace typu kontejner Windows, jako jsou ty postaveno na rozhraní .NET Framework. Jak existují významné rozdíly v jak operačního systému Windows i Linux poskytuje podporu kontejnerů, nejsou některé běžné Kubernetes a funkcím souvisejícím s pod aktuálně k dispozici pro fondy uzlů Windows.

Tento článek popisuje některé z omezení a koncepty operačního systému pro uzly Windows serveru ve službě AKS. Fondy uzlů pro systém Windows Server jsou aktuálně ve verzi preview.

> [!IMPORTANT]
> Funkce AKS ve verzi preview jsou samoobslužných služeb, vyjádřit výslovný souhlas. Jsou poskytovány shromažďovat zpětnou vazbu a chyb z naší komunitě. Ve verzi preview nejsou tyto funkce určené k použití v produkčním prostředí. Funkce ve verzi public preview spadají pod "co možná nejlepší" podporu. Pomoc od týmů AKS technická podpora je k dispozici během pracovní doby tichomořské časové pásmo (PST) pouze. Další informace najdete v tématu následující články o podpoře:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Omezení pro systém Windows Server v Kubernetes

Kontejnery Windows serveru, musíte spustit na hostiteli založené na Windows. Pro spouštění kontejnerů Windows serveru ve službě AKS, můžete [vytvořit fond uzlů, na kterém běží Windows Server] [ windows-node-cli] jako hostovaný operační systém. Podpora fond uzlů serveru okno zahrnuje určitá omezení, které jsou součástí Windows serveru pro odesílání dat v projektu Kubernetes. Tato omezení nejsou specifická pro AKS. Další informace o této nadřazeného podpoře pro systém Windows Server v Kubernetes najdete v tématu [kontejnery Windows serveru v Kubernetes omezení][upstream-limitations].

Následující nadřazený omezení pro kontejnery Windows serveru v Kubernetes jsou relevantní pro AKS:

- Windows Server kontejnery lze použít pouze 2019 systému Windows Server, který by odpovídal základní uzel Windows serveru operačního systému.
    - Image kontejnerů vytvořených pomocí Windows serveru 2016 jako základní operační systém nepodporují.
- Privilegované kontejnery nelze použít.
- Funkce specifická pro Linux, jako jsou vidění Spustit_jako_uživatel, SELinux, AppArmor nebo POSIX nejsou k dispozici v kontejnerech Windows serveru.
    - Omezení systému souborů, která jsou specifická pro Linux jako je například UUI/GUID, každý uživatel oprávnění také nejsou k dispozici v kontejnerech Windows serveru.
- Azure disky a soubory Azure jsou podporované typy svazků, přistupovat jako NTFS svazky v kontejneru Windows serveru.
    - Úložiště založené na systému souborů NFS / svazky nepodporují.

## <a name="aks-limitations-for-windows-server-node-pools"></a>AKS omezení pro fondy uzlů Windows Server

Následující další omezení platí pro podporu fondu uzel Windows Server ve službě AKS:

- AKS cluster vždy obsahuje uzel fondu s Linuxem jako první fond uzlů. Tento první uzel založených na Linuxu fond nejde odstranit, dokud je neodstraní samotného clusteru AKS.
- AKS v současné době podporuje pouze load balanceru úrovně basic, která umožňuje pouze pro jeden back-endového fondu, fond uzlů Linux výchozí. V důsledku toho odchozího provozu z Windows podů bude vždy [přeložit do Azure managed veřejnou IP adresu][azure-outbound-traffic]. Protože tuto IP adresu není konfigurovatelné, není na seznamu povolených IP adres provoz pocházející z Windows podů aktuálně možné. 
- AKS clusterů musíte použít Azure CNI o síťovém modelu (rozšířené).
    - Kubenet (basic) sítě se nepodporuje. Nelze vytvořit, která používá kubenet clusteru AKS. Další informace o rozdílech v modelech sítě, naleznete v tématu [sítě koncepty pro aplikace ve službě AKS][azure-network-models].
    - Model pro sítě Azure CNI vyžaduje dodatečné plánování a důležité informace týkající se správy IP adres. Další informace o tom, jak naplánovat a implementovat Azure CNI najdete v tématu [Azure CNI konfigurace sítě ve službě AKS][configure-azure-cni].
- Musí být uzly Windows serveru ve službě AKS *upgradovat* nejnovější verzi Windows serveru 2019 zachovat nejnovější opravu opravy a aktualizace. Aktualizace Windows nejsou povolené základní uzel obrázku ve službě AKS. V pravidelných intervalech kolem cyklu vydávání verzí Windows Update a procesu ověřování měli byste provést upgrade na fondy uzlů Windows Server ve vašem clusteru AKS. Další informace o upgradu fond uzlů Windows serveru, naleznete v tématu [fond uzlů ve službě AKS Upgrade][nodepool-upgrade].
    - Tyto upgrady uzlů Windows Server dočasně využívat další IP adresy v podsíti virtuální sítě, protože nasadíte nový uzel, před odebráním starých uzlu.
    - kvóty virtuálních procesorů se taky dočasně spotřebuje v rámci předplatného jako nový uzel nasazuje, poté původní uzel odebrán.
    - Nelze automaticky aktualizovat a spravovat pomocí restartování `kured` stejně jako u uzly s Linuxem ve službě AKS.
- AKS cluster může mít maximálně osm fondy uzlů.
    - Mezi tyto fondy osmi uzlů může mít maximálně 400 uzly.
- Název fondu uzel Windows Server má limit 6 znaků.
- Ve verzi Preview funkce ve službě AKS jako síťové zásady a automatického škálování clusteru, se schválenou sadou pro uzly Windows serveru.
- Řadiče příchozího přenosu dat by měl být naplánováno, pouze na Linuxových uzlů pomocí NodeSelector.
- Azure Dev prostory je momentálně dostupný jenom pro fondy uzlů založených na Linuxu.
- Skupina účty spravované služby (gMSA) podpora v případě, že na uzlech Windows serveru nejsou připojené k doméně služby Active Directory není aktuálně k dispozici ve službě AKS.
    - Open source, upstream [aks-engine] [ aks-engine] projekt aktuálně poskytuje podporu gMSA Pokud chcete tuto funkci používat.

## <a name="os-concepts-that-are-different"></a>Koncepty operačního systému, které se liší

Kubernetes je v minulosti zaměřené na Linuxu. Mnoho příkladů použitých v nadřazený [Kubernetes.io] [ kubernetes] webu jsou určeny k použití na uzly s Linuxem. Když vytvoříte nasazení, která používají kontejnery Windows serveru, na úrovni operačního systému použít následující aspekty:

- **Identita** -používá Linux, userID (UID) a groupID ID (skupiny), reprezentovaný jako typy celých čísel. Jména uživatelů a skupin nejsou canonical – jsou pouhý alias v */etc/skupiny* nebo */etc/hesel* zpět do UID + GID.
    - Windows Server používá větší binární identifikátor zabezpečení (SID) která je uložena v databázi správce zabezpečení přístupu k Windows (SAM). Tato databáze není sdílený mezi hostitelem a kontejnery, nebo mezi kontejnery.
- **Oprávnění souborů** – seznam řízení přístupu založené na identifikátory SID, spíše než bitová maska oprávnění a UID + GID používá Windows Server
- **Cesty k souborům** – konvence v systému Windows Server, je použít \ místo /.
    - V podu specifikace, které připojit svazky zadejte cestu správně pro kontejnery Windows serveru. Například místo přípojný bod *svazek/mnt/* v kontejneru Linuxu, zadejte písmeno jednotky a umístění jako */tis. / svazek* připojit jako *K:* jednotky.

## <a name="next-steps"></a>Další postup

Začínáme s kontejnery Windows serveru ve službě AKS, [vytvořit fond uzlů, na kterém běží Windows Server ve službě AKS][windows-node-cli].

<!-- LINKS - external -->
[upstream-limitations]: https://kubernetes.io/docs/setup/windows/#limitations
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
