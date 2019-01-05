---
title: Použijte jedinečné klíče ve službě Azure Cosmos DB
description: Další informace o použití jedinečné klíče v databázi Azure Cosmos DB
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.reviewer: sngun
ms.openlocfilehash: 20e5c96110f07d8eaec218ed167c87a48fd65782
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037374"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Jedinečné klíče ve službě Azure Cosmos DB

Jedinečné klíče umožňují přidat vrstvu integrity dat do kontejneru Cosmos. Vytvoříte zásady jedinečného klíče při vytváření kontejneru Cosmos. S jedinečnými klíči, zajistíte jedinečnost jedné nebo více hodnot v rámci logického oddílu (může zaručit jedinečnost za [klíč oddílu](partition-data.md)). Jakmile vytvoříte kontejner pomocí zásady jedinečného klíče, brání vytváření všechny nové (nebo aktualizované) duplicitní položky v rámci logického oddílu, jak jsou určené omezení unique key. Klíč oddílu v kombinaci s jedinečné klíče zaručuje jedinečnost položky v rámci oboru kontejneru.

Představte si třeba kontejneru Cosmos s e-mailovou adresu jako jedinečnosti klíče a `CompanyID` jako klíč oddílu. Tím, že nakonfigurujete uživatele e-mailová adresa jedinečný klíč, je zajistit, že každá položka má jedinečnou e-mailovou adresu v rámci danou `CompanyID`. Duplicitní e-mailové adresy a stejnou hodnotu klíče oddílu nelze vytvořit dvě položky.  

Pokud chcete uživatelům umožnit vytváření více položek pomocí stejné e-mailovou adresu, ale není stejný křestního jména, poslední jméno a e-mailovou adresu, můžete přidat dalších cest pro zásady jedinečného klíče. Místo vytváření jedinečné klíče založeného na e-mailovou adresu, můžete také vytvořit jedinečný klíč s kombinací křestní jméno, příjmení a e-mailová adresa (složený jedinečný klíč). V takovém případě se každá jedinečná kombinace tří hodnot v rámci danou `CompanyID` je povolen. Například kontejner může obsahovat položky s použitím následujících hodnot, ve kterém je každá položka příslušných omezení unique key.

|% CompanyID|Jméno|Příjmení|E-mailová adresa|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Společnost Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Společnost Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Pokud se pokusíte vložit jiná položka s kombinací uvedené v tabulce výše, zobrazí se chyba oznamující, že omezení unique key nebyla splněna. Zobrazí se, buď "prostředek se zadaným ID nebo názvem již existuje" nebo "Prostředek se zadaným ID, název nebo jedinečný index již existuje" jako návratovou zprávu.  

## <a name="defining-a-unique-key"></a>Definování jedinečný klíč

Jedinečné klíče můžete definovat pouze při vytváření kontejneru Cosmos. Jedinečný klíč, který působí na logický oddíl. V předchozím příkladu Pokud kontejner podle PSČ, skončíte s duplicitní položky v každém logického oddílu. Při vytváření jedinečné klíče, zvažte následující vlastnosti:

* Nelze aktualizovat existující kontejner používat jiný jedinečný klíč. Jinými slovy Jakmile se kontejner vytvoří se zásady jedinečného klíče, nelze změnit zásady.

* Pokud chcete nastavit jedinečný klíč pro existující kontejner, budete muset vytvořit nový kontejner s jedinečnosti klíče a použít nástroj pro migraci příslušná data pro přesun dat z existující kontejner do nového kontejneru. Pro kontejnery SQL, použijte [nástroj pro migraci dat](import-data.md) přesouvat data. Pro kontejnery MongoDB, použijte [mongoimport.exe nebo mongorestore.exe](mongodb-migrate.md) přesouvat data.

* Zásady jedinečného klíče může mít maximálně 16 hodnotami cestu (například: /firstName, /lastName, / adresa/PSC). Každé zásady jedinečného klíče může mít maximálně 10 jedinečných klíčů omezení nebo kombinace a kombinované cesty pro každého jedinečného indexu omezení by neměl být delší než 60 bajtů. V předchozím příkladu křestní jméno, příjmení a e-mailovou adresu společně se jenom jedno omezení a používá tři ze 16 možných cest.

* Pokud kontejner má zásady jedinečného klíče, poplatky za jednotky (RU) žádost o vytvoření, aktualizace a odstranění položky jsou mírně vyšší.

* Zhuštěný jedinečné klíče nejsou podporovány. Pokud chybí některé hodnoty jedinečnou cestu, jsou považovány za hodnoty null, které jsou zahrnuty v omezení jedinečnosti. Proto může existovat pouze jednu položku s hodnotou null pro toto omezení.

* Jedinečné názvy klíčů jsou malá a velká písmena. Představte si třeba kontejner s omezení unique key nastavena na /address/zipcode. Pokud data obsahují pole s názvem PSČ, vloží Cosmos DB "null" jako jedinečný klíč, protože není stejný jako "PSČ" "PSČ". Z důvodu tohoto rozlišování velikosti písmen nelze všechny záznamy s PSČ vložit, protože duplicitní "null" bude porušovat omezení unique key.

## <a name="next-steps"></a>Další postup

* Další informace o [logickými oddíly](partition-data.md)
