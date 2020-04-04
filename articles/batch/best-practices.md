---
title: Doporučené postupy – Azure Batch
description: Seznamte se s doporučenými postupy a užitečnými tipy pro vývoj řešení Azure Batch.
author: LauraBrenner
ms.author: labrenne
ms.date: 04/03/2020
ms.service: batch
ms.topic: article
manager: evansma
ms.openlocfilehash: 94483f8e15b0cd90f76e369034e987bec6da127c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655835"
---
# <a name="azure-batch-best-practices"></a>Doporučené postupy Azure Batch

Tento článek popisuje kolekci osvědčených postupů pro efektivní a efektivní používání služby Azure Batch. Tyto osvědčené postupy jsou odvozeny z našich zkušeností s Batch a zkušeností zákazníků Batch. Je důležité porozumět tomuto článku, abyste se vyhnuli nástrahám návrhu, potenciálním problémům s výkonem a anti-patterns při vývoji a používání dávky.

V tomto článku se dozvíte:

> [!div class="checklist"]
> - Jaké jsou osvědčené postupy
> - Proč byste měli používat osvědčené postupy
> - Co se může stát, pokud se vám nedodrží osvědčené postupy
> - Jak postupovat podle osvědčených postupů

## <a name="pools"></a>Fondy

Fondy dávek jsou výpočetní prostředky pro provádění úloh ve službě Batch. V následujících částech jsou uvedeny pokyny k nejlepším doporučeným postupům, které je třeba dodržovat při práci s fondy dávek.

### <a name="pool-configuration-and-naming"></a>Konfigurace a pojmenování fondu

