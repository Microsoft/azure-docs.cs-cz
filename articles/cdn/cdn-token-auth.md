---
title: Zabezpečení prostředků Azure CDN pomocí tokenu ověřování | Microsoft Docs
description: Další informace o použití ověřování tokenem zabezpečený přístup k prostředkům vaší Azure CDN.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: aaec713a7680aeda8317f5af41b9b99bcbdca4b7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Zabezpečení prostředků Azure CDN se ověření pomocí tokenu

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled

Ověření pomocí tokenu je mechanismus, který umožňuje zabránit obsluhující prostředky neoprávněným klientům Azure Content Delivery Network (CDN). Aby se obvykle provádí ověření pomocí tokenu *hotlinking* obsahu, ve kterém používá jiný web, jako je například Tabule, vaše prostředky bez povolení. Hotlinking může mít vliv na vaše náklady na doručování obsahu. Povolením ověřování tokenem na CDN jsou požadavky ověřována CDN hraniční server před CDN nabízí obsah. 

## <a name="how-it-works"></a>Jak to funguje

Ověření pomocí tokenu ověřuje, že požadavky jsou generovány jako důvěryhodný web tím, že požadavky na obsahovat hodnotu tokenu, aby blokování kódovaný informace o žadatel. Obsahu obsluhovaného pro žadatele pouze v případě, že je zakódovaný informace splňuje požadavky; požadavky, jinak budou odepřeny. Požadavky můžete nastavit pomocí jednoho nebo více z následujících parametrů:

- Země: Povolit nebo odmítnout požadavky, které pocházejí z uvedených zemích jejich [kód země](https://msdn.microsoft.com/library/mt761717.aspx).
- Adresa URL: Povolit pouze požadavky, které odpovídají daný prostředek nebo cestu.
- Hostitel: Povolit nebo odmítnout požadavky, které používají zadaní hostitelé v hlavičce žádosti.
- Odkazující server: Povolit nebo odepřít žádost ze zadaného odkazující server.
- IP adresa: Povolit pouze požadavky, které pochází z konkrétní IP adresu nebo podsíť protokolu IP.
- Protokol: Povolit nebo odepřít požadavky založené na protokolu slouží k vyžádání obsahu.
- Čas vypršení platnosti: přiřadit datum a čas období zajistit, aby zůstala odkaz platný pouze po omezenou dobu.

Další informace najdete v tématu příklady podrobnou konfiguraci pro každý parametr [nastavení ověření pomocí tokenu](#setting-up-token-authentication).

>[!IMPORTANT] 
> Pokud je token autorizace je povoleno pro jakoukoli cestu na tento účet, režimu mezipaměti standard je jediný režim, který lze použít pro ukládání do mezipaměti řetězce dotazu. Další informace najdete v tématu [Řízení chování Azure CDN při ukládání řetězců dotazu do mezipaměti](cdn-query-string-premium.md).

## <a name="reference-architecture"></a>Referenční architektura

Následující diagram pracovní postup popisuje, jak CDN používá ověření pomocí tokenu pro práci s vaší webové aplikace.

![Pracovní postup ověření pomocí tokenu CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Ověření tokenu logiky na koncový bod CDN
    
Následující vývojový diagram popisuje, jak Azure CDN ověří požadavek klienta po ověření tokenu je nakonfigurovaná na koncový bod CDN.

![CDN token ověřovací logiku](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Nastavení ověření pomocí tokenu

1. Z [portál Azure](https://portal.azure.com), přejděte na svůj profil CDN a pak vyberte **spravovat** ke spuštění na doplňkovém portálu.

    ![Tlačítko Spravovat profil CDN](./media/cdn-token-auth/cdn-manage-btn.png)

2. Pozastavte ukazatel myši nad **HTTP velké**, pak vyberte **tokenu ověřování** v plovoucím panelem. Pak můžete nastavit šifrovací klíč a parametry šifrování následujícím způsobem:

    1. Vytvořte jeden nebo více šifrovací klíče. Šifrovací klíč je malá a velká písmena a může obsahovat libovolnou kombinaci alfanumerických znaků. U jiných typů znaků včetně mezer, nejsou povoleny. Maximální délka je 250 znaků. Chcete, aby šifrovací klíče jsou náhodné, doporučujeme vám vytvořit pomocí [OpenSSL nástroj](https://www.openssl.org/). 

       Nástroj OpenSSL má následující syntaxi:

       ```rand -hex <key length>```

       Příklad:

       ```OpenSSL> rand -hex 32``` 

       Výpadky, vytvořte primární a záložní klíč. Záložní klíč poskytuje bez přerušení přístup k vašemu obsahu, když se primární klíč se právě aktualizuje.
    
    2. Zadejte jedinečný šifrovací klíč v **primární klíč** a volitelně zadejte klíč ze zálohy v **záložní klíč** pole.

    3. Vyberte verzi minimální šifrování pro každý klíč z jeho **minimální verze šifrování** seznamu a pak vyberte **aktualizace**:
       - **V2**: označuje, že klíč můžete použít ke generování tokenů verze 2.0 a 3.0. Tuto možnost použijte pouze v případě, že se přechodu ze šifrovací klíč starší verze 2.0 pro klíč verze 3.0.
       - **V3**: (doporučeno) označuje, že klíč lze použít pouze ke generování tokenů verze 3.0.

    ![CDN tokenu ověřování Instalační klíč](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    4. Použijte nástroj šifrovat nastavit parametry šifrování a vygenerovat token. Pomocí nástroje šifrování můžete povolit nebo odepřít požadavků podle čas vypršení platnosti, země, odkazující server, protokol a IP adresa klienta (v libovolné kombinace). I když neexistuje žádné omezení počtu a kombinace parametrů, které mohou být kombinovány a vytvořit token, je celková délka token maximálně 512 znaků. 

       ![CDN šifrování nástroje](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

       Zadejte hodnoty pro jeden nebo více z následujících parametrů šifrování v **šifrování nástroj** části: 

       > [!div class="mx-tdCol2BreakAll"] 
       > <table>
       > <tr>
       >   <th>Název parametru</th> 
       >   <th>Popis</th>
       > </tr>
       > <tr>
       >    <td><b>ec_expire</b></td>
       >    <td>Přiřadí čas vypršení platnosti tokenu, po jejímž uplynutí vyprší platnost tokenu. Požadavky na odeslání po čas vypršení platnosti je odepřen. Tento parametr používá časové razítko systému Unix, která je založena na počtu sekund od standardní epocha Unix `1/1/1970 00:00:00 GMT`. (Online nástroje můžete pro převod mezi (běžný čas) a Unix času.)> 
       >    Například, pokud chcete ukončit platnost tokenu `12/31/2016 12:00:00 GMT`, zadejte hodnotu časového razítka Unix, `1483185600`. 
       > </tr>
       > <tr>
       >    <td><b>ec_url_allow</b></td> 
       >    <td>Umožňuje přizpůsobit tokenů pro konkrétní prostředek nebo cestu. Omezuje přístup na požadavky, jejichž adresa URL začínat konkrétní relativní cesty. URL – adresy rozlišují velká a malá písmena. Vstupní více cest oddělením jednotlivé cesty oddělte čárkou; Nepřidávejte mezer. V závislosti na požadavcích můžete nastavit různé hodnoty poskytují různé úrovně přístupu.> 
       >    Například pro adresu URL `http://www.mydomain.com/pictures/city/strasbourg.png`, pro následující vstupní hodnoty jsou povoleny tyto požadavky: 
       >    <ul>
       >       <li>Vstupní hodnota `/`: jsou povoleny všechny požadavky.</li>
       >       <li>Vstupní hodnota `/pictures`, jsou povoleny následující požadavky: <ul>
       >          <li>`http://www.mydomain.com/pictures.png`</li>
       >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
       >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
       >       </ul></li>
       >       <li>Vstupní hodnota `/pictures/`: pouze požadavky obsahující `/pictures/` jsou povoleny cestu. Například, `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
       >       <li>Vstupní hodnota `/pictures/city/strasbourg.png`: pouze požadavky pro tento konkrétní cesty a asset jsou povoleny.</li>
       >    </ul>
       > </tr>
       > <tr>
       >    <td><b>ec_country_allow</b></td> 
       >    <td>Umožňuje použití jenom požadavky, které pocházejí z jedné nebo více zadaný zemí. Požadavky, které pocházejí z jiných zemí odmítnuty. Použít písmenem dva [kód země ISO 3166](https://msdn.microsoft.com/library/mt761717.aspx) pro jednotlivé země a oddělení každé z nich s použitím čárky; nepřidávejte mezeru. Například pokud chcete povolit přístup z USA a Francie, zadejte `US,FR`.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_country_deny</b></td> 
       >    <td>Odmítne požadavky, které pocházejí z jedné nebo více zadaný zemí. Jsou povoleny požadavky, které pocházejí z dalších zemích. Implementace je stejný jako <b>ec_country_allow</b> parametr. Pokud se kód země nachází v obou <b>ec_country_allow</b> a <b>ec_country_deny</b> parametry, <b>ec_country_allow</b> parametr má přednost před.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_ref_allow</b></td>
       >    <td>Umožňuje pouze požadavky z odkazující zadaný server. Odkazující server identifikuje adresu URL webové stránky, která je propojený s požadovaný prostředek. Parametr hodnoty nebudou obsahovat protokol.>    
       >    Jsou povoleny následující typy vstup:
       >    <ul>
       >       <li>Název hostitele nebo název hostitele a cestu.</li>
       >       <li>Chcete-li odkazující více servery. Chcete-li přidat více odkazujících serverů, oddělte čárkou; odkazující na každý server Nepřidávejte mezerou. Pokud zadáte hodnotu odkazující server, ale odkazující server informace nebudou odeslány v požadavek z důvodu konfigurace prohlížeče, požadavek se odmítne ve výchozím nastavení.</li> 
       >       <li>Požadavky s informacemi o odkazující server chybí nebo je prázdný. Ve výchozím nastavení <b>ec_ref_allow</b> parametr blokuje tyto typy požadavků. Povolit tyto požadavky, zadejte buď text, "chybí", nebo zadejte prázdnou hodnotu (pomocí koncové čárkou).</li> 
       >       <li>Subdomains. Chcete-li povolit subdomény, zadejte hvězdičku (\*). Chcete-li například povolit všechny subdomény z `contoso.com`, zadejte `*.contoso.com`.</li>
       >    </ul>     
       >    Chcete-li například povolit přístup pro žádosti od `www.contoso.com`, všem dílčím doménám `contoso2.com`, a požadavky s prázdné nebo chybějící odkazující servery, zadejte `www.contoso.com,*.contoso.com,missing`.</td>
       > </tr>
       > <tr> 
       >    <td><b>ec_ref_deny</b></td>
       >    <td>Odmítne požadavky z odkazující zadaný server. Implementace je stejný jako <b>ec_ref_allow</b> parametr. Pokud je k dispozici v obou odkazující server <b>ec_ref_allow</b> a <b>ec_ref_deny</b> parametry, <b>ec_ref_allow</b> parametr má přednost před.</td>
       > </tr>
       > <tr> 
       >    <td><b>ec_proto_allow</b></td> 
       >    <td>Umožňuje použití jenom požadavky od zadaný protokol. Platné hodnoty jsou `http`, `https`, nebo `http,https`.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_proto_deny</b></td>
       >    <td>Odmítne požadavky z zadaný protokol. Implementace je stejný jako <b>ec_proto_allow</b> parametr. Pokud je k dispozici v obou protokol <b>ec_proto_allow</b> a <b>ec_proto_deny</b> parametry, <b>ec_proto_allow</b> parametr má přednost před.</td>
       > </tr>
       > <tr>
       >    <td><b>ec_clientip</b></td>
       >    <td>Omezuje přístup k zadané žadatele IP adresu. Jsou podporovány adresy IPV4 a IPV6. Můžete zadat IP adresu jedné žádosti nebo IP adresy přidružené k určité podsíti. Například `11.22.33.0/22` povoluje požadavky z IP adresy 11.22.32.1 k 11.22.35.254.</td>
       > </tr>
       > </table>

    5. Po dokončení zadání hodnot parametrů šifrování, vyberte klíč k šifrování (Pokud jste vytvořili primární a záložní klíč) z **klíč k šifrování** seznamu.
    
    6. Vyberte verzi šifrování z **šifrování verze** seznamu: **V2** verze 2 nebo **V3** pro verze 3 (doporučeno). 

    7. Vyberte **šifrovat** k vygenerování tokenu.

    Po vygenerování tokenu se zobrazí v **vygenerovat Token** pole. Chcete-li použít token, připojte ji jako řetězec dotazu na konec souboru v cestě adresy URL. Například, `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
    8. Volitelně můžete Otestujte váš token pomocí nástroje dešifrování, aby mohli zobrazit parametry vašeho tokenu. Vložte hodnotu tokenu ve **tokenu k dešifrování** pole. Vyberte šifrovací klíč, který chcete použít z **klíč pro dešifrování** seznamu a pak vyberte **dešifrovat**.

    Po token se dešifruje, jeho parametry se zobrazí v **původní parametry** pole.

    9. Volitelně můžete přizpůsobte typ kód odpovědi, která je vrácena, pokud požadavek je odepřen. Vyberte **povoleno**, pak vyberte kód odpovědi **kód odpovědi** seznamu. **Název hlavičky** se automaticky nastaví na **umístění**. Vyberte **Uložit** implementovat nový kód odpovědi. Pro některé kódy odpovědí, musíte taky zadat adresu URL chybovou stránku v **hodnota hlavičky** pole. **403** kód odpovědi (zakázáno) je standardně vybraná. 

3. V části **HTTP velké**, vyberte **stroj pravidel**. Stroj pravidel používáte k definování cesty použít funkci, povolte funkci ověření pomocí tokenu a povolit další token funkce souvisejících s ověřováním. Další informace najdete v tématu [pravidla modul odkaz](cdn-rules-engine-reference.md).

    1. Vyberte existující pravidlo nebo vytvořte nové pravidlo, které definují asset nebo cestu, pro který chcete použít ověření tokenu. 
    2. Chcete-li ověření pomocí tokenu v pravidle, vyberte **[tokenu ověřování](cdn-rules-engine-reference-features.md#token-auth)** z **funkce** seznamu a pak vyberte **povoleno**. Vyberte **aktualizace** při aktualizaci pravidla nebo **přidat** Pokud vytváříte pravidlo.
        
    ![Příklad ověření tokenu povolit modul CDN pravidla](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. V modulu pravidel můžete také povolit další token funkcí souvisejících s ověřováním. Chcete-li některý z následujících funkcí, vyberte ho v **funkce** seznamu a pak vyberte **povoleno**.
    
    - **[Token Auth Denial kódu](cdn-rules-engine-reference-features.md#token-auth-denial-code)**: Určuje typ odpovědi, která se vrátí pro uživatele, když požadavek je odepřen. Kód odpovědi nastavena v přepsání pravidla nastavená **vlastní zpracování Denial** části na stránce ověřování na základě tokenu.

    - **[Token Auth Ignorovat adresy URL případ](cdn-rules-engine-reference-features.md#token-auth-ignore-url-case)**: Určuje, zda je adresa URL použitá k ověření tokenu malá a velká písmena.

    - **[Token ověření parametru](cdn-rules-engine-reference-features.md#token-auth-parameter)**: přejmenuje tokenu ověřování parametru řetězce dotazu, který se zobrazí v požadovanou adresu URL. 
        
    ![Pravidla CDN modul příklad nastavení ověření pomocí tokenu](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Váš token zabezpečení můžete přizpůsobit, přístup k zdrojový kód v [Githubu](https://github.com/VerizonDigital/ectoken).
Dostupné jazyky patří:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN funkce a zprostředkovatele ceny

Informace o funkcích najdete v tématu [funkce Azure CDN produktu](cdn-features.md). Informace o cenách najdete v tématu [Content Delivery Network ceny](https://azure.microsoft.com/pricing/details/cdn/).
