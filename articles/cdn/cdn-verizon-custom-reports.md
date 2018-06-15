---
title: Vlastní sestavy od společnosti Verizon | Microsoft Docs
description: 'Vzorce můžete zobrazit pro vaše CDN pomocí následující sestavy: šířky pásma, přenesená Data, přístupů, mezipaměti stavy, poměr přístupů do mezipaměti, přenesená Data IPV4 nebo IPV6.'
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: v-deasim
ms.openlocfilehash: f09195dc07a96ebcca7f7a9e4bcf521fae13630c
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2017
ms.locfileid: "26343884"
---
# <a name="custom-reports-from-verizon"></a>Vlastní sestavy od společnosti Verizon

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Pomocí sestav vlastní Verizon prostřednictvím portálu spravovat profilů Verizon můžete definovat typu dat, které se mají shromažďovat pro sestavy záznamů CNAME okraj.


## <a name="accessing-verizon-custom-reports"></a>Přístup k vlastní Verizon sestavy
1. Okno profil CDN, klikněte **spravovat** tlačítko.
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
2. Najeďte myší **Analytics** a potom přejděte myší **vlastní sestavy** plovoucím panelem. Klikněte na tlačítko **okraj záznamů CNAME**.
   
    ![Portál pro správu CDN - vlastní sestavy nabídky](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Vlastní sestavu Edge záznamů CNAME
Vlastní sestavu záznamů CNAME Edge poskytuje přístupů a přenesená data statistiky pro hraniční záznamů CNAME, na které bylo povoleno protokolování vlastní sestavy. Hraniční záznamů CNAME se skládají z koncového bodu Azure CDN názvy hostitelů a všechny názvy hostitelů přidružené vlastní doménu. 

Protokolování dat vlastní sestavu začne hodinu potom, co povolíte vlastní vykazovací funkci hraniční CNAME. Data sestavy můžete zobrazit pomocí generování zprávu o hraniční záznamů CNAME pro konkrétní platformu nebo pro všechny platformy. Pokrytí pro tuto sestavu je omezený na hraniční záznamů CNAME, pro které nebyla shromážděna vlastní sestavu data během zadaného časového období. Okraj sestavy záznamů CNAME se skládá z graf a datovou tabulku pro horní okraj 10 záznamů CNAME podle metriky definované v možnost nastavení. 

Generovat vlastní sestavy definováním následující možnosti sestavy:

- Metrika: Jsou podporovány následující možnosti:

   - Přístupů: Určuje celkový počet požadavků, které jsou směrované hraniční CNAME, na níž je povolen vlastní možnosti vytváření sestav. Tato metrika nezahrnuje stavový kód, který je vrácen do klienta.

   - Data Transfer: Označuje celkovou velikost dat přenášených ze serverů edge na klientů protokolu HTTP (například webové prohlížeče) pro požadavky, které jsou směrované hraniční CNAME, na níž je povolen vlastní možnosti vytváření sestav. Množství dat přenesených se počítá přidáním hlavičky HTTP odpovědi do těla odpovědi. V důsledku toho množství dat přenesených pro každý prostředek je větší než jeho aktuální velikost souboru.

- Seskupení: Určuje typ statistiky, které jsou uvedeny níže pruhový graf. Podporovány jsou následující možnosti:

   - Kódy odpovědí HTTP: Uspořádá statistiky podle kódu odpovědi HTTP (například 200, 403, atd.) vrácen do klienta. 

   - Stav mezipaměti: Slouží k uspořádání statistiky podle stavu mezipaměti.


Pokud chcete nastavit rozsah dat pro sestavu, můžete buď vybrat předem definované období, například **Dnes** nebo **tato týdnu**, z rozevíracího seznamu, nebo můžete vybrat **vlastní** a ručně zadejte rozsah dat kliknutím na ikony kalendáře. 

Jakmile vyberete rozsah dat, klikněte na tlačítko **přejděte** pro generování sestavy.

Kliknutím na symbol Excel napravo od můžete exportovat data ve formátu aplikace Excel **přejděte** tlačítko.

![Sestava záznamů CNAME](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Pole vlastní sestavy záznamů CNAME Edge

| Pole                     | Popis   |
|---------------------------|---------------|
| 2xx                       | Určuje celkový počet požadavků nebo přenesených dat (MB) pro hranici CNAME, který nastane stavový kód HTTP 2xx (například 200 OK). |
| 3xx                       | Určuje celkový počet požadavků nebo přenesených dat (MB) pro hranici CNAME, který nastane stavový kód HTTP 3xx (například 302 najít nebo 304 nedojde ke změně. |
| 4xx                       | Určuje celkový počet požadavků nebo přenesených dat (MB) pro hranici CNAME, který nastane stavový kód HTTP 4xx (například 400 Chybný požadavek, 403 zakázané nebo 404 nebyl nalezen). |
| 5xx                       | Určuje celkový počet požadavků nebo data přenesená (MB) pro hranici CNAME, jejímž výsledkem kód stavu HTTP 5xx (například 500 – Vnitřní chyba serveru nebo 502 Chybná brána). |
| Procento vstupů do mezipaměti               | Informuje o procentu Uložitelný požadavků, které se spouští přímo z mezipaměti na žadatel. |
| Přístupů k mezipaměti                | Určuje celkový počet požadavků nebo přenesených dat (MB) pro hranici CNAME, jejímž výsledkem vstupů do mezipaměti (například TCP_EXPIRED_HIT, TCP_HIT nebo TCP_PARTIAL_HIT). Vstupů do mezipaměti nastane, když je nalezena v mezipaměti verze požadovaného obsahu. |
| Přenesená data (MB)     | Označuje celkovou velikost přenesených dat (MB) ze serverů edge klientů protokolu HTTP (webových prohlížečů) pro hranici CNAME. Množství dat přenesených se počítá přidáním hlavičky HTTP odpovědi do těla odpovědi. V důsledku toho množství dat přenesených pro každý prostředek je větší než jeho aktuální velikost souboru. |
| Popis               | Identifikuje okraj CNAME podle jeho názvu hostitele |
| Přístupů                      | Určuje celkový počet požadavků na hranici CNAME |
| Neúspěchy                    | Určuje celkový počet požadavků nebo přenesených dat (MB) pro hranici CNAME, který vede neúspěšnému přístupu do mezipaměti (například TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS nebo TCP_MISS). K neúspěšnému přístupu do mezipaměti nastane, když je požadovaný obsah nebyl uložen do mezipaměti na serveru edge, který dodržení požadavku. | 
| Žádné mezipaměti                  | Určuje celkový počet požadavků nebo přenesených dat (MB) pro hranici CNAME, jejímž výsledkem CONFIG_NOCACHE mezipaměti stavový kód.  |
| Ostatní                     | Určuje celkový počet požadavků nebo data přenášená (MB) pro hranici CNAME uvedené, jejímž výsledkem stavový kód protokolu HTTP, která spadá mimo 2xx – 5xx rozsahu. |
| Platforma                  | Označuje platforma, která zpracovává edge CNAME provoz. |
| Nepřiřazené               | Určuje celkový počet požadavků nebo data přenesená (MB) pro CNAME edge, pro které mezipaměti stavový kód nebo stavového kódu protokolu HTTP nebylo do něj protokolují informace.  |
| Uncacheable               | Určuje celkový počet požadavků nebo přenesených dat (MB) pro hranici CNAME, který vede UNCACHEABLE mezipaměti stavový kód.  |


## <a name="considerations"></a>Požadavky
Sestavy lze vytvořit pouze v rámci poslední 18 měsíců.

