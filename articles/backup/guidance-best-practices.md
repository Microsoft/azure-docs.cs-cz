---
title: Doprovodné materiály a osvědčené postupy
description: Seznamte se s osvědčenými postupy a pokyny pro zálohování cloudových a místních úloh do cloudu.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 0b3f70061be4d158ae717a97779d6ab0445f5858
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593347"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>Zálohování cloudových a místních úloh do cloudu

## <a name="introduction"></a>Úvod

Azure Backup komplexní ochranu vašich datových assetů v Azure prostřednictvím jednoduchého, zabezpečeného a nákladově efektivního řešení, které vyžaduje nulovou infrastrukturu. Je to Azure's integrované řešení ochrany dat pro řadu úloh. Pomáhá chránit nejdůležitější úlohy, které běží v cloudu, a zajišťuje, aby vaše zálohy byly vždycky dostupné a spravované ve velkém měřítku napříč celou záloze.

### <a name="intended-audience"></a>Zamýšlená cílová skupina

Hlavní cílovou skupinou pro tento článek je IT a správci aplikací a implementátori velkých a středně velkých organizací, kteří se chtějí dozvědět o schopnostech integrované technologie ochrany dat v Azure Azure Backup a jak efektivně implementovat řešení, která lépe chrání vaše nasazení. Článek předpokládá, že jste obeznámeni se základními technologiemi Azure, koncepcemi ochrany dat a zkušenostmi s řešením zálohování. Pokyny popsané v tomto článku vám můžou usnadnit návrh řešení zálohování v Azure s využitím zavedených vzorů a vyhněte se známým nástrah.

### <a name="how-this-article-is-organized"></a>Způsob uspořádání tohoto článku

I když je snadné začít chránit infrastrukturu a aplikace v Azure, když zabezpečíte správné nastavení základních prostředků Azure a použijete optimální využití, můžete svůj čas zrychlit na hodnotu. Tento článek se zabývá stručným přehledem o požadavcích na návrh a pokyny pro optimální konfiguraci Azure Backup nasazení. Prověřuje základní komponenty (například Recovery Services trezor, zásady zálohování) a koncepty (například zásady správného řízení) a způsob jejich povýšení s odkazy na podrobnou dokumentaci k produktu.

## <a name="architecture"></a>Architektura

