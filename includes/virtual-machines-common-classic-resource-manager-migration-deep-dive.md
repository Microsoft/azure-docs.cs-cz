---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: tanmaygore
ms.openlocfilehash: 215057640dd08d9ea524d8f6b3bed8b03a8b5b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77068435"
---
## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Migrace prostředků IaaS z klasického modelu nasazení do Správce prostředků Azure
Nejprve je důležité pochopit rozdíl mezi operacemi roviny dat a správy v infrastruktuře jako prostředky služby (IaaS).

* *Rovina správy/řízení* popisuje volání, která přicházejí do roviny správy/řízení nebo rozhraní API pro úpravu prostředků. Například operace jako vytvoření virtuálního počítače, restartování virtuálního počítače a aktualizace virtuální sítě s použitím nové podsítě spravují spuštěné prostředky. Nemají přímý vliv na připojení k virtuálním připojením.
* *Rovina dat* (aplikace) popisuje runtime samotné aplikace a zahrnuje interakci s instancemi, které neprocházejí rozhraní azure api. Například přístup k webu nebo vytažení dat z běžící instance serveru SQL Server nebo serveru MongoDB jsou interakce roviny dat nebo aplikace. Mezi další příklady patří kopírování objektu blob z účtu úložiště a přístup k veřejné IP adrese pro použití protokolu RDP (RdP) nebo zabezpečeného prostředí (SSH) do virtuálního počítače. Tyto operace udržují spuštěnou aplikaci napříč výpočetními prostředky, síťovými službami a úložišti.

Rovina dat je stejná mezi klasickým modelem nasazení a zásobníky Správce prostředků. Rozdíl je v tom, že během procesu migrace společnost Microsoft překládá reprezentaci prostředků z modelu klasického nasazení do modelu v zásobníku Správce prostředků. V důsledku toho je třeba použít nové nástroje, api a sady SDK ke správě prostředků v zásobníku Správce prostředků.

