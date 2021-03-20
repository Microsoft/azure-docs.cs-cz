---
title: Použití jedinečných klíčů v Azure Cosmos DB
description: Naučte se definovat a používat jedinečné klíče pro databázi Azure Cosmos. Tento článek také popisuje, jak jedinečné klíče přidávají vrstvu integrity dat.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 9eb2b916bfe6c73a1535afb077b04fbb081dd5f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685716"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Omezení jedinečnosti klíčů v Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Jedinečné klíče přidávají vrstvu integrity dat do kontejneru Azure Cosmos. Při vytváření kontejneru Azure Cosmos vytvoříte jedinečnou zásadu klíčů. S jedinečnými klíči se ujistěte, že jedna nebo více hodnot v rámci logického oddílu je jedinečné. Můžete také zaručit jedinečnost na [klíč oddílu](partitioning-overview.md).

Po vytvoření kontejneru s jedinečnou zásadou klíče se zabrání vytvoření nové nebo aktualizace existující položky, která má za následek duplikování v rámci logického oddílu, jak je určeno omezením jedinečného klíče. Klíč oddílu v kombinaci s jedinečným klíčem zaručuje jedinečnost položky v rámci rozsahu kontejneru.

Zvažte například kontejner Azure Cosmos s `Email address` jako jedinečné omezení klíče a `CompanyID` jako klíč oddílu. Když nakonfigurujete e-mailovou adresu uživatele s jedinečným klíčem, každá položka má v rámci dané položky jedinečnou e-mailovou adresu `CompanyID` . Nelze vytvořit dvě položky s duplicitními e-mailovými adresami a stejnou hodnotou klíče oddílu. V rozhraní API ve Azure Cosmos DB SQL (Core) je položka uložená jako hodnota JSON. Tyto hodnoty JSON rozlišují velká a malá písmena. Když vyberete vlastnost jako jedinečný klíč, můžete pro tuto vlastnost vkládat hodnoty pro velká a malá písmena. Například pokud máte jedinečný klíč definovaný pro vlastnost Name, "Gaby" se liší od "Gaby" a můžete do kontejneru vložit obojí.

Pokud chcete vytvořit položky se stejnou e-mailovou adresou, ale ne se stejným křestním jménem, posledním jménem a e-mailovou adresou, přidejte další cesty k zásadě jedinečného klíče. Místo vytváření jedinečného klíče na základě e-mailové adresy můžete také vytvořit jedinečný klíč s kombinací křestního jména, příjmení a e-mailové adresy. Tento klíč je známý jako složený jedinečný klíč. V takovém případě je povolena každá jedinečná kombinace tří hodnot v rámci dané položky `CompanyID` . 

Kontejner může například obsahovat položky s následujícími hodnotami, kde každá položka respektuje omezení jedinečného klíče.

|CompanyID|Jméno|Příjmení|E-mailová adresa|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Pokud se pokusíte vložit další položku s kombinacemi uvedenými v předchozí tabulce, zobrazí se chyba. Chyba indikuje, že omezení jedinečného klíče nebylo splněno. Zobrazí se buď `Resource with specified ID or name already exists` nebo `Resource with specified ID, name, or unique index already exists` jako návratová zpráva. 

## <a name="define-a-unique-key"></a>Definování jedinečného klíče

Jedinečné klíče můžete definovat jenom v případě, že vytvoříte kontejner Cosmos Azure. Jedinečný klíč je vymezen na logický oddíl. Pokud v předchozím příkladu vytvoříte oddíly kontejneru založeného na PSČ, skončíte s duplicitními položkami v každém logickém oddílu. Při vytváření jedinečných klíčů Vezměte v úvahu následující vlastnosti:

* Existující kontejner nelze aktualizovat tak, aby používal jiný jedinečný klíč. Jinými slovy, po vytvoření kontejneru s jedinečnou zásadou klíče se zásady nedají změnit.

* Chcete-li nastavit jedinečný klíč pro existující kontejner, vytvořte nový kontejner s omezením jedinečného klíče. Pomocí vhodného nástroje pro migraci dat přesuňte data z existujícího kontejneru do nového kontejneru. Pro kontejnery SQL použijte nástroj pro [migraci dat](import-data.md) k přesunutí dat. V případě kontejnerů MongoDB použijte k přesunu dat [mongoimport.exe nebo mongorestore.exe](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) .

* Zásada jedinečného klíče může mít maximálně 16 hodnot cesty. Hodnoty mohou být například `/firstName` , `/lastName` a `/address/zipCode` . Každá zásada jedinečného klíče může mít maximálně 10 omezení nebo kombinací jedinečného klíče. Kombinované cesty pro každé omezení jedinečného indexu nesmí překročit 60 bajtů. V předchozím příkladu je jedno omezení jméno, příjmení a e-mailová adresa. Toto omezení využívá 3 z 16 možných cest.

* Pokud má kontejner jedinečnou klíčovou zásadu, poplatky za [jednotku (ru)](request-units.md) pro vytvoření, aktualizaci a odstranění položky jsou mírně vyšší.

* Zhuštěné jedinečné klíče se nepodporují. Pokud některé hodnoty jedinečné cesty chybí, považují se za hodnoty null, které se účastní omezení jedinečnosti. Z tohoto důvodu může být toto omezení splněno pouze pro jednu položku s hodnotou null.

* V názvech jedinečných klíčů se rozlišují malá a velká písmena. Vezměte například kontejner s omezením jedinečnosti Key nastaveným na `/address/zipcode` . Pokud vaše data obsahují pole s názvem `ZipCode` , Azure Cosmos DB vloží "null" jako jedinečný klíč, protože `zipcode` není stejný jako `ZipCode` . Z důvodu citlivosti nelze všechny ostatní záznamy s PSČ vložit, protože duplicitní hodnota null je v rozporu s omezením jedinečného klíče.

## <a name="next-steps"></a>Další kroky

* Další informace o [logických oddílech](partitioning-overview.md)
* Prozkoumat [Definování jedinečných klíčů](how-to-define-unique-keys.md) při vytváření kontejneru
