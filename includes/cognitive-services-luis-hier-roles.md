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
ms.openlocfilehash: 3803bc7f1e0da01fbaa8aa11bc6e8fc5c508b5ae
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528831"
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
