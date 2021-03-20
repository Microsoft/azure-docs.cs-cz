---
title: Nejčastější dotazy k Azure Cloud Services (Rozšířená podpora)
description: Nejčastější dotazy k Azure Cloud Services (Rozšířená podpora)
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 3338f7b6bd418cea2bfdbbcd40692b9342f48cfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744354"
---
# <a name="frequently-asked-questions-for-azure-cloud-services-extended-support"></a>Nejčastější dotazy ke službám Azure Cloud Services (s rozšířenou podporou)
Tento článek obsahuje nejčastější dotazy týkající se Azure Cloud Services (Rozšířená podpora).

## <a name="general"></a>Obecné

### <a name="what-is-the-resource-name-for-cloud-services-classic--cloud-services-extended-support"></a>Jaký je název prostředku pro Cloud Services (Classic) & Cloud Services (Rozšířená podpora)?
- Cloud Services (klasický): `microsoft.classiccompute/domainnames`
- Cloud Services (Rozšířená podpora): `microsoft.compute/cloudservices`

### <a name="what-locations-are-available-to-deploy-cloud-services-extended-support"></a>Jaká umístění jsou k dispozici pro nasazení Cloud Services (Rozšířená podpora)?
Cloud Services (Rozšířená podpora) je k dispozici ve všech oblastech veřejného cloudu.

### <a name="how-does-my-quota-change"></a>Jak se změní kvóta? 
Zákazníci budou muset požádat o kvótu pomocí stejných procesů jako u jakéhokoli jiného Azure Resource Manager produktu. Kvóta v Azure Resource Manager je regionální a pro každou oblast bude potřeba samostatná žádost o kvótu.

### <a name="why-dont-i-see-a-production--staging-slot-anymore"></a>Proč už nevidím produkční & pro přípravu?
Cloud Services (Rozšířená podpora) nepodporuje logický koncept hostované služby, která zahrnuje dva sloty (produkční & fázování). Každé nasazení je nezávislé nasazení cloudové služby (rozšířené podpory). Chcete-li otestovat a připravit novou verzi cloudové služby, nasaďte cloudovou službu (rozšířenou podporu) a označte ji jako vyměnitelné VIP s jinou cloudovou službou (Rozšířená podpora).

### <a name="why-cant-i-create-an-empty-cloud-service-anymore"></a>Proč už nejde vytvořit prázdnou cloudovou službu?
Koncept názvů hostovaných služeb už neexistuje, nemůžete vytvořit prázdnou cloudovou službu (Rozšířená podpora).

### <a name="does-cloud-services-extended-support-support-resource-health-check-rhc"></a>Podporuje Cloud Services (Rozšířená podpora) Resource Health kontrolu (RHC)?
Ne, Cloud Services (Rozšířená podpora) nepodporuje kontrolu Resource Health (RHC).

### <a name="how-are-role-instance-metrics-changing"></a>Jak se mění metriky instancí rolí?
Metriky instance role nemění žádné změny. 

### <a name="how-are-web--worker-roles-changing"></a>Jak se mění role pracovních procesů webu &?
Neexistují žádné změny v návrhu, architektuře ani součástech webových a pracovních rolí. 

### <a name="how-are-role-instances-changing"></a>Jak se mění instance rolí?
Neexistují žádné změny v návrhu, architektuře ani součástech instancí rolí. 

### <a name="how-will-guest-os-updates-change"></a>Jak se změní aktualizace operačního systému hosta?
 V metodě zavedení nejsou žádné změny. Cloud Services (Classic) a Cloud Services (Rozšířená podpora) získají stejné aktualizace.
 
### <a name="does-cloud-services-extended-support-support-stopped-allocated-and-stopped-deallocated-states"></a>Podporuje Cloud Services (Rozšířená podpora) stav zastaveno a zastaveno – přidělení zrušeno a zrušeno?

Nasazení Cloud Services (Rozšířená podpora) podporuje pouze zastavený stav, který se v Azure Portal zobrazí jako "zastaveno". Stav zastaveno (přidělení zrušeno) není podporováno. 

