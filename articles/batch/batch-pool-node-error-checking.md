---
title: Vyhledat chyby fondu a uzlů
description: Tento článek se zabývá operacemi na pozadí, ke kterým může dojít, a s chybami pro kontrolu a jejich zamezení při vytváření fondů a uzlů.
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: how-to
ms.openlocfilehash: 519b357e4e5fde30221f7dc804bb848ecec9704c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85979913"
---
# <a name="check-for-pool-and-node-errors"></a>Vyhledat chyby fondu a uzlů

Když vytváříte a spravujete fondy Azure Batch, dojde k okamžitému provedení některých operací. Některé operace jsou však asynchronní a spouštěny na pozadí, přičemž dokončení trvá několik minut.

Zjištění selhání operací, které jsou okamžitě prováděné, je jednoduché, protože jakékoli chyby jsou vráceny hned rozhraním API, CLI nebo UI.

Tento článek popisuje operace na pozadí, ke kterým může dojít pro fondy a uzly fondu. Určuje, jak můžete detekovat a vyhnout se chybám.

## <a name="pool-errors"></a>Chyby fondu

### <a name="resize-timeout-or-failure"></a>Změnit časový limit nebo selhání

Při vytváření nového fondu nebo změně velikosti existujícího fondu zadejte cílový počet uzlů.  Operace vytvoření nebo změny velikosti se okamžitě dokončí, ale skutečné přidělení nových uzlů nebo odebrání stávajících uzlů může trvat několik minut.  Časový limit pro změnu velikosti můžete zadat v rozhraní API pro [Vytvoření](/rest/api/batchservice/pool/add) nebo [změnu velikosti](/rest/api/batchservice/pool/resize) . Pokud dávka nemůže získat cílový počet uzlů během časového limitu změny velikosti, fond přejde do stabilního stavu a sestavy změní velikost.

