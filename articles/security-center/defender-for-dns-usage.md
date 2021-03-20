---
title: Reakce na upozornění služby DNS na Azure Defender
description: Přečtěte si o krocích nezbytných pro reakci na výstrahy z Azure Defenderu pro DNS.
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754669"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>Reakce na upozornění Azure Defenderu pro DNS

Když obdržíte výstrahu od Azure Defenderu pro DNS, doporučujeme, abyste prošetřili a odpověděli na výstrahu, jak je popsáno níže. Azure Defender pro službu DNS chrání všechny připojené prostředky, takže i když jste obeznámeni s aplikací nebo uživatelem, který výstrahu aktivoval, je důležité ověřit situaci okolní s každou výstrahou.  


## <a name="step-1-contact"></a>Krok 1. Kontakt

1. Obraťte se na vlastníka prostředku a zjistěte, zda bylo chování očekáváno nebo záměrné.
1. Pokud je aktivita očekávaná, zavřete výstrahu.
1. Pokud je aktivita neočekávaná, považovat prostředek za potenciálně napadení a zmírnit, jak je popsáno v dalším kroku.

## <a name="step-2-immediate-mitigation"></a>Krok 2. Okamžité zmírnění 

1. Izolujte prostředek ze sítě, abyste zabránili bočnímu pohybu.
1. Spusťte úplnou kontrolu proti malwaru na prostředku, a to podle jakékoli výsledné Rady pro nápravu.
1. Přečtěte si nainstalovaný a běžící software na prostředku a odeberte všechny neznámé nebo nežádoucí balíčky.
1. Vraťte počítač do známého funkčního stavu, v případě potřeby nainstalujte operační systém a obnovte software z ověřeného zdroje bez malwaru.
1. Vyřešte všechna Azure Security Center doporučení pro daný počítač, oprava zvýrazněné problémy se zabezpečením, aby se předešlo budoucímu narušení.


## <a name="next-steps"></a>Další kroky

Tato stránka vysvětluje proces reakce na výstrahu z Azure Defenderu pro DNS. Související informace najdete na následujících stránkách:

- [Seznámení s Azure Defenderem pro DNS](defender-for-dns-introduction.md)
- [Potlačit výstrahy z Azure Defenderu](alerts-suppression-rules.md)
- [Průběžný export Security Center dat](continuous-export.md)