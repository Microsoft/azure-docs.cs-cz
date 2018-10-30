---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: dc871b29cdafa57d337f9be6cf01e76212f31b67
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227139"
---
## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Migrovat prostředky IaaS z modelu nasazení classic do Azure Resource Manageru
Nejprve je potřeba pochopit rozdíl mezi rovina dat a rovině správy operací na infrastrukturu jako službu (IaaS) prostředky.

* *Rovina správy/řízení* popisuje volání přicházející do roviny správy/řízení nebo rozhraní API za účelem úpravy prostředků. Například operace jako vytvoření virtuálního počítače, restartování virtuálního počítače a aktualizace virtuální sítě s použitím nové podsítě spravují spuštěné prostředky. Nemají přímý vliv na připojení k virtuálním počítačům.
* *Rovina dat* (aplikace) Popisuje modul runtime samotná aplikace a zahrnuje interakce s instancemi, které neprochází přes rozhraní API služby Azure. Přístup k webu nebo načítání dat ze spuštěné instance SQL serveru nebo serveru MongoDB, jsou například data interakce v rovině nebo aplikace. Další příklady: kopírování objektu blob z účtu úložiště a přístup k veřejné IP adresy pro použití protokolu RDP (Remote Desktop) nebo Secure Shell (SSH) k virtuálnímu počítači. Tyto operace udržují spuštěnou aplikaci napříč výpočetními prostředky, síťovými službami a úložišti.

Rovina dat je stejný mezi modelem nasazení classic a Resource Manageru zásobníků. Rozdíl je, že během procesu migrace Microsoft přeloží reprezentaci prostředků z modelu nasazení classic, který v zásobníku správce prostředků. V důsledku toho budete muset použít nové nástroje, rozhraní API a sady SDK ke správě prostředků v zásobníku správce prostředků.

