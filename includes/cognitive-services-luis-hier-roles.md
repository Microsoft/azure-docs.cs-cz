---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: dee31fca841ea309128681637cc41fa0fb1e7aea
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55480647"
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
