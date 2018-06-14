---
title: Základní sestavy od společnosti Verizon | Microsoft Docs
description: 'Vzorce můžete zobrazit pro vaše CDN pomocí následující sestavy: šířky pásma, přenesená Data, přístupů, mezipaměti stavy, poměr přístupů do mezipaměti, přenesená Data IPV4 nebo IPV6.'
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
ms.openlocfilehash: fa828bfa736d677fb4881e5cc2628c0e03eb8749
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2017
ms.locfileid: "26345243"
---
# <a name="core-reports-from-verizon"></a>Základní sestavy od společnosti Verizon

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Pomocí sestav základní Verizon prostřednictvím portálu spravovat profilů Verizon můžete zobrazit vzorce pro vaše CDN s následující sestavy:

* Šířka pásma
* Data přenesená
* Přístupů
* Stavy mezipaměti
* Poměr přístupů do mezipaměti
* IPV4/IPV6 Data přenesená

## <a name="accessing-verizon-core-reports"></a>Přístup k základní Verizon sestavy
1. Okno profil CDN, klikněte **spravovat** tlačítko.
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Otevře se na portálu pro správu CDN.
2. Najeďte myší **Analytics** a potom přejděte myší **základní sestavy** plovoucím panelem. Klikněte na sestavu v nabídce.
   
    ![Portál pro správu CDN - nabídka základní sestavy](./media/cdn-reports/cdn-core-reports.png)

3. Pro každou sestavu, vyberte rozsah dat z **rozsah** seznamu. Můžete buď vybrat předem definované období, jako například **Dnes** nebo **tato týdnu**, nebo můžete vybrat **vlastní** a ručně zadejte rozsah dat kliknutím na ikony kalendáře. 

4. Jakmile vyberete rozsah dat, klikněte na tlačítko **přejděte** pro generování sestavy. 

4. Pokud chcete exportovat data ve formátu aplikace Excel, klikněte na ikonu aplikace Excel výše **přejděte** tlačítko.

## <a name="bandwidth"></a>Šířka pásma
Sestava šířky pásma se skládá z tabulku graf a data, která určuje CDN využití šířky pásma pro protokol HTTP a HTTPS, za určité časové období, v MB/s. Využití šířky pásma můžete zobrazit, přes všechny body POP nebo pro konkrétní POP. Tato sestava vám umožní zobrazit provoz špičky a distribuci pro bodů POP.

Z **uzly okraj** seznamu, vyberte **všechny uzly okraj** Zobrazit provoz ze všech uzlů a vybrat v určité oblasti.

Sestava je aktualizováno každých pět minut.

