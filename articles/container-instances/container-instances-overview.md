---
title: Kontejnery bez serveru v Azure
description: Služba Azure Container Instances nabízí nejrychlejší a nejjednodušší způsob, jak spouštět izolované kontejnery v Azure, aniž by bylo nutné spravovat virtuální počítače a bez nutnosti přijmout Orchestrator vyšší úrovně.
ms.topic: overview
ms.date: 08/10/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 873f7cc51d1b369503a72501ae000000ff06f805
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573009"
---
# <a name="what-is-azure-container-instances"></a>Co je služba Azure Container Instances?

Kontejnery se stávají upřednostňovaným způsobem balení, nasazování a správy cloudových aplikací. Služba Azure Container Instances nabízí nejrychlejší a nejjednodušší způsob spouštění kontejneru v Azure. Není přitom nutné spravovat žádné virtuální počítače nebo využívat služby vyšší úrovně.

Azure Container Instances je skvělým řešením pro jakýkoli scénář, který může fungovat v izolovaných kontejnerech, včetně jednoduchých aplikací, automatizace úkolů a úloh sestavení. Pro scénáře, kde potřebujete úplnou orchestraci kontejnerů, včetně zjišťování služeb napříč více kontejnery, automatického škálování a koordinovaných upgradů aplikací, doporučujeme [službu Azure Kubernetes Service (AKS)](../aks/index.yml).

## <a name="fast-startup-times"></a>Rychlé časy spuštění

Kontejnery nabízejí oproti virtuálním počítačům významné výhody při spouštění. Služba Azure Container Instances může spouštět kontejnery v Azure během několika sekund a bez nutnosti zřizovat a spravovat virtuální počítače.

Přeneste image kontejnerů pro Linux nebo Windows z dokovacího centra, privátního [registru kontejnerů Azure](../container-registry/index.yml)nebo jiného cloudového registru Docker. Navštivte [Nejčastější dotazy](container-instances-faq.md) , které vám pomůžou zjistit, které Registry podporuje ACI. Azure Container Instances ukládá do mezipaměti několik běžných imagí základního operačního systému, což pomáhá zrychlit nasazení vašich vlastních imagí aplikace.

## <a name="container-access"></a>Přístup k kontejneru

Azure Container Instances umožňuje vystavit skupiny kontejnerů přímo na internetu s IP adresou a plně kvalifikovaným názvem domény (FQDN). Při vytváření instance kontejneru můžete zadat vlastní popisek názvu DNS a zpřístupnit tak aplikaci na adrese *vlastní_popisek*.*oblast_Azure*.azurecontainer.io.

Azure Container Instances také podporuje provádění příkazu ve spuštěném kontejneru poskytnutím interaktivního prostředí pro pomoc s vývojem aplikací a řešením potíží. Přístup provádí přes protokol HTTPS pomocí protokolu TLS k zabezpečení připojení klientů.

> [!IMPORTANT]
> Od 13. ledna 2020 bude Azure Container Instances vyžadovat, aby všechna zabezpečená připojení ze serverů a aplikací používala protokol TLS 1,2. Bude vyřazena podpora TLS 1,0 a 1,1.

## <a name="compliant-deployments"></a>Vyhovující nasazení

### <a name="hypervisor-level-security"></a>Zabezpečení na úrovni hypervisoru

Kontejnery tradičně nabízejí izolaci závislostí aplikace a zásady správného řízení prostředků, ale nebyly považovány za dostatečně odolné pro použití v nehostinném prostředí více tenantů. Služba Azure Container Instances zaručuje izolaci vaší aplikace v kontejneru stejně, jako by byla na virtuálním počítači.

### <a name="customer-data"></a>Zákaznická data

Služba ACI ukládá minimální zákaznická data potřebná k tomu, aby vaše skupiny kontejnerů běžely podle očekávání. Ukládání zákaznických dat v jedné oblasti je aktuálně dostupné jenom v oblasti jihovýchodní Asie (Singapur) oblasti Asie a Tichomoří geografická a Brazílie – jih (stát Svatý Paulo) Brazílie geograficky. Pro všechny ostatní oblasti jsou zákaznická data uložená v [geograficky](https://azure.microsoft.com/global-infrastructure/geographies/). Další informace najdete v kontaktu s podporou Azure.

## <a name="custom-sizes"></a>Vlastní velikosti

Kontejnery jsou obvykle optimalizované pro spouštění jenom jedné aplikace, ale konkrétní požadavky těchto aplikací se můžou značně lišit. Služba Azure Container Instances zajišťuje optimální využití díky tomu, že umožňuje přesnou specifikaci jader procesoru a paměti. Platíte podle toho, co potřebujete, a účtuje se po sekundách, takže můžete podrobně upravovat náklady podle vašich aktuálních potřeb.

Pro úlohy náročné na výpočetní výkon, jako je Machine Learning, může Azure Container Instances naplánovat kontejnery Linux na používání [prostředků GPU](container-instances-gpu.md) NVIDIA Tesla (Preview).

## <a name="persistent-storage"></a>Trvalé úložiště

Aby bylo možné načíst stav a zachovat ho v Azure Container Instances, nabízíme přímé [připojení sdílených složek Azure Files](./container-instances-volume-azure-files.md) zajištěných pomocí Azure Storage.

## <a name="linux-and-windows-containers"></a>Kontejnery Windows a Linuxu

Služba Azure Container Instances může plánovat kontejnery Windows i Linuxu pomocí stejného rozhraní API. Při vytváření [skupiny kontejnerů](container-instances-container-groups.md) stačí jednoduše zadat typ operačního systému.

Některé funkce jsou aktuálně omezené na kontejnery Linux:

* Více kontejnerů na skupinu kontejnerů
* Připojení svazku ([soubory Azure](container-instances-volume-azure-files.md), [emptyDir](container-instances-volume-emptydir.md), [gitrepo nepodporují](container-instances-volume-gitrepo.md), [tajný kód](container-instances-volume-secret.md))
* [Metriky využití prostředků](container-instances-monitor.md) pomocí Azure monitor
* [Nasazení virtuální sítě](container-instances-vnet.md)
* [Prostředky GPU](container-instances-gpu.md) (Preview)

Pro nasazení kontejnerů Windows používejte image založené na běžných [bitových kopiích Windows Base](container-instances-faq.md#what-windows-base-os-images-are-supported).

## <a name="co-scheduled-groups"></a>Společně plánované skupiny

Azure Container Instances podporuje plánování [skupin více kontejnerů](container-instances-container-groups.md) , které sdílejí hostitelský počítač, místní síť, úložiště a životní cyklus. Díky tomu můžete kombinovat hlavní kontejner aplikace s dalšími podpůrnými kontejnery, jako jsou například sajdkáry protokolování.

## <a name="virtual-network-deployment"></a>Nasazení virtuální sítě

Azure Container Instances umožňuje [nasazení instancí kontejnerů do služby Azure Virtual Network](container-instances-vnet.md). Při nasazení do podsítě ve virtuální síti můžou instance kontejneru bezpečně komunikovat s ostatními prostředky ve virtuální síti, včetně místně (přes [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoute](../expressroute/expressroute-introduction.md)).

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si nasazení kontejneru do Azure jediným příkazem s využitím naší příručky Rychlý start:

> [!div class="nextstepaction"]
> [Rychlý úvod ke službě Azure Container Instances](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