![Diagram, který ukazuje rozdíl mezi rovinou správy/řízení a rovinou dat](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> Platforma Azure v některých scénářích migrace zastaví, uvolní a restartuje vaše virtuální počítače. To způsobí krátké prostoje datové roviny.
>

## <a name="the-migration-experience"></a>Možnosti migrace
Před zahájením migrace:

* Ujistěte se, že prostředky, které chcete migrovat, nepoužívají žádné nepodporované funkce ani konfigurace. Platforma obvykle tyto problémy rozpozná a vygeneruje chybu.
* Pokud máte virtuální počítače, které nejsou ve virtuální síti, jsou zastaveny a vráceny jako součást operace přípravy. Pokud nechcete ztratit veřejnou IP adresu, zvažte rezervaci IP adresy před aktivací operace přípravy. Pokud virtuální počítače jsou ve virtuální síti, nejsou zastaveny a navrácené.
* Naplánujte migraci mimo pracovní dobu, abyste mohli vyřešit případné neočekávané chyby, ke kterým by mohlo při migraci dojít.
* Pro usnadnění ověření po dokončení kroku přípravy si stáhněte aktuální konfiguraci vašich virtuálních počítačů pomocí PowerShellu, příkazů rozhraní příkazového řádku nebo rozhraní REST API.
* Před zahájením migrace aktualizujte skripty automatizace a operationalization tak, aby fungovaly v modelu nasazení Správce prostředků. Volitelně můžete provádět operace načtení, když jsou prostředky v připraveném stavu.
* Vyhodnoťte zásady řízení přístupu na základě rolí (RBAC), které jsou nakonfigurovány na prostředky IaaS v klasickém modelu nasazení a plánujte po dokončení migrace.

Pracovní postup migrace je následující:

![Diagram znázorňující pracovní postup migrace](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Operace popsané v následujících částech jsou idempotentní. Pokud máte jiný problém než nepodporovanou funkci nebo chybu konfigurace, opakujte operaci přípravy, přerušení nebo potvrzení. Azure se pokusí akci znovu.
>
>

### <a name="validate"></a>Ověření
Operace ověření je prvním krokem v procesu migrace. Cílem tohoto kroku je analyzovat stav prostředků, které chcete migrovat v modelu klasického nasazení. Operace vyhodnotí, zda jsou prostředky schopné migrace (úspěch nebo neúspěch).

Vyberete virtuální síť nebo cloudovou službu (pokud není ve virtuální síti), kterou chcete ověřit pro migraci. Pokud prostředek není schopen migrace, Azure uvádí důvody, proč.

#### <a name="checks-not-done-in-the-validate-operation"></a>Kontroly, které nebyly provedeny v operaci ověření.

Operace ověření analyzuje pouze stav prostředků v modelu klasického nasazení. Může zkontrolovat všechny chyby a nepodporované scénáře z důvodu různých konfigurací v modelu klasického nasazení. Není možné zkontrolovat všechny problémy, které zásobník Azure Resource Manager může uložit na prostředky během migrace. Tyto problémy jsou kontrolovány pouze v případě, že prostředky procházejí transformací v dalším kroku migrace (operace přípravy). V následující tabulce jsou uvedeny všechny problémy, které nebyly zaškrtnuty v operaci ověření:


|Síťové kontroly nejsou v operaci ověření.|
|-|
|Virtuální síť s bránami ER i VPN.|
|Připojení brány virtuální sítě v odpojeném stavu.|
|Všechny okruhy ER jsou předem migrovány do zásobníku Azure Resource Manager.|
|Kvóta Azure Resource Manager kontroluje síťové prostředky. Například: statická veřejná IP adresa, dynamické veřejné IP adresy, vyrovnávání zatížení, skupiny zabezpečení sítě, směrovací tabulky a síťová rozhraní. |
| Všechna pravidla nástroje pro vyrovnávání zatížení jsou platná napříč nasazením a virtuální sítí. |
| Konfliktní privátní IP adresy mezi servery s ukončením obchodu ve stejné virtuální síti. |

### <a name="prepare"></a>Příprava
Operace přípravy je druhým krokem v procesu migrace. Cílem tohoto kroku je simulovat transformaci prostředků IaaS z modelu klasického nasazení na prostředky Správce prostředků. Dále operace přípravy představuje tuto souběžnou pro vizualizaci.

> [!NOTE] 
> Vaše prostředky v modelu klasické nasazení nejsou změněny během tohoto kroku. Je to bezpečný krok ke spuštění, pokud zkoušíte migraci. 

Vyberete virtuální síť nebo cloudovou službu (pokud to není virtuální síť), kterou chcete připravit na migraci.

* Pokud prostředek není schopen migrace, Azure zastaví proces migrace a uvádí důvod, proč se nezdařila operace přípravy.
* Pokud prostředek je schopen migrace, Azure uzamkne operace rovina správy pro prostředky v rámci migrace. Například nemůžete do virtuálního počítače v rámci migrace přidat datový disk.

Azure pak spustí migraci metadat z klasického modelu nasazení do Správce prostředků pro migrující prostředky.

Po dokončení operace přípravy máte možnost vizualizovat prostředky v modelu klasického nasazení i ve Správci prostředků. Pro každou cloudovou službu v modelu nasazení Classic vytvoří platforma Azure název skupiny prostředků ve tvaru `cloud-service-name>-Migrated`.

> [!NOTE]
> Není možné vybrat název skupiny prostředků vytvořené pro migrované prostředky (to znamená "-Migrované"). Po dokončení migrace však můžete pomocí funkce přesunutí nástroje Azure Resource Manager přesunout prostředky do libovolné skupiny prostředků, které chcete. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../articles/resource-group-move-resources.md).

Následující dva snímky obrazovky ukazují výsledek po úspěšné operaci přípravy. První z nich zobrazuje skupinu prostředků, která obsahuje původní cloudovou službu. Druhá zobrazuje novou skupinu prostředků "Migrované", která obsahuje ekvivalentní prostředky Azure Resource Manager.

![Snímek obrazovky s původní cloudovou službou](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Snímek obrazovky, který zobrazuje prostředky Azure Resource Manageru v operaci přípravy](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

Zde je pohled do zákulisí vašich zdrojů po dokončení fáze přípravy. Všimněte si, že prostředek v rovině dat je stejný. Je reprezentován v rovině správy (klasický model nasazení) a rovině ovládacího prvku (Resource Manager).

![Schéma fáze přípravy](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Virtuální počítače, které nejsou ve virtuální síti v modelu klasického nasazení, jsou v této fázi migrace zastavené a vyrovnané.
>

### <a name="check-manual-or-scripted"></a>Kontrola (ruční nebo pomocí skriptu)
V kroku kontroly máte možnost použít konfiguraci, kterou jste stáhli dříve, abyste ověřili, že migrace vypadá správně. Případně se můžete přihlásit k portálu a na místě zkontrolovat vlastnosti a prostředky k ověření, že migrace metadat vypadá dobře.

Pokud migrujete virtuální síť, většina konfigurací virtuálních počítačů se nerestartuje. Pro aplikace na těchto virtuálních počítačích můžete ověřit, že aplikace je stále spuštěná.

Můžete otestovat monitorování a provozní skripty, abyste zjistili, jestli virtuální počítače fungují podle očekávání a jestli aktualizované skripty fungují správně. Když jsou prostředky v připraveném stavu, podporují se pouze operace načtení.

Neexistuje žádné nastavené časové okno, před kterým je třeba migraci potvrdit. V tomto stavu máte času, kolik chcete. Pro tyto prostředky je však uzamčena vrstva správy, dokud je nepřerušíte nebo nepotvrdíte.

Pokud nastanou nějaké problémy, vždycky můžete migraci přerušit a vrátit se k modelu nasazení Classic. Po vrácení zpět Azure otevře operace roviny správy na prostředky, takže můžete obnovit normální operace na těchto virtuálních počítačích v modelu klasické nasazení.

### <a name="abort"></a>Přerušení
Toto je volitelný krok, pokud chcete vrátit změny klasického modelu nasazení a zastavit migraci. Tato operace odstraní metadata Správce prostředků (vytvořené v kroku přípravy) pro vaše prostředky. 

![Diagram kroku přerušení](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Tuto operaci nelze provést po spuštění operace potvrzení.     
>

### <a name="commit"></a>Potvrzení
Po dokončení ověření můžete migraci potvrdit. Prostředky se již nezobrazují v klasickém modelu nasazení a jsou k dispozici pouze v modelu nasazení Správce prostředků. Migrované prostředky je možné spravovat pouze na novém portálu.

> [!NOTE]
> Toto je idempotentní operace. Pokud se nezdaří, opakujte operaci. Pokud se stále nezdaří, vytvořte lístek podpory nebo vytvořte fórum na [Microsoft Q&A](https://docs.microsoft.com/answers/index.html)
>
>

![Diagram kroku potvrzení](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Vývojový diagram migrace

Zde je vývojový diagram, který ukazuje, jak postupovat s migrací:

![Snímek obrazovky, který ukazuje kroky migrace](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Překlad klasického modelu nasazení do prostředků Správce prostředků Správce prostředků
Klasický model nasazení a reprezentace prostředků správce prostředků naleznete v následující tabulce. Jiné funkce a prostředky se aktuálně nepodporují.

| Reprezentace v modelu Classic | Reprezentace v modelu Resource Manager | Poznámky |
| --- | --- | --- |
| Název cloudové služby |Název DNS |Během migrace se pro každou cloudovou službu vytvoří nová skupina prostředků s formátem pojmenování `<cloudservicename>-migrated`. Tato skupina prostředků obsahuje všechny vaše prostředky. Název cloudové služby se stane názvem DNS přidruženým k veřejné IP adrese. |
| Virtuální počítač |Virtuální počítač |Vlastnosti specifické pro virtuální počítače se migrují beze změny. Některé informace osProfile, jako je název počítače, nejsou uloženy v klasickém modelu nasazení a po migraci zůstanou prázdné. |
| Prostředky disku připojené k virtuálnímu počítači |Implicitní disky připojené k virtuálnímu počítači |Disky se v modelu nasazení Resource Manager nemodelují jako prostředky nejvyšší úrovně. Migrují se jako implicitní disky v rámci virtuálního počítače. Aktuálně se podporují pouze disky připojené k virtuálnímu počítači. Virtuální počítače Správce prostředků teď můžou používat účty úložiště v klasickém modelu nasazení, což umožňuje snadnou migraci disků bez jakýchkoli aktualizací. |
| Rozšíření virtuálních počítačů |Rozšíření virtuálních počítačů |Z modelu nasazení Classic se migrují všechna rozšíření prostředků s výjimkou rozšíření XML. |
| Certifikáty virtuálních počítačů |Certifikáty v Azure Key Vault |Pokud cloudová služba obsahuje certifikáty služby, migrace vytvoří nový trezor klíčů Azure pro cloudovou službu a přesune certifikáty do trezoru klíčů. Virtuální počítače se aktualizují, aby odkazovaly na certifikáty z trezoru klíčů. <br><br> Neodstraňujte trezor klíčů. To může způsobit, že virtuální ms přejde do stavu selhání. |
| Konfigurace WinRM |Konfigurace WinRM v rámci osProfile |Konfigurace vzdálené správy systému Windows se v rámci migrace přesune beze změny. |
| Vlastnost sady dostupnosti |Prostředek sady dostupnosti | Specifikace sady dostupnosti je vlastnost na virtuálním počítači v modelu klasického nasazení. Skupiny dostupnosti se v rámci migrace stanou prostředkem nejvyšší úrovně. Následující konfigurace se nepodporují: více skupin dostupnosti na cloudovou službu a jedna nebo více skupin dostupnosti společně s virtuálními počítači, které nejsou v žádné skupině dostupnosti v cloudové službě. |
| Konfigurace sítě na virtuálním počítači |Primární síťové rozhraní |Konfiguraci sítě na virtuálním počítači po migraci představuje prostředek primárního síťového rozhraní. U virtuálních počítačů, které nejsou ve virtuální síti, se při migraci změní interní IP adresa. |
| Několik síťových rozhraní na virtuálním počítači |Síťová rozhraní |Pokud má virtuální počítač více síťových rozhraní, které jsou k němu přidruženy, každé síťové rozhraní se stane prostředkem nejvyšší úrovně jako součást migrace spolu se všemi vlastnostmi. |
| Sada koncových bodů s vyrovnáváním zatížení |Nástroj pro vyrovnávání zatížení |V modelu nasazení Classic platforma ke každé cloudové službě přiřadila implicitní nástroj pro vyrovnávání zatížení. Během migrace se vytvoří nový prostředek nástroje pro vyrovnávání zatížení a ze sady koncových bodů s vyrovnáváním zatížení se stanou pravidla nástroje pro vyrovnávání zatížení. |
| Příchozí pravidla NAT |Příchozí pravidla NAT |Vstupní koncové body definované na virtuálním počítači se během migrace převedou na pravidla příchozího překladu adres v rámi nástroje pro vyrovnávání zatížení. |
| Virtuální IP adresa |Veřejná IP adresa s názvem DNS |Virtuální IP adresa se stane veřejnou IP adresou a je přidružena k nástroji pro vyrovnávání zatížení. Virtuální IP adresu je možné migrovat pouze v případě, že k ní je přidružený vstupní koncový bod. |
| Virtuální síť |Virtuální síť |Virtuální síť se migruje se všemi vlastnostmi na model nasazení Resource Manager. Vytvoří se nová skupina prostředků s názvem `-migrated`. |
| Vyhrazené IP adresy |Veřejná IP adresa s metodou statického přidělování |Vyhrazené IP adresy přidružené k nástroji pro vyrovnávání zatížení se migrují společně s migrací cloudové služby nebo virtuálního počítače. Migrace nepřidružených vyhrazených IP adres se aktuálně nepodporuje. |
| Veřejná IP adresa na virtuální počítač |Veřejná IP adresa s metodou dynamického přidělování |Veřejná IP adresa přidružená k virtuálnímu počítači se převede na prostředek veřejné IP adresy s nastavenou statickou metodou přidělování. |
| Skupiny NSG |Skupiny NSG |Skupiny zabezpečení sítě (NSG) přidružené k podsíti se v rámci migrace klonují do modelu nasazení Resource Manager. Skupina NSG v modelu nasazení Classic se během migrace neodebere. Operace se skupinou NSG v rovině správy jsou však v průběhu migrace blokované. |
| Servery DNS |Servery DNS |Servery DNS přidružené k virtuální síti nebo virtuálnímu počítači se migrují v rámci migrace odpovídajícího prostředku, a to společně se všemi vlastnostmi. |
| UDR |UDR |Trasy definované uživatelem (UDR) přidružené k podsíti se v rámci migrace klonují do modelu nasazení Resource Manager. UDR v modelu nasazení Classic se během migrace neodebere. V průběhu migrace jsou blokované operace s UDR v rovině správy. |
| Vlastnost předávání IP v konfiguraci sítě na virtuálním počítači |Vlastnost předávání IP na síťové kartě |Vlastnost předávání IP na virtuálním počítači se během migrace převede na vlastnost na síťových rozhraních. |
| Nástroj pro vyrovnávání zatížení s několika IP adresami |Nástroj pro vyrovnávání zatížení s několika prostředky veřejné IP adresy |Každá veřejná IP adresa přidružená k nástroji pro vyrovnávání zatížení je převedena na veřejný prostředek IP a přidružena k nástroji pro vyrovnávání zatížení po migraci. |
| Interní názvy DNS na virtuálním počítači |Interní názvy DNS na síťové kartě |Během migrace se interní přípony DNS pro virtuální počítače migrují do vlastnosti jen pro čtení s názvem InternalDomainNameSuffix na síťové kartě. Přípona zůstane beze změny po migraci a rozlišení virtuálních míchacích by mělo fungovat jako dříve. |
| Brána virtuální sítě |Brána virtuální sítě |Vlastnosti brány virtuální sítě se migrují beze změny. Virtuální IP adresa přidružená k bráně se také nemění. |
| Místní síťová lokalita |Brána místní sítě |Vlastnosti lokality místní sítě jsou migrovány beze změny do nového prostředku nazývaného brána místní sítě. To představuje předpony místní adresy a IP vzdálené brány. |
| Odkazy na připojení |Připojení |Odkazy na připojení mezi bránou a lokalitou místní sítě v konfiguraci sítě představují nový prostředek s názvem Připojení. Všechny vlastnosti odkazu na připojení v konfiguračních souborech sítě jsou zkopírovány beze změny do prostředku připojení. Připojení mezi virtuálními sítěmi v klasickém modelu nasazení je dosaženo vytvořením dvou tunelových propojení IPsec do lokalit místních sítí představujících virtuální sítě. To se transformuje na typ připojení virtuální sítě k virtuální síti v modelu Resource Manager, aniž by bylo nutné brány místní sítě. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Změny automatizace a nástrojů po migraci
V rámci migrace prostředků z klasického modelu nasazení do modelu nasazení Správce prostředků je nutné aktualizovat stávající automatizaci nebo nástroje, abyste zajistili, že bude i po migraci pokračovat v práci.
