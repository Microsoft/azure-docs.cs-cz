---
title: Problémy s nasazením pro Microsoft Azure Cloud Services nejčastější dotazy | Microsoft Docs
description: V tomto článku jsou uvedené Nejčastější dotazy týkající se nasazení pro Microsoft Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: dd7b19a2c9e872b811c1aab6e504accb7de383b2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896473"
---
# <a name="deployment-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Problémy s nasazením pro Azure Cloud Services (Classic): nejčastější dotazy

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).
Tento článek obsahuje nejčastější dotazy týkající se problémů s nasazením pro [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Informace o velikosti najdete také na [stránce Cloud Services velikosti virtuálního počítače](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Proč nasazení cloudové služby do přípravného slotu někdy selže s chybou přidělení prostředků v případě, že v produkčním slotu již existuje nějaké nasazení?
Pokud má cloudová služba nasazení v obou slotech, je celá cloudová služba připnuté na konkrétní cluster. To znamená, že pokud nasazení již existuje v produkčním slotu, nové pracovní nasazení může být přiděleno pouze ve stejném clusteru jako produkční slot.

K selháním přidělení dojde v případě, že cluster, ve kterém se nachází vaše cloudová služba, nemá dostatek fyzických výpočetních prostředků, které by vyhověly vaší žádosti o nasazení

Nápovědu ke zmírnění těchto chyb přidělení najdete v tématu [selhání přidělení cloudové služby: řešení](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Proč při navýšení kapacity nebo škálování nasazení cloudové služby dojde k selhání přidělení?
Při nasazení cloudové služby se obvykle připnuté na konkrétní cluster. To znamená, že pro existující cloudovou službu musí být ve stejném clusteru přidělené nové instance. Pokud se cluster blíží kapacitě nebo není k dispozici požadovaná velikost/typ virtuálního počítače, může být požadavek neúspěšný.

Nápovědu ke zmírnění těchto chyb přidělení najdete v tématu [selhání přidělení cloudové služby: řešení](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Proč nasazení cloudové služby do skupiny vztahů někdy způsobí selhání přidělení?
Nové nasazení do prázdné cloudové služby může být přiděleno prostředky infrastruktury v jakémkoli clusteru v této oblasti, pokud není ke skupině vztahů připnuté cloudová služba. Nasazení do stejné skupiny vztahů se pokusí o stejný cluster. Pokud se cluster blíží kapacitě, požadavek může selhat.

Nápovědu ke zmírnění těchto chyb přidělení najdete v tématu [selhání přidělení cloudové služby: řešení](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Proč změna velikosti virtuálního počítače nebo přidání nového virtuálního počítače do existující cloudové služby někdy způsobí selhání přidělení?
Clustery v datacentru mohou mít různé konfigurace typů počítačů (například série, Av2 řady, D Series, Dv2 Series, G series, H Series atd.). Ale ne všechny clustery by nutně měly všechny typy virtuálních počítačů. Například pokud se pokusíte přidat virtuální počítač D Series do cloudové služby, která je již nasazena v clusteru pouze s řadou, dojde k selhání přidělení. K tomu dojde také v případě, že se pokusíte změnit velikost SKU virtuálního počítače (například přepínání z řady na řady D).

Nápovědu ke zmírnění těchto chyb přidělení najdete v tématu [selhání přidělení cloudové služby: řešení](cloud-services-allocation-failures.md#solutions).

Pokud chcete zjistit dostupné velikosti v oblasti, přečtěte si téma [Microsoft Azure: produkty dostupné v jednotlivých oblastech](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Proč dojde k selhání nasazení cloudové služby z důvodu limitů, kvót a omezení na mém předplatném nebo službě?
Nasazení cloudové služby může selhat, pokud prostředky, které se musí přidělit, překračují výchozí nebo maximální kvótu povolenou pro vaši službu na úrovni oblasti nebo datového centra. Další informace najdete v tématu [omezení Cloud Services](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits).

Můžete také sledovat aktuální využití/kvótu pro vaše předplatné na portálu: Azure Portal => předplatné => \<appropriate subscription>   => "využití + kvóta".

Informace související s využitím prostředků/spotřebou se dají získat taky prostřednictvím rozhraní API pro fakturaci Azure. Viz [Přehled rozhraní API pro Azure spotřeber](../cost-management-billing/manage/consumption-api-overview.md).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Jak můžu změnit velikost nasazeného virtuálního počítače cloudové služby bez opětovného nasazení?
Velikost virtuálního počítače nasazené cloudové služby nemůžete změnit bez opětovného nasazení. Velikost virtuálního počítače je integrována do lokality CSDEF, kterou lze aktualizovat pouze pomocí opětovného nasazení.

Další informace najdete v tématu [Postup aktualizace cloudové služby](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Proč nemůžu při použití účtu úložiště Azure Resource Manager nasadit Cloud Services prostřednictvím rozhraní API pro správu služeb nebo PowerShellu? 

Vzhledem k tomu, že cloudová služba je klasický prostředek, který není přímo kompatibilní s modelem Azure Resource Manager, nemůžete ho přidružit k účtům úložiště Azure Resource Manager. Tady je několik možností: 

- Nasazení prostřednictvím REST API.

    Při nasazení prostřednictvím REST API Service Management můžete obejít omezení zadáním adresy URL SAS pro úložiště objektů blob, který bude fungovat s klasickým i Azure Resource Manager účtem úložiště. Přečtěte [si další informace o vlastnosti '](/previous-versions/azure/reference/ee460813(v=azure.100))PackageUrl '.

- Nasazení prostřednictvím [Azure Portal](https://portal.azure.com).

    To bude fungovat z [Azure Portal](https://portal.azure.com) jako volání prostřednictvím proxy nebo překrytí, které umožňuje komunikaci mezi Azure Resource Manager a klasickými prostředky. 

## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Proč Azure Portal vyžaduje, aby měl účet úložiště pro nasazení?

Na klasickém portálu se balíček nahrál přímo do vrstvy rozhraní API pro správu a pak vrstva rozhraní API dočasně vloží balíček do interního účtu úložiště.  Tento proces způsobuje problémy s výkonem a škálovatelností, protože vrstva rozhraní API nebyla navržena jako služba pro nahrání souborů.  V Azure Portal (Správce prostředků modelu nasazení) jsme převedli dočasný krok prvního nahrání do vrstvy API, což vede k rychlejšímu a spolehlivější nasazení.

Stejně jako u nákladů je velmi malé a stejný účet úložiště můžete použít ve všech nasazeních. Pomocí [kalkulačky nákladů na úložiště](https://azure.microsoft.com/pricing/calculator/#storage1) můžete určit náklady na nahrání balíčku služby (CSPKG), stáhnout CSPKG a pak odstranit CSPKG.
