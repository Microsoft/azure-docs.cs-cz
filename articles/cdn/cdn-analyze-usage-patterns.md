---
title: Základní sestavy od Verizonu | Dokumentace Microsoftu
description: 'Můžete zobrazit vzorce používání vaší sítě CDN pomocí následující sestavy: šířky pásma, přenesená Data, přístupy, stavy mezipaměti, poměr přístupů do mezipaměti, přenesená Data IPV4/IPV6.'
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d10a40d03f0f76676e70afdec94e9adfaa0dd09f
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162066"
---
# <a name="core-reports-from-verizon"></a>Základní sestavy od Verizonu

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Použitím Verizon základní sestavy prostřednictvím portálu spravovat pro Verizon profily můžete zobrazit vzorce používání vaší sítě CDN se následující sestavy:

* Šířka pásma
* Přenesená data
* Přístupy
* Stavy mezipaměti
* Poměr přístupů do mezipaměti
* Přenesená Data IPV4/IPV6

## <a name="accessing-verizon-core-reports"></a>Přístup k Verizon základní sestavy
1. Z okna profil CDN, klikněte na tlačítko **spravovat** tlačítko.
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
2. Najeďte myší **Analytics** kartu a pak najeďte myší **základní sestavy** Kontextová nabídka. Klikněte na sestavu v nabídce.
   
    ![Portál pro správu CDN – nabídky základní sestavy](./media/cdn-reports/cdn-core-reports.png)

3. Pro každou sestavu, vyberte rozsah dat z **rozsah** seznamu. Můžete vybrat předem definovaný rozsah, jako například **Dnes** nebo **tento týden**, nebo můžete vybrat **vlastní** a ručně zadejte rozsah dat kliknutím na ikonu kalendáře. 

4. Jakmile vyberete rozsah dat, klikněte na tlačítko **Přejít** generování sestav. 

4. Pokud chcete exportovat data ve formátu aplikace Excel, klikněte na ikonu Excelu výše **Přejít** tlačítko.

## <a name="bandwidth"></a>Šířka pásma
Sestava šířky pásma se skládá z grafu a data tabulky, která určuje využití šířky pásma CDN za určité časové období, v MB/s pro protokol HTTP a HTTPS. Využití šířky pásma můžete zobrazit ve všech bodů POP, nebo pro konkrétní místní nabídky. Tato sestava umožňuje zobrazit provozní špičky a distribuci POP.

Z **hraničních uzlů** seznamu vyberte **všech hraničních uzlů** sledovat provoz ze všech uzlů nebo vyberte určité oblasti.

Sestava se aktualizuje každých pět minut.

