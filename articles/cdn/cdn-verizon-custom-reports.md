---
title: Vlastní sestavy od Verizonu | Dokumentace Microsoftu
description: 'Můžete zobrazit vzorce používání vaší sítě CDN pomocí následující sestavy: šířky pásma, přenesená Data, přístupy, stavy mezipaměti, poměr přístupů do mezipaměti, přenesená Data IPV4/IPV6.'
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: magattus
ms.openlocfilehash: 75a1fb4beb4025dc3ec63d29944331d07be35d8a
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092062"
---
# <a name="custom-reports-from-verizon"></a>Vlastní sestavy od Verizonu

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Pomocí sestav vlastní Verizon prostřednictvím portálu spravovat pro Verizon profily můžete definovat typ jde data shromažďovat pro sestavy edge záznamů CNAME.


## <a name="accessing-verizon-custom-reports"></a>Přístup k vlastní Verizon sestavy
1. Z okna profil CDN, klikněte na tlačítko **spravovat** tlačítko.
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
2. Najeďte myší **Analytics** kartu a pak najeďte myší **vlastní sestavy** Kontextová nabídka. Klikněte na tlačítko **hraniční záznamů CNAME**.
   
    ![Portál pro správu CDN – vlastní sestavy nabídky](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Sestava vlastní záznamy CNAME Edge
Záznamy CNAME Edge vlastní sestava obsahuje přístupů a statistiky přenosu dat pro edge záznamů CNAME, na které bylo povoleno protokolování vlastní sestavy. Záznamy CNAME Edge se skládá z koncového bodu Azure CDN názvy hostitelů a žádný název hostitele vlastní domény přidružené. 

Vlastní sestavu protokolování dat začíná hodinu potom, co povolíte vlastní možnosti vytváření sestav edge CNAME. Data sestavy můžete zobrazit tak generuje sestavu Edge záznamů CNAME pro konkrétní platformy nebo pro všechny platformy. Pokrytí této sestavy je omezená na edge záznamů CNAME, pro které byl shromážděn vlastní sestavu dat během zadaného časového období. Okraje sestavy záznamy CNAME se skládá z graf a datovou tabulku pro horní okraj 10 záznamů CNAME podle metrika definovaná ve variantě pro metriky. 

Generovat vlastní sestavy tak, že definujete následující možnosti sestavy:

- Metriky: Jsou podporovány následující možnosti:

   - Počet přístupů: Určuje celkový počet požadavků, které jsou směrované na edge CNAME, na kterých je povoleno vlastní možnosti vytváření sestav. Tato metrika nezahrnuje stavový kód, který je vrácen do klienta.

   - Data Transfer: Určuje celkové množství dat přenášených ze serverů edge klientům protokolu HTTP (například webové prohlížeče) pro požadavky, které jsou směrované na edge CNAME, na kterých je povoleno vlastní možnosti vytváření sestav. Objem přenesených dat se vypočte tak přidání hlavičky odpovědi do těla odpovědi. V důsledku toho objemu dat přenesených pro každý prostředek je větší než skutečná velikost.

- Seskupení: Určuje typ statistické údaje, které jsou uvedeny níže pruhový graf. Podporovány jsou následující možnosti:

   - Kódy odpovědí protokolu HTTP: Uspořádá statistické údaje podle kódu odpovědi HTTP (například 200, 403, atd.) vrácen do klienta. 

   - Stav mezipaměti: Uspořádá statistické údaje podle stavu mezipaměti.


Nastavit rozsah dat pro sestavy, můžete vybrat předem definovaný rozsah, jako například **Dnes** nebo **tento týden**, z rozevíracího seznamu nebo můžete vybrat **vlastní** a ručně zadejte rozsah dat kliknutím na ikonu kalendáře. 

Jakmile vyberete rozsah dat, klikněte na tlačítko **Přejít** generování sestav.

Kliknutím na symbol Excel napravo od můžete exportovat data ve formátu aplikace Excel **Přejít** tlačítko.

![Záznamy CNAME sestavy](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Pole vlastní sestavy záznamů CNAME Edge

| Pole                     | Popis   |
|---------------------------|---------------|
| 2xx                       | Určuje celkový počet požadavků nebo přenesená data (MB) pro edge CNAME, jejímž výsledkem stavový kód HTTP 2xx (například 200 OK). |
| 3xx                       | Určuje celkový počet požadavků nebo přenesená data (MB) pro edge CNAME, jejímž výsledkem stavový kód HTTP 3xx (například 302 najít nebo 304 nedojde ke změně. |
| 4xx                       | Určuje celkový počet požadavků nebo přenesená data (MB) pro edge CNAME, jejímž výsledkem stavový kód HTTP 4xx (například 400 o neplatném požadavku, 403 Zakázáno nebo 404 Nenalezeno). |
| 5xx                       | Určuje celkový počet požadavků nebo přenesená data (MB) pro edge CNAME, jejímž výsledkem kód stavu HTTP 5xx (například 500 – Interní chyba serveru nebo 502 – Chybná brána). |
| Procento vstupů do mezipaměti               | Informuje o procentu možné ukládat do mezipaměti požadavky, které byly žadateli obsluhovat přímo z mezipaměti. |
| Přístupy do mezipaměti                | Určuje celkový počet požadavků nebo přenesená data (MB) pro edge CNAME, který má za následek přístup do mezipaměti (například TCP_EXPIRED_HIT, TCP_HIT nebo TCP_PARTIAL_HIT). Přístup do mezipaměti dojde k verzi v mezipaměti požadovaný obsah nachází. |
| Přenesená data (MB)     | Určuje celkové množství přenesených dat (MB) ze serverů edge klientům protokolu HTTP (webové prohlížeče) pro edge CNAME. Objem přenesených dat se vypočte tak přidání hlavičky odpovědi do těla odpovědi. V důsledku toho objemu dat přenesených pro každý prostředek je větší než skutečná velikost. |
| Popis               | Identifikuje okraj CNAME podle jeho názvu hostitele |
| Přístupy                      | Určuje celkový počet požadavků na hraničních zařízeních CNAME |
| Neúspěšné přístupy                    | Určuje celkový počet požadavků nebo přenesená data (MB) pro edge CNAME, který má za následek Neúspěšné přístupy mezipaměti do (například TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS nebo TCP_MISS). Neúspěšný přístup do mezipaměti nastane, pokud požadovaný obsah nebyl uložen do mezipaměti na hraničním serveru, neuplatňují požadavku. | 
| Žádná mezipaměť                  | Určuje celkový počet požadavků nebo přenesená data (MB) pro záznam CNAME, jejímž výsledkem stavový kód CONFIG_NOCACHE mezipaměti na hraničních zařízeních.  |
| Ostatní                     | Určuje celkový počet požadavků nebo údaje přenesené (MB) pro edge CNAME, jehož výsledkem stavový kód HTTP, který spadá mimo 2xx – rozsahu 5xx. |
| Platforma                  | Určuje platformu, která zpracovává přenosy edge CNAME. |
| Nepřiřazeno               | Určuje celkový počet požadavků nebo přenesená data (MB) pro CNAME edge, pro které mezipaměti stavový kód nebo kód stavu HTTP nebyla protokolována informace.  |
| Uncacheable               | Určuje celkový počet požadavků nebo přenesená data (MB) pro záznam CNAME, který vede stavový kód UNCACHEABLE mezipaměti na hraničních zařízeních.  |


## <a name="considerations"></a>Požadavky
Sestavy se můžou generovat jenom během posledních 18 měsíců.

