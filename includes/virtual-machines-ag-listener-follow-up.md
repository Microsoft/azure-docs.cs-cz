---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226884"
---
Po vytvoření naslouchacího procesu skupiny dostupnosti může být nutné upravit parametry RegisterAllProvidersIP a HostRecordTTL clusteru pro prostředek naslouchacího procesu. Tyto parametry můžete zkrátit dobu opětovného připojení po převzetí služeb, které by mohly bránit vypršení časového limitu připojení. Další informace o těchto parametrů, stejně jako ukázkový kód, naleznete v tématu [vytvoření nebo konfigurace naslouchacího procesu skupiny dostupnosti](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

