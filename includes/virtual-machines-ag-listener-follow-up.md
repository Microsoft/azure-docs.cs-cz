---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097651"
---
Po vytvoření naslouchacího procesu skupiny dostupnosti může být nutné upravit parametry RegisterAllProvidersIP a HostRecordTTL clusteru pro prostředek naslouchacího procesu. Tyto parametry můžete zkrátit dobu opětovného připojení po převzetí služeb, které by mohly bránit vypršení časového limitu připojení. Další informace o těchto parametrů, stejně jako ukázkový kód, naleznete v tématu [vytvoření nebo konfigurace naslouchacího procesu skupiny dostupnosti](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

<!-- Update_Description: update meta properties -->