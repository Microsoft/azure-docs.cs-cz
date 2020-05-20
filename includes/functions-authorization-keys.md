---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 926434d7110877e234888682cb6c946afe3ae685
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648941"
---
Funkce umožňují používat klíče k tomu, aby během vývoje měly přístup k koncovým bodům funkce HTTP. Pokud není úroveň přístupu HTTP u funkce aktivované protokolem HTTP nastavená na `anonymous` , požadavky musí v žádosti zahrnovat přístupový klíč rozhraní API. 

#### <a name="authorization-scopes-function-level"></a>Obory autorizace (na úrovni funkcí)

Existují dva obory přístupu pro klíče na úrovni funkcí:

* **Funkce**: tyto klíče se vztahují pouze na konkrétní funkce, ve kterých jsou definovány. Když se použije jako klíč rozhraní API, povolí přístup k této funkci.

* **Hostitel**: klíče s oborem hostitele lze použít pro přístup ke všem funkcím v aplikaci Function App. Při použití jako klíč rozhraní API umožňují tyto funkce přístup k libovolné funkci v aplikaci Function App. 

Každý klíč má název pro referenci a výchozí klíč (s názvem "výchozí") na úrovni funkce a hostitele. Klíče funkcí mají přednost před klíči hostitele. Pokud jsou definovány dva klíče se stejným názvem, je klíč funkce vždy použit.

#### <a name="master-key-admin-level"></a>Hlavní klíč (úroveň správce) 

Každá aplikace Function App má také klíč hostitele na úrovni správce s názvem `_master` . Kromě poskytování přístupu na úrovni hostitele ke všem funkcím v aplikaci poskytuje hlavní klíč také přístup správce k rozhraním REST API pro modul runtime. Tento klíč nelze odvolat. Když nastavíte úroveň přístupu `admin` , musí žádosti používat hlavní klíč. všechny ostatní klíče mají za následek selhání přístupu.

> [!CAUTION]  
> Vzhledem ke zvýšeným oprávněním v aplikaci Function App udělené hlavním klíčem byste tento klíč neměli sdílet s třetími stranami nebo ho distribuovat v nativních klientských aplikacích. Při volbě úrovně přístupu správce buďte opatrní.
