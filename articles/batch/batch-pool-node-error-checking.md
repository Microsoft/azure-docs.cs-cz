---
title: Vyhledejte chyby fondu a uzel – Azure Batch
description: Zkontrolujte chyby a jak se jim vyhnout při vytváření fondů a uzlů
services: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: 4e1e645c25d2f1e49e222e39ecd719a414e1404e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790465"
---
# <a name="check-for-pool-and-node-errors"></a>Vyhledejte chyby fondu a uzlu

Při vytváření a Správa fondů služby Azure Batch, některé operace dojde okamžitě. Nicméně některé operace jsou asynchronní a běhu na pozadí. Může trvat několik minut.

Detekce selhání pro operace, které se projeví okamžitě je jednoduché, protože všechny chyby se vrátí okamžitě rozhraní API, rozhraní příkazového řádku nebo uživatelského rozhraní.

Tento článek se týká operace na pozadí, které mohou nastat pro fondy a uzly fondu. Určuje, jak můžete zjišťovat a vyhněte se selhání.

## <a name="pool-errors"></a>Fond chyby

### <a name="resize-timeout-or-failure"></a>Časový limit pro změnu velikosti nebo selhání

Při vytváření nového fondu nebo změnu velikosti existujícího fondu, zadejte cílový počet uzlů.  Operace dokončena okamžitě, ale skutečný přidělení nové uzly nebo odebrání existujících uzlů může trvat několik minut.  Zadejte časový limit změny velikosti v [vytvořit](https://docs.microsoft.com/rest/api/batchservice/pool/add) nebo [změnit velikost](https://docs.microsoft.com/rest/api/batchservice/pool/resize) rozhraní API. Pokud Batch nelze získat cílového počtu uzlů během změny velikosti časový limit, zastaví operace. Fond přejde do stabilního stavu a sestavy, změňte jeho velikost chyby.

[ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) vlastnost posledního vyhodnocení sestavy s časovým limitem změny velikosti a jsou uvedeny všechny chyby, ke kterým došlo.

Vypršení časového limitu pro změnu velikosti mezi běžné příčiny patří:

- Změna velikosti vypršení časového limitu je příliš krátké.
  - Ve většině situací je dostatečně dlouho uzly fondu přidělené nebo odebrání výchozího časového limitu 15 minut.
  - Pokud jste přidělování velký počet uzlů, doporučujeme nastavit časový limit pro změnu velikosti na 30 minut. Například při jste se změna velikosti více než 1000 uzlů z image Azure Marketplace, nebo do více než 300 uzlů z vlastní image virtuálního počítače.
- Kvóta pro jádra nedostatečné
  - Účet Batch je omezený počet jader, která může přidělit u všech fondů. Batch se zastaví přidělování uzly, jakmile bylo dosaženo této kvóty. Můžete [zvýšit](https://docs.microsoft.com/azure/batch/batch-quota-limit) kvótu jader, aby této služby Batch můžete přidělit víc uzlů.
- Nedostatečná podsítě IP adres při [fond je ve virtuální síti](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Podsíť virtuální sítě musí mít dostatek nepřiřazených IP adres pro přidělení na každý uzel požadovaný fond. V opačném případě uzly nelze vytvořit.
- Nedostatek prostředků při [fond je ve virtuální síti](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Můžete například vytvořit prostředky, jako jsou nástroje pro vyrovnávání zatížení, veřejné IP adresy a skupiny zabezpečení sítě ve stejném předplatném jako účet Batch. Zkontrolujte, zda jsou pro tyto prostředky dostatečné kvóty předplatného.
- Velké fondy s vlastních imagí virtuálních počítačů
  - Velké fondů, které používají vlastní Image virtuálních počítačů může trvat déle, může dojít k přidělení a změňte jeho velikost vypršení časového limitu.  Zobrazit [použití vlastní image k vytvoření fondu virtuálních počítačů](https://docs.microsoft.com/azure/batch/batch-custom-images) doporučení týkající se omezení a konfigurace.

### <a name="automatic-scaling-failures"></a>Automatické škálování selhání

Můžete také nastavit pro automatické škálování počtu uzlů ve fondu Azure Batch. Nastavit parametry pro [vzorce pro fond automatického škálování](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). Používá služba Batch vzorec pravidelně vyhodnotit z počtu uzlů ve fondu a nastavit nový cílový počet. Může dojít k následujícím typům problémy:

- Automatické škálování vyhodnocení se nezdaří.
- Výsledný operace změny velikosti se nezdaří a vyprší časový limit.
- Problém s vzorec automatického škálování vede k nesprávné uzel cílové hodnoty. Změnu velikosti funguje nebo vyprší časový limit.

Informace o vyhodnocení posledního automatického škálování můžete získat pomocí [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) vlastnost. Tato vlastnost hlásí doba hodnocení, hodnoty a výsledek a všechny chyby výkonu.

[Fondu změnit velikost události dokončení](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) shromažďuje informace o všechna hodnocení.

### <a name="delete"></a>Odstranění

Při odstranění fondu, který obsahuje uzly první dávku odstraní uzly. Pak odstraní samotného objektu fondu. Může trvat několik minut, než uzly fondu, která se má odstranit.

Batch sad [fondu stavu](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) k **odstranění** během procesu odstraňování. Volající aplikace může zjistit, pokud se odstranění fondu trvá moc dlouho pomocí **stavu** a **stateTransitionTime** vlastnosti.

## <a name="pool-compute-node-errors"></a>Fond výpočetních uzlů chyby

I v případě, že Batch úspěšně přiděluje uzly ve fondu, může způsobit různé problémy, některé z uzlů na není v pořádku takže nepůjdou použít. Tyto uzly budou účtovat poplatky za. Je důležité ke zjišťování problémů, takže nejsou placení za nepoužitelný uzly.

### <a name="start-task-failure"></a>Selhání spuštění úlohy

Můžete zadat volitelný [spouštěcí úkol](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) pro fond. Stejně jako u jakékoli jiné úlohy, můžete použít příkazový řádek a soubory prostředků můžete stáhnout z úložiště. Spouštěcí úkol běží pro každý uzel je spuštění. **WaitForSuccess** vlastnost určuje, zda Batch počká, až na nich spouštěcí úkol se úspěšně dokončena předtím, než se naplánuje žádné úlohy na uzlu.

Co když nakonfigurujete uzlu čekání na dokončení úkolu spuštění úspěšné, ale úkolů selhalo spuštění? V takovém případě uzel nepoužitelný, ale stále se neúčtují poplatky za.

Selhání spuštění úkolů můžete zjistit pomocí [výsledek](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) a [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) vlastnosti na nejvyšší úrovni [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) vlastnost uzlu.

Neúspěšné spouštěcí úkol zároveň způsobí, že služby Batch k nastavení uzlu [stavu](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) k **starttaskfailed** Pokud nastavíte **waitForSuccess** k **true**.

Stejně jako u každého úkolu můžete mít řadu příčin neúspěšného úkolu spuštění.  Řešení potíží s, zkontrolujte standardního výstupu skriptu, stderr a všechny soubory další protokolu specifické pro úlohy.

### <a name="application-package-download-failure"></a>Chyba při stahování balíčku aplikace

Můžete určit jeden nebo více balíčků aplikací fondu. Služba batch stáhne soubory zadaného balíčku k jednotlivým uzlům a dekomprimuje soubory po spuštění uzlu, ale před úkoly jsou naplánovány. Je běžné použití ve spojení s balíčky aplikací příkazový řádek úkolu spuštění. Například můžete kopírovat soubory do jiného umístění nebo ke spuštění instalačního programu.

Uzel [chyby](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) vlastnost hlásí selhání při stažení a dekomprimovat balíček aplikace. Batch nastaví stav uzlu **nepoužitelné**.

### <a name="node-in-unusable-state"></a>Uzel v nepoužitelném stavu

Azure Batch může být nastavena [stav uzlu](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) k **nepoužitelné** z mnoha důvodů. Stav uzlu nastavte **nepoužitelné**, úlohy nelze naplánovat na uzlu, ale stále budou vám účtovány poplatky.

Batch se vždy pokusí obnovit nepoužitelné uzly, ale obnovení může nebo nemusí být možné v závislosti na příčinu.

Pokud služby Batch můžete určit příčinu, uzel [chyby](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) vlastnost podává hlášení.

Další příklady příčiny **nepoužitelné** uzly patří:

- Vlastní image virtuálního počítače je neplatný. Například obrázek, který je připraven není správně.
- Virtuální počítač je přesunuta z důvodu selhání infrastruktury nebo upgradu na nízké úrovni. Batch obnoví uzlu.

### <a name="node-agent-log-files"></a>Soubory protokolu agenta uzlu

Proces agenta služby Batch, na kterém běží na všech uzlech fondu můžete zadat soubory protokolů, které by mohly být užitečné v případě, že budete muset požádat podporu o problém s uzlem fondu. Soubory protokolu pro uzel můžete odeslat prostřednictvím webu Azure portal, služby Batch Explorer nebo [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs). Je užitečné nahrávat a ukládat soubory protokolu. Následně můžete odstranit uzel nebo snížení nákladů spuštěné uzly fondu.

## <a name="next-steps"></a>Další postup

Zkontrolujte, že jste nastavili aplikace provádět kontrolu komplexní chyb, zejména pro asynchronní operace. Může být rozhodující o tom bezodkladně informuje detekovat a diagnostikovat problémy.
