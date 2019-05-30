---
title: Zakázat omezení úložiště a experimentovat latence díky vstupní a výstupní adresáře
description: V tomto článku zjistíte, kam chcete uložit experiment vstupních souborů a kde k zápisu výstupních souborů zabránit chybám omezení úložiště a experimentovat latence.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 05/28/2019
ms.openlocfilehash: 28d8c47db8ea9c8a51bc8e9deb0a689eb0b20177
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392903"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Je-li uložit a zapisovat soubory pro experimenty Azure Machine Learning

V tomto článku zjistíte, kam chcete uložit vstupních souborů a kde má být zapsán výstupní soubory ze své experimenty zabránit v ukládání omezení chyb a experimentovat latence.

Při spouštění školení spuštění [cílové výpočetní prostředí](how-to-set-up-training-targets.md), jsou izolované od mimo prostředí. Účelem tohoto návrhu je zajistit reprodukovatelnost a přenositelnost experimentu. Pokud dvakrát spusťte stejný skript ve stejném nebo jiném cílové výpočetní prostředí, se zobrazí stejné výsledky. S tímto návrhem lze považovat cílových výpočetních prostředí jako bezstavové výpočetní prostředky, mají spřažení s úlohami, které jsou spuštěny po jejich dokončení.

## <a name="where-to-save-input-files"></a>Kam chcete uložit vstupních souborů.

Než zahájíte experimentu na cílové výpočetní prostředí nebo na místním počítači, musíte zajistit, že jsou k dispozici k této cílové výpočetní prostředí, jako je například závislosti souborů a datových souborů, které váš kód je potřeba spustit potřebné soubory.

Azure Machine Learning běží trénovací skripty zkopírováním složce celý skript do cílové výpočetní kontext a potom pořídí snímek. Limit úložiště pro snímky experimentu je 300 MB a/nebo soubory 2000.

Z tohoto důvodu doporučujeme:

* **Ukládání souborů do Azure Machine Learning [datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).** To brání problémy s latencí experiment a má své výhody přístup k datům z cílové vzdálené výpočetní prostředí, což znamená, že ověřování a připojení jsou spravované službou Azure Machine Learning. Další informace o určení datového úložiště jako zdrojový adresář a nahrávání souborů do vašeho úložiště dat v [přístup k datům z vašich úložišť](how-to-access-data.md) článku.

* **Pokud potřebujete pouze několik datových souborů a závislostí, skripty a nemůžete použít úložiště dat,** soubory umístit do stejného adresáře složky jako cvičný skript. Vyberte tuto složku jako vaše `source_directory` přímo ve vašem skriptu školení, nebo v kódu, který volá cvičný skript.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Omezení úložiště snímků experimentu

Azure Machine Learning pro experimenty, automaticky provede experiment snímek založené na adresáři, který můžete navrhovat, když nakonfigurujete spuštění kódu. To má celkový limit 300 MB a/nebo soubory 2000. Pokud překročíte tento limit, se zobrazí následující chyba:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Chcete-li vyřešit tuto chybu, ukládat soubory experimentu na datového úložiště. Pokud nemůžete použít úložiště dat, následující tabulka nabízí možná alternativní řešení.

Experiment&nbsp;popis|Limit řešení úložiště
---|---
Méně než 2000 soubory a úložiště dat nelze použít.| Přepsat omezení velikosti snímku s <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> To může trvat několik minut v závislosti na počtu a velikosti souborů.
Musíte použít adresář specifického skriptu| Ujistěte se, `.amlignore` soubor při vyloučení souborů z experimentu snímek, které nejsou součástí zdrojového kódu. Přidání názvů souborů pro `.amlignore` souboru a jeho následné uložení do stejného adresáře jako cvičný skript. `.amlignore` Soubor používá stejný [syntaxi a vzory](https://git-scm.com/docs/gitignore) jako `.gitignore` souboru.
Kanál|Používat různé podadresář pro každý krok
Poznámkové bloky Jupyter| Vytvoření `.amlignore` souboru nebo přesunout v poznámkovém bloku do podadresáře, nový, prázdný, a znovu spusťte váš kód.

## <a name="where-to-write-files"></a>Kam chcete zapisovat soubory

Z důvodu izolace školení experimenty nejsou nutně trvalé změny souborů, ke kterým dochází při spuštění mimo prostředí. Pokud váš skript upraví soubory místní compute, nejsou trvalé změny pro další spuštění experimentu a jejich nejsou šířeny zpět do klientského počítače automaticky. Změny provedené při prvním pokusu spuštění proto nemáte a soubor by neměl mít vliv na ty za sekundu.

Při zápisu změn, doporučujeme zapisování souborů do úložiště dat Azure Machine Learning. Zobrazit [přístup k datům z vašich úložišť](how-to-access-data.md).

Pokud nevyžadujete úložiště dat, zapisovat soubory `./outputs` a/nebo `./logs` složky.

>[!Important]
> Dvě složky *výstupy* a *protokoly*, zvláštní zacházení technologii Azure Machine Learning. Během cvičení, při psaní soubory`./outputs` a`./logs` složky, soubory se automaticky odesílat do vaše historie spuštění, abyste měli přístup k nim po dokončení spuštění.

* **Pro výstup například zprávy o stavu nebo vyhodnocení výsledky** zapisovat soubory `./outputs` složku, takže jsou trvalé jako artefakty v historii spuštění. Mějte na paměti počet a velikost souborů, které jsou zapsány do složky, protože latence může dojít, pokud obsah jsou odeslány do historie spuštění. Pokud je latence žádný problém, zapisování souborů do úložiště dat se doporučuje.

* **Uložte soubor písemné zaznamená jako protokoly historie spouštění** zapisovat soubory k `./logs` složky. Odeslání protokolů v reálném čase, takže tato metoda je vhodná pro streamování živých aktualizace ze vzdálené spuštění.

## <a name="next-steps"></a>Další postup

* Další informace o [přístup k datům z vašich úložišť](how-to-access-data.md).

* Další informace o [tom, jak nastavit školení cíle](how-to-set-up-training-targets.md).
