---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: 0ad3b360611a12b95568e9a20f13a57c93b2e603
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086117"
---
## <a name="roles-versus-hierarchical-entities"></a>Role a hierarchické entity

Byste měli použít hierarchická entity nebo vzor s jednoduchou entitu s rolemi? 

To záleží. Vzory a příklad projevy jsou srovnatelné, které představují utterance uživatele a jsou specifické pro záměru.  

Pokud projevy nemají vymazat vzor, používejte hierarchické entity. 

|hierarchické entity|Jednoduché entity s rolemi|
|--|--|
|K dispozici v příkladu projevy a vzory záměr.|K dispozici jenom ve vzorcích|
|Musí mít projevy příklad v záměr s podřízené entity označené jako|V příkladu projevy v záměr nemohou být označeny role|
|Možná bude nutné **Další** projevy příklad v úmyslu extrahovat entity|By mělo stačit **méně** projevy příklad v záměr|
