---
title: Použijte jedinečné klíče ve službě Azure Cosmos DB
description: Další informace o použití jedinečné klíče v databázi Azure Cosmos
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: af3c7771ce977cf248c5f1b61ba1c535a10ccd3c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242516"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Omezení jedinečných klíčů ve službě Azure Cosmos DB

Jedinečné klíče přidat vrstvu integrity dat do kontejneru Azure Cosmos. Vytvoříte zásady jedinečného klíče při vytváření kontejneru Azure Cosmos. S jedinečnými klíči Ujistěte se, že jedna nebo více hodnot v rámci logického oddílu je jedinečný. Můžete rovněž zaručují jedinečnost za [klíč oddílu](partition-data.md). 

Po vytvoření kontejneru s zásady jedinečného klíče, vytvoření nového nebo aktualizaci existující položky výsledkem je duplicitní v rámci logického oddílu je zabráněno, podle omezení unique key. Klíč oddílu, v kombinaci s jedinečný klíč zaručuje jedinečnost položky v rámci oboru kontejneru.

Představte si třeba kontejneru Azure Cosmos pomocí e-mailovou adresu jako omezení unique key a `CompanyID` jako klíč oddílu. Při konfiguraci e-mailovou adresu uživatele s jedinečný klíč, každá položka má jedinečnou e-mailovou adresu v rámci danou `CompanyID`. Duplicitní e-mailové adresy a stejnou hodnotu klíče oddílu nelze vytvořit dvě položky. 

Vytvoření položek pomocí stejné e-mailové adresy, ale není stejné křestní jméno, příjmení a e-mailovou adresu, přidat další zásady jedinečného klíče. Místo vytváření jedinečné klíče založeného na pouze e-mailovou adresu, můžete také vytvořit jedinečný klíč s kombinací křestní jméno, příjmení a e-mailovou adresu. Tento klíč se označuje jako složený jedinečný klíč. V takovém případě se každá jedinečná kombinace tří hodnot v rámci danou `CompanyID` je povolen. 

Například kontejner může obsahovat položky s použitím následujících hodnot, kde každá položka respektuje omezení unique key.

|% CompanyID|Jméno|Příjmení|E-mailová adresa|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Společnost Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Společnost Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Pokud se pokusíte vložit jiná položka s kombinací uvedené v předchozí tabulce, zobrazí se chybová zpráva. Chyba udává, že omezení unique key nebyla splněna. Zobrazí se jedna `Resource with specified ID or name already exists` nebo `Resource with specified ID, name, or unique index already exists` jako návratovou zprávu. 

## <a name="define-a-unique-key"></a>Definovat jedinečný klíč

Jedinečné klíče můžete definovat pouze v případě, že vytvoříte kontejner služby Azure Cosmos. Jedinečný klíč, který působí na logický oddíl. V předchozím příkladu Pokud kontejner podle PSČ, skončíte se duplicitní položky v každém logického oddílu. Při vytvoření jedinečné klíče, zvažte následující vlastnosti:

* Nelze aktualizovat existující kontejner používat jiný jedinečný klíč. Jinými slovy po vytvoření kontejneru s zásady jedinečného klíče nelze změnit zásady.

* Pokud chcete nastavit jedinečný klíč pro existující kontejner, vytvořte nový kontejner s omezení unique key. Použijte nástroj pro migraci příslušná data pro přesun dat z existující kontejner do nového kontejneru. Pro kontejnery SQL, použijte [nástroj pro migraci dat](import-data.md) přesouvat data. Pro kontejnery MongoDB, použijte [mongoimport.exe nebo mongorestore.exe](mongodb-migrate.md) přesouvat data.

* Zásady jedinečného klíče může mít maximálně 16 hodnoty cest. Například může být hodnoty `/firstName`, `/lastName`, a `/address/zipCode`. Každé zásady jedinečného klíče může mít maximálně 10 jedinečných klíčů omezení nebo kombinace. Kombinované cesty pro každé omezení jedinečný index nesmí překročit 60 bajtů. V předchozím příkladu křestní jméno, příjmení a e-mailovou adresu společně se jedno omezení. Toto omezení používá 3 z 16 možných cest.

* Pokud kontejner má zásady jedinečného klíče, [jednotkách žádostí (RU)](request-units.md) poplatky, pokud chcete vytvořit, aktualizovat a odstraňovat položky jsou mírně vyšší.

* Zhuštěný jedinečné klíče nejsou podporovány. Pokud chybí některé hodnoty jedinečnou cestu, má zato, že hodnoty null, které jsou zahrnuty v omezení jedinečnosti. Z tohoto důvodu může být pouze jednu položku s hodnotou null pro toto omezení.

* Jedinečné názvy klíčů jsou malá a velká písmena. Představte si třeba kontejner s nastavena na omezení unique key `/address/zipcode`. Pokud data obsahují pole s názvem `ZipCode`, vloží se služby Azure Cosmos DB "null" jako jedinečný klíč protože `zipcode` není stejná jako `ZipCode`. Z důvodu tohoto rozlišování velikosti písmen nelze všechny záznamy s PSČ vložit, protože duplicitní "null" porušuje jedinečné omezení klíče.

## <a name="next-steps"></a>Další postup

* Další informace o [logickými oddíly](partition-data.md)
* Prozkoumejte [definování jedinečné klíče](how-to-define-unique-keys.md) při vytváření kontejneru