### <a name="do-cloud-services-extended-support-deployments-support-scaling-across-clusters-availability-zones-and-regions"></a>Podporují nasazení Cloud Services (Rozšířená podpora) škálování napříč clustery, zónami dostupnosti a oblastmi?
Nasazení Cloud Services (Rozšířená podpora) se nedají škálovat mezi víc clusterů, zón dostupnosti a oblastmi. 

### <a name="are-there-any-pricing-differences-between-cloud-services-classic-and-cloud-services-extended-support"></a>Existují nějaké cenové rozdíly mezi Cloud Services (Classic) a Cloud Services (Rozšířená podpora)?
Cloud Services (Rozšířená podpora) používá veřejné IP adresy Azure Key Vault a Basic (ARM).Zákazníci vyžadující certifikáty potřebují použít Azure Key Vault pro správu certifikátů ([Další informace](https://azure.microsoft.com/pricing/details/key-vault/) o cenách Azure Key Vault.)   Každá veřejná IP adresa pro Cloud Services (Rozšířená podpora) se účtuje samostatně ([Další informace](https://azure.microsoft.com/pricing/details/ip-addresses/) o cenách veřejných IP adres). 
## <a name="resources"></a>Zdroje informací 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-resource-group"></a>Které prostředky propojené s nasazením služby Cloud Services (Rozšířená podpora) musí být živé ve stejné skupině prostředků?
Nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě a směrovací tabulky musí být živé ve stejné oblasti a skupině prostředků. 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-region"></a>Jaké prostředky propojené s nasazením služby Cloud Services (Rozšířená podpora) potřebujete živě ve stejné oblasti?
Key Vault, virtuální síť, veřejné IP adresy, skupiny zabezpečení sítě a směrovací tabulky musí být živé ve stejné oblasti.

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-virtual-network"></a>Jaké prostředky propojené s nasazením služby Cloud Services (Rozšířená podpora) potřebují být živé ve stejné virtuální síti?
Veřejné IP adresy, nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě a směrovací tabulky musí být živé ve stejné virtuální síti. 

## <a name="deployment-files"></a>Soubory nasazení 

### <a name="how-can-i-use-a-template-to-deploy-or-manage-my-deployment"></a>Jak můžu použít šablonu k nasazení nebo správě svého nasazení?
Šablony a soubory parametrů lze předat jako parametr pomocí rozhraní REST, PowerShell a rozhraní příkazového řádku. Lze je také odeslat pomocí Azure Portal.  

### <a name="do-i-need-to-maintain-four-files-now-template-parameter-csdef-cscfg"></a>Potřebuji nyní zachovat čtyři soubory? (šablona, parametr, csdef, cscfg)
Šablony a soubory parametrů se používají jenom pro automatizaci nasazení. Podobně jako Cloud Services (Classic) můžete nejprve ručně vytvořit závislé prostředky a pak nasazení Cloud Services (rozšířené podpory) pomocí PowerShellu, příkazů CLI nebo prostřednictvím portálu se stávajícími možnostmi csdef, cscfg.

### <a name="how-does-my-application-code-change-on-cloud-services-extended-support"></a>Jak se změní můj kód aplikace na Cloud Services (Rozšířená podpora)
Pro kód aplikace zabalený v cspkg nejsou nutné žádné změny. Stávající aplikace budou i nadále fungovat jako dříve. 


## <a name="migration"></a>Migrace

### <a name="will-cloud-services-extended-support-mitigate-the-failures-due-to-allocation-failures"></a>Bude Cloud Services (Rozšířená podpora) zmírnit chyby kvůli chybám přidělení?
Ne, nasazení cloudové služby (Rozšířená podpora) jsou vázaná na cluster, jako je Cloud Services (Classic). Proto pokud je cluster plný, neúspěšné alokace budou i nadále existovat. 

### <a name="when-do-i-need-to-migrate"></a>Kdy je potřeba migrovat? 
Odhad potřebný čas a migrace složitosti závisí na rozsahu proměnných. Plánování je nejúčinnější krok pro pochopení rozsahu práce, blokování a složitosti migrace.

## <a name="networking"></a>Sítě

### <a name="why-cant-i-create-a-deployment-without-virtual-network"></a>Proč nemůžu vytvořit nasazení bez virtuální sítě?
Virtuální sítě jsou požadovaným prostředkem pro jakékoli nasazení na Azure Resource Manager. Nasazení Cloud Services (Rozšířená podpora) musí být v rámci virtuální sítě aktivní. 

### <a name="why-am-i-now-seeing-so-many-networking-resources"></a>Proč teď zobrazujeme tolik síťových prostředků? 
V Azure Resource Manager jsou komponenty nasazení Cloud Services (rozšířené podpory) zpřístupněny jako prostředek pro lepší přehlednost a lepší kontrolu. Stejný typ prostředků se použil v Cloud Services (Classic), ale byly teď skryté. Jedním z příkladů takového prostředku je veřejné Load Balancer, což je teď explicitní prostředek pro čtení, který je automaticky vytvořený platformou.

### <a name="what-restrictions-apply-for-a-subnet-with-respective-to-cloud-services-extended-support"></a>Jaká omezení platí pro podsíť s příslušnými Cloud Services (Rozšířená podpora)?
Podsíť obsahující nasazení Cloud Services (Rozšířená podpora) se nedají sdílet s nasazeními z jiných výpočetních produktů, jako je Virtual Machines, Virtual Machines sady škálování, Service Fabric atd.

### <a name="what-ip-allocation-methods-are-supported-on-cloud-services-extended-support"></a>Jaké metody přidělování IP adres jsou podporované v cloudových službách (Rozšířená podpora)?
Cloud Services (Rozšířená podpora) podporuje metody přidělování statických IP adres pro dynamické &. Statické IP adresy se v souboru. cscfg odkazují jako rezervované IP adresy.

### <a name="why-am-i-getting-charged-for-ip-addresses"></a>Proč se mi účtují poplatky za IP adresy?
Zákazníkům se fakturuje použití IP adres na Cloud Services (Rozšířená podpora), stejně jako se pro uživatele fakturují IP adresy přidružené k virtuálním počítačům. 

### <a name="can-i-use-a-dns-name-with-cloud-services-extended-support"></a>Můžu použít název DNS s Cloud Services (Rozšířená podpora)? 
Ano. Cloud Services (rozšířené podpoře) se taky dá předávat název DNS. U Azure Resource Manager je popisek DNS volitelnou vlastností veřejné IP adresy, která je přiřazena ke cloudové službě. Formát názvu DNS pro nasazení na základě Azure Resource Manager je `<userlabel>.<region>.cloudapp.azure.com`

## <a name="certificates--key-vault"></a>Certifikáty & Key Vault

### <a name="why-do-i-need-to-manage-my-certificates-on-cloud-services-extended-support"></a>Proč potřebuji spravovat certifikáty na Cloud Services (Rozšířená podpora)?
Cloud Services (Rozšířená podpora) přijal stejný postup jako jiné výpočetní nabídky, kde se certifikáty nacházejí v trezorech spravovaného klíče zákazníka. Díky tomu mohou zákazníci mít úplnou kontrolu nad svými tajnými klíči & certifikátů. 

### <a name="can-i-use-one-key-vault-for-all-my-deployments-in-all-regions"></a>Můžu použít jednu Key Vault pro všechna moje nasazení ve všech oblastech?
No. Key Vault je regionální prostředek a zákazníci potřebují jednu Key Vault v každé oblasti. Jeden Key Vault však lze použít pro všechna nasazení v rámci dané oblasti.

## <a name="next-steps"></a>Další kroky
Pokud chcete začít používat Cloud Services (Rozšířená podpora), přečtěte si téma [nasazení cloudové služby (Rozšířená podpora) pomocí PowerShellu](deploy-powershell.md) .