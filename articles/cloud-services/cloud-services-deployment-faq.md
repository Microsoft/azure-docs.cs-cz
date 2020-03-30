---
title: Nejčastější dotazy k nasazení cloudových služeb Microsoft Azure| Dokumenty společnosti Microsoft
description: V tomto článku jsou uvedeny nejčastější dotazy týkající se nasazení cloudových služeb Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 63a219078927e9001a8eb4085c722e7ec8d2fac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980632"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problémy s nasazením cloudových služeb Azure: Nejčastější dotazy (nejčastější dotazy)

Tento článek obsahuje nejčastější dotazy týkající se problémů s nasazením [cloudových služeb Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Informace o velikosti najdete také na [stránce Velikost virtuálního počítače cloudových služeb.](cloud-services-sizes-specs.md)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Proč nasazení cloudové služby do pracovního slotu někdy selže s chybou přidělení prostředků, pokud již existuje existující nasazení v produkčním slotu?
Pokud má cloudová služba nasazení v obou slotech, celá cloudová služba se připne na konkrétní cluster. To znamená, že pokud nasazení již existuje v produkčním slotu, nové pracovní nasazení lze přidělit pouze ve stejném clusteru jako produkční slot.

K selhání přidělení dochází, když cluster, ve kterém je umístěna cloudová služba, nemá dostatek fyzických výpočetních prostředků, které by uspokojily váš požadavek na nasazení.

Pomoc s zmírněním těchto selhání přidělení naleznete v [tématu Selhání přidělení cloudové služby: Řešení](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Proč horizontální navýšení kapacity nebo horizontální navýšení kapacity nasazení cloudové služby někdy vede k selhání přidělení?
Když se nasadí cloudová služba, obvykle se připne k určitému clusteru. To znamená, že horizontální navýšení kapacity a zvěskaní existující cloudové služby musí přidělit nové instance ve stejném clusteru. Pokud se cluster blíží kapacitě nebo požadovaná velikost/typ virtuálního počítače není k dispozici, může požadavek selhat.

Pomoc s zmírněním těchto selhání přidělení naleznete v [tématu Selhání přidělení cloudové služby: Řešení](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Proč nasazení cloudové služby do skupiny spřažení někdy vede k selhání přidělení?
Nové nasazení do prázdné cloudové služby může být přiděleno prostředků infrastruktury v libovolném clusteru v této oblasti, pokud cloudová služba je připnutá ke skupině spřažení. Nasazení do stejné skupiny spřažení bude pokus o stejný cluster. Pokud se cluster blíží kapacitě, požadavek může selhat.

Pomoc s zmírněním těchto selhání přidělení naleznete v [tématu Selhání přidělení cloudové služby: Řešení](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Proč změna velikosti virtuálního počítače nebo přidání nového virtuálního počítače do existující cloudové služby někdy vede k selhání přidělení?
Clustery v datovém centru mohou mít různé konfigurace typů počítačů (například řada A, řada Av2, řada D, řada D2, řada G, řada H atd.). Ale ne všechny clustery by nutně mít všechny druhy virtuálních měn. Například pokud se pokusíte přidat virtuální hod řady D do cloudové služby, která je již nasazená v clusteru pouze řady A, dojde k selhání přidělení. K tomu dojde také v případě, že se pokusíte změnit velikosti skladových položk virtuálního počítače (například přepnutí z řady A na řadu D).

Pomoc s zmírněním těchto selhání přidělení naleznete v [tématu Selhání přidělení cloudové služby: Řešení](cloud-services-allocation-failures.md#solutions).

Informace o velikostech dostupných ve vaší oblasti najdete v tématu [Microsoft Azure: Produkty dostupné podle oblastí](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Proč nasazení cloudové služby někdy selže z důvodu omezení/ kvót /omezení na mé předplatné nebo službu?
Nasazení cloudové služby může selhat, pokud prostředky, které mají být přiděleny, překročí výchozí nebo maximální kvótu povolenou pro vaši službu na úrovni oblasti nebo datového centra. Další informace najdete v [tématu omezení cloudových služeb](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-cloud-services-limits).

Můžete také sledovat aktuální využití/kvótu pro vaše předplatné na portálu: \<Portál Azure => Předplatná => příslušné předplatné> => "Využití + kvóta".

Informace o využití nebo spotřebě zdrojů lze také načíst prostřednictvím rozhraní API pro fakturaci Azure. Viz [Rozhraní API využití prostředků Azure (preview).](../cost-management-billing/manage/usage-rate-card-overview.md#azure-resource-usage-api-preview)

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Jak můžu změnit velikost nasazeného virtuálního počítače cloudové služby, aniž bych ho znovu nasazoval?
Velikost virtuálního počítače nasazené cloudové služby nelze změnit, aniž byste ji znovu nasadili. Velikost virtuálního počítače je integrovaná do CSDEF, které lze aktualizovat pouze s redeploy.

Další informace naleznete v [tématu Jak aktualizovat cloudovou službu](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Proč se mi při používání účtu úložiště Azure Resource Manager unesete pomocí api pro správu služeb nebo prostředí PowerShell? 

Vzhledem k tomu, že cloudová služba je klasický prostředek, který není přímo kompatibilní s modelem Azure Resource Manager, nelze přidružit k účtům úložiště Azure Resource Manager. Zde je několik možností: 

- Nasazení prostřednictvím rozhraní REST API.

    Při nasazení prostřednictvím rozhraní REST API pro správu služeb můžete omezení obejít zadáním adresy URL SAS do úložiště objektů blob, které bude fungovat s účtem úložiště Classic i Azure Resource Manager. Přečtěte si více o 'PackageUrl' vlastnost [zde](/previous-versions/azure/reference/ee460813(v=azure.100)).

- Nasazování přes [portál Azure](https://portal.azure.com).

    To bude fungovat z [portálu Azure](https://portal.azure.com) jako volání prochází proxy nebo překrytí, které umožňuje komunikaci mezi Azure Resource Manager a klasické prostředky. 

## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>Proč portál Azure vyžaduje, abych poskytl účet úložiště pro nasazení?

Na klasickém portálu byl balíček odeslán přímo do vrstvy rozhraní API pro správu a pak by vrstva rozhraní API dočasně vložila balíček do účtu interního úložiště.  Tento proces způsobuje problémy s výkonem a škálovatelností, protože vrstva rozhraní API nebyla navržena jako služba pro nahrávání souborů.  Na webu Azure Portal (model nasazení Správce prostředků) jsme obešli dočasný krok prvního odeslání do vrstvy rozhraní API, což má za následek rychlejší a spolehlivější nasazení.

Pokud jde o náklady, je velmi malý a můžete znovu použít stejný účet úložiště ve všech nasazeních. Kalkulačku nákladů na [úložiště](https://azure.microsoft.com/pricing/calculator/#storage1) můžete použít k určení nákladů na nahrání balíčku služby (CSPKG), stažení cspkg a následném odstranění CSPKG.
