---
ms.service: hpc-cache
ms.topic: include
ms.date: 03/15/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 2804b3bb36a31c9cfbf4cce8db94b7469c8975c7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563231"
---
| Model využití | Režim ukládání do mezipaměti | Ověření back-endu | Maximální zpoždění před zpětným zápisem |
|--|--|--|--|
| Čtení těžkých, nečastých zápisů | Číst | Nikdy | Žádné |
| Více než 15% zápisů | Čtení/zápis | 8 hodin | 20 minut |
| Klienti obcházejí mezipaměť | Číst | 30 sekund | Žádné |
| Více než 15% zápisů, časté kontroly back-endu (30 sekund) | Čtení/zápis | 30 sekund | 20 minut |
| Více než 15% zápisů, časté kontroly back-endu (60 sekund) | Čtení/zápis | 60 sekund | 20 minut |
| Více než 15% zápisů, časté zpětný zápis | Čtení/zápis | 30 sekund | 30 sekund |
| Těžké čtení, kontrola záložního serveru každé 3 hodiny | Číst | 3 hodiny | Žádné |
