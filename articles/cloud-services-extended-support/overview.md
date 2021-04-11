---
title: O službě Azure Cloud Services (Rozšířená podpora)
description: Přečtěte si o podřízených prvcích prvku konfigurace sítě konfiguračního souboru služby, který určuje Virtual Network a hodnoty DNS.
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cc470d12377f50f9f9ae4b362bcabf3a5ce34e8f
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169279"
---
# <a name="about-azure-cloud-services-extended-support"></a>O službě Azure Cloud Services (Rozšířená podpora)

> [!IMPORTANT]
> Cloud Services (Rozšířená podpora) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cloud Services (Rozšířená podpora) je nový model nasazení založený na [Azure Resource Manager](../azure-resource-manager/management/overview.md) pro [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) produkt a je v současnosti ve verzi Public Preview. Cloud Services (Rozšířená podpora) má primární výhodu při poskytování místní odolnosti a paritě funkcí v Azure Cloud Services nasazených pomocí Azure Service Manager. Nabízí také některé funkce ARM, jako je například přístup a řízení na základě role (RBAC), značky, zásady a podpora šablon nasazení.  

V této změně se model nasazení založený na Azure Service Manager pro Cloud Services přejmenuje [Cloud Services (Classic)](../cloud-services/cloud-services-choose-me.md). Zachováte si možnost vytvářet a rychle nasazovat webové a cloudové aplikace a služby. V závislosti na aktuální poptávce budete moci škálovat infrastrukturu cloudových služeb a zajistit, aby výkon vašich aplikací zůstal stále při současném snížení nákladů.  

## <a name="what-does-not-change"></a>Co se nemění 
- Vytvoříte kód, definujete konfigurace a nasadíte ho do Azure. Azure nastaví výpočetní prostředí, spustí kód a potom ho monitoruje a zachová za vás.
- Cloud Services (Rozšířená podpora) také podporuje dva typy rolí, [webů a pracovních procesů](../cloud-services/cloud-services-choose-me.md). Neexistují žádné změny v návrhu, architektuře ani součástech webových a pracovních rolí. 
- K dispozici jsou tři součásti cloudové služby, definice služby (. csdef), konfigurace služby (. cscfg) a balíček služby (. cspkg), a ve svých [formátech](cloud-services-model-and-package.md)se nemění. 
- Pro běhový kód se nevyžadují žádné změny, protože rovina dat je stejná a řídicí rovina se mění. 
- Verze Azure GuestOS a přidružené aktualizace jsou zarovnané Cloud Services (Classic).
- Základní proces aktualizace s ohledem na aktualizace domén, jak upgrade pokračuje, vrácení zpět a povolených změn služby během aktualizace se nemění

## <a name="changes-in-deployment-model"></a>Změny v modelu nasazení

Aby bylo možné nasadit Cloud Services (Rozšířená podpora), vyžadují se pro konfiguraci služby (. cscfg) a definice služby (. csdef) minimální změny. Pro běhový kód se nevyžadují žádné změny. Skripty nasazení však bude nutné aktualizovat, aby volaly nová rozhraní API založená na Azure Resource Manager. 

:::image type="content" source="media/overview-image-1.png" alt-text="Image zobrazuje konfiguraci klasických cloudových služeb s přidáním části šablony. ":::

Hlavní rozdíly mezi Cloud Services (Classic) a Cloud Services (Rozšířená podpora) s ohledem na nasazení jsou: 

- Azure Resource Manager nasazení používají [šablony ARM](../azure-resource-manager/templates/overview.md) , což je soubor JavaScript Object Notation (JSON), který definuje infrastrukturu a konfiguraci pro váš projekt. Tato šablona využívá deklarativní syntaxi, která umožňuje prohlásit, co máte v úmyslu vytvořit, aniž by k tomu bylo nutné psát sekvence programových příkazů. Konfigurace služby a soubor definice služby musí být v souladu s [šablonou ARM](../azure-resource-manager/templates/overview.md) během nasazování Cloud Services (Rozšířená podpora). To můžete provést buď [ručním vytvořením šablony ARM](deploy-template.md) , nebo pomocí [PowerShellu](deploy-powershell.md), [portálu](deploy-portal.md) a sady [Visual Studio](deploy-visual-studio.md).  