![Diagram, který ukazuje rozdíl mezi rovinou správy/řízení a rovinou dat](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> Platforma Azure v některých scénářích migrace zastaví, uvolní a restartuje vaše virtuální počítače. To způsobuje krátké výpadky roviny.
>

## <a name="the-migration-experience"></a>Možnosti migrace
Před zahájením migrace:

* Ujistěte se, že prostředky, které chcete migrovat, nepoužívají žádné nepodporované funkce ani konfigurace. Platforma obvykle tyto problémy rozpozná a vygeneruje chybu.
* Pokud máte virtuální počítače, které nejsou ve virtuální síti, se zastaví a uvolní jako součást operace přípravy. Pokud už nechcete přijít o veřejnou IP adresu, vezměte v úvahu před aktivací operace přípravy rezervaci IP adresy. Pokud jsou virtuální počítače ve virtuální síti, jejich nejsou zastaví a uvolní.
* Naplánujte migraci mimo pracovní dobu, abyste mohli vyřešit případné neočekávané chyby, ke kterým by mohlo při migraci dojít.
* Pro usnadnění ověření po dokončení kroku přípravy si stáhněte aktuální konfiguraci vašich virtuálních počítačů pomocí PowerShellu, příkazů rozhraní příkazového řádku nebo rozhraní REST API.
* Aktualizujte skripty automatizace a operacionalizaci ke zpracování modelu nasazení Resource Manager, před zahájením migrace. Volitelně můžete provádět operace načtení, když jsou prostředky v připraveném stavu.
* Vyhodnoťte zásady řízení přístupu na základě Role (RBAC), které jsou nakonfigurované pro prostředky IaaS v modelu nasazení classic a naplánujte po dokončení migrace.

Pracovní postup migrace je následující:

![Diagram znázorňující pracovní postup migrace](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> Operace popsané v následujících částech jsou všechny idempotentní. Pokud máte jiný problém, než je nepodporovaná funkce nebo Chyba konfigurace, zkuste přípravy, přerušení nebo potvrzení operace. Azure se pokusí akci provést znovu.
>
>

### <a name="validate"></a>Ověření
Operace ověření je prvním krokem v procesu migrace. Cílem tohoto kroku je analyzovat stav prostředky, které chcete migrovat v modelu nasazení classic. Operace vyhodnotí, jestli jsou prostředky schopné migrace (úspěch nebo neúspěch).

Vyberte virtuální síť nebo cloudovou službu (Pokud není ve virtuální síti), který chcete ověřit pro migraci. Pokud prostředek není schopen migrace, uvádí Azure proč důvody.

#### <a name="checks-not-done-in-the-validate-operation"></a>Neprovedeno v operace ověření kontroly

Operace ověření pouze analyzuje stav prostředků v modelu nasazení classic. Můžete vyhledat všechny chyby a nepodporované scénáře z důvodu různých konfiguracích v modelu nasazení classic. Není možné zkontrolovat všechny problémy, které zásobníku Azure Resource Manageru může uložit prostředky během migrace. Tyto problémy jsou kontrolována pouze při prostředky projít transformace v dalším kroku migrace (operace přípravy). V následující tabulce jsou uvedeny všechny problémy není zaregistrováno operace ověření:


|Kontroly sítě není v operace ověření|
|-|
|Virtuální síť s ER a VPN Gateway.|
|Připojení brány virtuální sítě v odpojeném stavu.|
|Všechny okruhy ER předem migrují do zásobníku správce prostředků Azure.|
|Kvóta Azure Resource Manageru zjišťuje síťové prostředky. Příklad: statické veřejné IP adresy, dynamické veřejné IP adresy, načítání nástroje pro vyrovnávání, skupiny zabezpečení sítě, směrovacích tabulek a síťových rozhraní. |
| Všechna pravidla nástroje pro vyrovnávání zatížení jsou platné v rámci nasazení a virtuální sítě. |
| Konfliktní privátních IP adres mezi zastavit a uvolnit virtuálními počítači ve stejné virtuální síti. |

### <a name="prepare"></a>Příprava
Operace přípravy je druhým krokem v procesu migrace. Cílem tohoto kroku je simulovat transformaci prostředků IaaS z modelu nasazení classic na prostředky Resource Manageru. Operace přípravy dále, představuje tento-souběžně umožní vizualizovat.

> [!NOTE] 
> Během tohoto kroku se nezmění vaše prostředky v modelu nasazení classic. Je bezpečné kroku ke spouštění, pokud se snažíte na migraci. 

Vyberete virtuální sítě nebo cloudové služby (pokud to není virtuální síť), že chcete připravit na migraci.

* Pokud prostředek není schopen migrace, Azure zastaví proces migrace a uvede důvody, proč operace přípravy selhala.
* Pokud prostředek je schopen migrace, Azure uzamkne operace roviny správy pro prostředky v rámci migrace. Například nemůžete do virtuálního počítače v rámci migrace přidat datový disk.

Azure pak spustí migraci metadat z modelu nasazení classic do Resource Manageru pro migrované prostředky.

Po dokončení operace přípravy máte možnost vizualizace prostředků v modelu nasazení classic a Resource Manageru. Pro každou cloudovou službu v modelu nasazení Classic vytvoří platforma Azure název skupiny prostředků ve tvaru `cloud-service-name>-Migrated`.

> [!NOTE]
> Není možné vybrat název skupiny prostředků vytvořené pro migrované prostředky (tedy "-migrovat"). Po dokončení migrace však můžete přesunout funkce Azure Resource Manageru k přesunutí prostředků do libovolné skupiny prostředků, které chcete. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../articles/resource-group-move-resources.md).

Na následujících dvou snímcích obrazovky ukazují výsledek po úspěšném operace přípravy. První z nich ukazuje skupinu prostředků, která obsahuje původní cloudovou službu. Je druhý řádek ukazuje novou "-migrovat" skupiny prostředků, která obsahuje ekvivalentní prostředky Azure Resource Manageru.

![Snímek obrazovky zobrazující původní cloudovou službu](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Snímek obrazovky s prostředky Azure Resource Manageru v operace přípravy](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

Po dokončení fáze prepare následuje pozadí pohled na vaše prostředky. Všimněte si, že prostředků v rovině dat je stejný. Je zastoupena v rovině správy (model nasazení classic) a rovina řízení (Resource Manager).

![Diagram fáze prepare](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Virtuální počítače, které nejsou ve virtuální síti v modelu nasazení classic se zastaví a uvolní v této fázi migrace.
>

### <a name="check-manual-or-scripted"></a>Kontrola (ruční nebo pomocí skriptu)
V kroku zkontrolujte máte možnost použít konfigurace, který jste předtím stáhli k ověření, že migrace vypadá v pořádku. Alternativně můžete přihlásit k portálu a na místě zkontrolovat vlastnosti a prostředky, abyste ověřili, že migrace metadat vypadá dobře.

Pokud migrujete virtuální síť, většina konfigurací virtuálních počítačů se nerestartuje. U aplikací na těchto virtuálních počítačů můžete ověřit, že aplikace pořád běží.

Můžete otestovat skripty monitorování a provozní virtuální počítače fungují podle očekávání a aktualizované skripty fungovat správně. Když jsou prostředky v připraveném stavu, podporují se pouze operace načtení.

Není k dispozici žádná sada okno dobu, před kterou je potřeba provést migraci. V tomto stavu máte času, kolik chcete. Pro tyto prostředky je však uzamčena vrstva správy, dokud je nepřerušíte nebo nepotvrdíte.

Pokud nastanou nějaké problémy, vždycky můžete migraci přerušit a vrátit se k modelu nasazení Classic. Po vrácení, Azure otevře roviny správy operací s prostředky, tak, aby mohli obnovit normální provoz na těchto virtuálních počítačích v modelu nasazení classic.

### <a name="abort"></a>Přerušení
Toto je volitelný krok, pokud chcete vrátit změny k modelu nasazení classic a zastavit migraci. Tato operace odstraní metadat Resource Manageru (vytvořený v kroku přípravy) pro vaše prostředky. 

![Diagram přerušení kroku](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Tuto operaci nelze provést po aktivaci operace potvrzení.     
>

### <a name="commit"></a>Potvrzení
Po dokončení ověření můžete migraci potvrdit. Prostředky se už nebudou zobrazovat v modelu nasazení classic a jsou k dispozici pouze v modelu nasazení Resource Manager. Migrované prostředky je možné spravovat pouze na novém portálu.

> [!NOTE]
> Toto je idempotentní operace. Pokud selže, zkuste operaci zopakovat. Pokud bude nadále nezdaří, vytvořte lístek podpory nebo vytvořte příspěvek se "ClassicIaaSMigration" značky našich [fóru k virtuálním počítačům](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).
>
>

![Diagram potvrzení kroku](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Vývojový diagram migrace

Tady je Vývojový diagram, který ukazuje, jak pokračovat v migraci:

![Snímek obrazovky, který ukazuje kroky migrace](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Překlad modelu nasazení classic na prostředky Resource Manageru
Model nasazení classic a Resource Manageru reprezentaci prostředků najdete v následující tabulce. Jiné funkce a prostředky se aktuálně nepodporují.

| Reprezentace v modelu Classic | Reprezentace v modelu Resource Manager | Poznámky |
| --- | --- | --- |
| Název cloudové služby |Název DNS |Během migrace se pro každou cloudovou službu vytvoří nová skupina prostředků s formátem pojmenování `<cloudservicename>-migrated`. Tato skupina prostředků obsahuje všechny vaše prostředky. Název cloudové služby se stane názvem DNS přidruženým k veřejné IP adrese. |
| Virtuální počítač |Virtuální počítač |Vlastnosti specifické pro virtuální počítače se migrují beze změny. Určité informace osProfile, jako je název počítače, není uložen v modelu nasazení classic a zůstávají prázdné i po migraci. |
| Prostředky disku připojené k virtuálnímu počítači |Implicitní disky připojené k virtuálnímu počítači |Disky se v modelu nasazení Resource Manager nemodelují jako prostředky nejvyšší úrovně. Migrují se jako implicitní disky v rámci virtuálního počítače. Aktuálně se podporují pouze disky připojené k virtuálnímu počítači. Virtuální počítače Resource Manageru teď můžou používat účty úložiště v modelu nasazení classic, což umožní disky snadnou migraci bez potřeby jakýchkoli aktualizací. |
| Rozšíření virtuálních počítačů |Rozšíření virtuálních počítačů |Z modelu nasazení Classic se migrují všechna rozšíření prostředků s výjimkou rozšíření XML. |
| Certifikáty virtuálních počítačů |Certifikáty v Azure Key Vault |Pokud je Cloudová služba obsahuje certifikáty služby, migraci vytvoří Azure nový trezor klíčů za cloudové služby a certifikáty se přesune do služby key vault. Virtuální počítače se aktualizují, aby odkazovaly na certifikáty z trezoru klíčů. <br><br> Odstranění trezoru klíčů. To může způsobit, že virtuální počítač přejde do stavu selhání. |
| Konfigurace WinRM |Konfigurace WinRM v rámci osProfile |Konfigurace vzdálené správy systému Windows se v rámci migrace přesune beze změny. |
| Vlastnost sady dostupnosti |Prostředek sady dostupnosti | Specifikace sady dostupnosti je vlastnost na virtuálním počítači v modelu nasazení classic. Skupiny dostupnosti se v rámci migrace stanou prostředkem nejvyšší úrovně. Následující konfigurace se nepodporují: více skupin dostupnosti na cloudovou službu a jedna nebo více skupin dostupnosti společně s virtuálními počítači, které nejsou v žádné skupině dostupnosti v cloudové službě. |
| Konfigurace sítě na virtuálním počítači |Primární síťové rozhraní |Konfiguraci sítě na virtuálním počítači po migraci představuje prostředek primárního síťového rozhraní. U virtuálních počítačů, které nejsou ve virtuální síti, se při migraci změní interní IP adresa. |
| Několik síťových rozhraní na virtuálním počítači |Síťová rozhraní |Pokud má virtuální počítač několik síťových rozhraní, které s ním spojená, každé síťové rozhraní se stane prostředek nejvyšší úrovně jako součást migrace, spolu se všemi vlastnostmi. |
| Sada koncových bodů s vyrovnáváním zatížení |Nástroj pro vyrovnávání zatížení |V modelu nasazení Classic platforma ke každé cloudové službě přiřadila implicitní nástroj pro vyrovnávání zatížení. Během migrace se vytvoří nový prostředek nástroje pro vyrovnávání zatížení a ze sady koncových bodů s vyrovnáváním zatížení se stanou pravidla nástroje pro vyrovnávání zatížení. |
| Příchozí pravidla NAT |Příchozí pravidla NAT |Vstupní koncové body definované na virtuálním počítači se během migrace převedou na pravidla příchozího překladu adres v rámi nástroje pro vyrovnávání zatížení. |
| Virtuální IP adresa |Veřejná IP adresa s názvem DNS |Virtuální IP adresa se stane veřejná IP adresa a souvisí s nástrojem pro vyrovnávání zatížení. Virtuální IP adresu je možné migrovat pouze v případě, že k ní je přidružený vstupní koncový bod. |
| Virtuální síť |Virtuální síť |Virtuální síť se migruje se všemi vlastnostmi na model nasazení Resource Manager. Vytvoří se nová skupina prostředků s názvem `-migrated`. |
| Vyhrazené IP adresy |Veřejná IP adresa s metodou statického přidělování |Vyhrazené IP adresy přidružené k nástroji pro vyrovnávání zatížení se migrují společně s migrací cloudové služby nebo virtuálního počítače. Migrace nepřidružených vyhrazených IP adres se aktuálně nepodporuje. |
| Veřejná IP adresa na virtuální počítač |Veřejná IP adresa s metodou dynamického přidělování |Veřejná IP adresa přidružená k virtuálnímu počítači se převede na prostředek veřejné IP adresy s nastavenou statickou metodou přidělování. |
| Skupiny NSG |Skupiny NSG |Skupiny zabezpečení sítě (NSG) přidružené k podsíti se v rámci migrace klonují do modelu nasazení Resource Manager. Skupina NSG v modelu nasazení Classic se během migrace neodebere. Operace se skupinou NSG v rovině správy jsou však v průběhu migrace blokované. |
| Servery DNS |Servery DNS |Servery DNS přidružené k virtuální síti nebo virtuálnímu počítači se migrují v rámci migrace odpovídajícího prostředku, a to společně se všemi vlastnostmi. |
| UDR |UDR |Trasy definované uživatelem (UDR) přidružené k podsíti se v rámci migrace klonují do modelu nasazení Resource Manager. UDR v modelu nasazení Classic se během migrace neodebere. V průběhu migrace jsou blokované operace s UDR v rovině správy. |
| Vlastnost předávání IP v konfiguraci sítě na virtuálním počítači |Vlastnost předávání IP na síťové kartě |Vlastnost předávání IP na virtuálním počítači se během migrace převede na vlastnost na síťových rozhraních. |
| Nástroj pro vyrovnávání zatížení s několika IP adresami |Nástroj pro vyrovnávání zatížení s několika prostředky veřejné IP adresy |Každá veřejná IP adresa spojená s nástrojem pro vyrovnávání zatížení je převést na prostředek veřejné IP adresy a související s nástrojem pro vyrovnávání zatížení po migraci. |
| Interní názvy DNS na virtuálním počítači |Interní názvy DNS na síťové kartě |Během migrace se interní přípony DNS pro virtuální počítače migrují do vlastnosti jen pro čtení s názvem InternalDomainNameSuffix na síťové kartě. Po migraci zůstane beze změny příponu a překlad názvů Virtuálních by ml fungovat stejně jako předtím. |
| Brána virtuální sítě |Brána virtuální sítě |Vlastnosti brány virtuální sítě se migrují beze změny. Virtuální IP adresa přidružená k bráně se také nemění. |
| Místní síťová lokalita |Brána místní sítě |Vlastnosti místní síťové lokality se migrují beze změny do nového prostředku označovaného jako brána místní sítě. Představuje předpony místních adres a IP adresu vzdálené brány. |
| Odkazy na připojení |Připojení |Odkazy na možnosti připojení mezi bránou a místní síťovou lokalitou v konfiguraci sítě je reprezentován nového prostředku označovaného jako připojení. Všechny vlastnosti odkazu na možnosti připojení v konfiguračních souborech sítě se beze změny zkopírují do prostředku připojení. Propojení mezi virtuálními sítěmi v modelu nasazení classic se dosahuje vytvořením dvou tunelů IPsec k místním síťovým lokalitám, které představují virtuální sítě. Se transformuje na typ sítě na virtuální-připojení virtuální sítě v modelu Resource Manager, bez nutnosti místní síťové brány. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Změny automatizace a nástrojů po migraci
Jako součást migrace prostředků z modelu nasazení classic do modelu nasazení Resource Manageru je nutné aktualizovat stávající automatizaci nebo nástroje, které Ujistěte se, že i nadále fungovat po migraci.
