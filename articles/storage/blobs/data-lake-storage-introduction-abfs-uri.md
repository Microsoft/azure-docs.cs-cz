---
title: Použít Azure Data Lake Storage Gen2 identifikátor URI
description: Použít Azure Data Lake Storage Gen2 identifikátor URI
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 04df30c2a97e865d23999df26768b38cb38be607
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855556"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Použít Azure Data Lake Storage Gen2 identifikátor URI

Ovladač [systému Hadoop](https://www.aosabook.org/en/hdfs.html) , který je kompatibilní s Azure Data Lake Storage Gen2, je známý identifikátorem `abfs` schématu (systém souborů Azure BLOB). Ovladače ABFS v souladu s jinými ovladači systému souborů Hadoop využívají formát identifikátoru URI k adresování souborů a adresářů v rámci účtu s možností Data Lake Storage Gen2.

## <a name="uri-syntax"></a>Syntaxe identifikátoru URI

Syntaxe identifikátoru URI pro Data Lake Storage Gen2 závisí na tom, jestli je váš účet úložiště nastavený tak, aby měl Data Lake Storage Gen2 jako výchozí systém souborů.

Pokud účet s možností Data Lake Storage Gen2, který chcete adresovat, **není** nastaven jako výchozí systém souborů během vytváření účtu, je syntaxe zkrácený identifikátor URI:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identifikátor schématu**: `abfs` Protokol se používá jako identifikátor schématu. Máte možnost připojit se s připojením SSL (Secure Socket Layer) nebo bez něj. Slouží `abfss` k připojení pomocí připojení s protokolem Secure Socket Layer.

2. **Systém souborů**: Nadřazené umístění, ve kterém jsou uloženy soubory a složky. To je stejné jako u kontejnerů ve službě Azure Storage BLOBs.

3. **Název účtu**: Název přidělený vašemu účtu úložiště během vytváření.

4. **Cesty**: Reprezentace adresářové struktury s lomítkem`/`().

5. **Název souboru**: Název jednotlivého souboru. Tento parametr je nepovinný, pokud adresování adresáře.

Pokud je však účet, který chcete adresovat, nastaven jako výchozí systém souborů během vytváření účtu, zkrácený identifikátor URI syntaxe je:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Cesta**: Reprezentace adresářové struktury s lomítkem`/`().

2. **Název souboru**: Název jednotlivého souboru.


## <a name="next-steps"></a>Další postup

- [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
