---
title: Zabezpečení prostředků Azure CDN pomocí ověřování tokenu | Dokumentace Microsoftu
description: Další informace o použití ověřování pomocí tokenu zabezpečení přístupu pro vaše prostředky Azure CDN.
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
ms.openlocfilehash: 6ff7b018cea8bedd93d3c9aef3b3250ecf5251b4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123721"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Zabezpečení prostředků Azure CDN pomocí ověření tokenu

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled

Ověřování pomocí tokenu je mechanismus, který umožňuje zabránit obsluhující prostředky neoprávněné klientům Azure Content Delivery Network (CDN). Ověřování pomocí tokenu se obvykle provádí, aby se zabránilo *hotlinking* obsahu, ve kterém používá jiný web, jako je například panel zpráv, vaše prostředky bez oprávnění. Hotlinking může mít vliv na náklady na doručování obsahu. Když zapnete ověřování pomocí tokenu v CDN, jsou požadavky ověřována CDN hraniční server před CDN nabízí obsah. 

## <a name="how-it-works"></a>Jak to funguje

Ověřování pomocí tokenu ověřuje, že žádosti jsou generovány jako důvěryhodný vyžadováním požadavky tak, aby obsahovala hodnotu tokenu, že kódování obsahuje informace o žadateli. Pouze v případě, že kódovaného informace splňuje požadavky; se obsah obsluhuje pro žadatele v opačném případě požadavků byl odepřen. Požadavky můžete nastavit pomocí jednoho nebo více z následujících parametrů:

- Země: Povolit nebo odepřít požadavky, které pocházejí ze zemí podle jejich [směrové číslo země](https://msdn.microsoft.com/library/mt761717.aspx).
- Adresa URL: Povolit pouze požadavky, které odpovídají zadaného prostředku nebo cesta.
- Hostitel: Povolit nebo odmítnout požadavky, které používají zadaní hostitelé v hlavičce požadavku.
- Odkazující server: Povolí nebo zakáže žádost ze zadaného odkazující server.
- IP adresa: Povolit pouze požadavky pocházejících z konkrétní IP adresu nebo podsíť protokolu IP.
- Protokol: Povolit nebo odepřít požadavky založené na protokol použitý pro vyžádání obsahu.
- Čas vypršení platnosti: Přiřaďte určité datum a čas, ujistěte se, že odkaz zůstane platný pouze pro omezené časové období.

Další informace, podívejte se na příklady podrobnou konfiguraci pro každý parametr [nastavení ověřování pomocí tokenu](#setting-up-token-authentication).

>[!IMPORTANT] 
> Pokud token autorizace je povolená pro libovolnou cestu pro tento účet, režimu mezipaměti standard je jediný režim, který slouží k ukládání do mezipaměti řetězce dotazu. Další informace najdete v tématu [Řízení chování Azure CDN při ukládání řetězců dotazu do mezipaměti](cdn-query-string-premium.md).

## <a name="reference-architecture"></a>Referenční architektura

Následující diagram pracovního postupu popisuje, jak síť CDN používá ověřování pomocí tokenu pro práci s vaší webovou aplikací.

![Pracovní postup ověřování pomocí tokenu CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Logika ověřování tokenů na koncový bod CDN
    
Následující diagram popisuje, jak Azure CDN ověří požadavek klienta při ověřování tokenu je nakonfigurovaná na koncový bod CDN.

![Logika ověřování tokenů CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Nastavení ověřování pomocí tokenu

1. Z [webu Azure portal](https://portal.azure.com), přejděte na svůj profil CDN a pak vyberte **spravovat** spustit doplňkového portálu.

    ![Tlačítko Spravovat profil CDN](./media/cdn-token-auth/cdn-manage-btn.png)

2. Najeďte myší na **HTTP velké**a pak vyberte **tokenu ověřování** v informační rámeček. Můžete pak nastavit šifrovací klíč a parametry šifrování následujícím způsobem:

   1. Vytvořte jeden nebo více šifrovací klíče. Šifrovací klíč je velká a malá písmena a může obsahovat libovolnou kombinaci alfanumerických znaků. Jiné druhy znaků včetně mezer, nejsou povoleny. Maximální délka je 250 znaků. K zajištění, že šifrovací klíče jsou náhodně, doporučujeme vám vytvořit pomocí [OpenSSL nástroj](https://www.openssl.org/). 

      Nástroj OpenSSL má následující syntaxi:

      ```rand -hex <key length>```

      Příklad:

      ```OpenSSL> rand -hex 32``` 

      Výpadky, vytvořte primární a záložní klíč. Záložní klíč poskytuje zajistit nepřerušený přístup k vašemu obsahu při aktualizaci váš primární klíč.
    
   2. Zadejte jedinečný šifrovací klíč v **primární klíč** pole a volitelně zadat záložní klíč v **záložní klíč** pole.

   3. Vyberte verzi minimální šifrování pro každý klíč z jeho **minimální verze šifrování** seznamu a pak vyberte **aktualizace**:
      - **V2**: Označuje, že klíč můžete použít ke generování tokenů verze 2.0 a 3.0. Tuto možnost použijte jenom v případě, že se převádějí ze starší verze 2.0 šifrovací klíč na klíč verze 3.0.
      - **V3**: (Doporučeno) Označuje, že klíč jde použít jenom ke generování tokenů verze 3.0.

      ![Klíč nastavení tokenu ověřování CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Pomocí nástroje šifrovat nastavit parametry šifrování a generovat token. Pomocí nástroje šifrování můžete povolit nebo odepřít požadavky na základě času vypršení platnosti, země, referrer, protokol a IP adresa klienta (v libovolnou kombinaci). Ačkoli neexistuje žádné omezení počtu a kombinace parametrů, které mohou být kombinovány k vytvoření tokenu, celková délka tokenu je omezen na 512 znaků. 

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
      >    <td>Přiřadí čas vypršení platnosti tokenu, po jejímž uplynutí vyprší platnost tokenu. Žádosti odeslané po čas vypršení platnosti je odepřen. Tento parametr používá unixové časové razítko, která je založena na počtu sekund od standardní epocha Unix `1/1/1970 00:00:00 GMT`. (Můžete použít online nástrojů pro převod mezi (běžný čas) a Unixový čas.)> 
      >    Například, pokud chcete, aby tokenu vyprší za `12/31/2016 12:00:00 GMT`, zadejte hodnotu časového razítka Unix `1483185600`. 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Umožňuje přizpůsobit tokeny do příslušného prostředku nebo cesta. Omezuje přístup na požadavky, jejichž adresy URL začínat konkrétní relativní cesta. Adresy URL jsou malá a velká písmena. Zadejte více cest podle jednotlivé cesty oddělte čárkou; Nepřidávejte mezery. V závislosti na požadavcích můžete nastavit různé hodnoty poskytují různé úrovně přístupu.> 
      >    Například pro adresu URL `http://www.mydomain.com/pictures/city/strasbourg.png`, tyto požadavky jsou povolené pro následující vstupní hodnoty: 
      >    <ul>
      >       <li>Vstupní hodnota `/`: Jsou povoleny všechny požadavky.</li>
      >       <li>Vstupní hodnota `/pictures`, jsou povoleny následující požadavky: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Vstupní hodnota `/pictures/`: Vyžádá obsahující pouze `/pictures/` cesty jsou povoleny. Například, `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Vstupní hodnota `/pictures/city/strasbourg.png`: Jsou povoleny pouze požadavky pro tento konkrétní cesty a prostředků.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Umožňuje pouze požadavky, které pocházejí z jedné nebo více zadanou zemí. Požadavky, které pocházejí z jiných zemí byl odepřen. Použití dvou písmen [směrové číslo země ISO 3166](https://msdn.microsoft.com/library/mt761717.aspx) pro jednotlivé země a oddělit každou z nich čárkou; nepřidávejte mezerou. Například pokud chcete povolit přístup ze Spojených států a (Francie), zadejte `US,FR`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Odmítne požadavky, které pocházejí z jedné nebo více zadanou zemí. Jsou povoleny požadavky, které pocházejí z jiných zemí. Implementace je stejné jako <b>ec_country_allow</b> parametru. Pokud je k dispozici v obou kód země <b>ec_country_allow</b> a <b>ec_country_deny</b> parametry, <b>ec_country_allow</b> parametr má přednost.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Umožňuje pouze požadavky ze zadaného odkazující server. Odkazující server určuje adresu URL webové stránky, který je propojený s požadovaný prostředek. Nesmí obsahovat protokol hodnoty parametru.> 
      >    Jsou povoleny následující typy vstupu:
      >    <ul>
      >       <li>Název hostitele nebo název hostitele a cestu.</li>
      >       <li>Více odkazující servery. Chcete-li přidat více odkazující servery, oddělte čárkou; každý odkazující server Nepřidávejte mezerou. Pokud zadáte hodnotu odkazující server však referrer informace nebudou odeslány v požadavek z důvodu konfigurace prohlížeče, je požadavek ve výchozím nastavení zakázaný.</li> 
      >       <li>Požadavky s informacemi o odkazujícího serveru chybí nebo je prázdný. Ve výchozím nastavení <b>ec_ref_allow</b> parametr blokuje tyto druhy žádostí. Pokud chcete povolit tyto požadavky, zadejte buď textu "chybí", nebo zadejte prázdnou hodnotu (s použitím koncovou čárkou).</li> 
      >       <li>Subdomény. Chcete-li povolit subdomény, zadejte hvězdičku (\*). Například chcete povolit všechny subdomény `contoso.com`, zadejte `*.contoso.com`.</li>
      >    </ul> 
      >    Například, pokud chcete povolit přístup pro žádosti od `www.contoso.com`, všem dílčím doménám domény `contoso2.com`, a zadejte požadavky s prázdnou nebo chybějící odkazující servery `www.contoso.com,*.contoso.com,missing`.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Odmítne požadavky z zadané odkazující server. Implementace je stejné jako <b>ec_ref_allow</b> parametru. Pokud je k dispozici v obou odkazující <b>ec_ref_allow</b> a <b>ec_ref_deny</b> parametry, <b>ec_ref_allow</b> parametr má přednost před.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Umožňuje pouze požadavky od zadaného protokolu. Platné hodnoty jsou `http`, `https`, nebo `http,https`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Odmítne požadavky z zadaný protokol. Implementace je stejné jako <b>ec_proto_allow</b> parametru. Pokud je k dispozici v obou protokol <b>ec_proto_allow</b> a <b>ec_proto_deny</b> parametry, <b>ec_proto_allow</b> parametr má přednost.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Omezuje přístup k zadané žadateli IP adresu. Jsou podporovány adresy IPV4 a IPV6. Můžete zadat jeden požadavek IP adresu nebo IP adresy přidružené k určité podsíti. Například `11.22.33.0/22` umožňuje požadavky z IP adres 11.22.32.1 k 11.22.35.254.</td>
      > </tr>
      > </table>

   5. Po dokončení zadávání hodnot parametru šifrování, vyberte klíč k šifrování (Pokud jste vytvořili primární a záložní klíč) z **klíč k šifrování** seznamu.
    
   6. Vyberte verzi šifrování z **šifrování verze** seznamu: **V2** verze 2 nebo **V3** verze 3 (doporučeno). 

   7. Vyberte **šifrovat** k vygenerování tokenu.

      Po vygenerování tokenu se zobrazí **vygenerovat Token** pole. Pokud chcete použít token, připojte ho jako řetězec dotazu na konec souboru v cestě adresy URL. Například, `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Volitelně můžete otestujte tokenu pomocí nástroje pro dešifrování tak, aby se zobrazí váš token parametry. Vložte hodnotu tokenu ve **Token dešifrovat** pole. Vyberte šifrovací klíč ze **klíč pro dešifrování** seznamu a pak vyberte **dešifrovat**.

      Poté, co se dešifrují token, jejích parametrů se zobrazí v **původní parametry** pole.

   9. Volitelně můžete upravte typ odpovědi kódu, která je vrácena, pokud je zamítl žádost o. Vyberte **povoleno**, vyberte kód odpovědi **kód odpovědi** seznamu. **Název hlavičky** se automaticky nastaví na **umístění**. Vyberte **Uložit** implementovat nový kód odpovědi. U určitých kódy odpovědí, musí také zadáte adresu URL stránky chyby v **hodnota hlavičky** pole. **403** je standardně vybraná kód odpovědi (zakázáno). 

3. V části **HTTP velké**vyberte **stroj pravidel**. Stroj pravidel můžete definovat cesty k použít funkci, povolit ověřování pomocí tokenu funkci a povolit další token funkce související s ověřováním. Další informace najdete v tématu [referenční informace ke stroji pravidel](cdn-rules-engine-reference.md).

   1. Vybrat existující pravidlo nebo vytvořit nové pravidlo, které definují asset nebo cesty, pro kterou chcete použít ověřování pomocí tokenu. 
   2. Pokud chcete povolit ověřování pomocí tokenu na pravidla, vyberte **[tokenu ověřování](cdn-rules-engine-reference-features.md#token-auth)** z **funkce** seznamu a pak vyberte **povoleno**. Vyberte **aktualizace** při aktualizaci pravidla nebo **přidat** Pokud vytváříte pravidlo.
        
      ![Příklad povolit ověřování pomocí tokenu ke stroji pravidel CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. V modulu pravidla můžete také povolit další token funkce související s ověřováním. Chcete-li některý z následujících funkcí, vyberte ho v **funkce** seznamu a pak vyberte **povoleno**.
    
   - **[Token kódu s cílem odepření vícefaktorového ověřování](cdn-rules-engine-reference-features.md#token-auth-denial-code)**: Určuje typ odpovědi, který je vrácen uživateli po odepření požadavku. Přepsat pravidla, která nastavuje tady odpovědi kódu v **vlastní zpracování odmítnutí** části na stránce ověřování na základě tokenu.

   - **[Token ověření ignorovat velikost písmen URL](cdn-rules-engine-reference-features.md#token-auth-ignore-url-case)**: Určuje, zda je adresa URL sloužící k ověření tokenu malá a velká písmena.

   - **[Token ověření parametru](cdn-rules-engine-reference-features.md#token-auth-parameter)**: Přejmenuje tokenu ověřování parametru řetězce dotazu, který se zobrazí požadovanou adresu URL. 
        
     ![Příklad nastavení ověřování pomocí tokenu ke stroji pravidel CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Můžete přizpůsobit svůj token díky přístupu do zdrojového kódu v [Githubu](https://github.com/VerizonDigital/ectoken).
   Dostupné jazyky patří:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN funkce a ceny poskytovatele

Informace o funkcích najdete v tématu [funkce produktu Azure CDN](cdn-features.md). Informace o cenách najdete v tématu [ceny Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).
