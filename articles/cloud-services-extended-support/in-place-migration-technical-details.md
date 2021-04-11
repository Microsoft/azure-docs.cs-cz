---
title: Technické podrobnosti a požadavky pro migraci na Azure Cloud Services (Rozšířená podpora)
description: Poskytuje technické podrobnosti a požadavky pro migraci z Azure Cloud Services (Classic) do Azure Cloud Services (Rozšířená podpora).
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 4ff7d9aa2075b675a7ecd979c08d5621bbdd831a
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286870"
---
# <a name="technical-details-of-migrating-to-azure-cloud-services-extended-support"></a>Technické podrobnosti o migraci na Azure Cloud Services (Rozšířená podpora)   

Tento článek popisuje technické podrobnosti týkající se nástroje pro migraci, které se týkají Cloud Services (Classic). 

> [!IMPORTANT]
> Migrace z Cloud Services (Classic) na Cloud Services (Rozšířená podpora) pomocí nástroje pro migraci je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="details-about-feature--scenarios-supported-for-migration"></a>Podrobnosti o funkcích a scénářích podporovaných pro migraci 

### <a name="extensions-and-plugin-migration"></a>Rozšíření a migrace modulu plug-in 
- Budou migrována všechna povolená a podporovaná rozšíření. 
- Zakázaná rozšíření nebudou migrována. 
- Moduly plug-in jsou starší verze konceptu a měly by být před migrací odebrány. Jsou podporované pro migraci, ale po migraci, pokud je potřeba povolit rozšíření, musíte nejdřív odebrat modul plug-in, aby se rozšíření nainstalovalo. Tato funkce má největší vliv na moduly plug-in a rozšíření pro vzdálené plochy.   
 
### <a name="certificate-migration"></a>Migrace certifikátu
- V Cloud Services (Rozšířená podpora) se certifikáty ukládají do Key Vault. V rámci migrace vytvoříme Key Vault pro zákazníky, kteří mají název cloudové služby a přenesli do Key Vault všechny certifikáty z Azure Service Manager. 
- Odkaz na tento Key Vault je uveden v šabloně nebo předaný prostřednictvím PowerShellu nebo rozhraní příkazového řádku Azure CLI. 

