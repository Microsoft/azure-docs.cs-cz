---
title: Kam ukládat soubory experimentů & Write
titleSuffix: Azure Machine Learning
description: Naučte se ukládat vstupní a výstupní soubory, abyste zabránili chybám omezení úložiště a latenci experimentů.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 59955b291ce706a77d0dd5ab052809fe725166d9
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387883"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Kam ukládat a zapisovat soubory pro Azure Machine Learning experimenty


V tomto článku se dozvíte, kam uložit vstupní soubory a kde zapisovat výstupní soubory z experimentů, abyste zabránili chybám limitu úložiště a latenci experimentů.

Při spouštění školicích běhů na [cílovém výpočetním cíli](concept-compute-target.md)jsou izolované od vnějších prostředí. Účelem tohoto návrhu je zajištění reprodukovatelnosti a přenositelnosti experimentu. Pokud stejný skript spustíte dvakrát, na stejném nebo jiném cílovém výpočetním cíli, dostanete stejné výsledky. S tímto návrhem můžete nakládat s výpočetními cíli jako s výpočetními prostředky bez stavů, přičemž každý z nich nemá žádné spřažení s úlohami, které jsou spuštěny po jejich dokončení.

## <a name="where-to-save-input-files"></a>Kam ukládat vstupní soubory

Předtím, než můžete zahájit experiment na výpočetním cíli nebo na místním počítači, je nutné zajistit, aby byly k dispozici nezbytné soubory pro tento výpočetní cíl, například soubory závislosti a datové soubory, které váš kód potřebuje ke spuštění.

Azure Machine Learning spouští školicí skripty zkopírováním celého zdrojového adresáře. Pokud máte citlivá data, která nechcete nahrávat, použijte [soubor. Ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) nebo ho nezahrnujte do zdrojového adresáře. Místo toho přístup k datům pomocí [úložiště](/python/api/azureml-core/azureml.data)dat.

Limit úložiště pro snímky experimentů je 300 MB nebo 2 000 souborů.

Z tohoto důvodu doporučujeme:

* **Soubory se ukládají do Azure Machine Learning [datové sady](/python/api/azureml-core/azureml.data).** Tím zabráníte problémům s latencí experimentů a máte výhody přístupu k datům ze vzdáleného výpočetního cíle, což znamená, že ověřování a připojování jsou spravovány Azure Machine Learning. Přečtěte si další informace o tom, jak zadat datovou sadu jako vstupní zdroj dat ve školicím skriptu s využitím [vlakových sad](how-to-train-with-datasets.md).

* **Pokud potřebujete jenom několik datových souborů a skriptů závislostí a nemůžete použít úložiště dat,** umístěte soubory do složky do stejného adresáře jako školicí skript. Tuto složku zadejte `source_directory` přímo ve školicím skriptu nebo v kódu, který volá váš školicí skript.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Omezení pro úložiště snímků experimentů

V případě experimentů Azure Machine Learning automaticky provede experiment svého kódu na základě adresáře, který navrhujete při konfiguraci běhu. To má celkový limit 300 MB a/nebo 2000 souborů. Pokud tento limit překročíte, zobrazí se následující chyba:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Chcete-li tuto chybu vyřešit, uložte soubory experimentů do úložiště dat. Pokud nemůžete použít úložiště dat, níže uvedená tabulka nabízí možná alternativní řešení.

Popis experimentu &nbsp;|Řešení omezení úložiště
---|---
Méně než 2000 souborů & nemůže používat úložiště dat| Přepsat omezení velikosti snímku pomocí <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> To může trvat několik minut v závislosti na počtu a velikosti souborů.
Musí používat konkrétní adresář skriptu.| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
Kanál|Pro každý krok použijte jiný podadresář
Jupyter Notebooks| Vytvořte `.amlignore` soubor nebo přesuňte svůj Poznámkový blok do nového, prázdného podadresáře a spusťte svůj kód znovu.

## <a name="where-to-write-files"></a>Kam zapisovat soubory

Vzhledem k izolaci experimentů při výuce se změny souborů, ke kterým dochází během spuštění, nemusí nutně uchovávat mimo vaše prostředí. Pokud váš skript upraví soubory, které jsou lokální na výpočetní výkon, změny se neukládají pro další spuštění experimentu a nešíří se automaticky zpátky do klientského počítače. Proto se změny provedené během prvního experimentu neprojeví a neměla by mít vliv na ty v druhém.

Při psaní změn doporučujeme zapisovat soubory do úložiště prostřednictvím Azure Machine Learning datovou sadu s [objektem OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig). Přečtěte si téma [Vytvoření OutputFileDatasetConfig](how-to-train-with-datasets.md#where-to-write-training-output).

V opačném případě zapište soubory do `./outputs` složky a/nebo `./logs` .

>[!Important]
> Dvě složky, *výstupy* a *protokoly* dostanou zvláštní zacházení Azure Machine Learning. Když při výuce zapisujete soubory do `./outputs` složky a, `./logs` budou soubory automaticky nahrány do historie spuštění, takže k nim budete mít přístup, až se vaše spuštění dokončí.

* **Pro výstup, jako jsou stavové zprávy nebo výsledky bodování,** zapište soubory do `./outputs` složky, aby byly uchovány jako artefakty v historii spuštění. Je třeba mít na vědomí počet a velikost souborů zapsaných do této složky, protože při nahrávání obsahu do historie spuštění může dojít k latenci. Pokud je latence obavy, doporučuje se zapisovat soubory do úložiště dat.

* Pokud **Chcete uložit zapsaný soubor jako protokoly v historii spuštění,** zapište soubory do `./logs` složky. Protokoly se odesílají v reálném čase, takže tato metoda je vhodná pro streamování aktualizací za provozu ze vzdáleného spuštění.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [přístupu k datům z úložiště](how-to-access-data.md).

* Další informace o [vytváření výpočetních cílů pro školení a nasazení modelu](how-to-create-attach-compute-studio.md)