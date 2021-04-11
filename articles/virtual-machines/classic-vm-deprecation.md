---
title: Vyřazování virtuálních počítačů Azure (Classic) do 1. března 2023
description: Tento článek poskytuje podrobný přehled o vyřazení virtuálních počítačů vytvořených pomocí modelu nasazení Classic.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 2fb710bab03d595d6e54bc8dd8fbda38c57123e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668203"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migrace prostředků IaaS do Azure Resource Manager do 1. března 2023 

V 2014 jsme na [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)spustili infrastrukturu jako službu (IaaS). Od verze jsme vylepšili možnosti. Vzhledem k tomu, že Azure Resource Manager nyní má úplné možnosti IaaS a další zálohy, jsme za 28. února 2020 zakázali správu virtuálních počítačů s IaaS prostřednictvím [Azure Service Manager](./migration-classic-resource-manager-faq.md#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM). Tato funkce bude plně vyřazena 1. března 2023. 

V dnešní době se přibližně 90% virtuálních počítačů s IaaS používá Azure Resource Manager. Pokud používáte prostředky IaaS prostřednictvím ASM, začněte plánovat migraci hned teď. Dokončete ji od 1. března 2023, abyste mohli využít výhod [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Virtuální počítače vytvořené pomocí modelu nasazení Classic se budou řídit [moderními zásadami životního cyklu](https://support.microsoft.com/help/30881/modern-lifecycle-policy) pro vyřazení.

## <a name="how-does-this-affect-me"></a>Co to pro mě znamená? 

- Od 28. února 2020 už zákazníci, kteří nevyužili virtuální počítače IaaS prostřednictvím ASM v měsíci z února 2020, už nemůžou vytvářet virtuální počítače (Classic). 
- Od 1. března 2023 už zákazníci nebudou moct spouštět virtuální počítače s IaaS pomocí ASM. Všechny, které jsou stále spuštěné nebo přidělené, se zastaví a zruší její přidělení. 
- Od 1. března 2023 budou předplatná, která nejsou migrována na Azure Resource Manager, informována o časových osách pro odstranění všech zbývajících virtuálních počítačů (Classic).  

Toto vyřazení *nemá vliv na* tyto služby a funkce Azure: 
- [Cloud Services Azure (Classic)](../cloud-services/cloud-services-choose-me.md)
- *Účty úložiště* nepoužívané virtuálními počítači (klasické) 
- Virtuální sítě  nepoužívané virtuálními počítači (Classic) 
- Jiné klasické prostředky

## <a name="what-resources-are-available-for-this-migration"></a>Jaké prostředky jsou k dispozici pro tuto migraci?

- [Microsoft Q&A](/answers/topics/azure-virtual-machines-migration.html): Podpora Microsoftu a komunity pro migraci.

- [Podpora migrace do Azure](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): tým vyhrazené podpory pro technickou pomoc při migraci. Zákazníci bez technické podpory můžou využít [bezplatné možnosti podpory](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%0A%20%20%20%20%22pesId%22%3A%20%22f3dc5421-79ef-1efa-41a5-42bf3cbb52c6%22%2C%0A%20%20%20%20%22supportTopicId%22%3A%20%22794bb734-af1b-e2d5-a757-dac7438009ab%22%2C%0A%20%20%20%20%22contextInfo%22%3A%20%22Migrate%20IAAS%20resources%20from%20Classic%20%28ASM%29%20to%20Azure%20Resource%20Manager%20%28ARM%29%22%2C%0A%20%20%20%20%22caller%22%3A%20%22NoSupportPlanASM2ARM%22%2C%0A%20%20%20%20%22severity%22%3A%20%222%22%0A%7D) , které jsou specifické pro tuto migraci. 

- [Rychlé sledování společnosti Microsoft](https://www.microsoft.com/fasttrack): rychlá stopa může pomáhat oprávněným zákazníkům s plánováním & provádění této migrace. [Identifikujte](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fprograms%2Fazure-fasttrack%2F%23nomination&data=02%7C01%7CTanmay.Gore%40microsoft.com%7C3e75bbf3617944ec663a08d85c058340%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637360526032558561&sdata=CxWTVQQPVWNwEqDZKktXzNV74pX91uyJ8dY8YecIgGc%3D&reserved=0) se pro program pro migraci řadiče domény.  

- Pokud vaše společnost nebo organizace spolupracuje se společností Microsoft nebo spolupracuje s zástupci Microsoftu (jako jsou architekti cloudových řešení (CSAs) nebo správci technického účtu (TAMs)), pracujte s nimi pro další prostředky pro migraci.

## <a name="what-actions-should-i-take"></a>Jaké akce mám provést? 

Začněte plánovat migraci na Azure Resource Manager dnes. 

1. Vytvořit seznam všech ovlivněných virtuálních počítačů: 

   - Virtuální počítače typu **virtuální počítače (Classic)** v [podokně virtuální počítač Azure Portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) jsou všechny ovlivněné virtuální počítače v rámci předplatného. 
   - [K zobrazení](../governance/resource-graph/concepts/work-with-data.md) seznamu všech virtuálních počítačů označených příznakem (Classic) a souvisejících informací pro vybraná předplatná se můžete dotazovat také pomocí [portálu](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22) Azure Resource Graph. 
   - 8. února 2020 jsme odeslali e-maily s předmětem "zahájení plánování migrace virtuálních počítačů s IaaS do Azure Resource Manager" vlastníkům předplatného. E-mail poskytuje seznam všech předplatných a virtuálních počítačů (klasických) virtuálních počítačů. Použijte je prosím k sestavení tohoto seznamu. 

1. [Přečtěte si další informace](./migration-classic-resource-manager-overview.md) o migraci virtuálních počítačů se systémem [Linux](./migration-classic-resource-manager-plan.md) a [Windows](./migration-classic-resource-manager-plan.md) (Classic) na Azure Resource Manager. Další informace najdete v tématu [Nejčastější dotazy k migraci z modelu Classic na Azure Resource Manager](./migration-classic-resource-manager-faq.md).

1. Pro migraci stávajících virtuálních počítačů se třemi jednoduchými kroky doporučujeme spustit plánování pomocí [Nástroje pro migraci podpory platformy](./migration-classic-resource-manager-overview.md) : Ověřte, připravte a potvrďte. Tento nástroj je navržený tak, aby se virtuální počítače migrovali do minimálního výpadku. 

   - První krok, ověřit, nemá žádný vliv na stávající nasazení a obsahuje seznam všech nepodporovaných scénářů migrace. 
   - Projděte si [seznam alternativních řešení](./migration-classic-resource-manager-overview.md#unsupported-features-and-configurations) a opravte nasazení a připravte ho na migraci. 
   - V ideálním případě, když jsou všechny chyby ověřování opraveny, neměli byste během postupu přípravy a potvrzení narazit na žádné problémy. Po úspěšném potvrzení se nasazení migruje za provozu do Azure Resource Manager a pak se dá spravovat prostřednictvím nových rozhraní API vystavených Azure Resource Manager. 

   Pokud nástroj pro migraci není vhodný pro migraci, můžete prozkoumat [jiné výpočetní nabídky](/azure/architecture/guide/technology-choices/compute-decision-tree) pro migraci. Vzhledem k tomu, že existuje spousta nabídek Azure COMPUTE a liší se od sebe, nemůžeme jim poskytnout cestu k migraci podporovanou platformou.  

1. V případě technických otázek, problémů a pomoci s přidáním předplatných do seznamu povolených [služeb se obraťte na podporu](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"}).

1. Dokončete migraci co nejdříve, abyste předešli dopadům na firmu a využili jste lepší výkon, zabezpečení a nové funkce Azure Resource Manager. 
