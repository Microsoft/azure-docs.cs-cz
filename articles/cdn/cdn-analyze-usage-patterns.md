---
title: Základní zprávy společnosti Verizon | Dokumenty společnosti Microsoft
description: 'Vzorce využití sítě CDN můžete zobrazit pomocí následujících sestav: Šířka pásma, Přenesená data, Přístupové požadavky, Stavy mezipaměti, Poměr přístupů do mezipaměti, IpV4/IPV6 Data Transferred.'
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d48ddafdc1ec30ae1533b3a3101582f33e7f4b5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594163"
---
# <a name="core-reports-from-verizon"></a>Základní zprávy od společnosti Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Pomocí základnísestavy verizonu prostřednictvím portálu Správa profilů verizonu můžete zobrazit vzorce využití sítě CDN pomocí následujících sestav:

* Šířka pásma
* Přenesená data
* Hits
* Stavy mezipaměti
* Poměr přístupů do mezipaměti
* Přenesená data IPV4/IPV6

## <a name="accessing-verizon-core-reports"></a>Přístup k jádrovým zprávám společnosti Verizon
1. V okně profilu CDN klepněte na tlačítko **Spravovat.**
   
    ![Tlačítko Spravovat profil CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Otevře se portál pro správu CDN.
2. Najeďte na kartu **Analytics** a najeďte na informační rámeček **Základní přehledy.** Klikněte na sestavu v nabídce.
   
    ![Portál pro správu CDN - Nabídka Základní sestavy](./media/cdn-reports/cdn-core-reports.png)

3. Pro každou sestavu vyberte rozsah dat ze seznamu **Rozsah dat.** Můžete buď vybrat předdefinované časové období, například **Dnes** nebo **Tento týden**, nebo můžete vybrat **Vlastní** a ručně zadat časové období kliknutím na ikony kalendáře. 

4. Po výběru období vygenerujte sestavu kliknutím na **Přejít.** 

4. Pokud chcete exportovat data ve formátu Excelu, klikněte na ikonu Excelu nad tlačítkem **Přejít.**

## <a name="bandwidth"></a>Šířka pásma
Sestava šířky pásma se skládá z grafu a tabulky dat, která označuje využití šířky pásma CDN pro protokoly HTTP a HTTPS za určité časové období v Mb/s. Využití šířky pásma můžete zobrazit ve všech popech POP nebo pro konkrétní protokol POP. Tato sestava umožňuje zobrazit špičky provozu a distribuci pro popy POP.

V seznamu **Uzly okrajů** vyberte **Všechny uzly okrajů,** chcete-li zobrazit provoz ze všech uzlů, nebo vyberte určitou oblast.

Sestava je aktualizována každých pět minut.

![Sestava šířky pásma](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Přenesená data
Tato sestava se skládá z grafu a tabulky dat, která označuje využití přenosů CDN pro protokoly HTTP a HTTPS za určité časové období v GB. Můžete zobrazit využití provozu napříč všemi popy POP nebo pro konkrétní protokol POP. Tato sestava umožňuje zobrazit špičky provozu a distribuci mezi popy POP.

V seznamu **Uzly okrajů** vyberte **Všechny uzly okrajů,** chcete-li zobrazit provoz ze všech uzlů, nebo vyberte určitou oblast.

Sestava je aktualizována každých pět minut.

![Sestava přenesených dat](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Požadavky na server (stavové kódy)
Tato sestava popisuje distribuci stavových kódů požadavků pro váš obsah. Každý požadavek na obsah generuje stavový kód HTTP. Stavový kód popisuje, jak hraniční hodnoty POP zpracovaly požadavek. Například stavový kód 2xx označuje, že požadavek byl úspěšně doručen klientovi, zatímco stavový kód 4xx označuje, že došlo k chybě. Další informace o stavových kódech HTTP naleznete v [tématu Seznam stavových kódů HTTP](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![Sestava přístupů](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Stavy mezipaměti
Tato sestava popisuje distribuci přístupů do mezipaměti a neúspěšných přístupů do mezipaměti pro požadavky klientů. Vzhledem k tomu, že nejrychlejší výsledky výkonu z přístupů do mezipaměti, můžete optimalizovat rychlost doručování dat minimalizací neúspěšných přístupů do mezipaměti a přístupů do mezipaměti, jejichž platnost vypršela. 

Chcete-li omezit počet neúspěšných přístupů do mezipaměti, nakonfigurujte zdrojový server tak, aby minimalizoval použití následujících položek: 
 * `no-cache`hlavičky odpovědí
 * Ukládání do mezipaměti řetězce dotazů, pokud to není nezbytně nutné  
 * Kódy odpovědí, které nejsou k uložení mezipaměti

Chcete-li snížit počet přístupů do `max-age` mezipaměti, jejichž platnost vypršela, nastavte datové hody na dlouhou dobu, abyste minimalizovali počet požadavků na zdrojový server.

![Sestava stavů mezipaměti](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Mezi stavy hlavní mezipaměti patří:
* TCP_HIT: Obsluhováno z hraničního serveru. Objekt byl v mezipaměti a nepřekročil maximální stáří.
* TCP_MISS: Obsluhováno ze zdrojového serveru. Objekt nebyl v mezipaměti a odpověď byla zpět k původu.
* TCP_EXPIRED _MISS: Obsluhována ze serveru původu po opětovném ověření s původem. Objekt byl v mezipaměti, ale překročil maximální stáří. Opětovné ověření s původem vedlo k tomu, že objekt mezipaměti byl nahrazen novou odpovědí od počátku.
* TCP_EXPIRED _HIT: Sloužil z edge po prodloužení platnosti s původem. Objekt byl v mezipaměti, ale překročil maximální věk. Opětovné ověření se zdrojovým serverem mělo za následek nezměnění objekt mezipaměti.

### <a name="full-list-of-cache-statuses"></a>Úplný seznam stavů mezipaměti
* TCP_HIT - Tento stav je hlášen, když je požadavek obsluhován přímo z popu klientovi. Datový zdroj je okamžitě obsluhován z protokolu POP, pokud je uložen do mezipaměti v protokolu POP, který je nejblíže klientovi, a má platný čas k životu (TTL). TTL je určena následujícími hlavičkami odpovědí:
  
  * Cache-Control: s-maxage
  * Cache-Control: max-věk
  * Platnost vyprší
* TCP_MISS: Tento stav označuje, že verze požadovaného datového zdroje uložená v mezipaměti nebyla nalezena v protokolu POP, který je nejblíže klientovi. Datový zdroj je požadován buď z původního serveru, nebo ze serveru štítu původu. Pokud zdrojový server nebo zdrojový štít server vrátí datový zdroj, je doručen klientovi a uložen do mezipaměti na klientském i hraničním serveru. V opačném případě je vrácen stavový kód non-200 (například 403 Forbidden nebo 404 Not Found).
* TCP_EXPIRED_HIT: Tento stav je hlášen, když požadavek, který cílí na datový zdroj s ttl vypršela byla doručena přímo z POP klientovi. Například když vypršela maximální stáří majetku. 
  
   Požadavek, jehož platnost vypršela, obvykle vede k požadavku na prodloužení platnosti na zdrojový server. Pro stav TCP_EXPIRED_HIT musí zdrojový server označovat, že novější verze datového zdroje neexistuje. Tato situace obvykle vede k aktualizaci hlavičky správce mezipaměti a vyprší platnost datového zdroje.
* TCP_EXPIRED_MISS: Tento stav je hlášen, když je z protokolu POP klientovi obsluhována novější verze datového zdroje uloženého v mezipaměti, jejíž platnost vypršela. K tomuto stavu dochází, když vypršela platnost ttl pro prostředek uložený v mezipaměti (například vypršela maximální stáří) a zdrojový server vrátí novější verzi tohoto datového zdroje. Tato nová verze datového zdroje je doručena klientovi namísto verze uložené v mezipaměti. Navíc je uložen do mezipaměti na hraničním serveru a klienta.
* CONFIG_NOCACHE: Tento stav označuje, že konfigurace specifická pro zákazníka edge POP zabránila ukládání datového zdroje do mezipaměti.
* NONE - Tento stav označuje, že nebyla provedena kontrola čerstvosti obsahu mezipaměti.
* TCP_CLIENT_REFRESH_MISS: Tento stav je hlášen, když klient HTTP, například prohlížeč, vynutí edge POP načíst novou verzi zastaralého datového zdroje z původního serveru. Ve výchozím nastavení brání servery klientovi HTTP v vynucení hraničních serverů k načtení nové verze datového zdroje ze zdrojového serveru.
* TCP_PARTIAL_HIT: Tento stav je hlášen, když požadavek na rozsah bajtů má za následek požadavek na server s částečně uloženým v mezipaměti. Požadovaný rozsah bajtů je okamžitě obsluhován z popu klientovi.
* UNCACHEABLE: Tento stav je hlášen, `Cache-Control` `Expires` když majetek a záhlaví označují, že by neměl být uložen do mezipaměti na POP nebo klientem HTTP. Tyto typy požadavků jsou obsluhovány ze serveru původu.

## <a name="cache-hit-ratio"></a>Poměr přístupů do mezipaměti
Tato sestava označuje procento požadavků uložených v mezipaměti, které byly obsluhovány přímo z mezipaměti.

Zpráva obsahuje následující podrobnosti:

* Požadovaný obsah byl uložen do mezipaměti v protokolu POP, který je nejblíže žadateli.
* Žádost byla doručena přímo z okraje naší sítě.
* Požadavek nevyžadoval opětovné ověření se zdrojovým serverem.

Sestava neobsahuje:

* Požadavky, které jsou odepřeny z důvodu možnosti filtrování země nebo oblasti.
* Požadavky na prostředky, jejichž hlavičky označují, že by neměly být ukládány do mezipaměti. Například `Cache-Control: private`, `Cache-Control: no-cache`nebo `Pragma: no-cache` záhlaví zabránit datového zdroje do mezipaměti.
* Požadavky na rozsah bajtů pro částečně uložený obsah v mezipaměti.

Vzorec je: (TCP_ HIT/(TCP_ HIT+TCP_MISS))*100

![Sestava poměru přístupů do mezipaměti](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>IPV4/IPV6 Přenesená data
Tato sestava zobrazuje distribuci využití provozu v ipv4 vs IPV6.

![IPV4/IPV6 Přenesená data](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Požadavky
Sestavy lze generovat pouze během posledních 18 měsíců.