![Sestava šířky pásma](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Přenesená data
Tato sestava se skládá z grafu a data tabulky, která určuje využití CDN provozu za určité časové období, v GB pro protokol HTTP a HTTPS. Využití provozu můžete zobrazit ve všech bodů POP, nebo pro konkrétní místní nabídky. Tato sestava umožňuje zobrazit provozní špičky a distribuci přes POP.

Z **hraničních uzlů** seznamu vyberte **všech hraničních uzlů** sledovat provoz ze všech uzlů nebo vyberte určité oblasti.

Sestava se aktualizuje každých pět minut.

![Přenesená data sestavy](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Počet přístupů (stavové kódy)
Tato sestava popisuje distribuce požadavek stavové kódy pro váš obsah. Každý požadavek pro obsah generuje stavový kód HTTP. Stavový kód popisuje, jak umístění POP serveru edge zpracování požadavku. Stavový kód 2xx například označuje, že žádost úspěšně vyřídila klientovi, zatímco 4xx stavový kód označuje, že došlo k chybě. Další informace o stavových kódů HTTP najdete v tématu [stavové kódy HTTP seznamu](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![Přístupy do sestavy](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Stavy mezipaměti
Tato sestava popisuje distribuce přístupů k mezipaměti a Neúspěšné přístupy do mezipaměti pro požadavky klientů. Vzhledem k tomu, abyste dosáhli nejlepšího výkonu vyplývá z přístupů k mezipaměti, můžete optimalizovat doručování rychlosti dat minimalizací Neúspěšné přístupy do mezipaměti a přístupů k mezipaměti vypršela platnost. 

Abyste snížili Neúspěšné přístupy do mezipaměti, nakonfigurujte server původu, abyste minimalizovali použití z následujících akcí: 
 * `no-cache` Hlavičky odpovědi
 * Řetězec dotazu do mezipaměti, pokud není nezbytně nutné  
 * Kódy odpovědí non-možné ukládat do mezipaměti

Chcete-li snížit přístupů k mezipaměti vypršela platnost, nastavte prostředků `max-age` na dlouhou dobu, chcete-li minimalizovat počet požadavků na zdrojový server.

![Sestava stavy mezipaměti](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Hlavní mezipaměti stavy patří:
* TCP_HIT: Obsluhovat z hraničního serveru. Objekt byl v mezipaměti a nebyla překročena jeho max-age.
* TCP_MISS: Obsluhují ze zdrojového serveru. Objekt nebyl v mezipaměti a odpověď byla zpět na původní název.
* TCP_EXPIRED _MISS: obsluhují ze zdrojového serveru po opětovné ověření s původu. Objekt byl v mezipaměti, ale překročil svůj max-age. Opětovné ověření s původem výsledkem objektu mezipaměti teď nahrazuje nová odpověď z původu.
* TCP_EXPIRED _HIT: obsluhovat z hraničních po opětovné ověření s původu. Objekt byl v mezipaměti, ale překročil svůj max-age. Opětovné ověření je zdrojový server je v objektu mezipaměti je bez jakýchkoli úprav.

### <a name="full-list-of-cache-statuses"></a>Úplný seznam stavy mezipaměti
* TCP_HIT – tento stav se zobrazí, když požadavek pochází přímo z POP klientovi. Prostředek okamžitě pochází z místní nabídky se uloží do mezipaměti na co nejblíže ke klientovi POP a má platný time to live (TTL). Hodnota TTL je určeno následující hlavičky odpovědi:
  
  * Cache-Control: s-maxage
  * Cache-Control: max-age
  * Platnost vyprší
* TCP_MISS: Tento stav indikuje, že verzi v mezipaměti požadovaný prostředek nebyl nalezen na nejbližší klientovi POP. Prostředek je požadováno ze zdrojového serveru nebo serveru shield původu. Pokud zdrojový server nebo server původu shield vrátí prostředek, je dodávat do klienta a ukládat do mezipaměti na klientovi a serveru edge. Jinak než 200 stavový kód (například 403 Zakázáno nebo 404 Nenalezeno) se vrátí.
* TCP_EXPIRED_HIT: Tento stav je uveden při zpracování žádosti, která cílí na prostředek s vypršenou platností TTL přímo v lokalitě POP do klienta. Například když majetek společnosti max-age vypršela platnost. 
  
   Vypršela platnost žádosti o obvykle vytváří požadavek na opětovné ověření na zdrojový server. Stav TCP_EXPIRED_HIT dochází musíte uvést server původu, novější verzi prostředku neexistuje. Tato situace obvykle výsledkem aktualizace prostředku Cache-Control a záhlaví Expires.
* TCP_EXPIRED_MISS: Tento stav je oznamují se novější verze prostředku uložená v mezipaměti vypršela platnost se načítají z POP do klienta. Tento stav dojde, pokud je hodnota TTL mezipaměti asset platnost (například s prošlou platností max-age) a zdrojový server vrátí novější verze tohoto prostředku. Tato nová verze prostředku slouží ke klientovi místo verze uložené v mezipaměti. Kromě toho je mezipaměti na hraničním serveru a klienta.
* CONFIG_NOCACHE: Tento stav indikuje, že konfigurace specifické pro zákazníka na hraničních zařízeních POP zabránila asset ukládat do mezipaměti.
* NONE – tento stav indikuje, že se neprovedla Kontrola aktuálnosti obsahu mezipaměti.
* TCP_CLIENT_REFRESH_MISS: Tento stav je uveden při klientovi HTTP, jako je například prohlížeč, vynutí okraj POP k načtení nové verze zastaralé asset ze zdrojového serveru. Ve výchozím nastavení servery zabránit v klientovi HTTP vynucení hraniční servery, které chcete načíst ze zdrojového serveru na novou verzi prostředku.
* TCP_PARTIAL_HIT: Tento stav je hlášené, když požadavek rozsahu bajtů výsledkem přístupů pro prostředek částečně uložené v mezipaměti. Požadovaný rozsah bajtů je okamžitě obsluhovat z POP do klienta.
* UNCACHEABLE: Tento stav je uveden při prostředků `Cache-Control` a `Expires` záhlaví znamenat, že by neměl být uložené v mezipaměti, na místní nabídky nebo pomocí klienta HTTP. Tyto druhy požadavky se obsluhují ze zdrojového serveru.

## <a name="cache-hit-ratio"></a>Poměr přístupů do mezipaměti
Tato sestava informuje o procentu žádosti uložené v mezipaměti, které byly obsluhovat přímo z mezipaměti.

Tato sestava poskytuje následující podrobnosti:

* Požadovaný obsah ukládá do mezipaměti na nejbližší žadateli POP.
* Přímo z hraničních zařízeních v naší síti zpracování žádosti.
* Požadavek na opětovné ověření je zdrojový server nevyžadoval.

Sestava neobsahuje:

* Požadavky, kterým je odepřen z důvodu možnosti filtrování země.
* Požadavky na prostředky, jejichž záhlaví znamenat, že by neměl být mezipaměti. Například `Cache-Control: private`, `Cache-Control: no-cache`, nebo `Pragma: no-cache` záhlaví zakázat prostředek ukládat do mezipaměti.
* Požadavky na zjištění rozsahu bajtů pro částečně uložené v mezipaměti obsahu.

Vzorec je: (stiskněte TCP_ / (TCP_ PŘÍSTUPŮ + TCP_MISS)) * 100

![Sestava poměr přístupů do mezipaměti](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Přenesená Data IPV4/IPV6
Tato sestava zobrazuje využití distribuce provozu v sadě Visual Studio IPV4 protokol IPV6.

![Přenesená Data IPV4/IPV6](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Požadavky
Sestavy se můžou generovat jenom během posledních 18 měsíců.

