---
title: Omezení pro fondy uzlů Windows serveru ve službě Azure Kubernetes Service (AKS)
description: Přečtěte si o známých omezeních při spouštění fondů uzlů Windows serveru a úloh aplikací ve službě Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 501aeb60eba1d94b4c5882a7c6cbfa8d0359e44d
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033916"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Aktuální omezení pro fondy uzlů Windows serveru a úlohy aplikací ve službě Azure Kubernetes Service (AKS)

Ve službě Azure Kubernetes Service (AKS) můžete vytvořit fond uzlů, na kterém běží Windows Server jako hostovaný operační systém na uzlech. Tyto uzly můžou spouštět nativní aplikace typu kontejner pro Windows, jako jsou ty, které jsou postavené na .NET Framework. Vzhledem k zásadním rozdílům v tom, jak operační systémy Linux a Windows poskytují podporu kontejnerů, nejsou aktuálně pro fondy uzlů Windows k dispozici některé běžné funkce související s Kubernetes a pod.

Tento článek popisuje některá omezení a koncepty operačního systému pro uzly Windows serveru v AKS. Fondy uzlů pro Windows Server jsou momentálně ve verzi Preview.

> [!IMPORTANT]
> Funkce služby AKS ve verzi Preview jsou samoobslužné přihlašovací. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepšího úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace o tom, jak se zaregistrují, najdete v následujících článcích podpory:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Omezení pro Windows Server v Kubernetes

Kontejnery Windows serveru musí běžet na hostiteli kontejneru založeném na systému Windows. Pokud chcete spouštět kontejnery Windows serveru v AKS, můžete [vytvořit fond uzlů, na kterém běží Windows Server][windows-node-cli] , jako hostovaný operační systém. Podpora fondu uzlů serveru oken zahrnuje některá omezení, která jsou součástí nadřazeného Windows serveru v projektu Kubernetes. Tato omezení nejsou specifická pro AKS. Další informace o této nadřazené podpoře pro Windows Server v Kubernetes najdete v tématu [kontejnery Windows serveru v omezeních Kubernetes](https://docs.microsoft.com/azure/aks/windows-node-limitations).

Pro AKS se vztahují následující omezení nadřazeného datového proudu pro kontejnery Windows serveru v Kubernetes:

- Kontejnery Windows serveru můžou používat jenom Windows Server 2019, který odpovídá základnímu OPERAČNÍmu uzlu Windows serveru.
    - Image kontejnerů sestavené pomocí systému Windows Server 2016, které nejsou podporovány jako základní operační systém, nejsou podporovány.
- Nelze použít žádné privilegované kontejnery.
- Funkce specifické pro Linux, jako jsou RunAsUser, SELinux, AppArmor nebo funkce POSIX, nejsou dostupné v kontejnerech Windows serveru.
    - Omezení systému souborů, která jsou specifická pro Linux, jako je například UUI/GUID, nejsou v kontejnerech Windows serveru k dispozici ani oprávnění pro jednotlivé uživatele.
- Disky Azure a soubory Azure jsou podporované typy svazků, ke kterým se dostanete jako svazky NTFS v kontejneru Windows serveru.
    - Úložiště/svazky založené na systému souborů NFS nejsou podporovány.

## <a name="aks-limitations-for-windows-server-node-pools"></a>Omezení AKS pro fondy uzlů Windows serveru

Následující dodatečná omezení platí pro podporu fondu uzlů Windows serveru v AKS:

- Cluster AKS vždy obsahuje fond uzlů Linux jako první fond uzlů. Tento první fond uzlů se systémem Linux nejde odstranit, pokud se neodstraní samotný cluster AKS.
- Clustery AKS musí používat model sítě Azure CNI (rozšířené).
    - Kubenet (základní) sítě se nepodporují. Nemůžete vytvořit cluster AKS, který používá kubenet. Další informace o rozdílech v síťových modelech najdete v tématu [Koncepty sítě pro aplikace v AKS][azure-network-models].
    - Model sítě Azure CNI vyžaduje další plánování a předpoklady pro správu IP adres. Další informace o plánování a implementaci Azure CNI najdete v tématu [Konfigurace sítě Azure CNI v AKS][configure-azure-cni].
- Aby bylo možné zachovat nejnovější opravy a aktualizace oprav, je třeba upgradovat uzly Windows serveru v AKS na nejnovější verzi Windows serveru 2019. Aktualizace Windows nejsou povolené v imagi základního uzlu v AKS. V pravidelných intervalech kolem cyklu vydávání web Windows Update a vlastního procesu ověřování byste měli provést upgrade ve fondech uzlů Windows serveru v clusteru AKS. Další informace o upgradu fondu uzlů Windows serveru najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].
    - Tyto upgrady uzlů Windows serveru dočasně využívají další IP adresy v podsíti virtuální sítě, protože je nasazený nový uzel před odebráním starého uzlu.
    - v rámci předplatného se také dočasně využívají kvóty vCPU, protože se nasadí nový uzel a starý uzel se odebral.
    - Nemůžete automaticky aktualizovat a spravovat restartování pomocí `kured` as s uzly Linux v AKS.
- Cluster AKS může mít maximálně osm fondů uzlů.
    - V těchto osmi fondech uzlů můžete mít maximálně 400 uzlů.
- Název fondu uzlů Windows serveru má maximálně 6 znaků.
- Funkce ve verzi Preview v AKS, jako je například zásada sítě a automatické škálování clusteru, nejsou pro uzly Windows serveru potvrzené.
- Řadiče příchozího přenosu dat by měly být naplánované jenom na uzlech se systémem Linux pomocí NodeSelector.
- Azure Dev Spaces je aktuálně k dispozici pouze pro fondy uzlů se systémem Linux.
- Podpora skupinových účtů spravované služby (gMSA), pokud uzly Windows serveru nejsou připojené k doméně služby Active Directory, aktuálně nejsou v AKS dostupné.
    - Open source projekt [AKS][aks-engine] v současné době poskytuje podporu gMSA, pokud potřebujete tuto funkci použít.

## <a name="os-concepts-that-are-different"></a>Různé koncepty operačního systému

Kubernetes je historicky zaměřený na Linux. Mnohé příklady, které se používají na webu [Kubernetes.IO][kubernetes] pro odesílání dat, jsou určené pro použití v uzlech se systémem Linux. Při vytváření nasazení, která používají kontejnery Windows serveru, platí následující požadavky na úrovni operačního systému:

- **Identita** – Linux používá UserID (UID) a GROUPID (GID), reprezentované jako typ Integer. Názvy uživatelů a skupin nejsou kanonické – jedná se pouze o alias v */etc/Groups* nebo */etc/PASSWD* zpět na UID + GID.
    - Windows Server používá větší binární identifikátor zabezpečení (SID), který je uložený v databázi SAM (Windows Security Access Manager). Tato databáze není sdílená mezi hostitelem a kontejnery nebo mezi kontejnery.
- **Oprávnění k souborům** – Windows Server používá seznam řízení přístupu založený na identifikátorech SID, nikoli bitovou masku oprávnění a UID + GID.
- **Cesty k souborům** – konvence ve Windows serveru slouží jako místo/.
    - V části pod specifikacemi, které připojovat svazky, zadejte cestu pro kontejnery Windows serveru správně. Například místo přípojného bodu */mnt/Volume* v kontejneru Linux zadejte písmeno jednotky a umístění, jako je například */K/Volume* , které chcete připojit jako jednotku *k:* .

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s kontejnery Windows serveru v AKS, [vytvořte fond uzlů, na kterém běží Windows Server v AKS][windows-node-cli].

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
