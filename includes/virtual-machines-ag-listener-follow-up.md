---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66165445"
---
Po vytvoření naslouchacího procesu skupiny dostupnosti může být nutné upravit parametry RegisterAllProvidersIP a HostRecordTTL clusteru pro prostředek naslouchacího procesu. Tyto parametry můžete zkrátit dobu opětovného připojení po převzetí služeb, které by mohly bránit vypršení časového limitu připojení. Další informace o těchto parametrů, stejně jako ukázkový kód, naleznete v tématu [vytvoření nebo konfigurace naslouchacího procesu skupiny dostupnosti](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).