- Zákazníci musí použít [Azure Key Vault](../key-vault/general/overview.md) ke [správě certifikátů v Cloud Services (Rozšířená podpora)](certificates-and-key-vault.md). Azure Key Vault umožňuje bezpečně ukládat a spravovat přihlašovací údaje aplikací, jako jsou tajné klíče, klíče a certifikáty, v centrálním a zabezpečeném cloudovém úložišti. Vaše aplikace se můžou ověřit, aby se v době spuštění načetly přihlašovací údaje, aby se Key Vault. 

- Všechny prostředky nasazené prostřednictvím [Azure Resource Manager](../azure-resource-manager/templates/overview.md) musí být ve virtuální síti. Virtuální sítě a podsítě se vytvářejí v Azure Resource Manager pomocí existujících rozhraní API Azure Resource Manager a při nasazení Cloud Services (Rozšířená podpora) se bude muset v oddílu NetworkConfiguration v. cscfg vykázat.   

- Každá cloudová služba (Rozšířená podpora) je jediné nezávislé nasazení. Služba Cloud Services (Rozšířená podpora) nepodporuje více slotů v rámci jedné cloudové služby.  
    - Schopnost prohození virtuálních IP adres se dá použít k prohození mezi dvěma Cloud Services (Rozšířená podpora). Chcete-li otestovat a připravit novou verzi cloudové služby, nasaďte cloudovou službu (rozšířenou podporu) a označte ji jako vyměnitelné VIP s jinou cloudovou službou (Rozšířená podpora).  

- Popisek služby DNS (Domain Name Service) je pro cloudovou službu volitelný (Rozšířená podpora). V Azure Resource Manager je popisek DNS vlastnost prostředku veřejné IP adresy přidruženého ke cloudové službě. 

## <a name="migration-to-azure-resource-manager"></a>Migrace na Azure Resource Manager

Cloud Services (Rozšířená podpora) poskytuje dvě cesty, které můžete migrovat z [Azure Service Manager](/powershell/azure/servicemanagement/overview) na [Azure Resource Manager](../azure-resource-manager/management/overview.md). 
1) Zákazníci nasadí cloudové služby přímo v Azure Resource Manager a pak odstraní starou cloudovou službu ve službě Azure Service Manager. 
2) Místní migrace podporuje možnost migrace Cloud Services (Classic) s minimálním až žádným výpadkem do Cloud Services (Rozšířená podpora). 

### <a name="additional-migration-options"></a>Další možnosti migrace

Při vyhodnocování plánů migrace z Cloud Services (Classic) na Cloud Services (Rozšířená podpora) můžete chtít prozkoumat další služby Azure, například: [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md), [App Service](../app-service/overview.md), [azure Kubernetes Service](../aks/intro-kubernetes.md)a [Azure Service Fabric](../service-fabric/service-fabric-overview.md). Tyto služby budou i nadále používat další funkce, zatímco Cloud Services (Rozšířená podpora) bude primárně udržovat paritu funkcí s Cloud Services (Classic). 

V závislosti na aplikaci může Cloud Services (Rozšířená podpora) vyžadovat, aby se přechodem na Azure Resource Manager v porovnání s jinými možnostmi podstatně nerovnalo. Pokud se vaše aplikace nevyvíjí, Cloud Services (Rozšířená podpora) je možnost, která se dá vzít v úvahu, protože poskytuje cestu k rychlé migraci. Naopak, pokud se vaše aplikace neustále vyvíjí a potřebuje moderní sadu funkcí, Prozkoumejte další služby Azure, abyste lépe vyřešili vaše aktuální a budoucí požadavky. 

## <a name="next-steps"></a>Další kroky
- Přečtěte si [požadavky na nasazení](deploy-prerequisite.md) pro Cloud Services (Rozšířená podpora).
- Nasaďte cloudovou službu (rozšířenou podporu) pomocí [Azure Portal](deploy-portal.md), [PowerShellu](deploy-powershell.md), [šablony](deploy-template.md) nebo sady [Visual Studio](deploy-visual-studio.md).
- Přečtěte si [Nejčastější dotazy](faq.md) k Cloud Services (Rozšířená podpora).
