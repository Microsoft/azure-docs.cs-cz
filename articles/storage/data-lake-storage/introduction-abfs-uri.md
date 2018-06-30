---
title: Pomocí Azure Data Lake Storage Gen2 náhledu identifikátor URI
description: Pomocí Azure Data Lake Storage Gen2 náhledu identifikátor URI
services: storage
keywords: ''
author: jamesbak
ms.topic: article
ms.author: jamesbak
manager: jahogg
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: a6130d8440b16e5a72c939fc07f6bf32c0946418
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114288"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Pomocí Azure Data Lake Storage Gen2 identifikátor URI

[Systému souborů Hadoop](http://www.aosabook.org/en/hdfs.html) ovladač, který je kompatibilní s Azure Data Lake Storage Gen2 Preview znáte jeho schéma identifikátoru `abfs` (systém souborů objektů Blob Azure). Formát identifikátoru URI k adresování souborů a adresářů v rámci účtu Data Lake Storage Gen2 podporující využívá ovladač ABFS konzistentní s další ovladače systému souborů Hadoop.

## <a name="uri-syntax"></a>Syntaxe identifikátoru URI

Syntaxe identifikátoru URI pro Data Lake Storage Gen2 je závislá na, jestli je váš účet úložiště nastavit tak, aby měl Gen2 úložiště Data Lake jako výchozí systém souborů.

Pokud účet podporující Data Lake Storage Gen2 chcete na adresu **není** nastavit jako výchozí systém souborů během vytváření účtu, potom sdružená syntaxe identifikátoru URI je:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.widows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identifikátor schématu**: `abfs` protokol se používá jako identifikátor schématu. Máte možnost připojit se s nebo bez připojení zabezpečené socket layer (SSL). Použití `abfss` pro připojení k připojení vrstvy zabezpečení soketu.

2. **Systém souborů**: nadřazeného umístění, která obsahuje soubory a složky. Toto je stejný jako kontejnery ve službě Azure Storage Blobs.

3. **Název účtu**: daný název účtu úložiště při vytváření.

4. **Cesty**: oddělený lomítkem (`/`) reprezentace strukturu adresáře.

5. **Název souboru**: název jednotlivých souborů. Tento parametr je nepovinný, pokud chcete dosáhnout adresáře.

Pokud účet, který chcete adresu nastaven jako výchozí systém souborů během vytváření účtu, potom sdružená syntaxe identifikátoru URI je však:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Cesta**: oddělený lomítkem (`/`) reprezentace strukturu adresáře.

2. **Název souboru**: název jednotlivých souborů.


## <a name="next-steps"></a>Další postup

- [Použití Azure Data Lake Storage Gen2 s Azure HDInsight clustery](use-hdi-cluster.md)