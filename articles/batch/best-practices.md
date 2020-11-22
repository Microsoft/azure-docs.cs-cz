---
title: Osvědčené postupy
description: Naučte se osvědčené postupy a užitečné tipy pro vývoj Azure Batchch řešení.
ms.date: 11/18/2020
ms.topic: conceptual
ms.openlocfilehash: 6aaed76ad398b5278850dd66ce1da6d5bd33807f
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2020
ms.locfileid: "95254659"
---
# <a name="azure-batch-best-practices"></a>Azure Batch osvědčené postupy

Tento článek popisuje shromažďování osvědčených postupů a užitečných tipů pro efektivní používání služby Azure Batch, a to na základě zkušeností v reálném čase s využitím služby Batch. Tyto tipy vám pomohou vylepšit výkon a vyhnout se nástrah návrhu ve vašich Azure Batchch řešeních.

## <a name="pools"></a>Fondy

[Fondy](nodes-and-pools.md#pools) jsou výpočetní prostředky pro provádění úloh ve službě Batch. V následujících částech jsou uvedeny doporučení pro práci s fondy služby Batch.

### <a name="pool-configuration-and-naming"></a>Konfigurace fondu a názvy

- **Režim přidělování fondů** Při vytváření účtu Batch si můžete vybrat mezi dvěma režimy přidělování fondů: předplatné **služby Batch** nebo **uživatele**. Ve většině případů byste měli použít výchozí režim služby Batch, ve kterém se fondy přidělují na pozadí v předplatných spravovaných dávkou. V alternativním režimu Předplatné uživatele se virtuální počítače a další prostředky služby Batch vytvářejí přímo ve vašem předplatném při vytvoření fondu. Účty předplatného uživatele se primárně používají k zajištění důležité, ale malé podmnožiny scénářů. Další informace o režimu předplatného uživatele najdete v [Další konfiguraci pro režim předplatného uživatele](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Při určování úlohy na mapování fondu zvažte čas spuštění úlohy a úlohy.**
    Pokud máte úlohy skládající se hlavně z krátkých spuštěných úloh a očekávaného celkového počtu úloh je malý, takže celková Očekávaná doba běhu úlohy není dlouhá, nepřiřazujte nový fond pro každou úlohu. Čas přidělení uzlů sníží dobu běhu úlohy.

- **Fondy by měly mít více než jeden výpočetní uzel.**
    V jednotlivých uzlech není zaručeno, že vždy bude k dispozici. I když nejsou běžné, selhání hardwaru, aktualizace operačního systému a hostitel jiných problémů, můžou způsobit, že jednotlivé uzly budou offline. Pokud vaše úloha Batch vyžaduje deterministický a zaručený průběh, měli byste přidělit fondy s více uzly.

- **Nepoužívejte názvy prostředků znovu.**
    Prostředky Batch (úlohy, fondy atd.) se často přidávají a procházejí v průběhu času. Můžete například vytvořit fond v pondělí, odstranit ho v úterý a pak vytvořit další fond ve čtvrtek. Každému novému prostředku, který vytvoříte, by se měl udělit jedinečný název, který jste předtím nepoužívali. To lze provést pomocí identifikátoru GUID (buď jako celého názvu prostředku, nebo jako jeho části), nebo vložením času vytvoření prostředku v názvu prostředku. Batch podporuje [DisplayName (zobrazovaný](/dotnet/api/microsoft.azure.batch.jobspecification.displayname)název), který se dá použít k poskytnutí snadno čitelného názvu prostředku, i když je skutečný identifikátor prostředku něco, co není pro člověka vhodné. Použití jedinečných názvů usnadňuje odlišení konkrétního prostředku v protokolech a metrikách. Pokud někdy budete potřebovat případ podpory pro určitý prostředek, odeberete taky nejednoznačnost.

- **Kontinuita při údržbě fondu a selhání.**
    Doporučujeme, aby vaše úlohy dynamicky používaly fondy. Pokud vaše úlohy používají stejný fond pro všechno, může se stát, že se vaše úlohy nespustí, pokud dojde k nějakému problému s fondem. To je obzvláště důležité pro časově citlivé úlohy. Pokud chcete tento problém vyřešit, vyberte nebo vytvořte fond dynamicky, když naplánujete každou úlohu, nebo máte možnost přepsat název fondu, abyste mohli obejít špatný fond.

- **Provozní kontinuita při údržbě fondu a selhání** Existuje mnoho možných příčin, které mohou zabránit, aby se fond rozrůst na požadovanou velikost, kterou si přejete, jako je například vnitřní chyba, omezení kapacity atd. Z tohoto důvodu byste měli být připravení změnit cílení úloh v jiném fondu (případně s jinou velikostí virtuálního počítače, který v případě potřeby podporuje dávku přes [UpdateJob](/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update)). Nepoužívejte identifikátor statického fondu s očekáváním, že se nikdy neodstraní a nikdy se nezmění.

### <a name="pool-lifetime-and-billing"></a>Doba života fondu a fakturace

Doba života fondu se může lišit v závislosti na metodě přidělování a parametrech, které se vztahují ke konfiguraci fondu. Fondy můžou mít v libovolném časovém okamžiku libovolnou dobu života a proměnlivý počet výpočetních uzlů ve fondu. Vaše zodpovědnost za správu výpočetních uzlů ve fondu buď výslovně, nebo prostřednictvím funkcí poskytovaných službou ([Automatické škálování nebo automatického](nodes-and-pools.md#automatic-scaling-policy) [fondu](nodes-and-pools.md#autopools)).

- **Udržujte fondy v čerstvém stavu.**
    Změňte velikost fondů na nulu každých několik měsíců, abyste měli jistotu, že získáte [nejnovější aktualizace agenta uzlů a opravy chyb](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md). Váš fond nebude dostávat aktualizace agenta uzlu, pokud není znovu vytvořen, nebo se změnila velikost na 0 výpočetních uzlů. Než znovu vytvoříte nebo změníte velikost fondu, doporučujeme, abyste si stáhli všechny protokoly agenta uzlů pro účely ladění, jak je popsáno v části [uzly](#nodes) .

- **Opětovné vytvoření fondu** Na podobném upozornění se nedoporučuje každý den odstranit a znovu vytvořit fondy. Místo toho vytvořte nový fond a aktualizujte stávající úlohy tak, aby odkazovaly na nový fond. Po přesunutí všech úkolů do nového fondu odstraňte starý fond.

- **Efektivita fondu a fakturace** Samotný Batch se neúčtují žádné další poplatky, ale za využité výpočetní prostředky se vám účtují poplatky. Účtují se vám všechny výpočetní uzly ve fondu bez ohledu na stav, ve kterém se nachází. To zahrnuje všechny poplatky potřebné ke spuštění uzlu, například náklady na úložiště a síť. Další informace o osvědčených postupech najdete v tématu [Analýza nákladů a rozpočty pro Azure Batch](budget.md).

### <a name="pool-allocation-failures"></a>Selhání přidělení fondů

Selhání přidělení fondu může probíhat kdykoli během prvního přidělení nebo po pozdější změně velikosti. Důvodem může být vyčerpání dočasné kapacity v oblasti nebo selhání jiných služeb Azure, na kterých služba Batch spoléhá. Kvóta základního prostředí není zárukou, ale omezením.

### <a name="unplanned-downtime"></a>Neplánovaný výpadek

Fondy služby Batch můžou při výpadku událostí v Azure vyskytnout. Mějte na paměti, že při plánování a vývoji vašeho scénáře nebo pracovního postupu pro dávku budete mít na paměti.

V případě, že uzel selhává, pokusí se Batch automaticky obnovit tyto výpočetní uzly vaším jménem. To může aktivovat přeplánování všech spuštěných úloh na uzlu, který se obnovil. Další informace o přerušených úlohách najdete v tématu [navrhování pro opakování](#design-for-retries-and-re-execution) .

### <a name="custom-image-pools"></a>Vlastní fondy imagí

Když vytváříte fond Azure Batch s použitím konfigurace virtuálního počítače, zadáte image virtuálního počítače, která poskytuje operační systém pro každý výpočetní uzel ve fondu. Fond můžete vytvořit pomocí podporované bitové kopie Azure Marketplace, nebo můžete [vytvořit vlastní image s obrázkem Galerie sdílených imagí](batch-sig-images.md). I když pomocí [spravované image](batch-custom-images.md) můžete vytvořit vlastní fond imagí, doporučujeme vytvářet vlastní image pomocí Galerie sdílených imagí, kdykoli to bude možné. Použití Galerie sdílených imagí vám pomůže zřídit fondy rychleji, škálovat větší množství virtuálních počítačů a zvýšit spolehlivost při zřizování virtuálních počítačů.

### <a name="third-party-images"></a>Image třetích stran

Fondy se dají vytvářet pomocí imagí třetích stran publikovaných na Azure Marketplace. V případě účtů Batch v režimu předplatného uživatele se může zobrazit chyba "přidělení nebylo úspěšné kvůli kontrole způsobilosti nákupu na webu Marketplace" při vytváření fondu s některými imagemi třetích stran. Chcete-li tuto chybu vyřešit, přijměte podmínky stanovené vydavatelem obrázku. Můžete to udělat pomocí [Azure PowerShell](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) nebo [Azure CLI](/cli/azure/vm/image/terms).

### <a name="azure-region-dependency"></a>Závislost oblasti Azure

Doporučujeme, abyste nezávislí na jedné oblasti Azure v případě, že máte časově citlivou nebo produkční úlohu. V některých případech dochází k problémům, které mohou ovlivnit celou oblast. Například pokud vaše zpracování potřebuje spustit v určitou dobu, zvažte možnost škálovat fond v hlavní oblasti *dobře před časem zahájení*. Pokud se škálování fondu nepovede, můžete se vrátit k vertikálnímu navýšení kapacity fondu v oblasti zálohování (nebo oblastech). Fondy napříč několika účty v různých oblastech poskytují připravenou a snadno dostupnou zálohu, pokud se něco pokazilo s jiným fondem. Další informace najdete v tématu [Návrh aplikace pro zajištění vysoké dostupnosti](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Úlohy

[Úloha](jobs-and-tasks.md#jobs) je kontejner navržený tak, aby obsahoval stovky, tisíce nebo dokonce miliony úloh. Při vytváření úloh postupujte podle těchto pokynů.

### <a name="fewer-jobs-more-tasks"></a>Méně úloh, další úlohy

Použití úlohy ke spuštění jedné úlohy je neefektivní. Například je efektivnější používat jednu úlohu obsahující 1000 úkoly místo vytváření 100 úloh, které obsahují 10 úkolů. Spouštění 1000 úloh, z nichž každá má jeden úkol, by představovalo nejméně efektivní, nejpomalejší a nejdražšího přístupu, které je potřeba provést.

Proto se ujistěte, že nenavrhnete řešení Batch, které vyžaduje tisíce současně aktivních úloh. Pro úlohy není k dispozici žádná kvóta, takže spuštění mnoha úkolů v rámci co nejmenšího počtu úloh může efektivně využívat [kvóty úloh a plánu úloh](batch-quota-limit.md#resource-quotas).

### <a name="job-lifetime"></a>Doba života úlohy

Úloha služby Batch má neomezenou dobu života, dokud se neodstraní ze systému. Jeho stav určuje, zda může přijmout více úloh pro plánování nebo nikoli.

Úloha se automaticky nepřesouvá do dokončeného stavu, pokud se explicitně neukončí. Tato možnost se dá automaticky aktivovat prostřednictvím vlastnosti [onAllTasksComplete](/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete) nebo [proměnné maxwallclocktime](/rest/api/batchservice/job/add#jobconstraints).

Existuje výchozí [kvóta pro aktivní úlohu a plán úlohy](batch-quota-limit.md#resource-quotas). Úlohy a plány úloh v dokončeném stavu se nepočítají k této kvótě.

## <a name="tasks"></a>Úlohy

[Úkoly](jobs-and-tasks.md#tasks) jsou jednotlivé pracovní jednotky, které tvoří úlohu. Úkoly jsou odesílány uživatelem a naplánovaly se službou Batch na výpočetní uzly. Při vytváření a spouštění úloh je potřeba provést několik otázek při návrhu. V následujících částech jsou vysvětlené běžné scénáře a postupy návrhu úloh pro zpracování problémů a efektivní provádění.

### <a name="save-task-data"></a>Uložit data úkolu

Výpočetní uzly jsou podle jejich povahy dočasný. V dávce je mnoho funkcí, jako je například automatické [fondy](nodes-and-pools.md#autopools) a [Automatické škálování](nodes-and-pools.md#automatic-scaling-policy) , které usnadňují, aby uzly zmizely. Když uzly opustí fond (z důvodu změny velikosti nebo fondu), odstraní se také všechny soubory na těchto uzlech. Z tohoto důvodu by úloha měla přesunout výstup mimo uzel, na kterém je spuštěný, a až do trvalého úložiště, než se dokončí. Podobně platí, že pokud úloha selže, měl by přesunout protokoly potřebné k diagnostice selhání do trvalého úložiště.

Batch má integrovanou podporu Azure Storage pro nahrávání dat prostřednictvím [OutputFiles](batch-task-output-files.md)a také pro celou řadu sdílených systémů souborů, nebo můžete nahrát sami sebe ve svých úlohách.

### <a name="manage-task-lifetime"></a>Správa životnosti úlohy

Odstraňte úkoly, které už nepotřebujete, nebo nastavte omezení úlohy [retentionTime](/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime) . Pokud `retentionTime` je nastavená, služba Batch automaticky vyčistí místo na disku, které úloha využívala při `retentionTime` vypršení platnosti.

Odstranění úloh provede dvě věci. Zajišťuje, abyste v úloze nemuseli sestavovat úlohy, což by mohlo ztížit dotazování a hledání úkolů, které vás zajímají (protože budete muset filtrovat přes dokončené úkoly). Vyčistí také odpovídající data úkolu v uzlu (v případě, že ještě `retentionTime` není dosaženo). To pomáhá zajistit, aby se uzly neplnily daty úlohy a aby nedostatek místa na disku.

### <a name="submit-large-numbers-of-tasks-in-collection"></a>Odeslání velkého počtu úkolů v kolekci

Úkoly lze odesílat na základě individuálních nebo v kolekcích. Odesílat úlohy v [kolekcích](/rest/api/batchservice/task/addcollection) až 100 v době, kdy se hromadně odesílají úkoly, které snižují náklady na režii a dobu odeslání.

### <a name="set-max-tasks-per-node-appropriately"></a>Nastavit maximální počet úkolů na uzel správně

Batch podporuje přepočet úkolů na uzlech (spouštění více úloh, než má uzel obsahuje jádra). Je to na vás, abyste se ujistili, že se vaše úkoly vejdou do uzlů ve fondu. Například můžete mít zhoršené prostředí, pokud se pokusíte naplánovat osm úloh, které každý využívá 25% využití CPU na jeden uzel (ve fondu s `taskSlotsPerNode = 8` ).

### <a name="design-for-retries-and-re-execution"></a>Návrh pro opakování a opakované spuštění

Úlohy mohou být automaticky opakovány službou Batch. Existují dva typy opakování: uživatel byl řízen a interní. Opakované pokusy řízené uživatelem jsou určeny [maxTaskRetryCount](/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount)úlohy. Když se program zadaný v úloze ukončí s nenulovým ukončovacím kódem, úloha se znovu vyzkouší do hodnoty `maxTaskRetryCount` .

I když je to zřídka, může se úloha opakovat interně z důvodu selhání ve výpočetním uzlu, jako je například neschopnost aktualizovat vnitřní stav nebo selhání uzlu v době, kdy je úloha spuštěná. Tato úloha se zopakuje na stejném výpočetním uzlu, pokud je to možné, až do interního limitu před tím, než se vrátíte k úloze a oddělíte úlohu, která má být přeplánována službou Batch, případně na jiném výpočetním uzlu.

Při provádění úloh na vyhrazených uzlech nebo s nízkou prioritou nejsou k dispozici žádné rozdíly v návrhu. Bez ohledu na to, jestli je úloha přerušená, když běží na uzlu s nízkou prioritou nebo je přerušená kvůli selhání na vyhrazeném uzlu, se obě situace zmírnit tím, že se úloha vyvinou chyby vyodolat.

### <a name="build-durable-tasks"></a>Sestavení trvalých úloh

Úkoly by měly být navržené tak, aby vydržely selhání a vyhovovaly opakování. To je důležité hlavně při dlouhotrvajících úlohách. Chcete-li to provést, zajistěte, aby úkoly generovaly stejný výsledek, i když jsou spouštěny více než jednou. Jedním ze způsobů, jak toho dosáhnout, je udělat si úkoly "hledání cílů". Další možností je zajistit, aby se vaše úkoly idempotentní (úlohy budou mít stejný výsledek bez ohledu na to, kolikrát se spouštějí).

Běžným příkladem je úloha kopírování souborů do výpočetního uzlu. Jednoduchý přístup je úkol, který kopíruje všechny zadané soubory pokaždé, když běží, což je neefektivní a není sestavené k vystavení selhání. Místo toho vytvořte úkol, abyste zajistili, že jsou soubory ve výpočetním uzlu. úloha, která nekopíruje soubory, které jsou již k dispozici. Tímto způsobem se úkol ponechá tam, kde byl přerušený.

### <a name="avoid-short-execution-time"></a>Vyhnout se krátké době provádění

Úlohy, které se spouštějí jenom po dobu jedné až dvou sekund, nejsou ideální. Měli byste se pokusit o významné množství práce v jednotlivých úkolech (minimálně 10 sekund, což je víc než hodiny nebo dny). Pokud je každý úkol spuštěný na jednu minutu (nebo více), pak se režijní náklady na plánování za zlomek celkového výpočetního času budou malé.

### <a name="use-pool-scope-for-short-tasks-on-windows-nodes"></a>Použití oboru fondu pro krátké úlohy v uzlech Windows

Při plánování úlohy na uzlech Batch můžete zvolit, jestli se má spustit s oborem úlohy nebo oborem fondu. Pokud se úloha spustí jenom krátkou dobu, obor úlohy může být neefektivní z důvodu prostředků potřebných k vytvoření účtu automatického uživatele pro tuto úlohu. Pro vyšší efektivitu zvažte nastavení těchto úloh na rozsah fondu. Další informace najdete v tématu [spuštění úlohy jako automatický uživatel s rozsahem fondu](batch-user-accounts.md#run-a-task-as-an-auto-user-with-pool-scope).

## <a name="nodes"></a>Uzly

[Výpočetní uzel](nodes-and-pools.md#nodes) je virtuální počítač Azure (VM) nebo virtuální počítač cloudové služby, který je vyhrazený pro zpracování části úlohy vaší aplikace. Při práci s uzly postupujte podle těchto pokynů.

### <a name="idempotent-start-tasks"></a>Idempotentní počáteční úlohy

Stejně jako u jiných úloh by měl být [spouštěcí úkol](jobs-and-tasks.md#start-task) uzlu idempotentní, protože bude znovu spuštěn při každém spuštění uzlu. Úkol idempotentní je jednoduše ten, který při spuštění několikrát vytvoří konzistentní výsledek.

### <a name="manage-long-running-services-via-the-operating-system-services-interface"></a>Správa dlouhotrvajících služeb prostřednictvím rozhraní služeb operačního systému

Někdy je potřeba spustit jiného agenta společně s agentem dávky v uzlu. Například můžete chtít shromažďovat data z uzlu a nahlásit ho. Doporučujeme, aby tyto agenty byly nasazeny jako služby operačního systému, například služba systému Windows nebo `systemd` Služba Linux.

Pokud tyto služby spouštíte, nesmí přebírat zámky souborů u všech souborů v adresářích spravovaných službou Batch v uzlu, protože jinak služba Batch nebude moci odstranit tyto adresáře z důvodu zámků souborů. Pokud například nainstalujete službu systému Windows do spouštěcí úlohy, místo spuštění služby přímo z pracovního adresáře spouštěcího úkolu zkopírujte soubory jinam (nebo pokud soubory existují pouze k přeskočení kopie). Pak z tohoto umístění nainstalujte službu. Když Batch znovu spustí spouštěcí úkol, odstraní pracovní adresář spouštěcí úlohy a znovu ho vytvoří. Tato operace funguje, protože služba má zámky souborů v jiném adresáři, nikoli pracovní adresář spouštěcího úkolu.

### <a name="avoid-creating-directory-junctions-in-windows"></a>Vyhněte se vytváření spojení adresářů ve Windows

Spojení adresářů, někdy označované jako pevné odkazy v adresáři, se obtížně zabývají při čištění úloh a úloh. Místo pevných odkazů používejte symbolických odkazů (Soft-Links).

### <a name="collect-the-batch-agent-logs"></a>Shromáždění protokolů agenta Batch

Pokud si všimnete problému s chováním uzlu nebo úloh, které jsou spuštěné na uzlu, Shromážděte protokoly služby Batch Agent před tím, než dojde k jejich navýšení na příslušné uzly. Protokoly služby Batch Agent se dají shromáždit pomocí rozhraní API pro nahrání protokolů služby Batch. Tyto protokoly je možné dodávat jako součást lístku podpory společnosti Microsoft a pomohou vám při řešení potíží a řešení problémů.

### <a name="manage-os-upgrades"></a>Spravovat upgrady operačního systému

Pro účty Batch v režimu předplatného uživatele můžou automatizované upgrady operačního systému přerušit průběh úloh, zejména v případě, že se úlohy dlouho spouští. [Vytváření úloh idempotentní](#build-durable-tasks) může přispět k omezení chyb způsobených těmito přerušeními. Doporučujeme také [naplánovat upgrady bitových kopií operačního systému po dobu, kdy se úlohy neočekávají, aby běžely](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md#manually-trigger-os-image-upgrades).

## <a name="isolation-security"></a>Zabezpečení izolace

Pro účely izolace, pokud váš scénář vyžaduje izolované úlohy od sebe navzájem, udělejte to tak, že je budete mít v samostatných fondech. Fond je hranice izolace zabezpečení ve službě Batch a ve výchozím nastavení nejsou dva fondy viditelné ani vzájemně vzájemně komunikují. Vyhněte se použití samostatných účtů Batch jako izolačního prostředku.

## <a name="moving-batch-accounts-across-regions"></a>Přesun účtů Batch napříč oblastmi

Existují scénáře, ve kterých může být užitečné přesunout existující účet Batch z jedné oblasti do druhé. Například můžete chtít přesunout do jiné oblasti v rámci plánování zotavení po havárii.

Účty Azure Batch nelze přímo přesunout z jedné oblasti do druhé. K exportu existující konfigurace účtu Batch ale můžete použít šablonu Azure Resource Manager. Potom můžete prostředek vytvořit v jiné oblasti tak, že účet Batch exportujete do šablony, upravíte parametry tak, aby odpovídaly cílové oblasti, a pak šablonu nasadíte do nové oblasti.

Po nahrání šablony do nové oblasti bude nutné znovu vytvořit certifikáty, plány úloh a balíčky aplikací. Chcete-li potvrdit změny a dokončit přesunutí účtu Batch, nezapomeňte odstranit původní účet Batch nebo skupinu prostředků.

Další informace o Správce prostředků a šablonách najdete v tématu [rychlý Start: vytvoření a nasazení Azure Resource Manager šablon pomocí Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

## <a name="connectivity"></a>Připojení

Přečtěte si následující pokyny týkající se připojení ve vašich dávkových řešeních.

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Skupiny zabezpečení sítě (skupin zabezpečení sítě) a uživatelsky definované trasy (udr)

Při zřizování [fondů služby Batch ve virtuální síti](batch-virtual-network.md)se ujistěte, že jste úzce využívali pokyny týkající se použití `BatchNodeManagement` značky služby, portů, protokolů a směru pravidla.
Místo používání základních IP adres služby Batch se doporučuje použít značku služby. Důvodem je to, že IP adresy se můžou v průběhu času měnit. Přímé použití IP adres služby Batch může způsobit nestabilitu, přerušení nebo výpadky pro fondy služby Batch.

Pro trasy definované uživatelem (udr) se ujistěte, že máte zavedený proces, který bude pravidelně aktualizovat IP adresy služby Batch v tabulce směrování, protože se tyto adresy mění v průběhu času. Informace o tom, jak získat seznam IP adres služby Batch, najdete v tématu věnovaném místním [značkám služby](../virtual-network/service-tags-overview.md). IP adresy služby Batch budou přidruženy k `BatchNodeManagement` značce služby (nebo k místní variantě, která odpovídá vaší oblasti účtu Batch).

### <a name="honoring-dns"></a>Respektování DNS

Zajistěte, aby vaše systémy dodržovaly hodnotu TTL (Time to Live) DNS pro adresu URL služby Batch. Navíc se ujistěte, že klienti služby Batch a další mechanismy připojení ke službě Batch nespoléhají na IP adresy (nebo [vytvořit fond se statickými veřejnými IP adresami](create-pool-public-ip.md) , jak je popsáno níže).

Pokud vaše žádosti dostanou odezvy HTTP na úrovni 5xx a v odpovědi se nachází hlavička "připojení: zavřít", klient služby Batch by měl toto doporučení obdržet tím, že uzavře stávající připojení, znovu překládá DNS pro adresu URL služby Batch a pokusí se o nové připojení zkusit následující požadavky.

### <a name="retry-requests-automatically"></a>Opakovat požadavky automaticky

Ujistěte se, že klienti služby Batch mají k dispozici vhodné zásady opakování, aby automaticky opakovaly vaše požadavky, a to i během normálního provozu, a ne výhradně během časových období údržby služby. Tyto zásady opakování by měly zahrnovat interval minimálně 5 minut. Automatické možnosti opakování jsou k dispozici s různými sadami SDK pro Batch, jako je například [Třída .NET RetryPolicyProvider](/dotnet/api/microsoft.azure.batch.retrypolicyprovider).

### <a name="static-public-ip-addresses"></a>Statické veřejné IP adresy

Virtuální počítače ve fondu Batch jsou obvykle přístupné prostřednictvím veřejných IP adres, které se můžou měnit po dobu života fondu. Díky tomu může být obtížné pracovat s databází nebo jinou externí službou, která omezuje přístup k určitým IP adresám. Aby se zajistilo, že se veřejné IP adresy ve vašem fondu neočekávaně nezmění, můžete vytvořit fond pomocí sady statických veřejných IP adres, které ovládáte. Další informace najdete v tématu [Vytvoření fondu Azure Batch se zadanými veřejnými IP adresami](create-pool-public-ip.md).

### <a name="testing-connectivity-with-cloud-services-configuration"></a>Testování připojení s konfigurací Cloud Services

Pro cloudové služby nemůžete použít normální protokol "/ICMP", protože protokol ICMP není povolený prostřednictvím nástroje pro vyrovnávání zatížení Azure. Další informace najdete v tématu věnovaném [připojení a síti pro Azure Cloud Services](../cloud-services/cloud-services-connectivity-and-networking-faq.md#can-i-ping-a-cloud-service).

## <a name="batch-node-underlying-dependencies"></a>Základní závislosti uzlu Batch

Při navrhování řešení Batch Vezměte v úvahu následující závislosti a omezení.

### <a name="system-created-resources"></a>Prostředky vytvořené systémem

Azure Batch vytvoří a spravuje skupinu uživatelů a skupin na virtuálním počítači, které by se neměly měnit. Jsou to tyto:

Windows:

- Uživatel s názvem **PoolNonAdmin**
- Skupina uživatelů s názvem **WATaskCommon**

Linux:

- Uživatel s názvem **_azbatch**

### <a name="file-cleanup"></a>Vyčištění souboru

Batch se aktivně snaží vyčistit pracovní adresář, ve kterém jsou spuštěné úlohy, jakmile doba uchování vyprší. Všechny soubory napsané mimo tento adresář jsou [vaší zodpovědností na vyčištění](#manage-task-lifetime) , aby nedošlo k zaplnění místa na disku.

Automatizované vyčištění pro pracovní adresář se zablokuje, pokud spustíte službu ve Windows z pracovního adresáře startTask, protože se tato složka pořád používá. Výsledkem bude snížení výkonu. Chcete-li tento problém vyřešit, změňte adresář této služby na samostatný adresář, který není spravován službou Batch.

## <a name="next-steps"></a>Další kroky

- [Vytvořte účet Azure Batch pomocí Azure Portal](batch-account-create-portal.md).
- Přečtěte si o [pracovních postupech služby Batch a primárních prostředcích](batch-service-workflow-features.md) , jako jsou fondy, uzly, úlohy a úkoly.
- Přečtěte si o [výchozích Azure Batch kvótách, omezeních a omezeních a o tom, jak se zvýší kvóta](batch-quota-limit.md).
