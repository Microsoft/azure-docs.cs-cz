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
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 1d7723f356274bbd18b1ea08e34046da82a1057c
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646342"
---
**Dotaz**: byste měli použít hierarchická entity nebo vzor s jednoduchou entitu s rolemi? 

**Odpověď**:, které závisí. Vzory a příklad projevy jsou srovnatelné, které představují utterance uživatele a jsou specifické pro záměru.  

Pokud projevy nemají vymazat vzor, používejte hierarchické entity. 

|hierarchické entity|Jednoduché entity s rolemi|
|--|--|
|musí mít projevy příklad s podřízené entity označené v záměry|Příklad projevy, musí mít **role nemohou být označeny v záměrů**|
|můžete použít ve vzorcích|**musíte** použít ve vzorcích|
|Možná bude nutné **Další** projevy příklad v záměr|Možná bude nutné **méně** projevy příklad v záměr|
