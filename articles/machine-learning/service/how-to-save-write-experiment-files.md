---
title: Kam ukládat soubory experimentů & Write
titleSuffix: Azure Machine Learning
description: Naučte se ukládat vstupní soubory experimentu a kde zapisovat výstupní soubory, aby se předešlo chybám omezení úložiště a latenci experimentu.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 41b2602e57d295cfd7e475f4b3aa5657bd4e24d7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489598"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Kam ukládat a zapisovat soubory pro Azure Machine Learning experimenty
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, kam uložit vstupní soubory a kde zapisovat výstupní soubory z experimentů, abyste zabránili chybám limitu úložiště a latenci experimentů.

Při spouštění školicích běhů na [cílovém výpočetním cíli](how-to-set-up-training-targets.md)jsou izolované od vnějších prostředí. Účelem tohoto návrhu je zajištění reprodukovatelnosti a přenositelnosti experimentu. Pokud stejný skript spustíte dvakrát, na stejném nebo jiném cílovém výpočetním cíli, dostanete stejné výsledky. S tímto návrhem můžete nakládat s výpočetními cíli jako s výpočetními prostředky bez stavů, přičemž každý z nich nemá žádné spřažení s úlohami, které jsou spuštěny po jejich dokončení.

## <a name="where-to-save-input-files"></a>Kam ukládat vstupní soubory

Předtím, než můžete zahájit experiment na výpočetním cíli nebo na místním počítači, je nutné zajistit, aby byly k dispozici nezbytné soubory pro tento výpočetní cíl, například soubory závislosti a datové soubory, které váš kód potřebuje ke spuštění.

Azure Machine Learning spustí školicí skripty zkopírováním celé složky skriptu do cílového výpočetního kontextu a pak pořídí snímek. Limit úložiště pro experimenty snímků je 300 MB nebo 2000 souborů.

Z tohoto důvodu doporučujeme:

* **Soubory se ukládají do [úložiště dat](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)Azure Machine Learning.** Tím zabráníte problémům s latencí experimentů a máte výhody přístupu k datům ze vzdáleného výpočetního cíle, což znamená, že ověřování a připojování jsou spravovány Azure Machine Learning. Přečtěte si další informace o zadávání úložiště dat jako zdrojového adresáře a nahrání souborů do úložiště dat v článku [přístup k datům z vašich úložišť dat](how-to-access-data.md) .

* **Pokud potřebujete jenom několik datových souborů a skriptů závislostí a nemůžete použít úložiště dat,** umístěte soubory do složky do stejného adresáře jako školicí skript. Tuto složku zadejte jako `source_directory` přímo ve školicím skriptu nebo v kódu, který volá váš školicí skript.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Omezení pro úložiště snímků experimentů

V případě experimentů Azure Machine Learning automaticky provede experiment svého kódu na základě adresáře, který navrhujete při konfiguraci běhu. To má celkový limit 300 MB a/nebo 2000 souborů. Pokud tento limit překročíte, zobrazí se následující chyba:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Chcete-li tuto chybu vyřešit, uložte soubory experimentů do úložiště dat. Pokud nemůžete použít úložiště dat, níže uvedená tabulka nabízí možná alternativní řešení.

Experimentování&nbsp;popisu|Řešení omezení úložiště
---|---
Méně než 2000 souborů & nemůže používat úložiště dat| Přepsat omezení velikosti snímku pomocí <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> To může trvat několik minut v závislosti na počtu a velikosti souborů.
Musí používat konkrétní adresář skriptu.| Vytvořte soubor `.amlignore` pro vyloučení souborů ze snímku experimentu, který není součástí zdrojového kódu. Přidejte do souboru `.amlignore` názvy souborů a umístěte je do stejného adresáře jako školicí skript. `.amlignore` soubor používá stejnou [syntaxi a vzory](https://git-scm.com/docs/gitignore) jako `.gitignore` soubor.
Kanál|Pro každý krok použijte jiný podadresář
Poznámkové bloky Jupyter| Vytvořte soubor `.amlignore` nebo přesuňte svůj Poznámkový blok do nového, prázdného podadresáře a spusťte svůj kód znovu.

## <a name="where-to-write-files"></a>Kam zapisovat soubory

Vzhledem k izolaci experimentů při výuce se změny souborů, ke kterým dochází během spuštění, nemusí nutně uchovávat mimo vaše prostředí. Pokud váš skript upraví soubory, které jsou lokální na výpočetní výkon, změny se neukládají pro další spuštění experimentu a nešíří se automaticky zpátky do klientského počítače. Proto se změny provedené během prvního experimentu neprojeví a neměla by mít vliv na ty v druhém.

Při psaní změn doporučujeme zapisovat soubory do úložiště dat Azure Machine Learning. Podívejte [se na přístup k datům z úložišť dat](how-to-access-data.md).

Pokud úložiště dat nepotřebujete, zapište soubory do složky `./outputs` a/nebo `./logs`.

>[!Important]
> Dvě složky, *výstupy* a *protokoly*dostanou zvláštní zacházení Azure Machine Learning. Při výuce při psaní souborů do`./outputs` a`./logs` složky se soubory automaticky nahrají do historie spuštění, abyste k nim měli přístup až po dokončení běhu.

* **Pro výstup, jako jsou stavové zprávy nebo výsledky bodování,** zapište soubory do složky `./outputs`, aby byly trvale uložené jako artefakty v historii spuštění. Je třeba mít na vědomí počet a velikost souborů zapsaných do této složky, protože při nahrávání obsahu do historie spuštění může dojít k latenci. Pokud je latence obavy, doporučuje se zapisovat soubory do úložiště dat.

* Pokud **Chcete uložit zapsaný soubor jako protokoly v historii spuštění,** zapište soubory do složky `./logs`. Protokoly se odesílají v reálném čase, takže tato metoda je vhodná pro streamování aktualizací za provozu ze vzdáleného spuštění.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [přístupu k datům z úložišť dat](how-to-access-data.md).

* Přečtěte si další informace o [tom, jak nastavit cíle školení](how-to-set-up-training-targets.md).
