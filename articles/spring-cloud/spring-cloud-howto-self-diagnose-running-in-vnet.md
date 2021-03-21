---
title: Jak provést samočinnou diagnostiku virtuální sítě VNET pro Azure jarní Cloud
description: Naučte se, jak sami diagnostikovat a řešit problémy v jarním cloudu Azure běžícím ve virtuální síti.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5407213b62902326d53b73e42ee3af1ba9b11524
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102125061"
---
# <a name="self-diagnose-running-azure-spring-cloud-in-vnet"></a>Samočinná diagnostika provozu Azure Pramenitého cloudu ve virtuální síti
Diagnostika jarní cloudu Azure podporuje interaktivní řešení potíží spouštěné ve virtuálních sítích bez konfigurace. Diagnostika Azure jaře cloudu identifikuje problémy a provede vás s informacemi, které vám pomohou řešit potíže a vyřešit je.

## <a name="navigate-to-the-diagnostics-page"></a>Přejít na stránku Diagnostika
Následující postup spustí diagnostiku pro síťové aplikace.
1. Přihlaste se k webu Azure Portal.
1. Přejít na stránku s přehledem jarního cloudu Azure.
1. V nabídce v levém navigačním podokně vyberte **Diagnostika a řešení problémů** .
1. Vyberte třetí kategorii, **síť**.

   ![Název pro samočinnou diagnostiku](media/spring-cloud-self-diagnose-vnet/self-diagostic-title.png)

## <a name="view-a-diagnostic-report"></a>Zobrazit diagnostickou sestavu
Po kliknutí na kategorii **sítě** si můžete zobrazit dva problémy, které se týkají sítě, které jsou specifické pro vaši virtuální síť, která byla vložená do Azure jarního cloudu: **rozlišení DNS** a **požadovaný odchozí provoz**.

   ![Možnosti pro samočinnou diagnostiku](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-req-outbound-options.png)

Vyberte cílový problém pro zobrazení diagnostické sestavy. Zobrazí se souhrn diagnostiky, jako například: 

* *Prostředek se odebral.*
* *Prostředek není nasazený ve vaší vlastní virtuální síti*.

Některé výsledky obsahují související dokumentaci. V různých podsítích budou výsledky zobrazeny samostatně.

## <a name="dns-resolution"></a>Překlad DNS 
Pokud vyberete **překlad DNS**, výsledky budou označovat, jestli jsou v aplikacích problémy se službou DNS.  Aplikace v pořádku jsou uvedené níže:

* *Problémy se službou DNS vyřešené bez problémů v podsíti "subnet01"*.
* *Problémy se službou DNS vyřešené bez problémů v podsíti "subnet02"*.

Následující příklad diagnostické sestavy indikuje, že stav aplikace je neznámý. Časový rámec vytváření sestav neobsahuje čas ohlášení stavu.  Předpokládejme, že je koncový čas kontextu *2021-03-03T04:20:00Z*. Poslední časové RAZÍTKo v **vykreslování tabulky překladu DNS** je *2021-03-03T03:39:00Z*, předchozí den. Protokol kontroly stavu nebyl pravděpodobně odeslán z důvodu blokované sítě. 

Výsledky neznámého stavu obsahují související dokumentaci.  Kliknutím na levou lomenou závorku můžete zobrazit rozevírací zobrazení.

   ![Neznámý server DNS](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-unknown.png)

Pokud jste nakonfigurovali sadu záznamů zóny Privátní DNS, získáte kritický výsledek, například: `Failed to resolve the Private DNS in subnet xxx` . 

V rozevíracím seznamu **vykreslování tabulky překladu DNS** najdete podrobné informace o zprávě, ze kterých můžete ověřit konfiguraci.

## <a name="required-outbound-traffic"></a>Požadovaný odchozí provoz 

Pokud vyberete **požadovaný odchozí provoz**, výsledky budou označovat, jestli dochází k problémům s odchozími přenosy s aplikacemi.  Aplikace v pořádku jsou uvedené níže:

* * Požadovaný odchozí provoz byl vyřešen bez problémů v podsíti "subnet01".
* * Požadovaný odchozí provoz byl vyřešen bez problémů v podsíti "subnet02".

Pokud NSG nebo pravidla brány firewall zablokuje nějakou podsíť a pokud jste protokol nezablokovali, najdete následující chyby. Můžete zkontrolovat, jestli jste přehledali [zodpovědnosti zákazníka](spring-cloud-vnet-customer-responsibilities.md).
    
   ![Koncový bod se nezdařil](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-failed.png)

Pokud v `Required Outbound Traffic Table Renderings` průběhu 30 minut nejsou žádná data, výsledek bude `health status unknown` . Možná je vaše síť blokovaná nebo služba protokolu nefunguje.

   ![Neznámý koncový bod diagnostiky](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-unknown.png)

## <a name="see-also"></a>Viz také
* [Jak provést samočinnou diagnostiku Azure jarního cloudu](spring-cloud-howto-self-diagnose-solve.md)