Vlastnost [ResizeError](/rest/api/batchservice/pool/get#resizeerror) pro nejnovější hodnocení vypíše seznam chyb, ke kterým došlo.

Mezi běžné příčiny chyb změny velikosti patří:

- Časový limit změny velikosti je moc krátký.
  - Ve většině případů je výchozí časový limit 15 minut dostatečně dlouhý pro přidělení nebo odebrání uzlů fondu.
  - Pokud přidělujete velký počet uzlů, doporučujeme nastavit časový limit pro změnu velikosti na 30 minut. Například když měníte velikost na více než 1 000 uzlů z bitové kopie Azure Marketplace nebo na více než 300 uzlů z vlastní image virtuálního počítače.
- Nedostatečná kvóta jádra
  - Účet Batch je omezený počtem jader, které může přidělit napříč všemi fondy. Dávka ukončí přidělování uzlů po dosažení této kvóty. [Můžete zvýšit](./batch-quota-limit.md) základní kvótu, aby dávka mohla přidělit více uzlů.
- Nedostatečné IP adresy podsítě [, pokud je fond ve virtuální síti](./batch-virtual-network.md)
  - Podsíť virtuální sítě musí mít dostatek nepřiřazených IP adres, které by bylo možné přidělit každému požadovanému uzlu fondu. V opačném případě nelze uzly vytvořit.
- Nedostatek prostředků, pokud [je fond ve virtuální síti](./batch-virtual-network.md)
  - V rámci stejného předplatného jako účet Batch můžete vytvořit prostředky, jako jsou třeba nástroje pro vyrovnávání zatížení, veřejné IP adresy a skupiny zabezpečení sítě. Ověřte, jestli jsou pro tyto prostředky dostatečné kvóty předplatného.
- Velké fondy s vlastními imagemi virtuálních počítačů
  - U velkých fondů, které používají vlastní image virtuálních počítačů, může docházet k přidělení a změně velikosti časových limitů.  Doporučení týkající se omezení a konfigurace najdete v tématu [Vytvoření fondu pomocí Galerie sdílených imagí](batch-sig-images.md) .

### <a name="automatic-scaling-failures"></a>Automatické škálování – chyby

Můžete také nastavit Azure Batch pro automatické škálování počtu uzlů ve fondu. Definujete parametry pro [vzorec automatického škálování pro fond](./batch-automatic-scaling.md). Služba Batch používá vzorec k pravidelnému vyhodnocení počtu uzlů ve fondu a k nastavení nového cílového čísla. Může dojít k následujícím typům problémů:

- Automatické vyhodnocení měřítka se nezdařilo.
- Výsledkem operace změny velikosti je chyba a vypršel časový limit.
- Problém se vzorcem automatického škálování vede k neplatným cílovým hodnotám uzlů. Velikost buď funguje, nebo je mimo časový limit.

Informace o posledním automatickém vyhodnocení měřítka můžete získat pomocí vlastnosti [autoScaleRun](/rest/api/batchservice/pool/get#autoscalerun) . Tato vlastnost oznamuje dobu hodnocení, hodnoty a výsledek a všechny chyby výkonu.

[Událost dokončení změny velikosti fondu](./batch-pool-resize-complete-event.md) zachycuje informace o všech hodnoceních.

### <a name="delete"></a>Odstranit

Když odstraníte fond, který obsahuje uzly, první Batch tyto uzly odstraní. Pak odstraní samotný objekt fondu. Odstranění uzlů fondu může trvat několik minut.

Batch nastaví [stav fondu](/rest/api/batchservice/pool/get#poolstate) na **odstranění** během procesu odstranění. Volající aplikace může zjistit, zda odstranění fondu trvá příliš dlouho pomocí vlastností **State** a **stateTransitionTime** .

## <a name="pool-compute-node-errors"></a>Chyby výpočetního uzlu fondu

I když Batch úspěšně přiděluje uzly ve fondu, můžou různé problémy způsobit, že některé uzly nejsou v pořádku a nemůžou spouštět úlohy. Těmto uzlům se pořád účtují poplatky, takže je důležité detekovat problémy, abyste se vyhnuli placení uzlů, které se nedají použít. Kromě běžných chyb uzlů je důležité vědět, že aktuální [stav úlohy](/rest/api/batchservice/job/get#jobstate) je užitečný při řešení potíží.

### <a name="start-task-failures"></a>Selhání úlohy spuštění

Možná budete chtít zadat volitelný [spouštěcí úkol](/rest/api/batchservice/pool/add#starttask) pro fond. Stejně jako u jakékoli úlohy můžete použít příkazový řádek a soubory prostředků ke stažení z úložiště. Spouštěcí úkol se spustí pro každý uzel po jeho spuštění. Vlastnost **waitForSuccess** určuje, zda bude dávka čekat, než se úloha spuštění dokončí úspěšně předtím, než naplánuje úlohy na uzel.

Co dělat v případě, že jste uzel nakonfigurovali tak, aby čekal na úspěšné dokončení úlohy spuštění, ale počáteční úkol se nezdařil? V takovém případě se uzel nebude používat, ale přesto se vám budou účtovat poplatky.

Chyby spuštění úlohy můžete detekovat pomocí vlastností [Result](/rest/api/batchservice/computenode/get#taskexecutionresult) a  [FailureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) vlastnosti uzlu [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) nejvyšší úrovně.

Neúspěšná úloha spuštění také způsobí, že služba Batch nastaví [stav](/rest/api/batchservice/computenode/get#computenodestate) uzlu na **starttaskfailed** , pokud byla  **waitForSuccess** nastavena na **hodnotu true**.

Stejně jako u jakékoli úlohy může být mnoho příčin neúspěšného spuštění úlohy.  Pokud chcete řešit potíže, podívejte se na záznam stdout, stderr a všechny další soubory protokolu pro konkrétní úlohy.

Spouštěcí úlohy je nutné znovu vytvořit, protože je možné, že je spouštěcí úkol ve stejném uzlu několikrát spuštěný. spouštěcí úkol se spustí, když se uzel znovu spustí nebo restartuje. Ve výjimečných případech se spustí spouštěcí úkol po události, která způsobila restart uzlu, kdy se jeden z operačních systémů nebo dočasné disky obnovil z image. Vzhledem k tomu, že úlohy spuštění služby Batch (stejně jako všechny úlohy služby Batch) běží z dočasného disku, obvykle to není problém, ale v některých případech, kdy spouštěcí úkol instaluje aplikaci na disk s operačním systémem a udržuje další data na dočasném disku, může to způsobit problémy, protože věci nejsou synchronizované. Pokud používáte oba disky, zabezpečte svou aplikaci odpovídajícím způsobem.

### <a name="application-package-download-failure"></a>Chyba stahování balíčku aplikace

Můžete určit jeden nebo více balíčků aplikací pro fond. Batch stáhne zadané soubory balíčku do každého uzlu a dekomprimuje soubory po spuštění uzlu, ale před naplánováním úkolů. V kombinaci s balíčky aplikací je běžné použít příkazový řádek spouštěcího úkolu. Například ke zkopírování souborů do jiného umístění nebo ke spuštění instalačního programu.

Vlastnost [Errors](/rest/api/batchservice/computenode/get#computenodeerror) uzlu hlásí selhání stažení a zrušení komprimace balíčku aplikace. stav uzlu je nastaven na **nepoužitelné**.

### <a name="container-download-failure"></a>Selhání stahování kontejneru

Můžete zadat jeden nebo více odkazů na kontejner ve fondu. Batch stáhne zadané kontejnery do každého uzlu. Vlastnost [Errors](/rest/api/batchservice/computenode/get#computenodeerror) uzlu hlásí selhání stažení kontejneru a nastaví stav uzlu na **Nepoužito**.

### <a name="node-in-unusable-state"></a>Uzel v nepoužitelném stavu

Azure Batch může nastavit [stav uzlu](/rest/api/batchservice/computenode/get#computenodestate) na **nepoužitelné** z mnoha důvodů. Když je stav uzlu nastavený na **nepoužitelné**, úlohy nejde naplánovat na uzel, ale pořád se tam účtují poplatky.

Uzly v **nepoužitelném** stavu, ale bez [chyb](/rest/api/batchservice/computenode/get#computenodeerror) znamená, že Batch nemůže komunikovat s virtuálním počítačem. V takovém případě se dávka vždy pokusí o obnovení virtuálního počítače. Dávka se automaticky nepokusí obnovit virtuální počítače, které selhaly při instalaci balíčků nebo kontejnerů aplikací, i když jejich stav není **použitelný**.

Pokud Batch může určit příčinu, vlastnost [chyb](/rest/api/batchservice/computenode/get#computenodeerror) uzlů ji ohlásí.

Mezi další příklady příčin **nepoužitelných** uzlů patří:

- Vlastní image virtuálního počítače je neplatná. Například obrázek, který není správně připraven.

- Virtuální počítač se přesune kvůli selhání infrastruktury nebo upgradu na nižší úrovni. Batch obnoví uzel.

- Image virtuálního počítače se nasadila na hardware, který ho nepodporuje. Například při pokusu o spuštění image CentOS HPC na [Standard_D1_v2m](../virtual-machines/dv2-dsv2-series.md) virtuálním počítači.

- Virtuální počítače jsou ve [virtuální síti Azure](batch-virtual-network.md)a provoz se zablokoval na porty klíčů.

- Virtuální počítače jsou ve virtuální síti, ale odchozí provoz do služby Azure Storage je blokovaný.

- Virtuální počítače jsou ve virtuální síti s konfigurací DNS zákazníka a server DNS nemůže přeložit službu Azure Storage.

### <a name="node-agent-log-files"></a>Soubory protokolu agenta uzlu

Proces dávkového agenta, který běží na jednotlivých uzlech fondu, může poskytovat soubory protokolu, které můžou být užitečné, pokud potřebujete kontaktovat podporu týkající se problému s uzlem fondu. Soubory protokolu pro uzel lze nahrát prostřednictvím Azure Portal, Batch Explorer nebo [rozhraní API](/rest/api/batchservice/computenode/uploadbatchservicelogs). Je vhodné nahrávat a ukládat soubory protokolu. Potom můžete uzel nebo fond odstranit, abyste ušetřili náklady na běžící uzly.

### <a name="node-disk-full"></a>Plný disk uzlu

Dočasná jednotka pro virtuální počítač uzlu fondu je používána dávkou pro soubory úloh, soubory úloh a sdílené soubory.

- Soubory balíčků aplikací
- Soubory prostředků úkolu
- Soubory specifické pro aplikaci se stáhly do jedné ze složek Batch.
- Stdout a stderr soubory pro každou úlohu spuštění aplikace
- Výstupní soubory specifické pro aplikaci

Některé z těchto souborů se napíší jenom jednou, když se vytvoří uzly fondu, jako jsou balíčky aplikací fondu nebo zdrojové soubory úloh spuštění fondu. I v případě, že se při vytvoření uzlu napíše jenom jednou, pokud jsou tyto soubory moc velké, můžou doplňovat dočasné jednotky.

Pro každý úkol, který běží na uzlu, jako je stdout a stderr, se zapisují jiné soubory. Pokud je ve stejném uzlu spuštěn velký počet úloh nebo jsou soubory úlohy příliš velké, může doplňovat dočasnou jednotku.

Velikost dočasné jednotky závisí na velikosti virtuálního počítače. Jediným aspektem při výběru velikosti virtuálního počítače je zajistit, aby na dočasném disku bylo dost místa.

- V Azure Portal při přidávání fondu je možné zobrazit úplný seznam velikostí virtuálních počítačů a sloupec "velikost disku prostředku".
- Články popisující všechny velikosti virtuálních počítačů mají tabulky se sloupcem dočasné úložiště; například [výpočetní optimalizované velikosti virtuálních počítačů](../virtual-machines/sizes-compute.md)

Pro soubory vytvářené jednotlivými úlohami je možné určit dobu uchovávání dat pro každou úlohu, která určuje, jak dlouho jsou soubory úlohy zachované, než se automaticky vyčistí. Dobu uchovávání můžete snížit, abyste snížili požadavky na úložiště.


Pokud dojde volné místo na dočasném disku (nebo se velmi blízko z místa), uzel se přesune do [nepoužitelného](/rest/api/batchservice/computenode/get#computenodestate) stavu a bude hlášena chyba uzlu s oznámením, že disk je plný.

### <a name="what-to-do-when-a-disk-is-full"></a>Co dělat, když je disk plný

Určete, proč je disk plný: Pokud si nejste jistí, co zabírá místo v uzlu, doporučujeme vzdálené řízení s uzlem a prozkoumat ručně, kde se místo dokončí. Můžete také využít [rozhraní API soubory služby Batch](/rest/api/batchservice/file/listfromcomputenode) k prohlédnutí souborů ve spravovaných složkách služby Batch (například výstupy úloh). Všimněte si, že toto rozhraní API obsahuje jenom soubory ve spravovaných adresářích Batch, a pokud vaše úkoly vytvořily soubory jinde, neuvidíte je.

Ujistěte se, že všechna data, která potřebujete, byla načtena z uzlu nebo odeslána do trvalého úložiště. Veškerá zmírnění problému s plným diskem zahrnuje odstranění dat, aby se uvolnilo místo.

### <a name="recovering-the-node"></a>Obnovování uzlu

1. Pokud je váš fond fondem [C. loudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) , můžete uzel znovu vytvořit pomocí [rozhraní API služby Batch pro opětovné](/rest/api/batchservice/computenode/reimage)vytvoření bitové kopie. Tím se vyčistí celý disk. Pro [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) fondy se v současné době nepodporuje znovu image.

2. Pokud je váš fond [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration), můžete odebrat uzel z fondu pomocí [rozhraní API odebrat uzly](/rest/api/batchservice/pool/removenodes). Pak můžete znovu zvětšit fond a nahradit špatný uzel jeho novou.

3.  Odstraňte staré dokončené úlohy nebo staré dokončené úlohy, jejichž data úkolů jsou stále na uzlech. V případě, že jsou data úloh a úkolů na uzlech, která jsou na uzlech, můžete hledat v [kolekci RecentTasks](/rest/api/batchservice/computenode/get#taskinformation) v uzlu nebo v [souborech na uzlu](/rest/api/batchservice/file/listfromcomputenode). Odstraněním úlohy dojde k odstranění všech úkolů v úloze a odstranění úloh v úloze aktivuje data v adresářích úloh na uzlu, aby se odstranila. tím se uvolní místo. Až uvolníte dostatek místa, restartujte uzel a měl by přesunout nečinný stav a nečinné znovu.

## <a name="next-steps"></a>Další kroky

Ověřte, že jste nastavili aplikaci pro implementaci komplexní kontroly chyb, zejména pro asynchronní operace. Může být zásadní, aby mohl rychle detekovat a diagnostikovat problémy.