- **Režim přidělení fondu** Při vytváření dávkového účtu si můžete vybrat mezi dvěma režimy přidělení fondu: **Dávková služba** nebo **uživatelské předplatné**. Ve většině případů byste měli použít výchozí režim dávkové služby, ve kterém jsou fondy přidělovány na pozadí v předplatných spravovaných dávky. V alternativním režimu Předplatné uživatele se virtuální počítače a další prostředky služby Batch vytvářejí přímo ve vašem předplatném při vytvoření fondu. Účty předplatného uživatelů se používají především k povolení důležité, ale malé podmnožiny scénářů. Další informace o režimu předplatného uživatele naleznete v části [Další konfigurace pro režim předplatného uživatele](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Při určování mapování úloh pro mapování fondu zvažte dobu spuštění úlohy a úlohy.**
    Pokud máte úlohy složené především z krátkodobých úloh a očekávaný celkový počet úkolů je malý, takže celková očekávaná doba spuštění úlohy není dlouhá, nepřidělujte pro každou úlohu nový fond. Doba přidělení uzlů zmenší dobu běhu úlohy.

- **Fondy by měly mít více než jeden výpočetní uzel.**
    U jednotlivých uzlů není zaručeno, že budou vždy k dispozici. Zatímco neobvyklé, selhání hardwaru, aktualizace operačního systému a řadu dalších problémů může způsobit, že jednotlivé uzly jsou offline. Pokud vaše dávkové úlohy vyžadují deterministický, zaručený průběh, měli byste přidělit fondy s více uzly.

- **Názvy prostředků nepoužívejte znovu.**
    Dávkové prostředky (úlohy, fondy atd.) často přicházejí a odcházejí v průběhu času. Můžete například vytvořit fond v pondělí, odstranit jej v úterý a pak vytvořit další fond ve čtvrtek. Každý nový prostředek, který vytvoříte, by měl mít jedinečný název, který jste dosud nepoužili. To lze provést pomocí identifikátoru GUID (buď jako celý název prostředku, nebo jako jeho součást) nebo vložením času, kdy byl prostředek vytvořen v názvu prostředku. Batch podporuje [DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet), který lze použít k poskytnutí prostředku čitelný název pro člověka i v případě, že skutečné ID prostředku je něco, co není tak přátelské k člověku. Použití jedinečných názvů usnadňuje rozlišení konkrétního prostředku v protokolech a metrikách. Také odstraňuje nejednoznačnost, pokud budete někdy muset podat případ podpory pro prostředek.

- **Kontinuita během údržby a selhání fondu.**
    Nejlepší je, aby vaše úlohy používaly fondy dynamicky. Pokud vaše úlohy používají stejný fond pro všechno, je tu šance, že vaše práce nebude fungovat, pokud se něco pokazí s fondem. To je obzvláště důležité pro úlohy citlivé na čas. Chcete-li tento problém vyřešit, vyberte nebo vytvořte fond dynamicky při plánování každé úlohy nebo mít způsob, jak přepsat název fondu, takže můžete obejít fond není v pořádku.

- **Kontinuita provozu během údržby a selhání fondu** Existuje mnoho možných příčin, které mohou zabránit fondu z rostoucí na požadovanou velikost, jako jsou vnitřní chyby, omezení kapacity, atd. Z tohoto důvodu byste měli být připraveni znovu zacílit úlohy v jiném fondu (případně s jinou velikostí virtuálního počítače – batch to podporuje prostřednictvím [updatejob)](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)v případě potřeby. Vyhněte se použití id statického fondu s očekáváním, že nikdy nebude odstraněna a nikdy se nezmění.

### <a name="pool-lifetime-and-billing"></a>Životnost bazénu a fakturace

Životnost fondu se může lišit v závislosti na způsobu přidělení a možnostech použitých v konfiguraci fondu. Fondy mohou mít libovolnou životnost a různý počet výpočetních uzlů ve fondu v libovolném okamžiku. Je vaší odpovědností spravovat výpočetní uzly ve fondu buď explicitně, nebo prostřednictvím funkcí poskytovaných službou (automatické škálování nebo automatické hodování).

- **Udržujte bazény čerstvé.**
    Měli byste změnit velikost fondů na nulu každých několik měsíců, abyste zajistili, že získáte nejnovější aktualizace agentů uzlů a opravy chyb. Fond neobdrží aktualizace agenta uzlu, pokud není znovu vytvořen nebo velikost velikosti 0 výpočetních uzlů. Před znovu vytvořit nebo změnit velikost fondu, doporučujeme stáhnout všechny protokoly agenta uzlu pro účely ladění, jak je popsáno v části [Uzly.](#nodes)

- **Opětovné vytvoření fondu** Podobně se nedoporučuje denně odstraňovat a znovu vytvářet fondy. Místo toho vytvořte nový fond a aktualizujte stávající úlohy tak, aby ukazovaly na nový fond. Po přesunutí všech úkolů do nového fondu odstraňte starý fond.

- **Efektivita bazénu a fakturace** Samotná dávka neúčtuje žádné další poplatky, ale účtuje se vám poplatky za použité výpočetní prostředky. Fakturuje se vám každý výpočetní uzel ve fondu, bez ohledu na jeho stav. To zahrnuje všechny poplatky potřebné pro spuštění uzlu, jako jsou náklady na úložiště a síť. Další doporučené postupy najdete v [tématu Analýza nákladů a rozpočty pro Azure Batch](budget.md).

### <a name="pool-allocation-failures"></a>Selhání přidělení fondu

Selhání přidělení fondu může dojít v libovolném okamžiku během první přidělení nebo následné změny velikosti. To může být způsobeno dočasné vyčerpání kapacity v oblasti nebo selhání v jiných službách Azure, které Batch spoléhá na. Základní kvóta není zárukou, ale spíše limitem.

### <a name="unplanned-downtime"></a>Neplánovaný výpadek

Fondy dávek mohou v Azure zaznamenat prostoje. To je důležité mít na paměti při plánování a vývoji scénáře nebo pracovního postupu pro Batch.

V případě, že uzel selže, Batch se automaticky pokusí obnovit tyto výpočetní uzly vaším jménem. To může vyvolat přeplánování jakékoli spuštěné úlohy v uzlu, který je obnoven. Další informace o přerušených úkolech najdete [v tématu Návrhy.](#designing-for-retries-and-re-execution)

- **Závislost oblasti Azure** Doporučujese nezáviset na jedné oblasti Azure, pokud máte zatížení citlivé na čas nebo produkční zatížení. Zatímco vzácné, existují problémy, které mohou ovlivnit celou oblast. Například pokud vaše zpracování potřebuje spustit v určitý čas, zvažte škálování fondu v primární oblasti *v hodně před časem zahájení*. Pokud se měřítko fondu nezdaří, můžete se vrátit k škálování fondu v oblasti zálohování (nebo oblastí). Fondy napříč více účty v různých oblastech poskytují připravenou a snadno přístupnou zálohu, pokud se něco pokazí s jiným fondem. Další informace naleznete v [tématu Návrh aplikace pro vysokou dostupnost](high-availability-disaster-recovery.md).

## <a name="jobs"></a>Úlohy

Úloha je kontejner navržený tak, aby obsahoval stovky, tisíce nebo dokonce miliony úkolů.

- **Vložte do úlohy mnoho úkolů** Použití úlohy ke spuštění jedné úlohy je neefektivní. Například je efektivnější použít jednu úlohu obsahující 1000 úkolů, spíše než vytvořit 100 úloh, které obsahují 10 úkolů každý. Spuštění 1000 úloh, z nichž každá má jeden úkol, by bylo nejméně efektivní, nejpomalejší a nejdražší přístup.

    Nenavrhujte řešení Batch, které vyžaduje tisíce současně aktivních úloh. Neexistuje žádná kvóta pro úkoly, takže provádění co nejvíce úkolů v rámci co nejméně úloh efektivně využívá [kvóty úlohy a plánu úloh](batch-quota-limit.md#resource-quotas).

- **Životnost práce** Dávková úloha má neomezenou životnost, dokud není odstraněna ze systému. Stav úlohy určuje, zda může přijmout více úkolů pro plánování či nikoli. Úloha se automaticky nepřesune do dokončeného stavu, pokud není explicitně ukončena. To lze automaticky spustit prostřednictvím [vlastnosti onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet) nebo [maxWallClockTime](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints).

Existuje výchozí [aktivní úloha a kvóta plánu úloh](batch-quota-limit.md#resource-quotas). Úlohy a plány úloh v dokončeném stavu se do této kvóty nezapočítávají.

## <a name="tasks"></a>Úkoly

Úkoly jsou jednotlivé jednotky práce, které tvoří úlohu. Úkoly jsou odeslány uživatelem a naplánovány batch na výpočetní uzly. Při vytváření a provádění úloh je třeba provést několik aspekty návrhu. V následujících částech jsou vysvětleny běžné scénáře a způsob navrhování úkolů pro řešení problémů a efektivní provádění.

- **Uložit data úkolu jako součást úkolu**
    Výpočetní uzly jsou ze své podstaty pomíjivé. Existuje mnoho funkcí v Batch, jako je automatické zařazování a automatické škálování, které usnadňují pro uzly zmizí. Když uzly opustí fond (z důvodu změny velikosti nebo odstranění fondu) všechny soubory na těchto uzlech jsou také odstraněny. Z tohoto důvodu se doporučuje, aby před dokončením úlohy přesune výstup z uzlu, na který je spuštěn, a do trvalého úložiště, podobně pokud úloha selže, by měla přesunout protokoly potřebné k diagnostice selhání do trvalého úložiště. Batch má integrovanou podporu Azure Storage pro nahrávání dat přes [OutputFiles](batch-task-output-files.md), stejně jako různé sdílené systémy souborů, nebo můžete provést nahrávání sami ve svých úkolech.

### <a name="task-lifetime"></a>Životnost úlohy

- **Po dokončení odstraňte úkoly.**
    Odstraňte úkoly, pokud již nejsou potřeba, nebo nastavte omezení úkolu [retentionTime.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet) Pokud `retentionTime` je nastavena, Batch automaticky vyčistí místo na `retentionTime` disku, které úkol používá po vypršení platnosti.

    Odstranění maže dvě věci. Zajišťuje, že nemáte sestavení úkolů v úloze, takže dotazování nebo hledání úkolu, který vás zajímá těžší (protože budete muset filtrovat přes dokončené úkoly). Také vyčistí odpovídající data úloh v uzlu (za `retentionTime` předpokladu, že ještě nebyla přístupů). Tím zajistíte, že se uzly nezaplní daty úloh a nedojde místo na disku.

### <a name="task-submission"></a>Odeslání úkolu

- **Odešlete velký počet úkolů v kolekci.**
    Úkoly lze odeslat individuálně nebo ve sbírkách. Odešlete úkoly v [kolekcích](https://docs.microsoft.com/rest/api/batchservice/task/addcollection) až 100 najednou při hromadném odesílání úkolů, abyste zkrátili dobu režie a odeslání.

### <a name="task-execution"></a>Spuštění úlohy

- **Výběr maximálního množství úkolů na uzel** Batch podporuje oversubscribing úkoly na uzlech (spuštění více úloh než uzel má jádra). Je na vás, abyste zajistili, že se vaše úkoly "vejdou" do uzlů ve vašem fondu. Například můžete mít zhoršené zkušenosti, pokud se pokusíte naplánovat osm úkolů, které každý `maxTasksPerNode = 8`spotřebovává 25 % využití procesoru na jeden uzel (ve fondu s).

### <a name="designing-for-retries-and-re-execution"></a>Návrh pro opakované pokusy a opětovné spuštění

Úkoly lze automaticky opakovat podle dávky. Existují dva typy opakování: řízené uživatelem a interní. Opakované pokusy řízené uživatelem jsou určeny [maxTaskRetryCount úlohy](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet). Pokud program zadaný v úloze ukončí s nenulovým ukončovacím kódem, bude úloha zopakována až do hodnoty `maxTaskRetryCount`.

I když vzácné, úloha může být opakována interně z důvodu selhání na výpočetní uzel, jako je například není možné aktualizovat vnitřní stav nebo selhání v uzlu, zatímco je úloha spuštěna. Úloha bude opakována na stejném výpočetním uzlu, pokud je to možné, až do vnitřního limitu před tím, než se vzdá te na úlohu a odloží úlohu, která má být přeplánována batchem, potenciálně na jiném výpočetním uzlu.

- **Vytváření trvalých úkolů** Úkoly by měly být navrženy tak, aby vydržely selhání a přizpůsobily se opakování. To je důležité zejména pro dlouhotrvající úlohy. Chcete-li to provést, ujistěte se, že úlohy generovat stejný, jeden výsledek i v případě, že jsou spuštěny více než jednou. Jedním ze způsobů, jak toho dosáhnout, je, aby vaše úkoly "cíl hledá". Dalším způsobem je zajistit, aby vaše úkoly byly idempotentní (úkoly budou mít stejný výsledek bez ohledu na to, kolikrát jsou spuštěny).

    Běžným příkladem je úloha kopírování souborů do výpočetního uzlu. Jednoduchý přístup je úkol, který zkopíruje všechny zadané soubory při každém spuštění, což je neefektivní a není vytvořen tak, aby vydržely selhání. Místo toho vytvořte úkol, který zajistí, že soubory jsou na výpočetním uzlu; úlohu, která nekopíruje soubory, které jsou již k dispozici. Tímto způsobem úkol naváže tam, kde skončil, pokud byl přerušen.

- **Uzly s nízkou prioritou** Neexistují žádné rozdíly v návrhu při provádění úloh na vyhrazené nebo s nízkou prioritou uzly. Bez ohledu na to, zda je úloha předběžně spuštěna při spuštění na uzlu s nízkou prioritou nebo přerušena z důvodu selhání na vyhrazeném uzlu, jsou obě situace zmírněny návrhem úlohy tak, aby vydržela selhání.

- **Čas spuštění úlohy** Vyhněte se úkolům s krátkou dobou provádění. Úkoly, které běží pouze jednu až dvě sekundy, nejsou ideální. Měli byste se pokusit provést značné množství práce v jednotlivém úkolu (minimálně 10 sekund, až na hodiny nebo dny). Pokud se každý úkol provádí po dobu jedné minuty (nebo více), pak režie plánování jako zlomek celkového výpočetního času je malý.

## <a name="nodes"></a>Uzly

- **Počáteční úkoly by měly být idempotentní.** Podobně jako u jiných úkolů by měla být úloha spuštění uzlu idempotentní, protože bude znovu spuštěna při každém spuštění uzlu. Idempotentní úloha je jednoduše ten, který vytváří konzistentní výsledek při spuštění vícekrát.

- **Spravujte dlouhotrvající služby prostřednictvím rozhraní služeb operačního systému.**
    Někdy je potřeba spustit jiného agenta vedle agenta Batch v uzlu, například shromažďovat data z uzlu a hlásit je. Doporučujeme, aby tito agenti nasadit jako služby operačního `systemd` systému, jako je například služba systému Windows nebo služby Linuxu.

    Při spuštění těchto služeb nesmí přijímat uzamčení souborů na žádné soubory v adresářích spravované dávky v uzlu, protože jinak Batch nebude moci odstranit tyto adresáře z důvodu uzamčení souborů. Pokud například nainstalujete službu Systému Windows do úlohy zahájení, namísto spuštění služby přímo z pracovního adresáře úlohy zahájení úlohy zkopírujte soubory jinam (pokud soubory existují, stačí kopii přeskočit). Nainstalujte službu z tohoto umístění. Po opětovném spuštění úlohy Batch odstraní pracovní adresář počáteční úlohy a znovu jej vytvoří. To funguje, protože služba má uzamčení souborů v jiném adresáři, nikoli v pracovním adresáři úlohy spuštění.

- **Vyhněte se vytváření křižovatek adresářů v systému Windows** Spojení adresářů, někdy nazývané pevné odkazy adresářů, je obtížné řešit během čištění úloh a úloh. Používejte spíše symbolické odkazy (soft-links) než pevné odkazy.

- **Shromažďovat protokoly agenta dávky, pokud dojde k problému** Pokud zjistíte problém týkající se chování uzlu nebo úlohy spuštěné na uzlu, je doporučeno shromažďovat protokoly agent a batch před zrušením přidělení uzly v otázce. Protokoly agenta batch lze shromažďovat pomocí rozhraní API protokolů služby Upload Batch. Tyto protokoly mohou být dodány jako součást lístku podpory společnosti Microsoft a pomohou s řešením potíží a řešením problémů.

## <a name="security"></a>Zabezpečení

### <a name="security-isolation"></a>Izolace zabezpečení

Pro účely izolace, pokud váš scénář vyžaduje izolaci úlohy od sebe navzájem, pak byste měli izolovat tyto úlohy tím, že je v samostatných fondech. Fond je hranice izolace zabezpečení v batch a ve výchozím nastavení dva fondy nejsou viditelné nebo mohou komunikovat mezi sebou. Nepoužívejte samostatné účty Batch jako prostředek izolace.

## <a name="moving"></a>Pohybující

### <a name="move-batch-account-across-regions"></a>Přesunout dávkový účet mezi oblastmi

Existují různé scénáře, ve kterých byste chtěli přesunout existující dávkový účet z jedné oblasti do druhé. Můžete se například přesunout do jiné oblasti jako součást plánování zotavení po havárii.

Účty Azure Batch nelze přesunout z jedné oblasti do druhé. Šablonu Azure Resource Manager však můžete použít k exportu existující konfigurace účtu Batch.  Potom můžete zinscenovat prostředek v jiné oblasti exportem účtu Batch do šablony, úpravou parametrů tak, aby odpovídaly cílové oblasti, a potom šablonu nasadit do nové oblasti. Po nahrání šablony do nové oblasti budete muset znovu vytvořit certifikáty, plány úloh a balíčky aplikací. Chcete-li potvrdit změny a dokončit přesun účtu Dávka, nezapomeňte odstranit původní účet nebo skupinu prostředků batch.

Další informace o Správci prostředků a šablonách najdete v [tématu Úvodní příručka: Vytvoření a nasazení šablon Azure Resource Manageru pomocí portálu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

## <a name="connectivity-to-the-batch-service"></a>Připojení k dávkové službě

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Skupiny zabezpečení sítě (NSG) a uživatelem definované trasy (UDR)

Při zřizování [fondů dávek ve virtuální síti](batch-virtual-network.md)se ujistěte, `BatchNodeManagement` že pečlivě dodržujete pokyny týkající se použití servisního štítku, portů, protokolů a směru pravidla.
Použití servisního štítku je vysoce doporučeno a nikoli základní ip adresy služby Batch, protože se mohou v průběhu času měnit. Přímé použití IP adres služby Batch se může projevit jako nestabilita, přerušení nebo výpadky pro fondy dávek, protože služby Batch aktualizují IP adresy používané v průběhu času. Pokud v současné době používáte ip adresy služby Batch v pravidlech skupiny nsg, doporučujeme přepnout na použití servisního označení.

U uživatelem definovaných tras se ujistěte, že máte v systému pravidelné aktualizace IP adres dávkové služby ve směrovací tabulce, protože se v průběhu času mění. Informace o tom, jak získat seznam IP adres služby Batch, naleznete [v místním tématu Service Tags](../virtual-network/service-tags-overview.md). Ip adresy dávkové služby budou `BatchNodeManagement` přidruženy k výrobnímu tagu (nebo k místní variantě, která odpovídá oblasti dávkového účtu).

### <a name="honoring-dns"></a>Ctít DNS

Ujistěte se, že vaše systémy respektují dns time-to-live (TTL) pro adresu URL služby batch účtu. Dále zajistěte, aby klienti služby Batch a další mechanismy připojení ke službě Batch nespoléhali na adresy IP.

Pokud vaše požadavky obdrží odpovědi HTTP na úrovni 5xx a v odpovědi je hlavička "Připojení: zavřít", měl by klient služby Batch dodržovat doporučení zavřením existujícího připojení, opětovným vyřešením dns pro adresu URL služby Batch a pokusem o následující požadavky na nové připojení.

### <a name="retrying-requests-automatically"></a>Automatické opakování požadavků

Ujistěte se, že klienti služby Batch mají vhodné zásady opakování, aby mohli automaticky opakovat vaše požadavky, a to i během běžného provozu a nikoli výhradně během období údržby servisu. Tyto zásady opakování by měly mít interval nejméně 5 minut. Možnosti automatického opakování jsou k dispozici s různými sadami Batch SDK, jako je například [třída .NET RetryPolicyProvider](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet).

