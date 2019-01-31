---
title: Nástroje pro práci s Azure Storage | Dokumentace Microsoftu
description: Seznam nástrojů, které umožňují zobrazit a pracovat s daty Azure Storage.
services: storage
author: dineshmurthy
ms.service: storage
ms.topic: article
ms.date: 09/06/2017
ms.author: dineshmurthy
ms.subservice: common
ms.openlocfilehash: c45c70055817eeeed19f9c8a832a76b10beceaa6
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467029"
---
# <a name="azure-storage-client-tools"></a>Klientské nástroje pro Azure Storage
Uživatele služby Azure Storage se často chtějí mít možnost zobrazit a pracovat s nimi svá data pomocí nástroje klienta služby Azure Storage. V následující tabulce uvádíme různé nástroje, které vám to umožní. Pokud poskytuje možnost vytvořit výčet nebo přístup k data abstraction máme v každém bloku "X". Tabulka také ukazuje, zda nástroje je zdarma, nebo ne. "Zkušební verze" znamená, že je bezplatná zkušební verze, ale plné verze produktu není zdarma. "A/N" znamená, že verze k dispozici zdarma, zatímco jiné verze se dá koupit.

Poskytujeme jenom snímek dostupné nástroje klienta služby Azure Storage. Tyto nástroje mohou nadále rozvoj a růst ve funkcích. Pokud jsou opravy nebo aktualizace, uveďte ji v poznámce a dejte nám vědět. Totéž platí, že pokud znáte nástroje, které by měla být tady – rádi je přidat.

**Klientské nástroje Microsoft Azure Storage**

<table>
  <tr>
    <th rowspan="2">Nástroj pro klienta služby Azure Storage</th>
    <th rowspan="2">Objekt blob bloku</th>
    <th rowspan="2">Objekt blob stránky</th>
    <th rowspan="2">Připojit objekt blob</th>
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
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Microsoft Azure Portal</a></td>
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
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Microsoft Visual Studio Server Explorer</a></td>
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

**Klientské nástroje pro Azure Storage třetích stran**

Jsme neověřili kvality převzatá podle následujících nástrojů třetích stran nebo funkce a jejich seznam neznamená microsoftem.

<table>
  <tr>
    <th rowspan="2">Nástroj pro klienta služby Azure Storage</th>
    <th rowspan="2">Objekt blob bloku</th>
    <th rowspan="2">Objekt blob stránky</th>
    <th rowspan="2">Připojit objekt blob</th>
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
    <td><a href="https://www.red-gate.com/products/azure-development/azure-explorer/index">Redgate: Průzkumník služby Azure</a></td>
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
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Azure Web Průzkumníka služby Storage</a></td>
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
    <td><a href="http://www.gapotchenko.com/cloudcombine">Cloud Combine</a></td>
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
    <td><a href="http://clumsyleaf.com">Společnosti ClumsyLeaf: AzureXplorer, CloudXplorer, TableXplorer</a></td>
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
    <td><a href="http://www.gladinet.com/Azure-Storage/index.htm">Gladinet Cloud</a></td>
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
