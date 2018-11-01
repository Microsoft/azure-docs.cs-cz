---
title: Problémy s nasazením pro Microsoft Azure Cloud Services – nejčastější dotazy | Dokumentace Microsoftu
description: Tento článek obsahuje nejčastější dotazy o vývoji pro Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: d44cfc0164892c34bcbe16ca07e9ec67190ada24
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415300"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problémy při nasazení pro Azure Cloud Services: Nejčastější dotazy (FAQ)

Tento článek obsahuje nejčastější dotazy týkající se problémů s nasazením pro [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Můžete také najdete [cloudové služby virtuálních počítačů velikosti stránky](cloud-services-sizes-specs.md) pro informace o velikosti.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Proč nasazení cloudové služby do přípravného slotu někdy selže s chybou přidělení prostředků Pokud již existuje stávající nasazení v produkčním slotu?
Pokud Cloudová služba má buď slot nasazení, cloudové služby je připnutá k konkrétní cluster. To znamená, že pokud nasazení již existuje v produkčním slotu, nové pracovní nasazení jde přidělit jenom ve stejném clusteru jako produkční slot.

Pokud cluster, kde se nachází vaše Cloudová služba nemá dostatek fyzické výpočetní prostředky, které by vyhovovaly žádosti o vaše nasazení dojde k selhání přidělení.

Nápovědu k minimalizaci takových chyby v přidělení najdete v tématu [Chyba přidělení cloudové služby: řešení](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Proč vertikální navýšení nebo snížení mimo nasazení cloudové služby v některých případech dojít k selhání přidělení?
Po nasazení cloudové služby je obvykle získá připnout na konkrétním clusteru. To znamená, že škálování nahoru vstupně-výstupní existující cloudovou službu, musíte přidělit nových instancí ve stejném clusteru. Pokud clusteru se už brzy kapacity nebo požadované velikosti a typu virtuálního počítače není k dispozici, žádost může selhat.

Nápovědu k minimalizaci takových chyby v přidělení najdete v tématu [Chyba přidělení cloudové služby: řešení](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Proč někdy nasazení cloudové služby do skupiny vztahů za následek došlo k chybě přidělení?
Nové nasazení do prázdné cloudové služby je možné přidělit podle prostředků infrastruktury v libovolný cluster. v dané oblasti, pokud je připnutý cloudové služby do skupiny vztahů. Nasazení do stejné skupiny vztahů se pokusí vytvořit na stejném clusteru. Pokud cluster se už brzy kapacitu, žádost může selhat.

Nápovědu k minimalizaci takových chyby v přidělení najdete v tématu [Chyba přidělení cloudové služby: řešení](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Proč Změna velikosti virtuálního počítače nebo přidávání nového virtuálního počítače do existující cloudovou službu v některých případech způsobit došlo k chybě přidělení?
Clustery v datacentru může obsahovat různé konfigurace typy počítačů (například řady, řady Av2, řady D series, řada dv2, řady G, H series atd.). Ale ne všechny clustery byste chtěli nutně všechny typy virtuálních počítačů. Například pokud se pokusíte přidat do cloudové služby, který je již nasazen v clusteru A jen pro řadu virtuálních počítačů řady D series, dojde k selhání přidělení. Pokud se pokusíte změnit k tomu dochází také, že skladovou Položku virtuálního počítače o velikosti (například přechod z A series D series).

Nápovědu k minimalizaci takových chyby v přidělení najdete v tématu [Chyba přidělení cloudové služby: řešení](cloud-services-allocation-failures.md#solutions).

Dostupné velikosti ve vaší oblasti, najdete v části [Microsoft Azure: dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Proč nějakou dobu nasazení cloudové služby selže z důvodu omezení/kvóty a omezení na své předplatné nebo služby?
Nasazení cloudové služby může selhat, pokud prostředky, které je potřeba přidělit překročit výchozí nebo maximální kvóty povoleny pro vaši službu na úrovni oblasti nebo datového centra. Další informace najdete v tématu [Cloud Services omezuje](../azure-subscription-service-limits.md#cloud-services-limits).

Může také sledovat aktuální využití a kvóty pro vaše předplatné na portálu pro: Azure portal = > předplatná = > \<příslušné předplatné > = > "Využití a kvóty".

Informací o souvisejícím s využití/spotřeby prostředků můžete získat také prostřednictvím rozhraní Azure API pro fakturaci. Zobrazit [rozhraní API (Preview) pro využití prostředků Azure](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Jak mohu změnit velikost nasazené cloudové službě virtuálního počítače bez jejího opětovného nasazení?
Nelze změnit velikost virtuálního počítače nasazeného cloudové služby bez jejího opětovného nasazení. Velikost virtuálního počítače je integrovaná do CSDEF, které lze aktualizovat pouze opětovné nasazení.

Další informace najdete v tématu [aktualizace cloudové služby](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Proč mi nejde nasadit cloudové služby Service Management API nebo Powershellu, při použití účtu úložiště Azure Resource Manageru? 

Cloudové služby je klasický prostředek, který není přímo kompatibilní s modelem Azure Resource Manageru, nelze ho propojit s účty úložiště Azure Resource Manageru. Tady je několik možností: 
 
- Nasazení pomocí rozhraní REST API.

    Při nasazení pomocí REST API pro správu služeb, může získat kolem omezení tak, že zadáte adresu URL SAS úložiště objektů blob, který bude fungovat s účtem Classic a Azure Resource Manager Storage. Další informace o vlastnosti "PackageUrl" [tady](https://msdn.microsoft.com/library/azure/ee460813.aspx).  
- Nasazení pomocí [webu Azure portal](https://portal.azure.com).

    Bude to fungovat z [webu Azure portal](https://portal.azure.com) jako volání prochází proxy/překrytí, který umožňuje komunikaci mezi prostředky Azure Resource Manager a Classic. 
 
## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Proč Azure portal vyžaduje me a použijte účet úložiště pro nasazení? 

Na portálu classic balíček byl nahrán do vrstvy API management přímo a vrstvu rozhraní API by dočasně vložte balíček do interního úložiště účtu.  Tento proces způsobuje problémy, výkon a škálovatelnost, protože vrstvu rozhraní API nebyl navržen, aby se služba nahrávání souborů.  Na webu Azure Portal (model nasazení Resource Manager) jsme mají obejít dočasné krok nejdříve odeslat do rozhraní API vrstvy, výsledkem je rychlejší a spolehlivější nasazení. 

Jako u náklady je velmi malý a můžete znovu použít stejný účet úložiště ve všech nasazeních. Můžete použít [cenová Kalkulačka úložiště](https://azure.microsoft.com/pricing/calculator/#storage1) k určení nákladů pro nahrání balíčku service (CSPKG), stáhněte si CSPKG a pak odstraňte CSPKG. 
