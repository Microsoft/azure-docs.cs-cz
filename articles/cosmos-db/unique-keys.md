---
title: Použití jedinečných klíčů v Azure Cosmos DB
description: Zjistěte, jak definovat a používat jedinečné klíče pro databázi Azure Cosmos. Tento článek také popisuje, jak jedinečné klíče přidat vrstvu integrity dat.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.reviewer: sngun
ms.openlocfilehash: f234579c6fb2b6f1bc0cd518b87ea69fae30093a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74869829"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Jedinečná omezení klíče v Db Služby Azure Cosmos

Jedinečné klíče přidávají vrstvu integrity dat do kontejneru Azure Cosmos. Při vytváření kontejneru Azure Cosmos vytvoříte zásadu jedinečného klíče. S jedinečnými klíči se ujistěte, že jedna nebo více hodnot v rámci logického oddílu je jedinečný. Můžete také zaručit jedinečnost na [klíč oddílu](partition-data.md).

Po vytvoření kontejneru s zásadou jedinečného klíče je zabráněno vytvoření nové nebo aktualizace existující položky, což má za následek duplikát v rámci logického oddílu, jak je určeno omezením jedinečného klíče. Klíč oddílu v kombinaci s jedinečný klíč zaručuje jedinečnost položky v rámci kontejneru.

Například zvažte kontejner Azure Cosmos s e-mailovou adresou jako jedinečné omezení klíče a `CompanyID` jako klíč oddílu. Při konfiguraci e-mailové adresy uživatele s jedinečným klíčem má každá `CompanyID`položka jedinečnou e-mailovou adresu v rámci daného . Dvě položky nelze vytvořit s duplicitními e-mailovými adresami a se stejnou hodnotou klíče oddílu. 

Chcete-li vytvořit položky se stejnou e-mailovou adresou, ale nikoli se stejným křestním jménem, příjmením a e-mailovou adresou, přidejte další cesty k zásadám jedinečného klíče. Namísto vytvoření jedinečného klíče pouze na základě e-mailové adresy můžete také vytvořit jedinečný klíč s kombinací křestního jména, příjmení a e-mailové adresy. Tento klíč se označuje jako složený jedinečný klíč. V tomto případě je povolena každá jedinečná kombinace tří hodnot v rámci daného. `CompanyID` 

Kontejner může například obsahovat položky s následujícími hodnotami, kde každá položka respektuje jedinečné omezení klíče.

|ID společnosti|Jméno|Příjmení|E-mailová adresa|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam (Fabrkam)|   |Duperre|gaby@fabraikam.com|
|Fabrkam (Fabrkam)|   |   |gaby@fabraikam.com|

Pokud se pokusíte vložit jinou položku s kombinacemi uvedenými v předchozí tabulce, zobrazí se chyba. Chyba označuje, že nebylo splněno omezení jedinečného klíče. Obdržíte `Resource with specified ID or name already exists` buď `Resource with specified ID, name, or unique index already exists` nebo jako návratovou zprávu. 

## <a name="define-a-unique-key"></a>Definování jedinečného klíče

Jedinečné klíče můžete definovat pouze při vytváření kontejneru Azure Cosmos. Jedinečný klíč je vymezen na logický oddíl. V předchozím příkladu pokud oddíl kontejneru na základě PSČ, skončíte s duplicitní položky v každém logickém oddílu. Při vytváření jedinečných klíčů zvažte následující vlastnosti:

* Existující kontejner nelze aktualizovat tak, aby používal jiný jedinečný klíč. Jinými slovy po vytvoření kontejneru s zásadou jedinečného klíče nelze zásadu změnit.

* Chcete-li nastavit jedinečný klíč pro existující kontejner, vytvořte nový kontejner s jedinečným omezením klíče. Pomocí příslušného nástroje pro migraci dat přesuňte data z existujícího kontejneru do nového kontejneru. Pro kontejnery SQL použijte [nástroj pro migraci dat](import-data.md) k přesunutí dat. Pro kontejnery MongoDB použijte [mongoimport.exe nebo mongorestore.exe](mongodb-migrate.md) k přesunutí dat.

* Zásady jedinečného klíče mohou mít maximálně 16 hodnot cesty. Hodnoty mohou být `/firstName`například `/lastName`, `/address/zipCode`a . Každá zásada jedinečného klíče může mít maximálně 10 jedinečných omezení nebo kombinací klíčů. Kombinované cesty pro každou jedinečnou vazbu indexu nesmí překročit 60 bajtů. V předchozím příkladu je jedno omezení křestní jméno, příjmení a e-mailová adresa. Toto omezení používá 3 z 16 možných cest.

* Pokud kontejner má zásady jedinečnéklíče, [poplatky jednotky požadavku (RU)](request-units.md) k vytvoření, aktualizaci a odstranění položky jsou o něco vyšší.

* Řídké jedinečné klíče nejsou podporovány. Pokud některé jedinečné hodnoty cesty chybí, jsou považovány za nulové hodnoty, které se účastní omezení jedinečnosti. Z tohoto důvodu může existovat pouze jedna položka s hodnotou null pro splnění tohoto omezení.

* Jedinečné názvy klíčů rozlišují malá a velká písmena. Zvažte například kontejner s jedinečným `/address/zipcode`omezením klíče nastaveným na . Pokud vaše data mají `ZipCode`pole s názvem , Azure Cosmos DB `zipcode` vloží "null" `ZipCode`jako jedinečný klíč, protože není stejný jako . Z důvodu tohoto rozlišování malých a velkých písmen nelze vložit všechny ostatní záznamy s kódem ZIP, protože duplicitní "null" porušuje omezení jedinečného klíče.

## <a name="next-steps"></a>Další kroky

* Další informace o [logických oddílech](partition-data.md)
* [Zjistěte, jak definovat jedinečné klíče](how-to-define-unique-keys.md) při vytváření kontejneru
