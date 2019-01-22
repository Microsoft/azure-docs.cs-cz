---
title: Úrovně konzistence a rozhraní API služby Cosmos DB
description: Principy úrovní konzistence napříč rozhraní API ve službě Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: a506c696cdb9ca6c6221b54c63d2446b7cb86a69
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430552"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Úrovně konzistence a rozhraní API služby Cosmos DB

Pět modelů konzistence nabízených službou Azure Cosmos DB nativně podporuje SQL API služby Azure Cosmos DB. Při použití služby Azure Cosmos DB, rozhraní SQL API je výchozí hodnota. 

Azure Cosmos DB poskytuje nativní podporu také pro přenosový protokol kompatibilní rozhraní API pro oblíbené databáze. Zahrnout databáze MongoDB, Apache Cassandra, Gremlin a Azure Table storage. Tyto databáze nenabízí přesně definovaných modelů konzistence a záruky jištěná smlouva SLA pro úrovně konzistence. Obvykle poskytují pouze podmnožinu pět modelů konzistence nabízených službou Azure Cosmos DB. Pro rozhraní SQL API, Gremlin API a rozhraní API tabulky se používá výchozí úroveň konzistence nakonfigurovaný na účtu Azure Cosmos DB. 

V následujících částech se dozvíte mapování mezi konzistence dat požadoval ovladač klienta OSS pro Apache Cassandra 4.x a MongoDB 3.4. Tento dokument taky zobrazuje odpovídající úrovně konzistence služby Azure Cosmos DB pro Apache Cassandra a MongoDB.

## <a id="cassandra-mapping"></a>Mapování mezi úrovněmi konzistence Apache Cassandra a Azure Cosmos DB

Tato tabulka uvádí mapování konzistence mezi Apache Cassandra a úrovně konzistence ve službě Azure Cosmos DB. Pro každý z Cassandra čtení a zápis úrovní konzistence odpovídající úroveň konzistence Cosmos DB nabízí silnější, například přísnější záruky.


<table>
<tr> 
  <th rowspan="2">Úroveň konzistence Cassandra</th> 
  <th rowspan="2">Úroveň konzistence cosmos DB</th> 
  <th colspan="3">Zápis konzistence mapování</th> 
  <th colspan="3">Mapování konzistenci pro čtení</th> 
