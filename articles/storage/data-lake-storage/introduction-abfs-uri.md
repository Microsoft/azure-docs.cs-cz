---
title: Použití Azure Data Lake Storage Gen2 Preview identifikátoru URI
description: Použití Azure Data Lake Storage Gen2 Preview identifikátoru URI
services: storage
keywords: ''
author: jamesbak
ms.topic: article
ms.author: jamesbak
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 27ea73862f834a834b1622d0e51d0076c9afbe71
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523100"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Použití Azure Data Lake Storage Gen2 identifikátoru URI

[Systému souborů Hadoop](http://www.aosabook.org/en/hdfs.html) ovladač, který je kompatibilní s Azure Data Lake Storage Gen2 ve verzi Preview znáte jeho identifikátor schématu `abfs` (systému souborů Azure Blob Azure). Konzistentní vzhledem k aplikacím s další ovladače systému souborů Hadoop, ovladač ABFS využívá formát identifikátoru URI k adresování souborů a adresářů v rámci účtu Data Lake Storage Gen2 podporuje.

## <a name="uri-syntax"></a>Syntaxe identifikátoru URI

Syntaxe identifikátoru URI pro Data Lake Storage Gen2 je závislá na Určuje, jestli je mít Gen2 úložiště Data Lake jako výchozí systém souborů nastavení vašeho účtu úložiště.

Pokud podporující účtu Data Lake Storage Gen2 budete chtít adresu **není** nastavena jako výchozí systém souborů během vytváření účtu, pak je zkrácený tvar vlastností syntaxe identifikátoru URI:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.widows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identifikátor schématu**: `abfs` protokol se používá jako identifikátor schématu. Máte možnost se připojit s nebo bez připojení k zabezpečené socket layer (SSL). Použití `abfss` pro připojení pomocí připojení vrstvy zabezpečení soketu.

2. **Systém souborů**: nadřazené místo, který obsahuje soubory a složky. To je stejný jako kontejnery ve službě Azure Storage BLOB.

3. **Název účtu**: název použitý pro váš účet úložiště během vytváření.

4. **Cesty**: oddělených lomítkem (`/`) reprezentace adresářovou strukturu.

5. **Název souboru**: název jednotlivých souborů. Tento parametr je nepovinný, pokud jsou adresování adresáře.

Pokud je účet, který chcete adresu nastaven jako výchozí systém souborů během vytváření účtu, potom Zkrácený tvar vlastností syntaxe identifikátoru URI je však:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Cesta**: oddělených lomítkem (`/`) reprezentace adresářovou strukturu.

2. **Název souboru**: název jednotlivých souborů.


## <a name="next-steps"></a>Další postup

- [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](use-hdi-cluster.md)