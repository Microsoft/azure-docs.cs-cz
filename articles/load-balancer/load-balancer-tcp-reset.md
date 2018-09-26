---
title: Načíst obnovení protokolu TCP nástroje pro vyrovnávání na nečinnosti | Dokumentace Microsoftu
description: Nástroj pro vyrovnávání zatížení s obousměrný TCP RVNÍ paketů na časový limit nečinnosti
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: kumud
ms.openlocfilehash: 212212d4650fe25b4ced591d4d4f1ee184e3fc44
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47163320"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>Nástroj pro vyrovnávání zatížení s TCP Nulováním nečinnosti (Public Preview)

Můžete použít [Load balanceru úrovně Standard](load-balancer-standard-overview.md) vytvoření předvídatelnějšího chování aplikace pro vaše scénáře s obousměrný TCP resetuje (TCP RVNÍ paket) pro každé konfigurovatelné nečinnosti časový limit.  Výchozí chování pro vyrovnávání zatížení je tiše síťový toky, když je dosaženo časového limitu nečinnosti toku.

![Obnovení protokolu TCP nástroje pro vyrovnávání zatížení](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>Nástroj pro vyrovnávání zatížení se vynulují funkce časového limitu nečinnosti protokolu TCP je v tuto chvíli dostupná jako veřejná verze Preview a k dispozici v omezenou sadu [oblastech](#regions). V této verzi preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce nemusí být podporované, nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Změnit toto výchozí chování a odesílat TCP resetuje na časový limit nečinnosti na pravidla příchozího překladu adres, Vyrovnávání zatížení a pravidla povolit a [odchozí pravidla](https://aka.ms/lboutboundrules).  Když povolena pro každé pravidlo nástroje pro vyrovnávání zatížení odešle obousměrné TCP Reset (TCP RVNÍ pakety) do koncových bodů klienta i serveru v době nečinnosti časový limit pro všechny toky odpovídající.

Příjem paketů TCP RVNÍ koncové body odpovídající soketu okamžitě ukončena. Tento postup umožňuje okamžité odeslání oznámení do koncových bodů, které vydání sady připojení došlo k chybě a všechny budoucí komunikaci na stejné připojení TCP se nezdaří.  Aplikace může odstranit připojení, když soket nezavře a znovu vytvořit připojení podle potřeby a bez čekání na připojení TCP se nakonec vypršení časového limitu.

Pro většinu scénářů to může snížit nutnost odesílání TCP (nebo aplikační vrstva) keepalive aktualizovat časový limit nečinnosti toku. 

Pokud vaše nečinnosti dob trvání překračují povolené konfigurací nebo vaše aplikace zobrazuje nežádoucí chování se resetuje TCP povolený, může stále muset použít keepalive TCP (nebo keepalive vrstvy aplikace) k monitorování aktivity připojení TCP.  Dále keepalive lze také budou fungovat i když je připojení směrovány přes proxy server někde v cestě, zejména keepalive vrstvy aplikace.  

Pečlivě zkontrolujte celý scénář koncového rozhodnout, jestli můžete využívat povolení TCP obnoví nastavení časového limitu nečinnosti, a pokud může být nutné další kroky k zajištění chování požadované aplikace.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Povoluje se na časový limit nečinnosti TCP Reset

Pomocí rozhraní API verze 2018-07-01, můžete povolit odesílání obousměrné TCP resetuje na časový limit nečinnosti na základě za pravidla:

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="regions"></a> Regionální dostupnost

Tento parametr je aktuálně efektivní v následujících oblastech.  V oblasti, které tu nejsou uvedené parametr nemá žádný vliv.

| Oblast |
|---|
| Jihovýchodní Asie |
| USA – východ 2 |
| USA – sever |
| USA – západ |

Tato tabulka bude aktualizovat, protože ve verzi preview je rozšířena do jiných oblastí.  

## <a name="limitations"></a>Omezení

- Omezené [dostupnost v oblastech](#regions).
- Portál nelze použít ke konfiguraci nebo zobrazit TCP Reset.  Použijte šablony, rozhraní REST API, Az CLI 2.0 nebo prostředí PowerShell.

## <a name="next-steps"></a>Další postup

- Další informace o [Load balanceru úrovně Standard](load-balancer-standard-overview.md).
- Další informace o [odchozí pravidla](load-balancer-outbound-rules-overview.md).