### <a name="service-configuration-and-service-definition-files"></a>Konfigurace služby a soubory definice služby
- Soubory. cscfg a. csdef je potřeba aktualizovat pro Cloud Services (Rozšířená podpora) s menšími změnami. 
- Názvy prostředků, jako jsou virtuální síť a SKU virtuálních počítačů, se liší. Přečtěte si téma [Překlad prostředků a pravidla pojmenování po migraci](#translation-of-resources-and-naming-convention-post-migration)
- Zákazníci můžou svoje nová nasazení načíst prostřednictvím [PowerShellu](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) a [rozhraní REST API](https://docs.microsoft.com/rest/api/compute/cloudservices/get). 

### <a name="cloud-service-and-deployments"></a>Cloudová služba a nasazení
- Každé nasazení Cloud Services (Rozšířená podpora) je nezávislá cloudová služba. Nasazení už není seskupené do cloudové služby pomocí slotů.
- Pokud máte ve vaší cloudové službě dva sloty (Classic), je nutné odstranit jednu patici (fázování) a pomocí nástroje pro migraci přesunout druhý (produkční) slot na Azure Resource Manager. 
- Veřejná IP adresa v nasazení cloudové služby zůstane stejná i po migraci na Azure Resource Manager a zveřejňuje se jako základní IP adresa SKU (dynamický nebo statický) prostředek. 
- Název DNS a doména (cloudapp.azure.net) migrované cloudové služby zůstávají stejné. 

### <a name="virtual-network-migration"></a>Migrace virtuální sítě
- Pokud je nasazení Cloud Services ve virtuální síti, pak se při migraci všech Cloud Services a přidružených virtuálních síťových prostředků společně migrují. 
- Po migraci je virtuální síť umístěná v jiné skupině prostředků než cloudová služba. 
- U virtuálních sítí s více Cloud Services se každou cloudovou službu migruje jednou po druhé. 

### <a name="migration-of-deployments-not-in-a-virtual-network"></a>Migrace nasazení mimo virtuální síť
- V 2017 se v Azure začali automaticky vytvářet nová nasazení (bez zákaznická virtuální sítě) na platformě, která vytvořila výchozí virtuální síť. Tyto výchozí virtuální sítě se od zákazníků skryjí.   
- V rámci migrace se tato výchozí virtuální síť zveřejňuje zákazníkům po Azure Resource Manager. Pokud chtějí zákazníci spravovat nebo aktualizovat nasazení v Azure Resource Manager, musí tyto informace o virtuální síti přidat v části NetworkConfiguration souboru. cscfg.    
- Výchozí virtuální síť, když se migruje na Azure Resource Manager, se umístí do stejné skupiny prostředků jako cloudová služba.
- Cloud Services vytvořené před tímto časem nebudou v žádné virtuální síti a nebude možné je migrovat pomocí tohoto nástroje. Zvažte opětovné nasazení těchto Cloud Services přímo v Azure Resource Manager. 
- Pokud chcete zkontrolovat, jestli má nasazení nárok na migraci, spusťte v nasazení ověřování API. Výsledek ověřování API bude obsahovat chybovou zprávu, která výslovně zmiňuje, jestli je toto nasazení opravňující k migraci.     

### <a name="load-balancer"></a>Load Balancer   
- Pro cloudovou službu využívající veřejný koncový bod je platforma vytvořená pro vyrovnávání zatížení, která je přidružená ke cloudové službě, vystavená v rámci předplatného zákazníka v Azure Resource Manager. Nástroj pro vyrovnávání zatížení je prostředkem jen pro čtení a aktualizace se omezují jenom prostřednictvím souborů konfigurace služby (. cscfg) a definice služby (. csdef). 

### <a name="key-vault"></a>Key Vault
- V rámci migrace Azure automaticky vytvoří nový Key Vault a migruje do něj všechny certifikáty. Nástroj vám neumožňuje používat existující Key Vault. 
- Cloud Services (Rozšířená podpora) vyžaduje Key Vault nacházející se ve stejné oblasti a předplatném. Tato Key Vault se automaticky vytvoří jako součást migrace. 


## <a name="translation-of-resources-and-naming-convention-post-migration"></a>Překlad prostředků a migrace podle konvence pojmenování po migraci
V rámci migrace se změnily názvy prostředků a několik Cloud Servicesch funkcí se zveřejňuje jako Azure Resource Manager prostředky. Tabulka shrnuje změny, které jsou specifické pro migraci Cloud Services.

| Cloud Services (klasický) <br><br> Název prostředku | Cloud Services (klasický) <br><br> Syntax| Cloud Services (Rozšířená podpora) <br><br> Název prostředku| Cloud Services (Rozšířená podpora) <br><br> Syntax | 
|---|---|---|---|
| Cloudová služba | `cloudservicename` | Nepřidruženo| Nepřidruženo |
| Nasazení (vytvořeno portálem) <br><br> Nasazení (není vytvořeno portálem)  | `deploymentname` | Cloud Services (Rozšířená podpora) | `deploymentname` |  
| Virtual Network | `vnetname` <br><br> `Group resourcegroupname vnetname` <br><br> Nepřidruženo |  Virtual Network (není vytvořen portál) <br><br> Virtual Network (vytvořeno portálem) <br><br> Virtuální sítě (výchozí) | `vnetname` <br><br> `group-resourcegroupname-vnetname` <br><br> `DefaultRdfevirtualnetwork_vnetid`|
| Nepřidruženo | Nepřidruženo | Key Vault | `cloudservicename` | 
| Nepřidruženo | Nepřidruženo | Skupina prostředků pro nasazení cloudových služeb | `cloudservicename-migrated` | 
| Nepřidruženo | Nepřidruženo | Skupina prostředků pro Virtual Network | `vnetname-migrated` <br><br> `group-resourcegroupname-vnetname-migrated`|
| Nepřidruženo | Nepřidruženo | Veřejná IP adresa (dynamická) | `cloudservicenameContractContract` | 
| Název Vyhrazená IP adresa | `reservedipname` | Vyhrazená IP adresa (není vytvořený portál) <br><br> Vyhrazená IP adresa (vytvořeno portálem) | `reservedipname` <br><br> `group-resourcegroupname-reservedipname` | 
| Nepřidruženo| Nepřidruženo | Load Balancer | `deploymentname-lb`|



## <a name="migration-issues-and-how-to-handle-them"></a>Problémy s migrací a jejich zpracování. 

### <a name="migration-stuck-in-an-operation-for-a-long-time"></a>Migrace se zablokuje v operaci po dlouhou dobu. 
- Potvrzení, příprava a přerušení můžou v závislosti na počtu nasazení trvat dlouhou dobu. Po 24 hodinách dojde k vypršení časového limitu operací.   
- Operace potvrzení, přípravy a přerušení jsou idempotentní. Většina problémů se fixable opakuje. Mohlo by dojít k přechodným chybám, které můžou být nepřítomné během několika minut. doporučujeme opakovat pokus za mezerou. Pokud migrujete pomocí Azure Portal a operace se zablokuje v "průběžném stavu", zkuste operaci zopakovat pomocí PowerShellu. 
- Obraťte se na podporu, která vám pomůžou migraci nebo vrácení nasazení zpátky z back-endu. 

### <a name="migration-failed-in-an-operation"></a>V operaci se nepovedlo migrovat. 
- Pokud se ověření nepovedlo, je to proto, že nasazení nebo virtuální síť obsahuje nepodporovaný scénář, funkci nebo prostředek. K vyhledání práce v dokumentech použijte seznam nepodporovaných scénářů.  
- Příprava operace nejprve provede ověření, včetně některých nákladných ověření (nejsou pokryta v Validate). Příprava k selhání může být způsobena nepodporovaným scénářem. Najděte scénář a práci ve veřejných dokumentech. Abyste se mohli vrátit k původnímu stavu a odemknout nasazení pro operace aktualizace a odstranění, je potřeba volat přerušení.
- Pokud se přerušení nepovedlo, zkuste operaci zopakovat. Pokud se opakování nezdaří, obraťte se na podporu.
- Pokud se potvrzení změn nepovedlo, zkuste operaci zopakovat. Pokud se to nepodaří, obraťte se na podporu. I v případě chyby potvrzení by nemělo být k nasazení žádný problém roviny dat. Vaše nasazení by mělo být schopné zpracovávat provoz zákazníků bez jakýchkoli problémů. 

### <a name="portal-refreshed-after-prepare-experience-restarted-and-commit-or-abort-not-visible-anymore"></a>Portál se po přípravě aktualizoval. Způsob opětovného spuštění a potvrzení nebo přerušení již nejsou viditelné. 
- Portál ukládá informace o migraci místně, a proto po aktualizaci spustí z Validate fáze i v případě, že je cloudová služba ve fázi Prepare.  
- Pomocí portálu můžete znovu projít kroky ověřit a připravit a vystavit tak tlačítko pro přerušení a potvrzení. Nezpůsobí se žádné chyby.
- Zákazníci můžou přerušit nebo potvrdit pomocí prostředí PowerShell nebo rozhraní REST API. 

### <a name="how-much-time-can-the-operations-takebr"></a>Jak dlouho můžou operace trvat?<br>
Ověřování je navrženo tak, aby bylo rychlé. Příprava je nejdelší spuštěná a v závislosti na celkovém počtu migrovaných instancí rolí trvá nějakou dobu. Přerušení a potvrzení může také nějakou dobu trvat, ale bude trvat méně času v porovnání s přípravou. Po 24 hodinách budou mít všechny operace časový limit. 
