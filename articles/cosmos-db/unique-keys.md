---
title: Použijte jedinečné klíče ve službě Azure Cosmos DB
description: Další informace o použití jedinečné klíče v databázi Azure Cosmos
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.reviewer: sngun
ms.openlocfilehash: 3a7133d9c092ab8ad8a4bc585e3b0df2b8ca1234
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55999262"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Omezení jedinečných klíčů ve službě Azure Cosmos DB

Jedinečné klíče přidat vrstvu integrity dat do kontejneru Azure Cosmos. Vytvoříte zásady jedinečného klíče při vytváření kontejneru Azure Cosmos. S jedinečnými klíči Ujistěte se, že jedna nebo více hodnot v rámci logického oddílu je jedinečný. Můžete rovněž zaručují jedinečnost za [klíč oddílu](partition-data.md). 

Po vytvoření kontejneru s zásady jedinečného klíče, vytváření nových nebo aktualizovaných duplicitní položky v rámci logického oddílu je zabráněno, podle omezení unique key. Klíč oddílu, v kombinaci s jedinečný klíč zaručuje jedinečnost položky v rámci oboru kontejneru.

Představte si třeba kontejneru Azure Cosmos pomocí e-mailovou adresu jako omezení unique key a `CompanyID` jako klíč oddílu. Při konfiguraci e-mailovou adresu uživatele s jedinečný klíč, každá položka má jedinečnou e-mailovou adresu v rámci danou `CompanyID`. Duplicitní e-mailové adresy a stejnou hodnotu klíče oddílu nelze vytvořit dvě položky. 

Vytvoření položek pomocí stejné e-mailové adresy, ale není stejné křestní jméno, příjmení a e-mailovou adresu, přidat další zásady jedinečného klíče. Místo vytváření jedinečné klíče založeného na e-mailovou adresu, můžete také vytvořit jedinečný klíč s kombinací křestní jméno, příjmení a e-mailovou adresu. Tento klíč se označuje jako složený jedinečný klíč. V takovém případě se každá jedinečná kombinace tří hodnot v rámci danou `CompanyID` je povolen. 

Například kontejner může obsahovat položky s použitím následujících hodnot, kde každá položka respektuje omezení unique key.

|% CompanyID|Jméno|Příjmení|E-mailová adresa|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Společnost Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Společnost Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Pokud se pokusíte vložit jiná položka s kombinací uvedené v předchozí tabulce, zobrazí se chybová zpráva. Chyba udává, že omezení unique key nebyla splněna. Zobrazí se buď "prostředek se zadaným ID nebo názvem již existuje" nebo "Prostředek se zadaným ID, název nebo jedinečný index již existuje" jako návratovou zprávu. 

## <a name="define-a-unique-key"></a>Definovat jedinečný klíč

Jedinečné klíče můžete definovat pouze v případě, že vytvoříte kontejner služby Azure Cosmos. Jedinečný klíč, který působí na logický oddíl. V předchozím příkladu Pokud kontejner podle PSČ, skončíte se duplicitní položky v každém logického oddílu. Při vytvoření jedinečné klíče, zvažte následující vlastnosti:

* Nelze aktualizovat existující kontejner používat jiný jedinečný klíč. Jinými slovy po vytvoření kontejneru s zásady jedinečného klíče nelze změnit zásady.

* Pokud chcete nastavit jedinečný klíč pro existující kontejner, vytvořte nový kontejner s omezení unique key. Použijte nástroj pro migraci příslušná data pro přesun dat z existující kontejner do nového kontejneru. Pro kontejnery SQL, použijte [nástroj pro migraci dat](import-data.md) přesouvat data. Pro kontejnery MongoDB, použijte [mongoimport.exe nebo mongorestore.exe](mongodb-migrate.md) přesouvat data.

* Zásady jedinečného klíče může mít maximálně 16 hodnoty cest. Hodnotami může být například /firstName /lastName a /address/zipCode. Každé zásady jedinečného klíče může mít maximálně 10 jedinečných klíčů omezení nebo kombinace. Kombinované cesty pro každé omezení jedinečný index nesmí překročit 60 bajtů. V předchozím příkladu křestní jméno, příjmení a e-mailovou adresu společně se jedno omezení. Toto omezení používá 3 z 16 možných cest.

* Pokud kontejner má zásady jedinečného klíče, poplatky za jednotkách žádostí (RU) k vytvoření, aktualizace a odstranění položky jsou mírně vyšší.

* Zhuštěný jedinečné klíče nejsou podporovány. Pokud chybí některé hodnoty jedinečnou cestu, má zato, že hodnoty null, které jsou zahrnuty v omezení jedinečnosti. Z tohoto důvodu může být pouze jednu položku s hodnotou null pro toto omezení.

* Jedinečné názvy klíčů jsou malá a velká písmena. Představte si třeba kontejner s omezení unique key nastavena na /address/zipcode. Pokud data obsahují pole s názvem PSČ, Azure Cosmos DB vloží "null" jako jedinečný klíč, protože "PSČ" není stejná jako "PSČ." Z důvodu tohoto rozlišování velikosti písmen nelze všechny záznamy s PSČ vložit, protože duplicitní "null" porušuje jedinečné omezení klíče.

## <a name="next-steps"></a>Další postup

* Další informace o [logické oddíly](partition-data.md).
