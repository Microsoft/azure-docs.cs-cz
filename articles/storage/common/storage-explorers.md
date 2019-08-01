---
title: Nástroje pro práci s Azure Storage | Microsoft Docs
description: Seznam nástrojů, které vám umožní zobrazit data Azure Storage a pracovat s nimi.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2017
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 14cbc076f4c9eebd2647cd667acc856b393e4d93
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68665734"
---
# <a name="azure-storage-client-tools"></a>Klientské nástroje pro Azure Storage
Uživatelé Azure Storage často chtějí pomocí Azure Storage klientského nástroje zobrazit data a pracovat s nimi. V níže uvedených tabulkách uvádíme několik nástrojů, které vám to umožňují. "X" vložte do každého bloku, pokud nabízí možnost výčtu a/nebo přístupu k abstrakci dat. Tabulka také ukazuje, jestli jsou nástroje bezplatné nebo ne. "Zkušební" znamená, že je k dispozici bezplatná zkušební verze, ale plný produkt není bezplatný. "Y/N" označuje, že je verze dostupná zdarma, ale k nákupu je k dispozici odlišná verze.

Poskytli jsme jenom snímek dostupných Azure Storage klientských nástrojů. Tyto nástroje mohou nadále vyvíjet a rozšiřovat funkce. Pokud jsou k dispozici opravy nebo aktualizace, ponechejte prosím komentář a sdělte nám, jak nás zajímá. Totéž platí, pokud znáte nástroje, které by měly být zde – chceme je přidat.

**Nástroje pro Microsoft Azure Storage klienta**

<table>
  <tr>
    <th rowspan="2">Nástroj klienta Azure Storage</th>
    <th rowspan="2">Objekt blob bloku</th>
    <th rowspan="2">Objekt blob stránky</th>
    <th rowspan="2">Připojit objekt blob</th>
    <th rowspan="2">Tabulky</th>
    <th rowspan="2">Fronty</th>
    <th rowspan="2">Soubory</th>
    <th rowspan="2">Zdarma</th>
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
    <td><a href="https://storageexplorer.com/">Microsoft Azure Storage Explorer</a></td>
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
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Microsoft Visual Studio Průzkumník serveru</a></td>
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

**Nástroje klienta Azure Storage třetích stran**

Neověřili jsme funkčnost ani kvalitu, které požadovaly následující nástroje třetích stran, a jejich seznam neznamená potvrzení od Microsoftu.

<table>
  <tr>
    <th rowspan="2">Nástroj klienta Azure Storage</th>
    <th rowspan="2">Objekt blob bloku</th>
    <th rowspan="2">Objekt blob stránky</th>
    <th rowspan="2">Připojit objekt blob</th>
    <th rowspan="2">Tabulky</th>
    <th rowspan="2">Fronty</th>
    <th rowspan="2">Soubory</th>
    <th rowspan="2">Zdarma</th>
    <th colspan="4">Platforma</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Azure Management Studio</a></td>
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
    <td><a href="https://www.red-gate.com/products/azure-development/azure-explorer/index">Redgate Průzkumník Azure</a></td>
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
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Web Azure Průzkumník služby Storage</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
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
    <td><a href="https://www.cloudberrylab.com/explorer/microsoft-azure.aspx">CloudBerry Explorer</a></td>
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
    <td><a href="https://www.gapotchenko.com/cloudcombine">Cloud – kombinace</a></td>
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
    <td><a href="https://clumsyleaf.com">ClumsyLeaf: AzureXplorer, CloudXplorer, TableXplorer</a></td>
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
    <td><a href="https://www.gladinet.com/Azure-Storage/index.htm">Gladinet Cloud</a></td>
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