![Sestava šířky pásma](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Data přenesená
Tato sestava se skládá z tabulku graf a data, která určuje využití provozu CDN pro protokol HTTP a HTTPS, za určité časové období, v GB. Využití provozu můžete zobrazit, přes všechny body POP nebo pro konkrétní POP. Tato sestava umožňuje zobrazovat špičky provoz a distribučních bodů POP.

Z **uzly okraj** seznamu, vyberte **všechny uzly okraj** Zobrazit provoz ze všech uzlů a vybrat v určité oblasti.

Sestava je aktualizováno každých pět minut.

![Přenesená data sestavy](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Přístupů (stavové kódy)
Tato sestava popisuje distribuci požadavek stavové kódy pro obsah. Každý požadavek pro obsah generuje stavový kód HTTP. Stavový kód popisuje, jak bodů POP edge zpracování žádosti. Stavový kód 2xx například označuje, že žádost úspěšně vyřídila ke klientovi, zatímco stavový kód 4xx značí, že došlo k chybě. Další informace o stavových kódech HTTP najdete v tématu [stavové kódy seznamu HTTP](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![Sestava přístupů](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Stavy mezipaměti
Tato sestava popisuje distribuci přístupů k mezipaměti a Neúspěšné přístupy do mezipaměti pro požadavky klientů. Protože nejrychlejší výkon výsledkem přístupů k mezipaměti, můžete optimalizovat rychlosti doručování dat minimalizovat Neúspěšné přístupy do mezipaměti a přístupů k mezipaměti vypršela platnost. 

Abyste snížili Neúspěšné přístupy do mezipaměti, nakonfigurujte server původu, abyste minimalizovali použití následující: 
 * `no-cache`hlavičky odpovědi
 * Řetězec dotazu ukládání do mezipaměti, pokud není nezbytně nutné  
 * Kódy lze uložit do mezipaměti bez odpovědi

Chcete-li snížit přístupů k mezipaměti vypršela platnost, nastavte prostředek služby `max-age` na dlouhou dobu, chcete-li minimalizovat počet požadavků na zdrojový server.

![Sestavy stavů mezipaměti](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Hlavní mezipaměti stavy, které patří:
* TCP_HIT: Zpracovat z hraničního serveru. Objekt byl v mezipaměti a nepřesahuje jeho maximální stáří.
* TCP_MISS: Zpracovat ze zdrojového serveru. Objekt nebyl v mezipaměti a odpověď byla zpět na počátku.
* TCP_EXPIRED _MISS: obsluhovat ze zdrojového serveru po opětovné ověření s původu. Objekt byl v mezipaměti, ale překročil jeho maximální stáří. Opětovné ověření s původem výsledkem objekt mezipaměti nahrazují novou odpověď z počátku.
* TCP_EXPIRED _HIT: zpracovaných od okraje po opětovné ověření s původu. Objekt byl v mezipaměti, ale překročil jeho maximální stáří. Opětovné ověření je zdrojový server má za následek objekt mezipaměti se ponechat beze změny.

### <a name="full-list-of-cache-statuses"></a>Úplný seznam stavů mezipaměti
* TCP_HIT – tento stav se zobrazí, když požadavek pochází přímo z POP do klienta. Prostředek okamžitě pochází z bodu POP při se uloží do mezipaměti na serveru POP nejbližší klientovi a má hodnotu platný time to live (TTL). Hodnota TTL je dáno následující hlavičky odpovědi:
  
  * Cache-Control: s-maxage
  * Cache-Control: maximální stáří
  * Vypršení platnosti
* TCP_MISS: Tento stav indikuje, že v mezipaměti verzi požadovaný prostředek nebyl nalezen na serveru POP nejbližší klientovi. Asset se požaduje ze zdrojového serveru nebo serveru štítu původu. Pokud je zdrojový server nebo server štítu počátek vrátí prostředek, je zpracování do klienta a ukládat do mezipaměti na klientovi i serveru edge. Jinak bez 200 stavový kód (například 403 zakázané nebo 404 nebyl nalezen) je vrácen.
* TCP_EXPIRED_HIT: Tento stav se zobrazí při zpracování žádosti, která je cílena prostředek s vypršenou platností TTL přímo z POP klientovi. Například když asset je maximální doba vypršela platnost. 
  
   Vypršela platnost žádosti o žádost o opětovné ověření obvykle výsledkem na zdrojový server. Stav TCP_EXPIRED_HIT proběhnout musí na zdrojový server znamenat, že na novější verzi asset neexistuje. Tato situace obvykle výsledkem aktualizace Cache-Control assetu a hlavičky Expires.
* TCP_EXPIRED_MISS: Tento stav se zobrazí při na novější verzi prostředek s vypršenou platností v mezipaměti pochází z POP do klienta. Tento stav nastane, když hodnota TTL pro prostředek v mezipaměti vypršela (například vypršení platnosti maximální stáří) a zdrojový server vrátí na novější verzi tohoto prostředku. Tuto novou verzi prostředku je zpracovat klientovi místo v mezipaměti. Kromě toho je mezipaměti hraničního serveru a klienta.
* CONFIG_NOCACHE: Tento stav indikuje, že zákaznické konfigurace edge POP zabránila asset ukládat do mezipaměti.
* ŽÁDNÁ – tento stav označuje, že nebyla provedena kontrola aktuálnost obsahu mezipaměti.
* TCP_CLIENT_REFRESH_MISS: Tento stav se zobrazí při klienta HTTP, jako je například prohlížeč, vynutí okraj POP načíst nové verze zastaralé asset ze zdrojového serveru. Ve výchozím nastavení servery zabránit klientem HTTP vynucení edge serverům pro načtení novou verzi asset ze zdrojového serveru.
* TCP_PARTIAL_HIT: Tento stav se zobrazí, pokud žádost o rozsah bajtů, které jsou výsledkem stiskněte klávesu pro prostředek částečně v mezipaměti. Požadovaný rozsah bajtů je okamžitě zpracovat ze serveru POP do klienta.
* UNCACHEABLE: Tento stav se zobrazí, když prostředek služby `Cache-Control` a `Expires` hlavičky znamenat, že by neměl být uložené v mezipaměti, na serveru POP nebo klienta HTTP. Tyto typy požadavků se zpracovávají ze zdrojového serveru.

## <a name="cache-hit-ratio"></a>Poměr přístupů do mezipaměti
Tato sestava informuje o procentu uložené v mezipaměti požadavků, které se spouští přímo z mezipaměti.

Sestava obsahuje následující podrobnosti:

* Požadovaný obsah se ukládá do mezipaměti na serveru POP nejblíže k žadatel.
* Zpracování žádosti přímo z okraje naše sítě.
* Požadavek nebylo nutné opětovné ověření je zdrojový server.

Sestava neobsahuje:

* Požadavky, které jsou odepřen v důsledku země možnosti filtrování.
* Požadavky na prostředky, jejichž hlavičky znamenat, že by neměl být mezipaměti. Například `Cache-Control: private`, `Cache-Control: no-cache`, nebo `Pragma: no-cache` hlavičky zabránit prostředek ukládat do mezipaměti.
* Požadavky na rozsah bajtů částečně v mezipaměti obsahu.

Vzorec je: (stiskněte KLÁVESU TCP_ / (stiskněte KLÁVESU TCP_ + TCP_MISS)) * 100

![Sestava poměr přístupů do mezipaměti](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Přenesená Data IPV4/IPV6
Tato sestava zobrazuje využití distribuce přenosů v sadě vs IPV4 protokol IPV6.

![Přenesená Data IPV4/IPV6](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Požadavky
Sestavy můžete generovat pouze během posledních 18 měsíců.

