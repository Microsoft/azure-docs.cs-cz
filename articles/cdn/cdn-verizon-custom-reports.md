---
title: Vlastní sestavy z Verizon | Microsoft Docs
description: 'Vzorce používání pro CDN můžete zobrazit pomocí následujících sestav: šířka pásma, přenesená data, počet přístupů, stavy mezipaměti, poměr přístupů do mezipaměti, přenesených dat IPV4/IPV6.'
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/11/2017
ms.author: allensu
ms.openlocfilehash: cff355aeb74efc9dec2780840ae57fea4c5764cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84888706"
---
# <a name="custom-reports-from-verizon"></a>Vlastní sestavy od Verizonu

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Pomocí vlastních sestav Verizon prostřednictvím portálu spravovat portál pro profily Verizon můžete definovat typ dat, která se mají shromažďovat pro sestavy CNAME Edge.


## <a name="accessing-verizon-custom-reports"></a>Přístup k vlastním sestavám Verizon
1. V okně profilu CDN klikněte na tlačítko **Spravovat** .
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
2. Najeďte myší na kartu **Analytics** a pak najeďte myší na informační rámeček **vlastních sestav** . Klikněte na **Edge CNAME**.
   
    ![Portál pro správu CDN – nabídka vlastní sestavy](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Vlastní sestava hraničních záznamů CNAME
Vlastní sestava hraničních záznamů CNAME poskytuje pro záznamy CNAME hraničních záznamů, ve kterých je povolené vlastní protokolování sestav, údaje o počtu a datech přenosu dat. Záznamy CNAME Edge se skládají z Azure CDN názvů hostitelů koncových bodů a všech přidružených názvů vlastních domén. 

Protokolování dat vlastní sestavy začíná jednu hodinu po povolení možnosti vytváření sestav CNAME okraje. Data sestavy můžete zobrazit tak, že vygenerujete sestavu hraničních záznamů CNAME pro konkrétní platformu nebo pro všechny platformy. Pokrytí této sestavy je omezeno na okraje CNAME, pro které byla během zadaného časového období shromážděna vlastní data sestavy. Sestava hraničních záznamů CNAME se skládá z grafu a tabulky dat pro prvních 10 záznamů CNAME na základě metriky definované v možnosti metriky. 

Vygenerujte vlastní sestavu definováním následujících možností sestavy:

- Metriky: podporovány jsou následující možnosti:

   - Počet přístupů: označuje celkový počet požadavků, které jsou směrovány na hraniční záznam CNAME, na kterém je povolena funkce vlastního generování sestav. Tato metrika nezahrnuje stavový kód vrácený klientovi.

   - Přenesená data: označuje celkové množství dat přenesených z hraničních serverů do klientů HTTP (například webových prohlížečů) pro požadavky směrované na hraniční záznam CNAME, na kterém je povolená možnost vlastního generování sestav. Množství přenesených dat se počítá přidáním hlaviček odpovědi HTTP do těla odpovědi. V důsledku toho je objem přenesených dat pro každý Asset větší než skutečná velikost souboru.

- Seskupení: Určuje typ statistik zobrazených pod pruhovým grafem. Podporovány jsou následující možnosti:

   - Kódy odpovědí HTTP: uspořádá statistiku podle kódu odpovědi HTTP (například 200, 403 atd.), který se vrátí klientovi. 

   - Stav mezipaměti: uspořádá statistiku podle stavu mezipaměti.


Chcete-li nastavit rozsah kalendářních dat pro sestavu, můžete buď vybrat předem definovaný rozsah dat, například **dnešní den** nebo **týden**, z rozevíracího seznamu, nebo můžete vybrat možnost **vlastní** a ručně zadat rozsah dat kliknutím na ikony kalendáře. 

Po výběru rozsahu kalendářních dat kliknutím na tlačítko **Přejít** vygenerujte sestavu.

Data ve formátu aplikace Excel můžete exportovat kliknutím na symbol aplikace Excel napravo od tlačítka **Přejít** .

![Sestava záznamů CNAME](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Pole vlastní sestavy CNAME Edge

| Pole                     | Description   |
|---------------------------|---------------|
| 2xx                       | Označuje celkový počet požadavků nebo dat přenesených (MB) pro záznam CNAME hraničního zařízení, které má za následek 2xx stavový kód HTTP (například 200 OK). |
| 3xx                       | Označuje celkový počet požadavků nebo dat přenesených (MB) pro záznam CNAME hraničního typu, který má za následek 3xx stavový kód HTTP (například Nalezeno 302 nebo 304 Nezměněno. |
| 4xx                       | Označuje celkový počet požadavků nebo dat přenesených (MB) pro záznam CNAME hraničního typu, který má za následek 4xx stavový kód HTTP (například 400 Chybný požadavek, 403 zakázán nebo 404 nebyl nalezen). |
| 5xx                       | Označuje celkový počet požadavků nebo dat přenesených (MB) pro záznam CNAME hraničního zařízení, které má za následek 5xx stavový kód HTTP (například 500 interní chyba serveru nebo 502 Chybná brána). |
| % Přístupů do mezipaměti               | Označuje procento požadavků v mezipaměti, které byly obsluhovány přímo z mezipaměti žadateli. |
| Přístupy do mezipaměti                | Označuje celkový počet požadavků nebo dat přenesených (MB) pro záznam CNAME hraničního typu, který má za následek dosažení mezipaměti (například TCP_EXPIRED_HIT, TCP_HIT nebo TCP_PARTIAL_HIT). K mezipaměti dojde, když je nalezena verze požadovaného obsahu v mezipaměti. |
| Přenesená data (MB)     | Označuje celkový objem přenesených dat (MB) z hraničních serverů do klientů HTTP (webových prohlížečů) pro hraniční záznam CNAME. Množství přenesených dat se počítá přidáním hlaviček odpovědí HTTP do těla odpovědi. V důsledku toho je objem přenesených dat pro každý Asset větší než skutečná velikost souboru. |
| Description               | Identifikuje hraniční záznam CNAME podle jeho názvu hostitele. |
| Hits                      | Určuje celkový počet žádostí na hraniční záznam CNAME. |
| Výpadky                    | Označuje celkový počet požadavků nebo dat přenesených (MB) pro záznam CNAME hraničního typu, který má za následek neúspěšný přenos do mezipaměti (například TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS nebo TCP_MISS). K mezipaměti nedochází, když požadovaný obsah nebyl uložen do mezipaměti na hraničním serveru, který danou žádost dopustil. | 
| Žádná mezipaměť                  | Označuje celkový počet požadavků nebo dat přenesených (MB) pro záznam CNAME hraničního typu, který má za následek stavový kód CONFIG_NOCACHE cache.  |
| Jiné                     | Určuje celkový počet požadavků nebo přenesených dat (MB) pro hraniční záznam CNAME, který má za následek stavový kód HTTP, který spadá mimo rozsah 2xx-5xx. |
| Platforma                  | Označuje platformu, která zpracovává provoz CNAME hraničního záznamu. |
| Unassigned               | Označuje celkový počet požadavků nebo přenesených dat (MB) pro hraniční záznam CNAME, pro který se neprotokoluje informace o stavu mezipaměti nebo informace o stavovém kódu protokolu HTTP.  |
| Neukládatelné do mezipaměti               | Označuje celkový počet požadavků nebo dat přenesených (MB) pro záznam CNAME hraničního zařízení, které má za následek neukládatelné kódy stavu mezipaměti.  |


## <a name="considerations"></a>Důležité informace
Sestavy lze generovat pouze během posledních 18 měsíců.

