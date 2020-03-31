---
title: Kontrola chyb fondu a uzlu – Azure Batch
description: Tento článek popisuje operace na pozadí, které mohou nastat, spolu s chybami ke kontrole a jak se jim vyhnout při vytváření fondů a uzlů.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: a68d812a044c776819d169d5bf179f011d06390f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472941"
---
# <a name="check-for-pool-and-node-errors"></a>Kontrola chyb fondu a uzlu

Když vytváříte a spravujete fondy Azure Batch, některé operace se stanou okamžitě. Některé operace jsou však asynchronní a spustit na pozadí, trvá několik minut na dokončení.

Zjišťování selhání pro operace, které probíhají okamžitě, je jednoduché, protože všechny chyby jsou vráceny okamžitě rozhraní API, rozhraní CLI nebo ui.

Tento článek popisuje operace na pozadí, které mohou nastat pro fondy a uzly fondu. Určuje, jak lze zjistit a vyhnout se chybám.

## <a name="pool-errors"></a>Chyby fondu

### <a name="resize-timeout-or-failure"></a>Změna velikosti časového opojení nebo selhání

Při vytváření nového fondu nebo změna velikosti existujícího fondu zadáte cílový počet uzlů.  Operace vytvoření nebo změny velikosti se dokončí okamžitě, ale skutečné přidělení nových uzlů nebo odebrání existujících uzlů může trvat několik minut.  Časový čas změny velikosti zadáte v [rozhraní API pro vytvoření](https://docs.microsoft.com/rest/api/batchservice/pool/add) nebo [velikost.](https://docs.microsoft.com/rest/api/batchservice/pool/resize) Pokud Batch nemůže získat cílový počet uzlů během období časového období změny velikosti fondu přejde do ustáleného stavu a hlásí chyby změny velikosti.

Vlastnost [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) pro poslední vyhodnocení uvádí chyby, ke kterým došlo.

Mezi běžné příčiny chyb změny velikosti patří:

- Časový čas změny velikosti je příliš krátký.
  - Ve většině případů je výchozí časový limit 15 minut dostatečně dlouhý pro přidělení nebo odebrání uzlů fondu.
  - Pokud přidělujete velký počet uzlů, doporučujeme nastavit časový rozsah změny velikosti na 30 minut. Například když převádíte velikost na více než 1 000 uzlů z image Azure Marketplace nebo na více než 300 uzlů z vlastní image virtuálního počítače.
- Nedostatečná kvóta jádra
  - Účet Batch je omezen v počtu jader, které může přidělit ve všech fondech. Dávka zastaví přidělování uzlů, jakmile bylo dosaženo této kvóty. [Můžete zvýšit](https://docs.microsoft.com/azure/batch/batch-quota-limit) kvótu jádra tak, aby Batch můžete přidělit více uzlů.
- Nedostatek IP programů podsítě, pokud [je fond ve virtuální síti](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Podsíť virtuální sítě musí mít dostatek nepřiřazených adres IP, které budou přidělovat každému požadovanému uzlu fondu. V opačném případě nelze vytvořit uzly.
- Nedostatek prostředků, pokud [je fond ve virtuální síti](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Můžete vytvořit prostředky, jako jsou vyvažovače zatížení, veřejné IP adresy a skupiny zabezpečení sítě ve stejném předplatném jako účet Batch. Zkontrolujte, zda kvóty předplatného jsou dostatečné pro tyto prostředky.
- Velké fondy s vlastními image virtuálních můník
  - Velké fondy, které používají vlastní image virtuálních počítače může trvat déle přidělit a změnit velikost časové opozí může dojít.  Doporučení týkající se omezení a konfigurace [najdete v tématu Vytvoření fondu s Galerií sdílených obrázků.](batch-sig-images.md)

### <a name="automatic-scaling-failures"></a>Selhání automatického škálování

Můžete také nastavit Azure Batch automaticky škálovat počet uzlů ve fondu. Definujete parametry [vzorce automatického škálování pro fond](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). Služba Batch používá vzorec k pravidelnému vyhodnocení počtu uzlů ve fondu a nastavení nového cílového čísla. Může dojít k následujícím typům problémů:

- Automatické škálování vyhodnocení nezdaří.
- Výsledná operace změny velikosti se nezdaří a časový čas.
- Problém se vzorcem automatického škálování vede k nesprávným cílovým hodnotám uzlu. Změna velikosti funguje nebo časový čas.

Můžete získat informace o poslední automatické škálování hodnocení pomocí [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) vlastnost. Tato vlastnost hlásí čas vyhodnocení, hodnoty a výsledek a všechny chyby výkonu.

Změna [velikosti fondu complete událost](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) zachycuje informace o všech hodnocení.

### <a name="delete"></a>Odstranění

Když odstraníte fond, který obsahuje uzly, první Batch odstraní uzly. Potom odstraní samotný objekt fondu. Může trvat několik minut pro uzly fondu, které mají být odstraněny.

Batch nastaví [stav fondu](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) na **odstranění** během procesu odstranění. Volající aplikace může zjistit, pokud odstranění fondu trvá příliš dlouho pomocí **state** a **stateTransitionTime** vlastnosti.

## <a name="pool-compute-node-errors"></a>Chyby výpočetního uzlu fondu

I v případě, že Batch úspěšně přiděluje uzly ve fondu, různé problémy mohou způsobit, že některé uzly nejsou v pořádku a nelze spustit úlohy. Těmto uzlům stále vznikají poplatky, takže je důležité zjistit problémy, abyste se vyhnuli placení za uzly, které nelze použít. Kromě běžných chyb uzlů je znalost aktuálního [stavu úlohy](/rest/api/batchservice/job/get#jobstate) užitečná pro řešení potíží.

### <a name="start-task-failures"></a>Spuštění selhání úloh

Můžete chtít zadat volitelnou [počáteční úlohu](/rest/api/batchservice/pool/add#starttask) pro fond. Stejně jako u všech úloh můžete ke stažení z úložiště použít příkazový řádek a soubory prostředků. Počáteční úloha je spuštěna pro každý uzel po jeho spuštění. Vlastnost **waitForSuccess** určuje, zda batch čeká, dokud se úloha zahájení úspěšně nedokončí, než naplánuje všechny úkoly do uzlu.

Co když jste nakonfigurovali uzel tak, aby čekal na úspěšné dokončení úkolu zahájení, ale počáteční úloha se nezdaří? V takovém případě uzel nebude použitelný, ale bude stále účtovány poplatky.

Selhání úloh spuštění můžete zjistit pomocí vlastností [result](/rest/api/batchservice/computenode/get#taskexecutionresult) a [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) vlastností uzlu [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) nejvyšší úrovně.

Úloha spuštění se nezdařilo také způsobí, že Batch nastavit [stav](/rest/api/batchservice/computenode/get#computenodestate) uzlu **starttaskfailed,** pokud **waitForSuccess** byla nastavena na **hodnotu true**.

Stejně jako u všech úkolů může existovat mnoho příčin selhání počáteční úlohy.  Chcete-li vyřešit potíže, zkontrolujte stdout, stderr a další soubory protokolu specifické pro úlohy.

Počáteční úkoly musí být znovu účastníkem, protože je možné, že počáteční úloha je spuštěna vícekrát na stejném uzlu; počáteční úloha je spuštěna při opětovném zobrazení nebo restartování uzlu. Ve výjimečných případech bude počáteční úloha spuštěna poté, co událost způsobila restartování uzlu, kde byl jeden z operačního systému nebo dočasných disků znovu zobrazen, zatímco druhý nebyl. Vzhledem k tomu, batch spuštění úlohy (stejně jako všechny dávkové úlohy) spustit z dočasného disku, to není obvykle problém, ale v některých případech, kdy je počáteční úloha instalace aplikace na disk operačního systému a zachování dalších dat na dočasný disk, to může způsobit problémy, protože věci jsou nesynchronizované. Chraňte aplikaci odpovídajícím způsobem, pokud používáte oba disky.

### <a name="application-package-download-failure"></a>Selhání stahování balíčku aplikace

Můžete zadat jeden nebo více balíčků aplikací pro fond. Batch stáhne zadané soubory balíčků do každého uzlu a rozbalí soubory po spuštění uzlu, ale před naplánováním úloh. Je běžné používat příkazový řádek úlohy start ve spojení s balíčky aplikací. Chcete-li například zkopírovat soubory do jiného umístění nebo spustit instalační program.

Vlastnost [chyb uzlu](/rest/api/batchservice/computenode/get#computenodeerror) hlásí selhání stažení a odkomprimování balíčku aplikace; stav uzlu je nastaven na **nepoužitelný**.

### <a name="container-download-failure"></a>Selhání stahování kontejneru

Můžete zadat jeden nebo více odkazů kontejnerů ve fondu. Batch stáhne zadané kontejnery do každého uzlu. Vlastnost [chyb uzlu](/rest/api/batchservice/computenode/get#computenodeerror) hlásí selhání stažení kontejneru a nastaví stav uzlu na **nepoužitelný**.

### <a name="node-in-unusable-state"></a>Uzel v nepoužitelném stavu

Azure Batch může nastavit [stav uzlu](/rest/api/batchservice/computenode/get#computenodestate) na **nepoužitelný** z mnoha důvodů. Se stavem uzlu nastaveným na **nepoužitelný**, úkoly nelze naplánovat do uzlu, ale stále se účtuje poplatky.

Uzly v **nepoužitelném** stavu, ale bez [chyb](/rest/api/batchservice/computenode/get#computenodeerror) znamená, že Batch není schopen komunikovat s virtuálním virtuálním ms. V tomto případě Batch vždy pokusí obnovit virtuální ho. Batch se automaticky nepokusí obnovit virtuální chřtinové služby, kterým se nepodařilo nainstalovat balíčky aplikací nebo kontejnery, přestože jejich stav je **nepoužitelný**.

Pokud Batch může určit příčinu, vlastnost [chyb uzlu](/rest/api/batchservice/computenode/get#computenodeerror) ji hlásí.

Mezi další příčiny **nepoužitelných** uzlů patří:

- Vlastní obrázek virtuálního virtuálního klienta je neplatný. Například obrázek, který není správně připraven.

- Virtuální virtuální byl přesunut z důvodu selhání infrastruktury nebo upgradu nízké úrovně. Batch obnoví uzel.

- Image virtuálního počítače byla nasazená na hardware, který ji nepodporuje. Například při pokusu o spuštění image CentOS HPC na [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) virtuálním počítači.

- Virtuální počítače jsou ve [virtuální síti Azure](batch-virtual-network.md)a provoz byl zablokován na klíčové porty.

- Virtuální počítače jsou ve virtuální síti, ale odchozí provoz do úložiště Azure je blokován.

- Virtuální počítače jsou ve virtuální síti s konfigurací DNS zákazníka a DNS server nemůže vyřešit úložiště Azure.

### <a name="node-agent-log-files"></a>Soubory protokolu agenta uzlu

Proces agenta batch, který běží na každém uzlu fondu, může poskytnout soubory protokolu, které mohou být užitečné, pokud potřebujete kontaktovat podporu týkající se problému s uzlem fondu. Soubory protokolu pro uzel lze nahrát prostřednictvím portálu Azure, Batch Explorer nebo [rozhraní API](/rest/api/batchservice/computenode/uploadbatchservicelogs). Je užitečné nahrát a uložit soubory protokolu. Poté můžete odstranit uzel nebo fond a ušetřit náklady na spuštěné uzly.

### <a name="node-disk-full"></a>Disk uzlu je plný.

Dočasná jednotka pro virtuální virtuální uzel fondu používá Batch pro soubory úloh, soubory úloh a sdílené soubory.

- Soubory balíčků aplikací
- Soubory prostředků úkolu
- Soubory specifické pro aplikaci stažené do jedné ze složek Batch
- Stdout a stderr soubory pro každé spuštění aplikace úlohy
- Výstupní soubory specifické pro aplikaci

Některé z těchto souborů jsou zapsány pouze jednou při vytvoření uzlů fondu, jako jsou balíčky aplikací fondu nebo soubory prostředků spuštění fondu úlohy. I v případě, že pouze jednou při vytvoření uzlu, pokud tyto soubory jsou příliš velké, které by mohly vyplnit dočasnou jednotku.

Ostatní soubory jsou zapsány pro každou úlohu, která je spuštěna na uzlu, například stdout a stderr. Pokud je na stejném uzlu spuštěn velký počet úloh nebo jsou soubory úloh příliš velké, mohou zaplnit dočasnou jednotku.

Velikost dočasné jednotky závisí na velikosti virtuálního počítače. Při výběru velikosti virtuálního počítače je třeba zajistit, aby dočasná jednotka byla dostatek místa.

- Na webu Azure Portal při přidávání fondu se dá zobrazit úplný seznam velikostí virtuálních počítačů a je sloupec Velikost disku prostředků.
- Články popisující všechny velikosti virtuálních zařízení mají tabulky se sloupcem Dočasné úložiště; Například [velikosti virtuálních počítačů optimalizované pro výpočetní výkon](/azure/virtual-machines/windows/sizes-compute)

U souborů zapsaných jednotlivými úkoly lze pro každý úkol zadat čas uchování, který určuje, jak dlouho budou soubory úloh uchovávány před automatickým vyčištěním. Retenční čas lze zkrátit, aby se snížily požadavky na úložiště.


Pokud dočasný disk vyčerpá místo (nebo je velmi blízko k vyčerpání místa), uzel se přesune do [stavu Nepoužitelný](/rest/api/batchservice/computenode/get#computenodestate) a bude hlášena chyba uzlu s udáním, že disk je plný.

### <a name="what-to-do-when-a-disk-is-full"></a>Co dělat, když je disk plný

Zjistěte, proč je disk plný: Pokud si nejste jisti, co zabírá místo v uzlu, doporučujeme vzdálenit uzel a ručně zkoumat, kde se místo dostalo. Rozhraní [API Dávkových seznamů souborů](https://docs.microsoft.com/rest/api/batchservice/file/listfromcomputenode) můžete také využít ke kontrole souborů ve spořitelé složky batch (například výstupy úloh). Všimněte si, že toto rozhraní API obsahuje pouze soubory v adresářích spravovaných dávkou a pokud vaše úkoly vytvořily soubory jinde, neuvidíte je.

Ujistěte se, že všechna data, která potřebujete, byla načtena z uzlu nebo odeslána do trvalého úložiště. Všechny zmírnění problému celého disku zahrnují odstranění dat, aby se uvolnilo místo.

### <a name="recovering-the-node"></a>Obnovení uzlu

1. Pokud váš fond je [C.loudServiceConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) fondu, můžete re-image uzlu prostřednictvím [dávky re-image ROZHRANÍ API](https://docs.microsoft.com/rest/api/batchservice/computenode/reimage). Tím se vyčistí celý disk. Re-image není aktuálně podporována pro fondy [VirtualMachineConfiguration.](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)

2. Pokud je váš fond [VirtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration), můžete uzel z fondu odebrat pomocí [rozhraní API pro odebrání uzlů](https://docs.microsoft.com/rest/api/batchservice/pool/removenodes). Poté můžete znovu pěstovat fond, abyste nahradili špatný uzel novým.

3.  Odstraňte staré dokončené úlohy nebo staré dokončené úkoly, jejichž data úkolů jsou stále v uzlech. Nápovědu k datům úloh a úkolů v uzlech můžete hledat v [kolekci RecentTasks](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskinformation) v uzlu nebo v [souborech v uzlu](https://docs.microsoft.com//rest/api/batchservice/file/listfromcomputenode). Odstraněním úlohy odstraníte všechny úkoly v úloze a odstranění mačkání mne v úloze spustí data v adresářích úloh v uzlu, který má být odstraněn, čímž se uvolní místo. Jakmile uvolníte dostatek místa, restartujte uzel a měl by se přesunout ze stavu "Nepoužitelný" a znovu do "Idle".

## <a name="next-steps"></a>Další kroky

Zkontrolujte, zda jste nastavili aplikaci pro implementaci komplexní kontroly chyb, zejména pro asynchronní operace. Může být důležité okamžitě zjistit a diagnostikovat problémy.
