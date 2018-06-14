---
title: Nástroje pro práci s Azure Storage | Microsoft Docs
description: Seznam nástrojů, které vám umožní zobrazit nebo práci s daty Azure Storage.
services: storage
documentationcenter: ''
author: dineshmurthy
manager: jahogg
editor: tysonn
ms.assetid: e4748642-98c4-437e-b0ed-4f9641c2e894
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: dineshmurthy
ms.openlocfilehash: 5c2add48b128a3e5a632c048f0feb4413fcb26cc
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2017
ms.locfileid: "23933386"
---
# <a name="azure-storage-client-tools"></a>Klientské nástroje pro Azure Storage
Uživatelé Azure Storage se často chtějí mít možnost k zobrazení nebo interakci s jejich daty pomocí klienta nástroje Azure Storage. V následujících tabulkách jsme seznam několik nástrojů, které vám umožní to. Můžeme uvést "X" v každém bloku pokud poskytuje možnost vytvořit výčet nebo přístup k abstrakci data. V tabulce také ukazuje, zda je volné nástroje, či nikoliv. "Zkušební verze" znamená, že je bezplatná zkušební verze, ale kompletní produkt není volné. "Y/N" označuje, že verze je k dispozici zdarma, zatímco jiné verze je možné zakoupit.

Připravili jsme vám pouze snímek dostupných nástrojích klienta Azure Storage. Tyto nástroje mohou nadále můžete rozvíjet a rozšiřovat funkcí. Pokud jsou opravy nebo aktualizace, uveďte poznámky a dejte nám vědět. Totéž platí, že pokud znáte nástrojů, které by měla být zde - rádi bychom je přidat.

**Nástroje pro klienta úložiště Microsoft Azure**

<table>
  <tr>
    <th rowspan="2">Nástroj klienta úložiště Azure</th>
    <th rowspan="2">Objekt Blob bloku</th>
    <th rowspan="2">Objekt Blob stránky</th>
    <th rowspan="2">Append – objekt Blob</th>
    <th rowspan="2">Tabulky</th>
    <th rowspan="2">Fronty</th>
    <th rowspan="2">Soubory</th>
    <th rowspan="2">Free</th>
    <th colspan="4">Platforma</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Portál Microsoft Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Ano</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://storageexplorer.com/">Microsoft Azure Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Ano</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Průzkumník serveru Microsoft Visual Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Ano</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**Úložiště Azure třetích stran klienta nástroje**

Jsme nebyly ověřeny funkce nebo kvality nárokován následující nástroje třetích stran a jejich seznam neznamená neznamená společností Microsoft.

<table>
  <tr>
    <th rowspan="2">Nástroj klienta úložiště Azure</th>
    <th rowspan="2">Objekt Blob bloku</th>
    <th rowspan="2">Objekt Blob stránky</th>
    <th rowspan="2">Append – objekt Blob</th>
    <th rowspan="2">Tabulky</th>
    <th rowspan="2">Fronty</th>
    <th rowspan="2">Soubory</th>
    <th rowspan="2">Free</th>
    <th colspan="4">Platforma</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="http://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Azure Management Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Zkušební verze</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.red-gate.com/products/azure-development/azure-explorer/index">Redgate: Průzkumník Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>Ano</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Službě Azure Web Storage Exploreru</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Ano</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.cloudberrylab.com/explorer/microsoft-azure.aspx">CloudBerry Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>A/N</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gapotchenko.com/cloudcombine">Combine cloudu</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Zkušební verze</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://clumsyleaf.com">ClumsyLeaf: TableXplorer AzureXplorer, CloudXplorer,</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Ano</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gladinet.com/Azure-Storage/index.htm">Gladinet cloudu</a></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>Zkušební verze</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>
