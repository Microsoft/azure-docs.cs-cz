---
title: Kam uložit & psát experimentální soubory
titleSuffix: Azure Machine Learning
description: Zjistěte, kam uložit vstupní soubory experimentu a kde zapisovat výstupní soubory, abyste zabránili chybám omezení úložiště a latenci experimentu.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 12a38b08fd429280f34b4eb02d4b72187b622261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79078436"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Kde uložit a zapisovat soubory pro experimenty Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, kam uložit vstupní soubory a kde zapisovat výstupní soubory z experimentů, abyste zabránili chybám omezení úložiště a latenci experimentu.

Při spuštění školení běží na [výpočetní cíl](how-to-set-up-training-targets.md), jsou izolovány od vnějšího prostředí. Účelem tohoto návrhu je zajistit reprodukovatelnost a přenositelnost experimentu. Pokud spustíte stejný skript dvakrát, na stejný nebo jiný výpočetní cíl, obdržíte stejné výsledky. S tímto návrhem můžete považovat výpočetní cíle jako bezstavové výpočetní prostředky, z nichž každá nemá žádnou spřažení s úlohami, které jsou spuštěny po dokončení.

## <a name="where-to-save-input-files"></a>Kam uložit vstupní soubory

Před zahájením experimentu na výpočetní cíl nebo místní počítač, musíte zajistit, že potřebné soubory jsou k dispozici pro tento výpočetní cíl, jako jsou soubory závislostí a datové soubory, které váš kód potřebuje ke spuštění.

Azure Machine Learning spouští školicí skripty zkopírováním celé složky skriptu do cílového výpočetního kontextu a pak pořídí snímek. Limit úložiště pro snímky experimentů je 300 MB nebo 2 000 souborů.

Z tohoto důvodu doporučujeme:

* **Ukládání souborů v [úložišti dat](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)Azure Machine Learning .** Tím se zabrání problémům s latencí experimentu a má výhody přístupu k datům z vzdáleného výpočetního cíle, což znamená, že ověřování a montáž jsou spravované Azure Machine Learning. Další informace o určení úložiště dat jako zdrojového adresáře a nahrávání souborů do úložiště dat v [článku o accessových úložištích.](how-to-access-data.md)

* **Pokud potřebujete pouze několik datových souborů a skriptů závislostí a nemůžete používat úložiště dat,** umístěte soubory do stejného adresáře složek jako trénovací skript. Zadejte tuto `source_directory` složku jako přímo v trénovacím skriptu nebo v kódu, který volá váš školicí skript.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limity úložiště snímků experimentu

Pro experimenty Azure Machine Learning automaticky vytvoří snímek experimentu vašeho kódu na základě adresáře, který navrhnete při konfiguraci spuštění. To má celkový limit 300 MB nebo 2000 souborů. Pokud tento limit překročíte, zobrazí se následující chyba:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Chcete-li tuto chybu vyřešit, uložte soubory experimentu do úložiště dat. Pokud úložiště dat nemůžete používat, níže uvedená tabulka nabízí možná alternativní řešení.

Popis&nbsp;experimentu|Řešení limitu úložiště
---|---
Méně než 2000 souborů & nelze použít úložiště dat| Přepsat limit velikosti snímku <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> To může trvat několik minut v závislosti na počtu a velikosti souborů.
Musí používat určitý adresář skriptů.| Vytvořte `.amlignore` soubor pro vyloučení souborů ze snímku experimentu, které nejsou součástí zdrojového kódu. Přidejte názvy `.amlignore` souborů do souboru a umístěte jej do stejného adresáře jako trénovací skript. Soubor `.amlignore` používá stejnou [syntaxi a vzorky](https://git-scm.com/docs/gitignore) jako `.gitignore` soubor.
Kanál|Použití jiného podadresáře pro každý krok
Poznámkové bloky Jupyter| Vytvořte `.amlignore` soubor nebo přesuňte poznámkový blok do nového prázdného podadresáře a znovu spusťte kód.

## <a name="where-to-write-files"></a>Kde psát soubory

Vzhledem k izolaci školení experimenty, změny souborů, ke kterým dochází během spuštění nejsou nutně trvalé mimo vaše prostředí. Pokud skript upraví soubory místní vypočítat, změny nejsou trvalé pro další experiment spustit a nejsou šířeny zpět do klientského počítače automaticky. Proto změny provedené během prvního spuštění experimentu nemají a neměly by mít vliv na ty v druhém.

Při psaní změn doporučujeme zapisovat soubory do úložiště dat Azure Machine Learning. Viz [Přístup k datům z datových úložišť](how-to-access-data.md).

Pokud úložiště dat nepotřebujete, zapisujte `./outputs` soubory `./logs` do složky a/nebo do složky.

>[!Important]
> Dvě složky, *výstupy* a *protokoly*, obdrží speciální zacházení azure machine learning. Během školení, když píšete`./logs` soubory a`./outputs` složky, soubory se automaticky nahrají do historie spuštění, takže k nim budete mít přístup po dokončení spuštění.

* **Pro výstup, jako jsou stavové zprávy nebo výsledky hodnocení,** zapisujte soubory do `./outputs` složky, aby byly trvalé jako artefakty v historii běhu. Mějte na paměti počet a velikost souborů zapsaných do této složky, protože latence může dojít při nahrání obsahu ke spuštění historie. Pokud latence je problém, psaní souborů do úložiště dat se doporučuje.

* **Chcete-li uložit zapsaný soubor jako protokoly do historie spuštění,** zapisujte soubory do `./logs` složky. Protokoly jsou nahrány v reálném čase, takže tato metoda je vhodná pro streamování živých aktualizací ze vzdáleného běhu.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [přístupu k datům z datových úložišť](how-to-access-data.md).

* Další informace o [nastavení tréninkových cílů](how-to-set-up-training-targets.md).
