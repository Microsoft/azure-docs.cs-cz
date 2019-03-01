---
title: Co se děje s Azure Batch AI? | Dokumenty Microsoft
description: Další informace o tom, co se děje na Azure Batch AI a službou Azure Machine Learning compute možnost.
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: edd6a7e5f385d766d51631d77f5889233af2469a
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194498"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Co se děje s Azure Batch AI?

**Služba Azure Batch AI je vyřazení z provozu v březnu.** Ve velkém měřítku trénování a vyhodnocování funkcí služby Batch AI jsou teď dostupné v [služby Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md), která se stala všeobecně dostupná na 4. prosince 2018.

Spolu s mnoha jiných strojového učení zahrnuje službu Azure Machine Learning založené na cloudu spravované cílové výpočetní prostředí pro školení, nasazování a vyhodnocování modelů strojového učení. Tato cílového výpočetního prostředí se nazývá [Azure Machine Learning Compute](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Spuštění migrace a jeho použití Dnes](#migrate). Můžete pracovat se službou Azure Machine Learning prostřednictvím jeho [sady SDK pro Python](../machine-learning/service/quickstart-create-workspace-with-python.md), rozhraní příkazového řádku a [webu Azure portal](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Časový plán podpory

V tuto chvíli můžete použít existující předplatných Azure Batch AI jako před. Ale žádný **nová předplatná** se provádějí jsou možné a žádné nové investice.

Od března&nbsp;31&#x2c;&nbsp;2019, nevyužitých předplatných služby Batch AI se už nebude fungovat.

## <a name="compare-to-azure-machine-learning"></a>Porovnat s Azure Machine Learning
Je Cloudová služba, který použijete k natrénování, nasazení, automatizaci a správu modelů strojového učení, vše na široké měřítko, které cloud poskytuje. Získejte přehled, [služby Azure Machine Learning v tomto přehledu](../machine-learning/service/overview-what-is-azure-ml.md).
 

Životní cyklus vývoje typické model zahrnuje přípravy dat, školení a experimentování a fáze nasazení. Tento cyklus koncového lze orchestrované s využitím kanálů Machine Learning.

![Vývojový diagram](./media/overview-what-happened-batch-ai/lifecycle.png)


[Další informace o fungování služby a jeho hlavní koncepty](../machine-learning/service/concept-azure-machine-learning-architecture.md). Mnoho popsaných konceptů v pracovním postupu trénování modelu jsou podobné existující koncepty služby Batch AI. 

Tady je konkrétně, jak byste měli uvažovat o jejich mapování:
 
|Služba batch AI|  Služba Azure Machine Learning|
|:--:|:---:|
|Pracovní prostor|Pracovní prostor|
|Cluster|   Výpočetní prostředky typu `AmlCompute`|
|Souborové servery|DataStores|
|Experimenty|Experimenty|
|Úlohy|Spustí (umožňuje vnořené spuštění)|
 
Tady je jiný pohled stejné tabulky, které pomůžou vám vizualizace další věci:
 
### <a name="batch-ai-hierarchy"></a>Batch AI hierarchie
![Vývojový diagram](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
### <a name="azure-machine-learning-service-hierarchy"></a>Hierarchie služby Azure Machine Learning
![Vývojový diagram](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

## <a name="platform-capabilities"></a>Funkce platformy
Azure Machine Learning, které služba přináší sadu skvělé nové funkce, včetně komplexní školení -> sada pro nasazení, můžete použít při vývoji AI, aniž byste museli spravovat veškeré prostředky Azure. Tato tabulka obsahuje porovnání podpora funkce k trénování mezi těmito dvěma službami.

|Funkce|Služba batch AI|Služba Azure Machine Learning|
|-------|:-------:|:-------:|
|Volba velikosti virtuálního počítače |CPU/GPU    |CPU/GPU. Také podporuje pro odvozování FPGA|
|Připravený Cluster AI (ovladače, Docker, atd.)|  Ano |Ano|
|Příprava uzlu| Ano|    Ne|
|Řada operačního systému podle výběru   |Částečné    |Ne|
|Vyhrazené a LowPriority virtuálních počítačů  |Ano    |Ano|
|Automatické škálování   |Ano    |Ano (ve výchozím nastavení)|
|Čekací doba pro automatické škálování  |Ne |Ano|
|SSH    |Ano|   Ano|
|Připojování na úrovni clusteru |Ano (FileShares, objekty BLOB, systém souborů NFS, vlastní)   |Ano (připojit nebo stažení vašeho úložiště dat)|
|Distribuované trénování|  Ano |Ano|
|Režim spuštění úlohy|    Virtuální počítač nebo kontejneru|    Kontejner|
|Vlastní Image kontejneru|    Ano |Ano|
|Všechny sady Toolkit    |Ano    |Ano (Python spuštění skriptu)|
|JobPreparation|    Ano |Zatím ne|
|Připojování na úrovni úlohy |Ano (FileShares, objekty BLOB, systém souborů NFS, vlastní)   |Ano (FileShares, objekty BLOB)|
|Monitorování úloh     |prostřednictvím GetJob|    prostřednictvím historie spuštění (podrobnější informace, modul runtime vlastní tak, aby nabízel další metriky)|
|Získat protokoly úlohy a soubory/modelů |   prostřednictvím ListFiles a rozhraní API úložiště  |prostřednictvím služby artefaktu|
 |Podpora pro Tensorboard   |Ne|    Ano|
|Kvóty úrovně řady virtuálních počítačů |Ano    |Ano (s předchozím kapacitu přenesena)|
 
Kromě v předchozí tabulce jsou funkce ve službě Azure Machine Learning, které byly v BatchAI tradičně nejsou podporovány.

|Funkce|Služba batch AI|Služba Azure Machine Learning|
|-------|:-------:|:-------:|
|Příprava prostředí    |Ne |Ano (Conda přípravě a nahrávání do služby ACR)|
|Hyperparametrů  |Ne|    Ano|
|Správa modelů   |Ne |Ano|
|Operacionalizace/nasazení| Ne  |Prostřednictvím AKS a ACI|
|Příprava dat   |Ne |Ano|
|Cílových výpočetních prostředí    |Virtuální počítače Azure  |Místní BatchAI (jako AmlCompute), DataBricks, HDInsight|
|Automatizované Machine Learning |Ne|    Ano|
|Kanály  |Ne |Ano|
|Dávkové vyhodnocování  |Ano    |Ano|
|Podpora portálu nebo rozhraní příkazového řádku|    Ano |Ano|


## <a name="programming-interfaces"></a>Rozhraní pro programování

Tato tabulka představuje různé programovací rozhraní pro každou službu k dispozici.
    
|Funkce|BatchAI služby|Služba Azure Machine Learning|
|-------|:-------:|:-------:|
|Sada SDK    |Java, C#, Python, Node.js   |Python (spuštění na základě konfigurace a odhad na základě běžných architektur)|
|Rozhraní příkazového řádku    |Ano    |Zatím ne|
|portál Azure   |Ano    |Ano (s výjimkou odeslání úlohy)|
|REST API   |Ano    |Ano ale distribuované napříč mikroslužeb|


Upgrade z verze Preview služby Batch AI ve službě GA'ed Azure Machine Learning poskytuje lepší základními pojmy, které se snadněji používá jako jsou odhady a úložišť. Zaručuje taky všeobecné dostupnosti služeb Azure na úrovni smlouvy o úrovni služeb a zákaznické podpory.

Služba Azure Machine Learning, které službu také přináší nové funkce, jako automatické machine learning, hyperparametrů a ML kanály, které jsou užitečné v nejvíce rozsáhlé úlohy AI. Schopnost nasadit trénovaného modelu bez nutnosti přepínat do samostatné služby pomáhá dokončit datové vědy smyčky od přípravy dat (s použitím sady SDK pro Data Prep) k operacionalizaci a sledování modelů.

<a name="migrate"></a>
## <a name="migrate"></a>Migrace

Zjistěte, jak migrovat a jak mapuje kódu ve službě Azure Machine Learning v kódu můžete použít [migrace do služby Azure Machine Learning](how-to-migrate.md) článku.

## <a name="get-support"></a>Získat podporu

Batch AI je plánované vyřadit z provozu 31. března a už blokuje nová předplatná v registraci na službu, pokud není na seznamu povolených vyvoláním výjimky prostřednictvím podpory.  Kontaktujte nás na adrese [Azure Batch AI školení ve verzi Preview](mailto:AzureBatchAITrainingPreview@service.microsoft.com) jakékoli dotazy nebo pokud máte nějakou zpětnou vazbu, jak migrovat do služby Azure Machine Learning.

Služba Azure Machine Learning je obecně dostupné služby. To znamená, že obsahuje potvrzené smlouva SLA a můžete vybírat z různých plánech podpory.

Ceny za použití infrastruktury Azure pomocí služby Batch AI nebo ve službě Azure Machine Learning by neměl lišit v závislosti na, účtujeme jenom cena za spotřebované výpočetní prostředky v obou případech. Další informace najdete v tématu [cenové kalkulačky](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Zobrazit na dostupnost podle oblastí mezi těmito dvěma službami [webu Azure portal](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>Další postup

+ Přečtěte si [migrace](how-to-migrate.md) a způsobu mapování kódu můžete nyní použít kód ve službě Azure Machine Learning.

+ Přečtěte si [Přehled služby Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Konfigurace cílové výpočetní prostředí k tréninku modelu](../machine-learning/service/how-to-set-up-training-targets.md) službou Azure Machine Learning.

+ Zkontrolujte [plány Azure do budoucna](https://azure.microsoft.com/updates/) a získejte informace o další aktualizace služby Azure.
