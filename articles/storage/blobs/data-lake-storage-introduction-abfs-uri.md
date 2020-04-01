---
title: Azure Data Lake Storage Gen2 URI
description: Azure Data Lake Storage Gen2 URI
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: fa0f67e0d72ee5710a42b6de744ddae98e20220a
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437132"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Azure Data Lake Storage Gen2 URI

Ovladač [souborového systému Hadoop,](https://www.aosabook.org/en/hdfs.html) který je kompatibilní s Azure `abfs` Data Lake Storage Gen2, je známý podle identifikátoru schématu (Azure Blob File System). V souladu s ostatními ovladači souborového systému Hadoop používá ovladač ABFS formát URI k adresám souborů a adresářů v rámci účtu s podporou úložiště datového jezera Gen2.

## <a name="uri-syntax"></a>Syntaxe identifikátoru URI

Syntaxe identifikátoru URI pro Data Lake Storage Gen2 závisí na tom, jestli je váš účet úložiště nastaven tak, aby jako výchozí systém souborů měl datový lake storage Gen2.

Pokud účet s podporou úložiště datového jezera Gen2, který chcete adresovat, **není** nastaven jako výchozí systém souborů při vytváření účtu, je syntaxe identifikátoru URI zkratky:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identifikátor schématu**: Protokol se `abfs` používá jako identifikátor schématu. Máte možnost připojit se k nebo bez zabezpečení transportní vrstvy (TLS), dříve známé jako SSL (Secure Sockets Layer). Slouží `abfss` k připojení pomocí připojení TLS.

2. **Systém souborů**: Nadřazené umístění, ve které jsou soubory a složky. To je stejné jako kontejnery ve službě objektů blobs úložiště Azure.

3. **Název účtu**: Název přidělený vašemu účtu úložiště během vytváření.

4. **Cesty**: Lomítko oddělené`/`( ) reprezentace adresářové struktury.

5. **Název souboru**: Název jednotlivého souboru. Tento parametr je volitelný, pokud řešíte adresář.

Pokud je však účet, který chcete adresovat, nastaven jako výchozí systém souborů během vytváření účtu, je syntaxe identifikátoru URI zkratky:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Cesta**: Lomítko`/`oddělené ( ) reprezentace adresářové struktury.

2. **Název souboru**: Název jednotlivého souboru.


## <a name="next-steps"></a>Další kroky

- [Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