![Architektura služby Azure Backup](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>Úlohy

Azure Backup umožňuje ochranu dat pro různé úlohy (místně i v cloudu). Jedná se o zabezpečený a spolehlivý vestavěný mechanismus ochrany dat v Azure. Může hladce škálovat svou ochranu napříč více úlohami bez jakýchkoli režijních nákladů na správu. K dispozici je také více kanálů pro automatizaci (prostřednictvím PowerShellu, CLI, Azure Resource Manager šablon a rozhraní REST API).

* **Škálovatelné, odolné a zabezpečené úložiště –** Azure Backup používá spolehlivé úložiště objektů BLOB s funkcemi pro zajištění vysoké dostupnosti a vysokou dostupností. Pro zálohovaná data můžete zvolit úložiště LRS, GRS nebo RA-GRS.  

* **Integrace nativních úloh –** Azure Backup poskytuje nativní integraci s úlohami Azure (virtuální počítače, SAP HANA, SQL ve virtuálních počítačích Azure a dokonce i soubory Azure), aniž byste museli spravovat agenty a infrastrukturu, psát nové skripty nebo zřizovat úložiště.

### <a name="data-plane"></a>Rovina dat

* **Automatizovaná správa úložiště** – Azure Backup automatizuje zřizování a správu účtů úložiště pro zálohovaná data, aby se zajistilo, že se budou škálovat při zvětšování dat zálohování.

* **Škodlivá ochrana proti odstranění –** Chraňte proti náhodným a zlomyslným pokusům o odstranění záloh pomocí obnovitelného odstranění záloh. Odstraněná data zálohy se ukládají 14 dnů zdarma a umožňují obnovení z tohoto stavu.

* **Zabezpečené šifrované zálohy –** Azure Backup zajistí bezpečné uložení zálohovaných dat, a to s využitím integrovaných funkcí zabezpečení platformy Azure, jako je Azure RBAC a šifrování.

* **Správa životního cyklu zálohovaných dat –** Azure Backup automaticky vyčistí starší zálohovaná data, aby splňovala zásady uchovávání informací. Data z provozního úložiště můžete také navrstvit do úložiště trezoru.

### <a name="management-plane"></a>Rovina správy

* **Řízení přístupu** – trezory (Recovery Services a trezory služby Backup) poskytují možnosti správy a jsou přístupné prostřednictvím Azure Portal, centra zálohování, řídicích panelů trezoru, sady SDK, CLI a dokonce rozhraní REST API. Je to také hranice Azure RBAC a poskytuje vám možnost omezit přístup k zálohám jenom na autorizované správce zálohování.

* **Správa zásad** – zásady Azure Backup v rámci jednotlivých úložišť definují, kdy se mají spustit zálohy a jak dlouho je potřeba uchovat. Tyto zásady můžete také spravovat a použít je v několika položkách.

* **Monitorování a vytváření sestav** – Azure Backup se integruje s Log Analytics a poskytuje možnost zobrazovat sestavy i prostřednictvím sešitů.

* **Správa snímků** – Azure Backup pořizuje snímky pro některé nativní úlohy Azure (virtuální počítače a soubory Azure), tyto snímky spravuje a umožňuje rychlé obnovení z nich. Tato možnost výrazně zkracuje čas na obnovení dat do původního úložiště.

## <a name="vault-considerations"></a>Požadavky na úložiště

Azure Backup používá trezory (Recovery Services a trezory služby Backup) k orchestraci a správě záloh. Používá taky trezory k ukládání zálohovaných dat. Efektivní návrh trezoru pomáhá organizacím vytvořit strukturu pro uspořádání a správu zálohovacích prostředků v Azure za účelem podpory vašich obchodních priorit. Při vytváření trezoru Vezměte v úvahu následující pokyny:  

### <a name="align-to-subscription-design-strategy"></a>Zarovnat k strategii návrhu předplatného

Vzhledem k tomu, že trezor je vymezený pro předplatné, můžete přizpůsobit návrh trezoru tak, aby splňoval strategii návrhu předplatného, jako je například *strategie kategorií aplikace* , kde jsou předplatná oddělená na základě konkrétních aplikací nebo služeb nebo na řádcích aplikace archetypes. Další informace najdete v tomto [článku](/azure/cloud-adoption-framework/decision-guides/subscriptions/).

### <a name="single-or-multiple-vault"></a>Jeden nebo více trezorů

K uspořádání a správě zálohování můžete použít jeden trezor nebo více trezorů. Zvažte následující pokyny:

* Pokud jsou vaše úlohy všechny spravované jedním předplatným a jediným prostředkem, můžete k monitorování a správě služby Backup použít jeden trezor.

* Pokud jsou vaše úlohy rozloženy mezi předplatnými, můžete vytvořit více trezorů, jeden nebo více pro každé předplatné.
  * Centrum zálohování umožňuje mít jediné podokno skla, ve kterém můžete spravovat všechny úlohy týkající se zálohování. [Další informace najdete tady]().
  * Můžete přizpůsobit zobrazení pomocí šablon sešitu. Průzkumník zálohování je jedna z těchto šablon pro virtuální počítače Azure. [Další informace najdete tady](monitor-azure-backup-with-backup-explorer.md).
  * Pokud jste potřebovali konzistentní zásady napříč trezory, můžete pomocí zásad Azure rozšířit zásady zálohování napříč několika trezory. Můžete napsat vlastní [definici Azure Policy](../governance/policy/concepts/definition-structure.md) , která pomocí efektu ["deployifnotexists"](../governance/policy/concepts/effects.md#deployifnotexists) rozšíří zásady zálohování mezi více trezorů. Tuto definici Azure Policy můžete [přiřadit](../governance/policy/assign-policy-portal.md) taky konkrétnímu oboru (předplatnému nebo RG), aby nasadila prostředek zásady zálohování do všech trezorů Recovery Services v oboru přiřazení Azure Policy. Nastavení zásad zálohování (například četnost zálohování, uchovávání atd.) by mělo být zadáno uživatelem jako parametry v přiřazení Azure Policy.

* Jak roste vaše organizační nároky, možná budete chtít přesunout úlohy mezi předplatnými z následujících důvodů: zarovnání podle zásad zálohování, konsolidace trezorů, kompromisů při nižší redundanci za účelem úspory nákladů (přesunout z GRS do LRS).  Azure Backup podporuje přesun trezoru Recovery Services napříč předplatnými Azure nebo do jiné skupiny prostředků v rámci stejného předplatného. [Další informace najdete tady](backup-azure-move-recovery-services-vault.md).

### <a name="review-default-settings"></a>Kontrola výchozích nastavení

Před konfigurací záloh v trezoru zkontrolujte výchozí nastavení typu replikace úložiště a nastavení zabezpečení, aby splňovalo vaše požadavky.

* *Typ replikace úložiště* je ve výchozím nastavení nastaven na geograficky redundantní (GRS). Po nakonfigurování zálohy bude možnost úprav zakázána. Chcete-li zkontrolovat a upravit nastavení, postupujte podle [těchto](backup-create-rs-vault.md#set-storage-redundancy) kroků.

* *Obnovitelné odstranění* je ve výchozím nastavení povolené u nově vytvořených trezorů za účelem ochrany zálohovaných dat před náhodnými nebo škodlivými odstraněními. Chcete-li zkontrolovat a upravit nastavení, postupujte podle [těchto](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) kroků.

* *Obnovení mezi oblastmi* umožňuje obnovení virtuálních počítačů Azure v sekundární oblasti, která je spárována s Azure. Tato možnost umožňuje postupovat podle požadavků na audit nebo dodržování předpisů a obnovovat virtuální počítač nebo jeho disk, pokud dojde k havárii v primární oblasti. CRR je funkce výslovného souhlasu pro libovolný trezor GRS. [Další informace najdete tady](backup-create-rs-vault.md#set-cross-region-restore).

* Před dokončením návrhu trezoru si přečtěte [matice podpory trezoru](backup-support-matrix.md#vault-support) , abyste porozuměli faktorům, které by mohly ovlivnit nebo omezovat možnosti návrhu.

## <a name="backup-policy-considerations"></a>Pokyny k zásadám zálohování

Zásady Azure Backup mají dvě komponenty: *plán* (kdy se má provést zálohování) a *uchování* (doba uchování zálohy). Můžete definovat zásady na základě typu zálohovaných dat, požadavků na RTO/RPO, požadavků na operační nebo legislativní dodržování předpisů a typu úlohy (například virtuální počítač, databáze, soubory). [Další informace najdete tady](backup-architecture.md#backup-policy-essentials).

Při vytváření zásad zálohování Vezměte v úvahu následující pokyny:

### <a name="schedule-considerations"></a>Důvody plánu

* Zvažte seskupení virtuálních počítačů, které vyžadují stejný čas zahájení, četnost a nastavení uchovávání v rámci jedné zásady.

* Zajistěte, aby čas zahájení zálohování byl během provozní aplikace mimo špičku.

* Pro distribuci přenosů záloh zvažte zálohování různých virtuálních počítačů v různou denní dobu a ujistěte se, že se časy nepřekrývají.

### <a name="retention-considerations"></a>Požadavky na uchovávání

* Krátkodobé uchovávání může být "minuty" nebo "den". Doba uchovávání "týdně", "měsíčního" nebo "ročního" bodu zálohování se označuje jako dlouhodobá doba uchovávání.

* Dlouhodobé uchovávání:
  * Plánováno (požadavky na dodržování předpisů) – Pokud víte předem, že data jsou od aktuálního času vyžadována za roky, pak použijte dlouhodobou dobu uchovávání.
  * Neplánované (požadavek na vyžádání) – Pokud si nejste jistí předem, můžete použít na vyžádání s konkrétním vlastním nastavením uchovávání informací (Tato vlastní nastavení uchování ovlivněná nastaveními zásad).

* Zálohování na vyžádání s vlastním uchováváním – Pokud potřebujete provést zálohování, které není naplánované prostřednictvím zásad zálohování, můžete použít zálohování na vyžádání. To může být užitečné při pořizování záloh, které nevyhovují plánovanému zálohování, nebo pro provádění podrobného zálohování (například několik záloh virtuálních počítačů IaaS za den, protože plánované zálohování povoluje jenom jednu zálohu za den). Je důležité si uvědomit, že zásady uchovávání informací definované v naplánovaných zásadách se nevztahují na zálohování na vyžádání.

### <a name="optimize-backup-policy"></a>Optimalizace zásad zálohování

* Jak se vaše obchodní požadavky změní, možná budete muset zvětšit nebo zmenšit dobu uchování. V takovém případě můžete očekávat následující:  
  * Pokud je uchovávání dat rozšířené, existující body obnovení jsou označeny a udržovány v souladu s novou zásadou.
  * Pokud je uchovávání dat sníženo, body obnovení jsou označeny pro vyřazení v následující úloze čištění a následně odstraněny.
  * Nejnovější pravidla uchovávání platí pro všechny body uchovávání (kromě bodů uchovávání na vyžádání). Takže pokud se doba uchovávání prodlouží (například na 100 dní), pak při vynechání zálohy (například ze 100 dnů na sedm dní) se všechna zálohovaná data uchovávají v závislosti na poslední zadané době uchování (tj. 7 dní).

* Azure Backup poskytuje flexibilitu při *zastavování ochrany a správy záloh*:
  * *Zastavte ochranu a zachovejte data záloh*. Pokud vyřadíte nebo vyřadíte z provozu zdroj dat (virtuální počítač, aplikaci), ale potřebujete zachovat data pro účely auditu nebo dodržování předpisů, můžete tuto možnost použít, chcete-li zastavit všechny budoucí úlohy zálohování z ochrany zdroje dat a zachovat zálohované body obnovení. Ochranu virtuálního počítače pak můžete obnovit nebo obnovit.
  * *Zastavte ochranu a odstraňte zálohovaná data*. Tato možnost zastaví všechny budoucí úlohy zálohování z ochrany virtuálního počítače a odstraní všechny body obnovení. Nebudete moct obnovit virtuální počítač ani použít možnost obnovit zálohování.

  * Pokud obnovíte ochranu (ze zdroje dat, který byl zastaven s uchováním dat), budou pravidla uchovávání platit. Všechny body obnovení s vypršenou platností budou odebrány (v naplánovaném čase).

* Před dokončením návrhu zásad je důležité mít na paměti následující faktory, které by mohly mít vliv na možnosti návrhu.
  * Zásada zálohování je vymezená na trezor.
  * Existuje omezení počtu položek na zásadu (například 100 virtuálních počítačů). Chcete-li škálovat, můžete vytvořit duplicitní zásady se stejnými nebo různými plány.
  * Nelze selektivně odstranit konkrétní body obnovení.
  * Naplánované zálohování nemůžete zcela zakázat a zachovat zdroj dat v chráněném stavu. Minimální častá záloha, kterou můžete pomocí zásad nakonfigurovat, je mít jedno týdenní naplánované zálohování. Alternativou je zastavení ochrany při zachování dat a povolení ochrany pokaždé, když budete chtít provést zálohování, provést zálohování na vyžádání a pak vypnout ochranu, ale zachovejte data záloh. [Další informace najdete tady](backup-azure-manage-vms.md#stop-protecting-a-vm).

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Abychom vám pomohli chránit vaše Zálohovaná data a plnit požadavky na zabezpečení vaší firmy, Azure Backup poskytuje záruku, integritu a zajištění dostupnosti proti úmyslnému útoku a zneužití vašich cenných dat a systémů. Vezměte v úvahu následující pokyny pro zabezpečení Azure Backup řešení:

### <a name="authentication-and-authorization"></a>Ověřování a autorizace

* Řízení přístupu na základě role v Azure (Azure RBAC) umožňuje jemně odstupňovanou správu přístupu, oddělení funkcí v rámci týmu a udělení přístupu k uživatelům potřebným k provádění svých úloh. [Další informace najdete tady](backup-rbac-rs-vault.md).

* Azure Backup poskytuje tři předdefinované role pro řízení operací správy zálohování: přispěvatelé, operátoři a čtenáři zálohování. [Další informace najdete tady](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions).

* Azure Backup obsahuje několik ovládacích prvků zabezpečení, které jsou součástí služby pro prevenci, detekci a reakci na slabá místa zabezpečení (Další informace).

* Účty úložiště, které používá služba Recovery Services trezory, jsou izolované a uživatelé k nim nemají k dispozici žádné škodlivé účely. Přístup je povolený jenom prostřednictvím operací správy Azure Backup, jako je například obnovení.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>Šifrování dat při přenosu a v klidovém režimu

Šifrování chrání vaše data a pomáhá splnit závazky zabezpečení a dodržování předpisů vaší organizace.

* V rámci Azure jsou data přenášená mezi službou Azure Storage a trezorem chráněná protokolem HTTPS. Tato data zůstávají v síti Azure.

* Zálohovaná data se automaticky šifrují pomocí klíčů spravovaných Microsoftem. Alternativně můžete použít vlastní klíče, označované také jako [zákaznické spravované klíče](encryption-at-rest-with-cmk.md).

* Azure Backup podporuje zálohování a obnovení virtuálních počítačů Azure, které mají svoje disky s operačním systémem nebo daty šifrované pomocí Azure Disk Encryption (ADE). [Další informace najdete tady](backup-azure-vms-encryption.md).

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>Ochrana zálohovaných dat před neúmyslnými odstraněními

Azure Backup poskytuje funkce zabezpečení, které vám pomůžou chránit zálohovaná data i po jejím odstranění. Pokud uživatel odstraní zálohu (pro virtuální počítač, SQL Server databázi, sdílenou složku Azure, SAP HANA databázi), budou se data zálohy uchovávat 14 dalších dnů, což umožňuje obnovení této zálohované položky bez ztráty dat. Další 14 dní uchovávání zálohovaných dat ve stavu "obnovitelné odstranění" za vás neúčtují žádné náklady. [Další informace najdete tady](backup-azure-security-feature-cloud.md).

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>Monitorování a výstrahy podezřelé aktivity

Azure Backup poskytuje integrované možnosti monitorování a upozorňování pro zobrazení a konfiguraci akcí pro události související s Azure Backup. [Další informace najdete tady](security-overview.md#monitoring-and-alerts-of-suspicious-activity).

### <a name="security-features-to-help-protect-hybrid-backups"></a>Funkce zabezpečení, které vám pomůžou chránit hybridní zálohy

Služba Azure Backup používá agenta Microsoft Azure Recovery Services (MARS) k zálohování a obnovení souborů, složek a svazku nebo stavu systému z místního počítače do Azure. MARS nyní poskytuje funkce zabezpečení: heslo, které se má zašifrovat před nahráním a dešifrováním po stažení z Azure Backup. data odstraněných záloh se uchovávají po dobu dalších 14 dní od data odstranění a kritická operace (např. změnu hesla můžete provést jenom uživatelům, kteří mají platné přihlašovací údaje Azure. [Další informace najdete tady](backup-azure-security-feature.md).

## <a name="network-considerations"></a>Důležité informace z hlediska využívání sítě

Azure Backup vyžaduje přesun dat z vaší úlohy do trezoru Recovery Services. Azure Backup poskytuje několik možností ochrany zálohovaných dat neúmyslným zveřejněním (například útoku prostředníkem na síť). Zvažte následující pokyny:

### <a name="internet-connectivity"></a>Připojení k internetu

* *Zálohování virtuálních počítačů Azure* – veškerá požadovaná komunikace a přenos dat mezi úložištěm a Azure Backup službou se provádí v síti Azure, aniž by bylo potřeba mít přístup k virtuální síti. Zálohování virtuálních počítačů Azure umístěných v zabezpečených sítích proto nevyžaduje povolení přístupu k žádným IP adresám nebo plně kvalifikovaným názvům domén.

* *SAP HANA databáze na virtuálním počítači Azure, SQL Server databází na virtuálním počítači Azure* – vyžaduje připojení ke službě Azure Backup, Azure Storage a Azure Active Directory. Toho lze dosáhnout použitím privátních koncových bodů nebo povolením přístupu k požadovaným veřejným IP adresám nebo plně kvalifikovanému názvu domény. Pokud nepovolíte správné připojení k požadovaným službám Azure, může dojít k selhání operací, jako je zjišťování databáze, konfigurace zálohování, provádění zálohování a obnovování dat. Úplný návod k síti při použití značek NSG, brány Azure firewall a proxy serveru HTTP najdete v těchto článcích [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) a [SAP HANA](./backup-azure-sap-hana-database.md#establish-network-connectivity) .

* *Hybridní* – agent MARS (Microsoft Azure Recovery Services) vyžaduje přístup k síti pro všechny kritické operace – instalace, konfigurace, zálohování a obnovení. Agent MARS se může připojit k Azure Backup službě přes [Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) pomocí veřejného partnerského vztahu (k dispozici pro staré okruhy) a partnerského vztahu Microsoftu [s příslušnými ovládacími prvky přístupu](install-mars-agent.md#verify-internet-access)pomocí [privátních koncových bodů](install-mars-agent.md#private-endpoints) nebo přes proxy server nebo bránu firewall.

### <a name="private-endpoints-for-azure-backup"></a>Soukromé koncové body pro Azure Backup

[Privátní koncový bod](../private-link/private-endpoint-overview.md) Azure je síťové rozhraní, které se připojuje soukromě a bezpečně ke službě využívající privátní propojení Azure. Azure Backup umožňuje bezpečně zálohovat a obnovovat data z trezorů Recovery Services pomocí privátních koncových bodů.

* Když u trezoru povolíte privátní koncové body, použijí se jenom pro zálohování a obnovení úloh SQL a SAP HANA v zálohách virtuálních počítačů Azure a MARS agentů.  Trezor můžete použít i pro zálohování dalších úloh (nevyžadují i privátní koncové body). Kromě zálohování úloh SQL a SAP HANA a zálohování pomocí agenta MARS se k obnovení souborů v případě zálohování virtuálních počítačů Azure používá i privátní koncové body. [Další informace najdete tady](private-endpoints.md#recommended-and-supported-scenarios).

* Azure Active Directory v současné době nepodporuje privátní koncové body. IP adresy a plně kvalifikované názvy domény, které jsou potřeba pro Azure Active Directory, musí mít povolený odchozí přístup ze zabezpečené sítě při provádění zálohování databází ve virtuálních počítačích Azure a při zálohování pomocí agenta MARS. Můžete také použít značky NSG a značky Azure Firewall pro povolení přístupu ke službě Azure AD (podle potřeby). Další informace o [požadavcích najdete tady](./private-endpoints.md#before-you-start).

## <a name="governance-considerations"></a>Požadavky na zásady správného řízení

Zásady správného řízení v Azure jsou primárně implementovány pomocí [Azure Policy](../governance/policy/overview.md) a [Azure cost management](../cost-management-billing/cost-management-billing-overview.md). [Azure Policy](../governance/policy/overview.md) umožňuje vytvářet, přiřazovat a spravovat definice zásad pro vymáhání vašich prostředků. Tato funkce udržuje tyto prostředky v souladu s vašimi podnikovými standardy. [Azure cost management](../cost-management-billing/cost-management-billing-overview.md) vám umožňuje sledovat využití cloudu a výdaje za prostředky Azure a další cloudové poskytovatele. Následující nástroje, jako je například [Cenová Kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/) , a [Azure Advisor](../advisor/advisor-overview.md)  hrají důležitou roli v procesu správy nákladů.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure Backup podporují dva klíčové scénáře prostřednictvím integrované Azure Policy

* Ujistěte se, že nově vytvořené počítače kritické pro podnikání jsou automaticky zálohovány pomocí správného nastavení uchovávání. Azure Backup poskytuje vestavěnou zásadu (pomocí Azure Policy), kterou je možné přiřadit ke všem virtuálním počítačům Azure v zadaném umístění v rámci předplatného nebo skupiny prostředků. Když se tato zásada přiřadí k danému oboru, všechny nové virtuální počítače vytvořené v tomto oboru se automaticky nakonfigurují pro zálohování do existujícího trezoru ve stejném umístění a předplatném. Uživatel může zadat trezor a zásady uchovávání, ke kterým by měly být připojené záložní virtuální počítače. [Další informace najdete tady](backup-azure-auto-enable-backup.md).

* Ujistěte se, že nově vytvořená trezory mají povolenou diagnostiku pro podporu sestav. Často ruční přidání nastavení diagnostiky pro každý trezor může být náročný úkol. Všechny vytvořené nové trezory musí mít povolené nastavení diagnostiky, abyste mohli zobrazit sestavy pro tento trezor. Pro zjednodušení vytváření nastavení diagnostiky se škálováním (s Log Analytics jako cíl) Azure Backup poskytuje integrovaný Azure Policy. Tato zásada přidá nastavení diagnostiky LA do všech trezorů v každém předplatném nebo skupině prostředků. Následující části obsahují pokyny k použití této zásady. [Další informace najdete tady](azure-policy-configure-diagnostics.md).

### <a name="azure-backup-cost-considerations"></a>Azure Backup – požadavky na náklady

Funkce služby Azure Backup nabízejí flexibilitu pro efektivní správu vašich nákladů a pořád uspokojují vaše požadavky na BCDR (provozní kontinuita a zotavení po havárii). Zvažte následující pokyny:

* Pomocí cenové kalkulačky můžete vyhodnotit a optimalizovat náklady úpravou různých páky. [Další informace](azure-backup-pricing.md)

* Průzkumník zálohování: k pochopení a optimalizaci růstu úložiště zálohování použijte Průzkumníka služby Backup nebo sestavy zálohování, zastavte zálohování pro nekritické úlohy nebo odstraněné virtuální počítače. Z perspektivy zálohování můžete získat agregované zobrazení celé nemovitosti. To zahrnuje nejen informace o zálohovaných položkách, ale také prostředky, které nejsou nakonfigurované pro zálohování. Tím zajistíte, že nikdy nebudete mít na starosti ochranu důležitých dat a vaše zálohy jsou optimalizované pro nekritické úlohy nebo odstraněné úlohy.

* Optimalizace zásad zálohování
  * Optimalizace nastavení plánu a uchovávání na základě archetypes úloh (například kritická, nekritická)
  * Optimalizovat nastavení uchování pro okamžité obnovení
  * Vyberte si správný typ zálohování pro splnění požadavků a při současném získání podporovaného typu zálohování (úplné, přírůstkové, protokolové, rozdílové) podle Azure Backup v úvahu.

* Selektivní zálohování disků: vyloučit disk (funkce Preview) poskytuje efektivní a cenově výhodné volbu pro selektivní zálohování důležitých dat. Například zálohujte jenom jeden disk, když nechcete zálohovat zbývající disky připojené k virtuálnímu počítači. To je užitečné také v případě, že máte více řešení pro zálohování. Například když zálohujete databáze nebo data pomocí řešení zálohování úloh (SQL Server databázi v zálohování virtuálních počítačů Azure) a chcete pro vybrané disky použít zálohování na úrovni virtuálního počítače Azure.

* Azure Backup pořizování snímků virtuálních počítačů Azure a ukládá je spolu s disky pro zvýšení vytvoření bodu obnovení a urychlení operací obnovení. Tato funkce se označuje jako okamžité obnovení. Ve výchozím nastavení se snímky okamžitého obnovení uchovávají po dobu dvou dnů. Tato funkce umožňuje operaci obnovení z těchto snímků vyjmutím doby obnovení. Zkracuje dobu potřebnou k transformaci a zkopírování dat zpět z trezoru. V důsledku toho se zobrazí náklady na úložiště, které odpovídají snímkům provedeným během tohoto období. [Další informace najdete tady](backup-instant-restore-capability.md#configure-snapshot-retention).

* Typ replikace úložiště Azure Backupového trezoru je ve výchozím nastavení nastaven na geograficky redundantní (GRS). Tuto možnost nelze po ochraně položek změnit. Geograficky redundantní úložiště (GRS) poskytuje vyšší úroveň odolnosti dat než místně redundantní úložiště (LRS), umožňuje výslovný souhlas s používáním obnovení mezi oblastmi a náklady. Projděte si kompromisy mezi nižšími náklady a vyšší trvanlivostí dat a rozhodněte se, co je pro váš scénář nejvhodnější. [Další informace](backup-create-rs-vault.md#set-storage-redundancy)

* Pokud chráníte úlohy běžící v rámci virtuálního počítače a samotného virtuálního počítače, zkontrolujte, jestli je tato duální ochrana potřebná.

## <a name="monitoring-and-alerting-considerations"></a>Výstrahy týkající se monitorování a upozorňování

Jako uživatel, který má záložního uživatele nebo správce, byste měli být schopni monitorovat všechna řešení zálohování a dostávat upozornění na důležité scénáře. Tato část podrobně popisuje možnosti monitorování a oznámení poskytované službou Azure Backup.

### <a name="monitoring"></a>Monitorování

* Azure Backup poskytuje **integrované monitorování úloh** pro operace, jako je konfigurace zálohování, zálohování, obnovení, odstranění zálohy atd. Toto je vymezené na trezor a ideální pro monitorování jednoho trezoru. [Další informace najdete tady](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

* Pokud potřebujete monitorovat provozní aktivity se škálováním, pak **Průzkumník zálohování** poskytuje agregované zobrazení celé služby pro zálohování a umožňuje podrobnou analýzu a řešení potíží s podrobnostmi. Je to vestavěný Azure Monitor sešit, který poskytuje jediné centrální umístění, které vám umožní monitorovat provozní činnosti napříč celou záloze v Azure, zahrnující klienty, umístění, předplatná, skupiny prostředků a trezory. [Další informace najdete tady](monitor-azure-backup-with-backup-explorer.md).
  * Použijte ho k identifikaci prostředků, které nejsou nakonfigurované pro zálohování, a ujistěte se, že nebudete nikdy chtít chránit kritická data ve vaší rostoucí nemovitosti.
  * Řídicí panel poskytuje provozní činnosti za posledních sedm dní (maximum). Pokud potřebujete tato data zachovat, můžete je exportovat jako excelový soubor a zachovat je.
  * Pokud jste uživatelem Azure Lighthouse, můžete zobrazit informace v různých klientech a povolit monitorování bez omezení.

* Pokud potřebujete zachovat a zobrazit provozní aktivity pro dlouhodobé používání sestav, použijte **sestavy**. Běžným požadavkem pro správce zálohování je získat přehled o zálohách na základě dat, která přesahují delší dobu. Případy použití takového řešení zahrnují:
  * Přidělování a prognózování spotřebovaného cloudového úložiště.
  * Auditování záloh a obnovení.
  * Identifikujte klíčové trendy v různých úrovních členitosti.

* Navíc
  * Do pracovního prostoru **Log Analytics** můžete odesílat data (například úlohy, zásady atd.). Tím umožníte, aby se funkce protokolů Azure Monitor povolily korelace dat s jinými daty monitorování shromážděnými pomocí Azure Monitor, Konsolidujte položky protokolu z několika předplatných Azure a klientů do jednoho umístění pro účely analýzy, pomocí dotazů protokolu provádíte komplexní analýzu a získáte podrobné přehledy o položkách protokolu. [Další informace najdete tady](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace).
  * Data můžete odesílat do centra událostí, abyste mohli odesílat položky mimo Azure, například SIEM třetí strany (informace o zabezpečení a správu událostí) nebo jiné řešení Log Analytics. [Další informace najdete tady](../azure-monitor/essentials/activity-log.md#send-to-azure-event-hubs).
  * Data můžete do účtu Azure Storage odeslat, pokud chcete uchovávat data protokolu déle než 90 dní pro audit, statickou analýzu nebo zálohování. Pokud potřebujete události jenom po dobu 90 dnů nebo i méně, nemusíte nastavovat archivy na účet úložiště, protože události protokolu aktivit jsou uchovávány na platformě Azure po dobu 90 dnů. [Další informace](../azure-monitor/essentials/activity-log.md#send-to--azure-storage).

### <a name="alerting"></a>Zobrazení výstrah

* Výstrahy jsou primárně způsob, jak získat oznámení o příslušné akci. Část výstrahy zálohování zobrazuje výstrahy vygenerované službou Azure Backup.

* Azure Backup poskytuje integrovaný mechanizmus oznámení o **výstrahách** prostřednictvím e-mailu pro chyby, varování a kritické operace. Můžete zadat jednotlivé e-mailové adresy nebo distribuční seznamy, které budou oznamovány při vygenerování výstrahy. Můžete si také vybrat, jestli se má zobrazit upozornění na každou jednotlivou výstrahu, nebo je seskupit do hodinových Digest a pak získat oznámení.
  * Tyto výstrahy definuje služba a poskytují podporu pro omezené scénáře – selhání zálohování a obnovování, zastavení ochrany při zachování dat/zastavení ochrany pomocí odstranit data atd. [Další informace najdete tady](backup-azure-monitoring-built-in-monitor.md#alert-scenarios).
  * Pokud se provede destruktivní operace, jako je zastavení ochrany pomocí odstranění dat, vygeneruje se výstraha a vlastníkům předplatného, správcům a spolupracovníkům se pošle e-mail, i když pro Recovery Services trezoru **nejsou nakonfigurované oznámení** .
  * Některé úlohy můžou generovat vysokou frekvenci selhání (například SQL Server každých 15 minut). Aby nedocházelo k zahlcení výstrahám, které jsou vyvolány pro jednotlivé výskyty selhání, jsou výstrahy konsolidovány. [Další informace najdete tady](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts).
  * Integrované výstrahy nelze přizpůsobit a jsou omezeny na e-maily definované v Azure Portal.

* Pokud potřebujete **vytvořit vlastní výstrahy** (například výstrahy na úspěšné úlohy), pak použijte Log Analytics. V Azure Monitor můžete vytvořit vlastní výstrahy v pracovním prostoru Log Analytics. Hybridní úlohy (DPM/MABS) taky můžou odesílat data do LA a používat LA k poskytování běžných výstrah napříč úlohami, které Azure Backup podporuje.

* Můžete také dostávat oznámení prostřednictvím integrovaných **protokolů aktivit** služby Recovery Services trezor. Podporuje ale omezené scénáře a není vhodné pro operace, jako je například naplánované zálohování, které se lépe zarovnají s protokoly prostředků než s protokoly aktivit. Další informace o těchto omezeních a o tom, jak můžete používat Log Analytics pracovní prostor pro monitorování a upozorňování ve velkém měřítku pro všechny vaše úlohy, které jsou chráněné pomocí Azure Backup, najdete v tomto [článku](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale).

## <a name="next-steps"></a>Další kroky

Doporučujeme, abyste si následující články přečetli jako výchozí body pro použití Azure Backup:

* [Přehled Azure Backup](backup-overview.md)
* [Nejčastější dotazy](backup-azure-backup-faq.md)
