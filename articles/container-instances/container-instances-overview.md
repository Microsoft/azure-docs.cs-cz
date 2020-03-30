---
title: Kontejnery bez serveru v Azure
description: Služba Azure Container Instances nabízí nejrychlejší a nejjednodušší způsob, jak spouštět izolované kontejnery v Azure, aniž byste museli spravovat virtuální počítače a aniž byste museli přijmout orchestrátor vyšší úrovně.
ms.topic: overview
ms.date: 04/25/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: c871c09e29b64c4f0dcd107361154efdce306481
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240251"
---
# <a name="what-is-azure-container-instances"></a>Co je služba Azure Container Instances?

Kontejnery se stávají upřednostňovaným způsobem balení, nasazování a správy cloudových aplikací. Služba Azure Container Instances nabízí nejrychlejší a nejjednodušší způsob spouštění kontejneru v Azure. Není přitom nutné spravovat žádné virtuální počítače nebo využívat služby vyšší úrovně.

Azure Container Instances je skvělým řešením pro jakýkoli scénář, který může fungovat v izolovaných kontejnerech, včetně jednoduchých aplikací, automatizace úkolů a úloh sestavení. Pro scénáře, kde potřebujete úplnou orchestraci kontejnerů, včetně zjišťování služeb napříč více kontejnery, automatického škálování a koordinovaných upgradů aplikací, doporučujeme službu [Azure Kubernetes Service (AKS)](../aks/index.yml).

## <a name="fast-startup-times"></a>Rychlé časy spuštění

Kontejnery nabízejí oproti virtuálním počítačům významné výhody při spouštění. Služba Azure Container Instances může spouštět kontejnery v Azure během několika sekund a bez nutnosti zřizovat a spravovat virtuální počítače.

## <a name="container-access"></a>Přístup k kontejneru

Azure Container Instances umožňuje vystavení skupin kontejnerů přímo na internetu s IP adresou a plně kvalifikovaným názvem domény (Plně kvalifikovaný název domény). Při vytváření instance kontejneru můžete zadat vlastní popisek názvu DNS a zpřístupnit tak aplikaci na adrese *vlastní_popisek*.*oblast_Azure*.azurecontainer.io.

Azure Container Instances také podporuje provádění příkazu v běžícím kontejneru tím, že poskytuje interaktivní prostředí, které vám pomůže s vývojem aplikací a odstraňováním potíží. Přístup probíhá přes protokol HTTPS a k zabezpečení připojení klientů používá protokol TLS.

> [!IMPORTANT]
> ledna 2020 budou instance kontejnerů Azure vyžadovat všechna zabezpečená připojení ze serverů a aplikací k použití TLS 1.2. Podpora tls 1.0 a 1.1 bude vyřazena.

## <a name="hypervisor-level-security"></a>Zabezpečení na úrovni hypervisoru

Kontejnery tradičně nabízejí izolaci závislostí aplikace a zásady správného řízení prostředků, ale nebyly považovány za dostatečně odolné pro použití v nehostinném prostředí více tenantů. Služba Azure Container Instances zaručuje izolaci vaší aplikace v kontejneru stejně, jako by byla na virtuálním počítači.


## <a name="custom-sizes"></a>Vlastní velikosti

Kontejnery jsou obvykle optimalizované pro spouštění jenom jedné aplikace, ale konkrétní požadavky těchto aplikací se můžou značně lišit. Služba Azure Container Instances zajišťuje optimální využití díky tomu, že umožňuje přesnou specifikaci jader procesoru a paměti. Platíte podle toho, co potřebujete, a účtuje se po sekundách, takže můžete podrobně upravovat náklady podle vašich aktuálních potřeb.

Pro úlohy náročné na výpočetní výkon, jako je strojové učení, můžete instance kontejnerů Azure naplánovat kontejnery Linuxu tak, aby [používaly prostředky GPU](container-instances-gpu.md) NVIDIA Tesla (náhled).

## <a name="persistent-storage"></a>Trvalé úložiště

Abychom načetli a událi stav s instancemi kontejnerů Azure, nabízíme přímé [připojení sdílených složek Azure Files podporovaných](container-instances-mounting-azure-files-volume.md) službou Azure Storage.

## <a name="linux-and-windows-containers"></a>Kontejnery Windows a Linuxu

Služba Azure Container Instances může plánovat kontejnery Windows i Linuxu pomocí stejného rozhraní API. Při vytváření [skupiny kontejnerů](container-instances-container-groups.md) stačí jednoduše zadat typ operačního systému.

Některé funkce jsou v současné době omezeny na linuxové kontejnery:

* Více kontejnerů na skupinu kontejnerů
* Připojení svazku ([Azure Files](container-instances-volume-azure-files.md), [emptyDir](container-instances-volume-emptydir.md), [GitRepo](container-instances-volume-gitrepo.md), [tajný](container-instances-volume-secret.md)klíč)
* [Metriky využití prostředků](container-instances-monitor.md) pomocí Azure Monitoru
* [Nasazení virtuální sítě](container-instances-vnet.md)
* [Zdroje GPU](container-instances-gpu.md) (náhled)

Pro nasazení kontejnerů windows použijte bitové kopie založené na běžných [iparacích systému Windows](container-instances-faq.md#what-windows-base-os-images-are-supported).

> [!NOTE]
> Použití bitových kopií založených na Windows Serveru 2019 v instanci kontejnerů Azure je ve verzi preview.

## <a name="co-scheduled-groups"></a>Společně plánované skupiny

Azure Container Instances podporuje plánování [skupin s více kontejnery,](container-instances-container-groups.md) které sdílejí hostitelský počítač, místní síť, úložiště a životní cyklus. Díky tomu můžete kombinovat hlavní kontejner aplikace s dalšími podpůrnými kontejnery, jako jsou například sajdkáry protokolování.

## <a name="virtual-network-deployment"></a>Nasazení virtuální sítě

Tato funkce Azure Container Instances, která je momentálně dostupná pro produkční úlohy v podmnožině oblastí Azure, umožňuje [nasazení instancí kontejnerů do virtuální sítě Azure.](container-instances-vnet.md) Když nasadíte instance kontejnerů do podsítě ve virtuální síti, můžou zabezpečeně komunikovat s jinými prostředky ve virtuální síti včetně těch, které jsou v místním prostředí (prostřednictvím [brány virtuální sítě](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoute](../expressroute/expressroute-introduction.md)).

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si nasazení kontejneru do Azure jediným příkazem s využitím naší příručky Rychlý start:

> [!div class="nextstepaction"]
> [Rychlý úvod ke službě Azure Container Instances](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
