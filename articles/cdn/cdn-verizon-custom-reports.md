---
title: Vlastní sestavy společnosti Verizon | Dokumenty společnosti Microsoft
description: 'Vzorce využití sítě CDN můžete zobrazit pomocí následujících sestav: Šířka pásma, Přenesená data, Přístupové požadavky, Stavy mezipaměti, Poměr přístupů do mezipaměti, IpV4/IPV6 Data Transferred.'
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
ms.topic: article
ms.date: 10/11/2017
ms.author: allensu
ms.openlocfilehash: 737803f5b7fcddb07787afbb48354b4ca922e9ed
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259744"
---
# <a name="custom-reports-from-verizon"></a>Vlastní sestavy od společnosti Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Pomocí vlastních sestav Verizon prostřednictvím portálu Správa pro profily Verizon můžete definovat typ dat, která mají být shromažďována pro sestavy hraničních CNAMEs.


## <a name="accessing-verizon-custom-reports"></a>Přístup k vlastním sestavům společnosti Verizon
1. V okně profilu CDN klepněte na tlačítko **Spravovat.**
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
2. Najeďte na kartu **Analytics** a najeďte na informační rámeček **Vlastní přehledy.** Klepněte na **okraj cnames**.
   
    ![Portál pro správu CDN - Nabídka Vlastní sestavy](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Vlastní sestava CNAMES okrajů
Vlastní sestava Edge CNAMES poskytuje požadavky na server a statistiky přenesené daty pro hraniční CNAMEs, na kterých bylo povoleno vlastní protokolování sestav. Edge CNAMEs se skládají z názvů hostitelů koncového bodu Azure CDN a všech přidružených názvů hostitelů vlastních domén. 

Protokolování vlastních dat sestavy začíná jednu hodinu po povolení funkce vlastního vytváření sestav CNAME. Data sestavy můžete zobrazit generováním sestavy Edge CNAMEs pro konkrétní platformu nebo pro všechny platformy. Pokrytí pro tuto sestavu je omezeno na hraniční CNAMEs, pro které byla během zadaného časového období shromážděna vlastní data sestavy. Sestava hraničních CNAMse se skládá z grafu a tabulky dat pro 10 okrajových cnamů podle metriky definované v možnosti Metriky. 

Vygenerujte vlastní sestavu definováním následujících možností sestavy:

- Metriky: Jsou podporovány následující možnosti:

   - Požadavky na server: Označuje celkový počet požadavků, které jsou směrovány na okraj CNAME, na kterém je povolena vlastní schopnost vytváření sestav. Tato metrika nezahrnuje stavový kód vrácený klientovi.

   - Přenesená data: Označuje celkové množství dat přenesených z hraničních serverů klientům HTTP (například webovým prohlížečům) pro požadavky, které jsou směrovány na hraniční cname, na kterém je povolena vlastní funkce vykazování. Množství přenesených dat se vypočítá přidáním hlaviček odpovědi HTTP do těla odpovědi. V důsledku toho je množství dat přenesených pro každý datový zdroj větší než jeho skutečná velikost souboru.

- Seskupení: Určuje typ statistiky, které jsou zobrazeny pod pruhovým grafem. Podporovány jsou následující možnosti:

   - Kódy odpovědí HTTP: Uspořádá statistiky podle kódu odpovědi HTTP (například 200, 403 atd.) vrácených klientovi. 

   - Stav mezipaměti: Uspořádá statistiky podle stavu mezipaměti.


Chcete-li nastavit rozsah dat sestavy, můžete buď vybrat předdefinovaný rozsah dat, například **Dnes** nebo **Tento týden**, z rozevíracího seznamu nebo můžete vybrat **Vlastní** a ručně zadat časové období klepnutím na ikony kalendáře. 

Po výběru období vygenerujte sestavu klepnutím na **tlačítko Přejít.**

Data můžete exportovat ve formátu Excelu kliknutím na symbol Aplikace Excel vpravo od tlačítka **Přejít.**

![Zpráva CNAMEs](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Vlastní pole sestavy Hraniční názvy CNAMES

| Pole                     | Popis   |
|---------------------------|---------------|
| 2xx                       | Označuje celkový počet požadavků nebo dat přenesených (MB) pro hraniční CNAME, jehož výsledkem je stavový kód 2xx HTTP (například 200 OK). |
| 3xx                       | Označuje celkový počet požadavků nebo dat přenesených (MB) pro okraj CNAME, jehož výsledkem je stavový kód 3xx HTTP (například 302 Nalezeno nebo 304 Nezměněno. |
| 4xx                       | Označuje celkový počet požadavků nebo dat přenesených (MB) pro okraj CNAME, jehož výsledkem je 4xx HTTP stavový kód (například 400 chybný požadavek, 403 zakázáno nebo 404 nebyl nalezen). |
| 5xx                       | Označuje celkový počet požadavků nebo dat přenesených (MB) pro hraniční CNAME, jehož výsledkem je stavový kód 5xx HTTP (například 500 Vnitřní chyba serveru nebo 502 Chybná brána). |
| Počet přístupů do mezipaměti %               | Označuje procento požadavků, které lze uložit do mezipaměti a které byly doručeny přímo z mezipaměti žadateli. |
| Přístupy do mezipaměti                | Označuje celkový počet požadavků nebo přenesených dat (MB) pro hraniční CNAME, který má za následek přístup do mezipaměti (například TCP_EXPIRED_HIT, TCP_HIT nebo TCP_PARTIAL_HIT). K přístupu do mezipaměti dochází při nalezení verze požadovaného obsahu uložené v mezipaměti. |
| Přenesená data (MB)     | Označuje celkové množství dat přenesených (MB) z hraničních serverů do klientů HTTP (webových prohlížečů) pro hraniční cname. Množství přenesených dat se vypočítá přidáním hlaviček odpovědi HTTP do těla odpovědi. V důsledku toho je množství dat přenesených pro každý datový zdroj větší než jeho skutečná velikost souboru. |
| Popis               | Identifikuje okraj CNAME podle názvu hostitele. |
| Hits                      | Označuje celkový počet požadavků na okraj CNAME. |
| Chybí                    | Označuje celkový počet požadavků nebo dat přenesených (MB) pro hraniční CNAME, což má za následek chybě v mezipaměti (například TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS nebo TCP_MISS). K chybě mezipaměti dojde, když požadovaný obsah nebyl uložen do mezipaměti na hraničním serveru, který požadavek dodržel. | 
| Žádná mezipaměť                  | Označuje celkový počet požadavků nebo dat přenesených (MB) pro hraniční CNAME, což má za následek CONFIG_NOCACHE stavový kód mezipaměti.  |
| Ostatní                     | Označuje celkový počet požadavků nebo dat přenesených (MB) pro okraj CNAME uvedeno, že výsledkem je stavový kód HTTP, který spadá mimo rozsah 2xx - 5xx. |
| Platforma                  | Označuje platformu, která zpracovává provoz hrany CNAME. |
| Unassigned               | Označuje celkový počet požadavků nebo dat přenesených (MB) pro hraniční CNAME, pro který nebyl zaznamenán stavový kód mezipaměti nebo informace o stavu HTTP.  |
| Neuložit do mezipaměti               | Označuje celkový počet požadavků nebo dat přenesených (MB) pro hraniční CNAME, což má za následek stavový kód mezipaměti UNCACHEABLE.  |


## <a name="considerations"></a>Požadavky
Sestavy lze generovat pouze během posledních 18 měsíců.

