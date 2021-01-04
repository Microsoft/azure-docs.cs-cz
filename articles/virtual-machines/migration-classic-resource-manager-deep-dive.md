---
title: Nástroj pro migraci podporovaný platformou.
description: Technická podrobněá migrace prostředků z modelu nasazení Classic na platformu, která je pro Azure Resource Manager podporovaná
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: tagore
ms.openlocfilehash: c17ade2af751b80e612aa104a9af1a22c4325413
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695740"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager

> [!IMPORTANT]
> V dnešní době se o 90% virtuálních počítačů IaaS používají [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28. února 2020 se klasické virtuální počítače zastaraly a budou plně vyřazeny od 1. března 2023. [Přečtěte si další informace]( https://aka.ms/classicvmretirement) o této zastaralosti a [o tom, jak vás to ovlivní](./classic-vm-deprecation.md#how-does-this-affect-me).

Pojďme se podrobně na migraci z modelu nasazení Azure Classic na model nasazení Azure Resource Manager. Podíváme se na zdroje na úrovni prostředků a funkcí, které vám pomůžou pochopit, jak platforma Azure migruje prostředky mezi těmito dvěma modely nasazení. Další informace najdete v článku oznámení služby: [migrace prostředků IaaS podporovaných platformou z klasický na Azure Resource Manager](migration-classic-resource-manager-overview.md).


## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Migrace prostředků IaaS z modelu nasazení Classic na Azure Resource Manager
Nejdřív je důležité pochopit rozdíl mezi operacemi na úrovni datové roviny a rovinou správy v prostředcích infrastruktury jako služby (IaaS).

* *Rovina správy/řízení* popisuje volání, která přicházejí do roviny pro správu/řízení nebo do rozhraní API pro úpravu prostředků. Například operace jako vytvoření virtuálního počítače, restartování virtuálního počítače a aktualizace virtuální sítě s použitím nové podsítě spravují spuštěné prostředky. Nemají přímý vliv na připojení k virtuálním počítačům.
* *Rovina dat* (aplikace) popisuje modul runtime samotné aplikace a zahrnuje interakci s instancemi, které NEprojde rozhraním API Azure. Například přístup k webu nebo načtení dat z běžící SQL Server instance nebo serveru MongoDB jsou datová rovina nebo interakce aplikací. Mezi další příklady patří kopírování objektu BLOB z účtu úložiště a přístup k veřejné IP adrese pro použití protokol RDP (Remote Desktop Protocol) (RDP) nebo Secure Shell (SSH) do virtuálního počítače. Tyto operace udržují spuštěnou aplikaci napříč výpočetními prostředky, síťovými službami a úložišti.

Rovina dat je stejná mezi modelem nasazení Classic a zásobníky Správce prostředků. Rozdílem je to, že během procesu migrace společnost Microsoft překládá reprezentace prostředků z modelu nasazení Classic na rozhraní Správce prostředků Stack. V důsledku toho je potřeba použít nové nástroje, rozhraní API a sady SDK ke správě prostředků v Správce prostředkůovém zásobníku.

![Diagram, který ukazuje rozdíl mezi rovinou správy/řízení a rovinou dat](./media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> Platforma Azure v některých scénářích migrace zastaví, uvolní a restartuje vaše virtuální počítače. To způsobuje krátké výpadky datové roviny.
>

## <a name="the-migration-experience"></a>Možnosti migrace
Než zahájíte migraci:

* Ujistěte se, že prostředky, které chcete migrovat, nepoužívají žádné nepodporované funkce ani konfigurace. Platforma obvykle tyto problémy rozpozná a vygeneruje chybu.
* Pokud máte virtuální počítače, které nejsou ve virtuální síti, jsou zastaveny a uvolněny v rámci operace přípravy. Pokud nechcete, aby se veřejná IP adresa ztratila, před aktivací operace Prepare zvažte možnost rezervace IP adresy. Pokud jsou virtuální počítače ve virtuální síti, nejsou zastaveny a navráceny.
* Naplánujte migraci mimo pracovní dobu, abyste mohli vyřešit případné neočekávané chyby, ke kterým by mohlo při migraci dojít.
* Pro usnadnění ověření po dokončení kroku přípravy si stáhněte aktuální konfiguraci vašich virtuálních počítačů pomocí PowerShellu, příkazů rozhraní příkazového řádku nebo rozhraní REST API.
* Než začnete s migrací, aktualizujte skripty pro automatizaci a provozní prostředí, aby zpracovávala model nasazení Správce prostředků. Volitelně můžete provádět operace načtení, když jsou prostředky v připraveném stavu.
* Vyhodnoťte zásady řízení přístupu na základě role Azure (Azure RBAC), které jsou nakonfigurované na IaaSch prostředcích v modelu nasazení Classic, a naplánujte po dokončení migrace.

Pracovní postup migrace je následující:

![Diagram znázorňující pracovní postup migrace](./media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Operace popsané v následujících částech jsou všechny idempotentní. Pokud máte jiný problém než Nepodporovaná funkce nebo chybu konfigurace, zkuste operaci příprava, přerušení nebo potvrzení zopakovat. Azure se o akci pokusí znovu.
>
>

### <a name="validate"></a>Ověření
Operace ověření je prvním krokem v procesu migrace. Cílem tohoto kroku je analyzovat stav prostředků, které chcete migrovat v modelu nasazení Classic. Tato operace vyhodnotí, jestli jsou prostředky schopné migrovat (úspěch nebo neúspěch).

Vyberete virtuální síť nebo cloudovou službu (Pokud není ve virtuální síti), kterou chcete ověřit pro migraci. Pokud prostředek není schopný migrovat, Azure vypíše důvody, proč.

#### <a name="checks-not-done-in-the-validate-operation"></a>V operaci ověření se neudělaly kontroly.

Operace ověřování analyzuje pouze stav prostředků v modelu nasazení Classic. Může kontrolovat všechny chyby a nepodporované scénáře z důvodu různých konfigurací v modelu nasazení Classic. Není možné kontrolovat všechny problémy, které může Azure Resource Manager zásobník v průběhu migrace v rámci prostředků ukládat. Tyto problémy se kontrolují jenom v případě, že se prostředky přejdou transformaci v dalším kroku migrace (operace Prepare). V následující tabulce jsou uvedeny všechny problémy, které nejsou v operaci ověření zaškrtnuté:


|Kontroly sítě, které nejsou v operaci ověření|
|-|
|Virtuální síť s bránou ER i VPN.|
|Připojení brány virtuální sítě v odpojeném stavu.|
|Všechny okruhy ER jsou předem migrovány do sady Azure Resource Manager Stack.|
|Azure Resource Manager kontroly kvót pro síťové prostředky. Příklad: statická veřejná IP adresa, dynamické veřejné IP adresy, nástroj pro vyrovnávání zatížení, skupiny zabezpečení sítě, směrovací tabulky a síťová rozhraní. |
| Všechna pravidla nástroje pro vyrovnávání zatížení jsou platná v rámci nasazení a virtuální sítě. |
| Konfliktní privátní IP adresy mezi virtuálními počítači, které jsou ve stejné virtuální síti, se nepřiřazují. |

### <a name="prepare"></a>Příprava
Operace přípravy je druhým krokem v procesu migrace. Cílem tohoto kroku je simulovat transformaci prostředků IaaS z modelu nasazení Classic na prostředky Správce prostředků. Tato operace přípravy prezentuje tuto funkci vedle sebe, abyste ji mohli vizualizovat.

> [!NOTE] 
> Vaše prostředky v modelu nasazení Classic se v průběhu tohoto kroku nezmění. Je to bezpečný krok, který se spustí, pokud se pokoušíte o migraci. 

Vyberete virtuální síť nebo cloudovou službu (pokud to není virtuální síť), kterou chcete připravit na migraci.

* Pokud prostředek není schopen migrace, Azure zastaví proces migrace a uvede důvod, proč se operace přípravy nezdařila.
* Pokud je prostředek schopný migrovat, Azure pro prostředky v rámci migrace zamkne operace roviny správy. Například nemůžete do virtuálního počítače v rámci migrace přidat datový disk.

Azure potom spustí migraci metadat z modelu nasazení Classic na Správce prostředků pro migraci prostředků.

Po dokončení operace přípravy máte možnost vizualizovat prostředky v modelu nasazení Classic i Správce prostředků. Pro každou cloudovou službu v modelu nasazení Classic vytvoří platforma Azure název skupiny prostředků ve tvaru `cloud-service-name>-Migrated`.

> [!NOTE]
> Není možné vybrat název skupiny prostředků vytvořené pro migrované prostředky (tj. "-migrované"). Po dokončení migrace ale můžete použít funkci přesunout Azure Resource Manager k přesunutí prostředků do jakékoli skupiny prostředků, kterou požadujete. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Následující dva snímky obrazovky znázorňují výsledek po úspěšné operaci přípravy. První z nich zobrazuje skupinu prostředků, která obsahuje původní cloudovou službu. Druhá z nich ukazuje novou skupinu prostředků "-migrované", která obsahuje ekvivalentní Azure Resource Manager prostředky.

![Snímek obrazovky, který zobrazuje původní cloudovou službu](./media/migration-classic-resource-manager/portal-classic.png)

![Snímek obrazovky zobrazující Azure Resource Manager prostředky v operaci Prepare](./media/migration-classic-resource-manager/portal-arm.png)

Tady je pohled na pozadí po skončení fáze přípravy. Všimněte si, že zdroj v rovině dat je stejný. Je reprezentován jak v rovině správy (model nasazení Classic), tak i rovině ovládacího prvku (Správce prostředků).

![Diagram fáze přípravy](./media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Virtuální počítače, které nejsou ve virtuální síti v modelu nasazení Classic, se v této fázi migrace zastaví a oddělují.
>

### <a name="check-manual-or-scripted"></a>Kontrola (ruční nebo pomocí skriptu)
V kroku Kontrola máte možnost použít konfiguraci, kterou jste předtím stáhli, abyste ověřili, že migrace vypadá správně. Alternativně se můžete přihlásit k portálu a zkontrolovat vlastnosti a prostředky, abyste ověřili, že migrace metadat vypadá dobře.

Pokud migrujete virtuální síť, většina konfigurací virtuálních počítačů se nerestartuje. Pro aplikace na těchto virtuálních počítačích můžete ověřit, jestli je aplikace pořád spuštěná.

Chcete-li zjistit, zda virtuální počítače pracují podle očekávání a zda aktualizované skripty fungují správně, můžete otestovat své operační a provozní skripty. Když jsou prostředky v připraveném stavu, podporují se pouze operace načtení.

K dispozici není žádné okno nastavení času, než bude nutné provést migraci. V tomto stavu máte času, kolik chcete. Pro tyto prostředky je však uzamčena vrstva správy, dokud je nepřerušíte nebo nepotvrdíte.

Pokud nastanou nějaké problémy, vždycky můžete migraci přerušit a vrátit se k modelu nasazení Classic. Až se vrátíte zpátky, Azure otevře v prostředcích operace správy roviny, aby bylo možné obnovit běžné operace na těchto virtuálních počítačích v modelu nasazení Classic.

### <a name="abort"></a>Přerušení
Tento krok je volitelný, pokud chcete vrátit změny v modelu nasazení Classic a zastavit migraci. Tato operace odstraní metadata Správce prostředků (vytvořená v kroku Příprava) pro vaše prostředky. 

![Diagram kroku přerušení](media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Tuto operaci nelze provést po aktivaci operace potvrzení.     
>

### <a name="commit"></a>Potvrzení
Po dokončení ověření můžete migraci potvrdit. Prostředky se již v modelu nasazení Classic neobjevují a jsou k dispozici pouze v modelu nasazení Správce prostředků. Migrované prostředky je možné spravovat pouze na novém portálu.

> [!NOTE]
> Toto je idempotentní operace. Pokud dojde k chybě, zkuste operaci zopakovat. Pokud se tato chyba bude opakovat, vytvořte lístek podpory nebo vytvořte fórum na [webu Microsoft Q&](/answers/index.html)
>
>

![Diagram kroku potvrzení](media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Vývojový diagram migrace

Tady je vývojový diagram, který ukazuje, jak pokračovat v migraci:

![Snímek obrazovky, který ukazuje kroky migrace](media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Překlad modelu nasazení Classic na prostředky Správce prostředků
Model nasazení Classic a Správce prostředků reprezentace prostředků najdete v následující tabulce. Jiné funkce a prostředky se aktuálně nepodporují.

| Reprezentace v modelu Classic | Reprezentace v modelu Resource Manager | Poznámky |
| --- | --- | --- |
| Název cloudové služby |Název DNS |Během migrace se pro každou cloudovou službu vytvoří nová skupina prostředků s formátem pojmenování `<cloudservicename>-migrated`. Tato skupina prostředků obsahuje všechny vaše prostředky. Název cloudové služby se stane názvem DNS přidruženým k veřejné IP adrese. |
| Virtuální počítač |Virtuální počítač |Vlastnosti specifické pro virtuální počítače se migrují beze změny. Některé osProfile informace, jako je název počítače, se neukládají v modelu nasazení Classic a po migraci zůstávají prázdné. |
| Prostředky disku připojené k virtuálnímu počítači |Implicitní disky připojené k virtuálnímu počítači |Disky se v modelu nasazení Resource Manager nemodelují jako prostředky nejvyšší úrovně. Migrují se jako implicitní disky v rámci virtuálního počítače. Aktuálně se podporují pouze disky připojené k virtuálnímu počítači. Správce prostředků virtuální počítače teď můžou používat účty úložiště v modelu nasazení Classic, což umožňuje snadnou migraci disků bez jakýchkoli aktualizací. |
| Rozšíření virtuálních počítačů |Rozšíření virtuálních počítačů |Z modelu nasazení Classic se migrují všechna rozšíření prostředků s výjimkou rozšíření XML. |
| Certifikáty virtuálních počítačů |Certifikáty v Azure Key Vault |Pokud cloudová služba obsahuje certifikáty služeb, při migraci se vytvoří nový trezor klíčů Azure pro každou cloudovou službu a certifikáty se přesunou do trezoru klíčů. Virtuální počítače se aktualizují, aby odkazovaly na certifikáty z trezoru klíčů. <br><br> Neodstraňujte Trezor klíčů. To může způsobit, že virtuální počítač přejde do stavu selhání. |
| Konfigurace WinRM |Konfigurace WinRM v rámci osProfile |Konfigurace vzdálené správy systému Windows se v rámci migrace přesune beze změny. |
| Vlastnost sady dostupnosti |Prostředek sady dostupnosti | Specifikace sady dostupnosti je vlastnost na virtuálním počítači v modelu nasazení Classic. Skupiny dostupnosti se v rámci migrace stanou prostředkem nejvyšší úrovně. Následující konfigurace se nepodporují: více skupin dostupnosti na cloudovou službu a jedna nebo více skupin dostupnosti společně s virtuálními počítači, které nejsou v žádné skupině dostupnosti v cloudové službě. |
| Konfigurace sítě na virtuálním počítači |Primární síťové rozhraní |Konfiguraci sítě na virtuálním počítači po migraci představuje prostředek primárního síťového rozhraní. U virtuálních počítačů, které nejsou ve virtuální síti, se při migraci změní interní IP adresa. |
| Několik síťových rozhraní na virtuálním počítači |Síťová rozhraní |Pokud je k virtuálnímu počítači přidruženo více síťových rozhraní, každé síťové rozhraní se v rámci migrace stal prostředkem nejvyšší úrovně společně se všemi vlastnostmi. |
| Sada koncových bodů s vyrovnáváním zatížení |Nástroj pro vyrovnávání zatížení |V modelu nasazení Classic platforma ke každé cloudové službě přiřadila implicitní nástroj pro vyrovnávání zatížení. Během migrace se vytvoří nový prostředek nástroje pro vyrovnávání zatížení a ze sady koncových bodů s vyrovnáváním zatížení se stanou pravidla nástroje pro vyrovnávání zatížení. |
| Příchozí pravidla NAT |Příchozí pravidla NAT |Vstupní koncové body definované na virtuálním počítači se během migrace převedou na pravidla příchozího překladu adres v rámi nástroje pro vyrovnávání zatížení. |
| Virtuální IP adresa |Veřejná IP adresa s názvem DNS |Virtuální IP adresa se stala veřejnou IP adresou a je přidružená k nástroji pro vyrovnávání zatížení. Virtuální IP adresu je možné migrovat pouze v případě, že k ní je přidružený vstupní koncový bod. |
| Virtuální síť |Virtuální síť |Virtuální síť se migruje se všemi vlastnostmi na model nasazení Resource Manager. Vytvoří se nová skupina prostředků s názvem `-migrated`. |
| Vyhrazené IP adresy |Veřejná IP adresa s metodou statického přidělování |Vyhrazené IP adresy přidružené k nástroji pro vyrovnávání zatížení se migrují společně s migrací cloudové služby nebo virtuálního počítače. Nepřidružené rezervované IP adresy se dají migrovat pomocí [Move-AzureReservedIP](/powershell/module/servicemanagement/azure.service/move-azurereservedip?view=azuresmps-4.0.0).  |
| Veřejná IP adresa na virtuální počítač |Veřejná IP adresa s metodou dynamického přidělování |Veřejná IP adresa přidružená k virtuálnímu počítači se převede na prostředek veřejné IP adresy s nastavenou statickou metodou přidělování. |
| Skupiny NSG |Skupiny NSG |Skupiny zabezpečení sítě (NSG) přidružené k podsíti se v rámci migrace klonují do modelu nasazení Resource Manager. Skupina NSG v modelu nasazení Classic se během migrace neodebere. Operace se skupinou NSG v rovině správy jsou však v průběhu migrace blokované. Nepřidružená skupin zabezpečení sítě se dají migrovat pomocí [Move-AzureNetworkSecurityGroup](/powershell/module/servicemanagement/azure.service/move-azurenetworksecuritygroup?view=azuresmps-4.0.0).|
| Servery DNS |Servery DNS |Servery DNS přidružené k virtuální síti nebo virtuálnímu počítači se migrují v rámci migrace odpovídajícího prostředku, a to společně se všemi vlastnostmi. |
| UDR |UDR |Trasy definované uživatelem (UDR) přidružené k podsíti se v rámci migrace klonují do modelu nasazení Resource Manager. UDR v modelu nasazení Classic se během migrace neodebere. V průběhu migrace jsou blokované operace s UDR v rovině správy. Nepřidružená udr se dají migrovat pomocí [Move-AzureRouteTable](/powershell/module/servicemanagement/azure.service/Move-AzureRouteTable?view=azuresmps-4.0.0). |
| Vlastnost předávání IP v konfiguraci sítě na virtuálním počítači |Vlastnost předávání IP na síťové kartě |Vlastnost předávání IP na virtuálním počítači se během migrace převede na vlastnost na síťových rozhraních. |
| Nástroj pro vyrovnávání zatížení s několika IP adresami |Nástroj pro vyrovnávání zatížení s několika prostředky veřejné IP adresy |Každá veřejná IP adresa přidružená k nástroji pro vyrovnávání zatížení je po migraci převedena na prostředek veřejné IP adresy a přidružená k nástroji pro vyrovnávání zatížení. |
| Interní názvy DNS na virtuálním počítači |Interní názvy DNS na síťové kartě |Během migrace se interní přípony DNS pro virtuální počítače migrují do vlastnosti jen pro čtení s názvem InternalDomainNameSuffix na síťové kartě. Přípona zůstane po migraci beze změny a řešení virtuálních počítačů by mělo dál fungovat jako dřív. |
| Brána virtuální sítě |Brána virtuální sítě |Vlastnosti brány virtuální sítě se migrují beze změny. Virtuální IP adresa přidružená k bráně se také nemění. |
| Místní síťová lokalita |Brána místní sítě |Vlastnosti místní síťové lokality se migrují beze změny do nového prostředku, který se nazývá Brána místní sítě. To představuje předpony místních adres a IP adresu vzdálené brány. |
| Odkazy na připojení |Připojení |Odkazy na připojení mezi bránou a lokalitou místní sítě v konfiguraci sítě představuje nový prostředek s názvem připojení. Všechny vlastnosti odkazu na připojení v konfiguračních souborech sítě se zkopírují beze změny do prostředku připojení. Připojení mezi virtuálními sítěmi v modelu nasazení Classic se dosahuje vytvořením dvou tunelů IPsec v místních síťových lokalitách, které představují virtuální sítě. To se transformuje na typ připojení typu virtuální síť k virtuální síti v modelu Správce prostředků, aniž by bylo nutné vyžadovat brány místní sítě. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Změny automatizace a nástrojů po migraci
V rámci migrace prostředků z modelu nasazení Classic do modelu nasazení Správce prostředků musíte aktualizovat existující automatizaci nebo nástroje, aby se zajistilo, že po migraci budou i nadále fungovat.


## <a name="next-steps"></a>Další kroky

* [Přehled migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md)
* [Použití PowerShellu k migraci prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí rozhraní příkazového řádku](migration-classic-resource-manager-cli.md)
* [Migrace VPN Gateway Classic na Správce prostředků](../vpn-gateway/vpn-gateway-classic-resource-manager-migration.md)
* [Migrace okruhů ExpressRoute a přidružených virtuálních sítí z modelu nasazení Classic do modelu nasazení Správce prostředků](../expressroute/expressroute-migration-classic-resource-manager.md)
* [Komunitní nástroje pro pomoc s migrací prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md)
* [Přečtěte si nejčastější dotazy týkající se migrace prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-faq.md)
