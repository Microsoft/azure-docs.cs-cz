---
title: Přehled služby Azure Container Instances
description: Vysvětlení služby Azure Container Instances
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 03/29/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 2da61d52e43857237f6e231c03ea62ba5eefdadd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33767967"
---
# <a name="azure-container-instances"></a>Azure Container Instances

Kontejnery se stávají upřednostňovaným způsobem balení, nasazování a správy cloudových aplikací. Služba Azure Container Instances nabízí nejrychlejší a nejjednodušší způsob spouštění kontejneru v Azure. Není přitom nutné spravovat žádné virtuální počítače nebo využívat služby vyšší úrovně.

Azure Container Instances je skvělým řešením pro jakýkoli scénář, který může fungovat v izolovaných kontejnerech, včetně jednoduchých aplikací, automatizace úkolů a úloh sestavení. Pro scénáře, kde potřebujete úplnou orchestraci kontejnerů, včetně zjišťování služeb napříč více kontejnery, automatického škálování a koordinovaných upgradů aplikací, doporučujeme službu [Azure Kubernetes Service (AKS)](../aks/index.yml).

## <a name="fast-startup-times"></a>Rychlé časy spuštění

Kontejnery nabízejí významné výhody při spouštění oproti virtuálním počítačům. Služba Azure Container Instances může spouštět kontejnery v Azure během několika sekund a bez nutnosti zřizovat a spravovat virtuální počítače.

## <a name="public-ip-connectivity-and-dns-name"></a>Připojení pomocí veřejné IP adresy a název DNS

Služba Azure Container Instances umožňuje zveřejňovat kontejnery přímo na internetu s použitím IP adresy a plně kvalifikovaného názvu domény. Při vytváření instance kontejneru můžete zadat vlastní popisek názvu DNS a zpřístupnit tak aplikaci na adrese *vlastní_popisek*.*oblast_Azure*.azurecontainer.io.

## <a name="hypervisor-level-security"></a>Zabezpečení na úrovni hypervisoru

Kontejnery tradičně nabízejí izolaci závislostí aplikace a zásady správného řízení prostředků, ale nebyly považovány za dostatečně odolné pro použití v nehostinném prostředí více tenantů. Služba Azure Container Instances zaručuje izolaci vaší aplikace v kontejneru stejně, jako by byla na virtuálním počítači.

## <a name="custom-sizes"></a>Vlastní velikosti

Kontejnery jsou obvykle optimalizované pro spouštění jenom jedné aplikace, ale konkrétní požadavky těchto aplikací se můžou značně lišit. Služba Azure Container Instances zajišťuje optimální využití díky tomu, že umožňuje přesnou specifikaci jader procesoru a paměti. Platíte podle toho, co potřebujete, a účtuje se po sekundách, takže můžete podrobně upravovat náklady podle vašich aktuálních potřeb.

## <a name="persistent-storage"></a>Trvalé úložiště

Pro načtení a uložení stavu pomocí služby Azure Container Instances nabízíme přímé [připojení sdílených složek Azure](container-instances-mounting-azure-files-volume.md).

## <a name="linux-and-windows-containers"></a>Kontejnery Windows a Linuxu

Služba Azure Container Instances může plánovat kontejnery Windows i Linuxu pomocí stejného rozhraní API. Při vytváření [skupiny kontejnerů](container-instances-container-groups.md) stačí jednoduše zadat typ operačního systému.

Některé funkce jsou momentálně omezené jenom na linuxové kontejnery. Pracujeme na tom, abychom zajistili paritu funkcí pro kontejnery Windows. Aktuální rozdíly pro tyto platformy najdete v tématu věnovaném [kvótám a dostupnosti oblastí pro Azure Container Instances](container-instances-quotas.md).

## <a name="co-scheduled-groups"></a>Společně plánované skupiny

Azure Container Instances podporuje plánování [skupin více kontejnerů](container-instances-container-groups.md), které sdílejí hostitelský počítač, místní síť, úložiště a životní cyklus. Díky tomu můžete kombinovat hlavní kontejner aplikace s dalšími podpůrnými kontejnery, jako jsou například sajdkáry protokolování.

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si nasazení kontejneru do Azure jediným příkazem s využitím naší příručky Rychlý start:

> [!div class="nextstepaction"]
> [Rychlý úvod ke službě Azure Container Instances](container-instances-quickstart.md)