</tr> 


 
 <tr> 
  <th>Cassandra</th> 
  <th>Databáze Cosmos</th> 
  <th>Záruka</th> 
  <th>Z Cassandry</th> 
  <th>Ke službě Cosmos DB</th> 
  <th>Záruka</th> 
 </tr> 
 
  <tr> 
  <td rowspan="6">VŠE</td> 
  <td rowspan="6">Silné</td> 
  <td>VŠE</td> 
  <td>Silné</td> 
  <td>Linearizovatelnost</td> 
  <td>ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO, ONE, LOCAL_ONE</td> 
  <td>Silné</td> 
  <td>Linearizovatelnost</td> 
 </tr> 
 
 <tr> 
  <td rowspan="2">EACH_QUORUM</td> 
  <td rowspan="2">Silné</td> 
  <td rowspan="2">Linearizovatelnost</td> 
  <td>ALL, QUORUM, SERIAL,  LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO</td> 
  <td>Silné</td> 
  <td >Linearizovatelnost</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, ONE</td>
  <td>Konzistentní předpona</td>
   <td>Global Consistent Prefix</td>
 </tr>
 

 <tr> 
  <td rowspan="2">KVORA, SÉRIOVÉHO PORTU</td> 
  <td rowspan="2">Silné</td> 
  <td rowspan="2">Linearizovatelnost</td> 
  <td>VŠECHNY KVORA, SÉRIOVÉ</td> 
  <td>Silné</td> 
  <td >Linearizovatelnost</td> 
 </tr> 

 <tr>
   <td>LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE</td>
   <td>Konzistentní předpona</td>
   <td>Global Consistent Prefix</td>
 </tr>
 
 
 <tr> 
 <td>LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, <b>ANY</b></td> 
  <td>Konzistentní předpona</td> 
  <td>Global Consistent Prefix</td> 
  <td>LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM</td> 
  <td>Konzistentní předpona</td> 
  <td>Global Consistent Prefix</td>
 </tr> 
 
 
  <tr> 
  <td rowspan="6">EACH_QUORUM</td> 
  <td rowspan="6">Silné</td> 
  <td rowspan="2">EACH_QUORUM</td> 
  <td rowspan="2">Silné</td> 
  <td rowspan="2">Linearizovatelnost</td> 
  <td>ALL, QUORUM, SERIAL,  LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO</td> 
  <td>Silné</td> 
  <td>Linearizovatelnost</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, ONE</td>
  <td>Konzistentní předpona</td>
   <td>Global Consistent Prefix</td>
 </tr>
 
 
 
 <tr> 
  <td rowspan="2">KVORA, SÉRIOVÉHO PORTU</td> 
  <td rowspan="2">Silné</td> 
  <td rowspan="2">Linearizovatelnost</td> 
  <td>VŠECHNY KVORA, SÉRIOVÉ</td> 
  <td>Silné</td> 
  <td>Linearizovatelnost</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE</td>
  <td>Konzistentní předpona</td>
   <td>Global Consistent Prefix</td>
 </tr>
 
 
  <tr> 
  <td rowspan="2">LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY</td> 
  <td rowspan="2">Konzistentní předpona</td> 
  <td rowspan="2">Global Consistent Prefix</td> 
  <td>VŠE</td> 
  <td>Silné</td> 
  <td>Linearizovatelnost</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM</td>
  <td>Konzistentní předpona</td>
   <td>Global Consistent Prefix</td>
 </tr>


  <tr> 
  <td rowspan="4">KVORA</td> 
  <td rowspan="4">Silné</td> 
  <td rowspan="2">KVORA, SÉRIOVÉHO PORTU</td> 
  <td rowspan="2">Silné</td> 
  <td rowspan="2">Linearizovatelnost</td> 
  <td>VŠECHNY KVORA, SÉRIOVÉ</td> 
  <td>Silné</td> 
  <td>Linearizovatelnost</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE</td>
  <td>Konzistentní předpona</td>
   <td>Global Consistent Prefix</td>
 </tr>
 
 
 <tr> 
  <td rowspan="2">LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY</td> 
  <td rowspan="2">Konzistentní předpona </td> 
  <td rowspan="2">Global Consistent Prefix </td> 
  <td>VŠE</td> 
  <td>Silné</td> 
  <td>Linearizovatelnost</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM</td>
  <td>Konzistentní předpona</td>
   <td>Global Consistent Prefix</td>
 </tr>
 
 <tr> 
  <td rowspan="4">LOCAL_QUORUM, THREE, TWO</td> 
  <td rowspan="4">Omezená neaktuálnost</td> 
  <td rowspan="2">LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE</td> 
  <td rowspan="2">Omezená neaktuálnost</td> 
  <td rowspan="2">Omezená Neaktuálnost.<br/>
Maximálně K verzí nebo t čas za bránou.<br/>
Přečtěte si nejnovější potvrzená hodnota v oblasti. 
</td> 
  
  <td>QUORUM, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE</td> 
  <td>Omezená neaktuálnost</td> 
  <td>Omezená Neaktuálnost.<br/>
Maximálně K verzí nebo t čas za bránou. <br/>
Přečtěte si nejnovější potvrzená hodnota v oblasti. </td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, ONE</td>
  <td>Konzistentní předpona</td>
   <td>Konzistentní předpona v jednotlivých oblastech</td>
 </tr>
 
 
 <tr> 
  <td>ONE, LOCAL_ONE, ANY</td> 
  <td>Konzistentní předpona </td> 
  <td >Konzistentní předpona v jednotlivých oblastech </td> 
  <td>LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM</td> 
  <td>Konzistentní předpona</td> 
  <td>Konzistentní předpona v jednotlivých oblastech</td> 
 </tr> 
</table>

## <a id="mongo-mapping"></a>Mapování mezi úrovněmi konzistence MongoDB 3.4 a Azure Cosmos DB

V následující tabulce jsou uvedeny "načíst obavy" mapování mezi MongoDB 3.4 a výchozí úrovně konzistence ve službě Azure Cosmos DB. V tabulce jsou uvedeny nasazení ve více oblastech a jedné oblasti.

| **MongoDB 3.4** | **Azure Cosmos DB (více oblastí)** | **Azure Cosmos DB (jedné oblasti)** |
| - | - | - |
| Linearizable | Silné | Silné |
| Většina | Omezená neaktuálnost | Silné |
| Místní | Konzistentní předpona | Konzistentní předpona |

## <a name="next-steps"></a>Další postup

Další informace o úrovních konzistence a kompatibilitu API služby Azure Cosmos DB pomocí rozhraní API pro open source. Viz následující články:

* [Dostupnost a výkon kompromisy pro různé úrovně konzistence](consistency-levels-tradeoffs.md)
* [Funkce MongoDB podporovaných rozhraním API Azure Cosmos DB pro MongoDB](mongodb-feature-support.md)
* [Apache Cassandra funkcí podporovaných službou Azure Cosmos DB Cassandra API](cassandra-support.md)