---
title: zahrnout soubor
description: zahrnout soubor
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: b34d3ed2481600cde7ffb3275d635b8a57926bd6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665946"
---
Pro spolupráci jsou k dispozici následující role:

|Role|Funkce|Přístup přes rozhraní API|Oprávnění rozhraní API|
|--|--|--|--|
|Vlastník|Vše|Ověřovací klíč|Vše|
|Přispěvatel|Vše kromě možnosti přidávat nové členy k rolím|Ověřovací klíč|Vše kromě možnosti přidávat nové členy k rolím|
|QnA Maker čtení<br>oprávnění|Exportovat/stáhnout<br>Testování|Nosný token|1. stažení rozhraní API KB<br>2. seznam aktualizací KB pro rozhraní API pro uživatele<br>3. Získejte podrobnosti znalostní báze.<br>4. stažení změn<br>Generovat odpověď |
|Editor QnA Maker<br>(čtení a zápis)|Exportovat/stáhnout<br>Testování<br>Aktualizace KB<br>Exportovat KB<br>Import KB<br>Nahradit KB<br>Create KB|Nosný token|1. vytvoření rozhraní API KB<br>2. aktualizace KB API<br>3. nahrazení rozhraní API KB<br>4. nahrazení změn<br>5. "výukové rozhraní API" [v novém modelu služby V5]|
|Uživatel služby rozpoznávání<br>(čtení, zápis a publikování)|Vše|Nosný token|Vše|