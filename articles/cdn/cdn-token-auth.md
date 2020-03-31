---
title: Zabezpečení prostředků Azure CDN pomocí ověřování tokenů| Dokumenty společnosti Microsoft
description: Přečtěte si, jak používat ověřování pomocí tokenu k zabezpečení přístupu k prostředkům Azure CDN.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: fa71f472294b91baebc2a6075ddb2b50123e545d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593393"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Zabezpečení prostředků Azure CDN pomocí ověřování tokenů

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled

Ověřování tokenů je mechanismus, který umožňuje zabránit síti pro doručování obsahu Azure (CDN) v zobrazování prostředků neoprávněným klientům. Ověřování tokenů se obvykle provádí, aby se zabránilo *hotlinking* obsahu, ve kterém jiný web, jako je například vývěska, používá vaše prostředky bez povolení. Hotlinking může mít vliv na náklady na doručování obsahu. Povolením ověřování tokenů na síti CDN jsou požadavky ověřeny serverem EDGE CDN před tím, než cdn doručí obsah. 

## <a name="how-it-works"></a>Jak to funguje

Ověřování tokenu ověří, že požadavky jsou generovány důvěryhodným webem tím, že vyžadují, aby požadavky obsahovaly hodnotu tokenu, která obsahuje kódované informace o žadateli. Obsah se žadateli doručuje pouze v případě, že kódované informace splňují požadavky; v opačném případě jsou požadavky odepřeny. Požadavky můžete nastavit pomocí jednoho nebo více z následujících parametrů:

- Země: Povolit nebo odepřít žádosti, které pocházejí ze zemí nebo oblastí určených jejich [kódem země](/previous-versions/azure/mt761717(v=azure.100)).
- Adresa URL: Povolit pouze požadavky, které odpovídají zadanému datovému zdroji nebo cestě.
- Hostitel: Povolit nebo odepřít požadavky, které používají zadané hostitele v hlavičce požadavku.
- Odkazný odkaz: Povolit nebo odepřít požadavek od zadaného odkazu.
- IP adresa: Povolit pouze požadavky, které pocházejí z určité adresy IP nebo podsítě IP.
- Protokol: Povolit nebo odepřít požadavky na základě protokolu použitého k vyžádání obsahu.
- Čas vypršení platnosti: Přiřaďte datum a časové období, abyste zajistili, že odkaz zůstane platný pouze po omezenou dobu.

Další informace naleznete v podrobných příkladech konfigurace pro každý parametr v [nastavení ověřování tokenu](#setting-up-token-authentication).

>[!IMPORTANT] 
> Pokud je povolena autorizace tokenu pro libovolnou cestu v tomto účtu, režim standardní mezipaměti je jediný režim, který lze použít pro ukládání řetězce dotazu do mezipaměti. Další informace najdete v tématu [Řízení chování Azure CDN při ukládání řetězců dotazu do mezipaměti](cdn-query-string-premium.md).

## <a name="reference-architecture"></a>Referenční architektura

Následující diagram pracovního postupu popisuje, jak CDN používá ověřování tokenů pro práci s vaší webovou aplikací.

![Pracovní postup ověřování tokenů CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Logika ověření tokenu v koncovém bodě CDN
    
Následující vývojový diagram popisuje, jak Azure CDN ověřuje požadavek klienta při ověřování tokenu je nakonfigurován na koncovém bodu CDN.

![Logika ověření tokenu CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Nastavení ověřování tokenu

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do svého profilu CDN a pak vyberte **Spravovat** a spusťte doplňkový portál.

    ![Tlačítko Spravovat profil CDN](./media/cdn-token-auth/cdn-manage-btn.png)

2. Najeďte přes **HTTP Large**a v informačním rámečku vyberte **Uštovit token.** Poté můžete nastavit šifrovací klíč a parametry šifrování následujícím způsobem:

   1. Vytvořte jeden nebo více šifrovacích klíčů. Šifrovací klíč rozlišuje malá a velká písmena a může obsahovat libovolnou kombinaci alfanumerických znaků. Jiné typy znaků, včetně mezer, nejsou povoleny. Maximální délka je 250 znaků. Chcete-li zajistit, aby šifrovací klíče byly náhodné, doporučujeme je vytvořit pomocí [nástroje OpenSSL](https://www.openssl.org/). 

      Nástroj OpenSSL má následující syntaxi:

      ```rand -hex <key length>```

      Například:

      ```OpenSSL> rand -hex 32``` 

      Chcete-li se vyhnout prostojům, vytvořte primární i záložní klíč. Záložní klíč poskytuje nepřetržitý přístup k obsahu při aktualizaci primárního klíče.
    
   2. Do pole **Primární klíč** zadejte jedinečný šifrovací klíč a volitelně zadejte záložní klíč do pole **Klíč zálohování.**

   3. Vyberte minimální verzi šifrování pro každý klíč ze seznamu **Minimální verze šifrování** a pak vyberte **Aktualizovat**:
      - **V2**: Označuje, že klíč lze použít ke generování tokenů verze 2.0 a 3.0. Tuto možnost použijte pouze v případě, že přecházíte ze staršího šifrovacího klíče verze 2.0 na klíč verze 3.0.
      - **V3**: (Doporučeno) Označuje, že klíč lze použít pouze ke generování tokenů verze 3.0.

      ![Klíč nastavení tokenu CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Pomocí šifrovacího nástroje nastavte parametry šifrování a vygenerujte token. Pomocí šifrovacího nástroje můžete povolit nebo odepřít požadavky na základě doby vypršení platnosti, země nebo oblasti, odkazované adresy, protokolu a IP adresy klienta (v libovolné kombinaci). Přestože neexistuje žádné omezení počtu a kombinace parametrů, které lze kombinovat do vytvoření tokenu, celková délka tokenu je omezena na 512 znaků. 

      ![Nástroj pro šifrování CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Do části **Šifrovací nástroj** zadejte hodnoty pro jeden nebo více následujících parametrů šifrování: 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Název parametru</th> 
      >   <th>Popis</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Přiřadí čas vypršení platnosti tokenu, po kterém vyprší platnost tokenu. Požadavky odeslané po vypršení platnosti jsou odmítnuty. Tento parametr používá unixové časové razítko, které je založeno na počtu `1/1/1970 00:00:00 GMT`sekund od standardní unixové epochy . (Můžete použít on-line nástroje pro převod mezi standardním časem a unixového času.)> 
      >    Pokud například chcete, aby platnost `12/31/2016 12:00:00 GMT`tokenu vypršela na `1483185600`, zadejte hodnotu časového razítka Unixu . 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Umožňuje přizpůsobit tokeny konkrétnímu prostředku nebo cestě. Omezuje přístup k požadavkům, jejichž adresa URL začíná určitou relativní cestou. Adresy URL rozlišují malá a velká písmena. Zadejte více cest oddělením každé cesty čárkou; nepřidávejte mezery. V závislosti na vašich požadavcích můžete nastavit různé hodnoty, které poskytují různé úrovně přístupu.> 
      >    Například pro adresu `http://www.mydomain.com/pictures/city/strasbourg.png`URL jsou tyto požadavky povoleny pro následující vstupní hodnoty: 
      >    <ul>
      >       <li>Vstupní `/`hodnota : Všechny požadavky jsou povoleny.</li>
      >       <li>Vstupní `/pictures`hodnota , jsou povoleny následující požadavky: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Vstupní `/pictures/`hodnota : Jsou povoleny pouze požadavky obsahující `/pictures/` cestu. Například, `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Vstupní `/pictures/city/strasbourg.png`hodnota : Jsou povoleny pouze požadavky pro tuto konkrétní cestu a prostředek.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Umožňuje pouze požadavky, které pocházejí z jedné nebo více určených zemí nebo oblastí. Požadavky, které pocházejí ze všech ostatních zemí nebo oblastí, jsou odmítnuty. Pro každou zemi použijte dvoupísmenný [kód země ISO 3166](/previous-versions/azure/mt761717(v=azure.100)) a každou z nich oddělte čárkou; nepřidávejte mezeru. Chcete-li například povolit přístup pouze ze Spojených `US,FR`států a Francie, zadejte .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Odmítne požadavky, které pocházejí z jedné nebo více určených zemí nebo oblastí. Požadavky, které pocházejí ze všech ostatních zemí nebo oblastí, jsou povoleny. Implementace je stejná jako <b>parametr ec_country_allow.</b> Pokud je v parametrech <b>ec_country_allow</b> i <b>ec_country_deny</b> k dispozici kód země, má přednost <b>ec_country_allow</b> parametr.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Umožňuje pouze požadavky od zadaného odkazu. Odkazovatel identifikuje adresu URL webové stránky, která je propojena s požadovaným zdrojem. Nezahrnejte protokol do hodnoty parametru.> 
      >    Následující typy vstupů jsou povoleny:
      >    <ul>
      >       <li>Název hostitele nebo název hostitele a cesta.</li>
      >       <li>Několik odkazovatelé. Chcete-li přidat více odkazovat, oddělte každý odkazovat s čárkou; nepřidávejte mezeru. Pokud zadáte hodnotu odkazovat, ale odkazovat informace není odeslána v požadavku z důvodu konfigurace prohlížeče, požadavek je ve výchozím nastavení odepřen.</li> 
      >       <li>Požadavky s chybějícími nebo prázdnými informacemi referreru. Ve výchozím nastavení <b>ec_ref_allow</b> parametr blokuje tyto typy požadavků. Chcete-li tyto požadavky povolit, zadejte buď text "chybí", nebo zadejte prázdnou hodnotu (pomocí koncové čárky).</li> 
      >       <li>Subdomény. Chcete-li povolit subdomény, zadejte hvězdičku (\*). Chcete-li například povolit `contoso.com`všechny `*.contoso.com`subdomény položky , zadejte .</li>
      >    </ul> 
      >    Chcete-li například povolit `www.contoso.com`přístup k požadavkům `contoso2.com`z , všech subdomén v `www.contoso.com,*.contoso.com,missing`písmenu a) a požadavků s prázdnými nebo chybějícími odkazujícími osobami, zadejte .</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Odmítne požadavky od zadaného odkazu. Implementace je stejná jako <b>parametr ec_ref_allow.</b> Pokud odkazovaní je k dispozici v <b>ec_ref_allow</b> a <b>ec_ref_deny</b> parametry, <b>ec_ref_allow</b> parametr má přednost.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Umožňuje pouze požadavky ze zadaného protokolu. Platné hodnoty `http` `https`jsou `http,https`, , nebo .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Odmítne požadavky ze zadaného protokolu. Implementace je stejná jako <b>parametr ec_proto_allow.</b> Pokud je protokol k dispozici v <b>ec_proto_allow</b> i <b>ec_proto_deny</b> parametry, <b>ec_proto_allow</b> parametr má přednost.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Omezuje přístup k IP adrese zadaného uchazeče. Jsou podporovány protokoly IPV4 i IPV6. Můžete zadat adresu IP s jedním požadavkem nebo adresy IP přidružené k určité podsíti. Například `11.22.33.0/22` umožňuje požadavky z IP adres 11.22.32.1 na 11.22.35.254.</td>
      > </tr>
      > </table>

   5. Po zadání hodnot parametrů šifrování vyberte klíč, který chcete šifrovat (pokud jste vytvořili primární i záložní klíč) ze seznamu **Klíč k šifrování.**
    
   6. Vyberte verzi šifrování ze seznamu **Verze šifrování:** **V2** pro verzi 2 nebo **V3** pro verzi 3 (doporučeno). 

   7. Chcete-li token **vygenerovat,** vyberte šifrovat.

      Po vygenerování tokenu se zobrazí v poli **Generovaný token.** Chcete-li použít token, přidejte jej jako řetězec dotazu na konec souboru v cestě url. Například, `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Volitelně otestujte token pomocí dešifrovacího nástroje, abyste mohli zobrazit parametry tokenu. Vložte hodnotu tokenu do pole **Token k dešifrování.** Vyberte šifrovací klíč, který chcete použít, ze seznamu **Kód k dešifrování** a pak vyberte **Dešifrovat**.

      Po dešifrování tokenu jsou jeho parametry zobrazeny v poli **Původní parametry.**

   9. Volitelně můžete přizpůsobit typ kódu odpovědi, který je vrácen při odepření požadavku. Vyberte **Možnost Povoleno**a pak vyberte kód odpovědi ze seznamu **Kód odpovědi.** **Název záhlaví** je automaticky nastaven na **umístění**. Chcete-li implementovat nový kód odpovědi, vyberte **možnost Uložit.** U některých kódů odpovědí musíte také zadat adresu URL chybové stránky do pole **Hodnota záhlaví.** Ve výchozím nastavení je vybrán kód odpovědi **403** (Zakázáno). 

3. V části **HTTP Large**vyberte **Modul pravidel**. Modul pravidel slouží k definování cest pro použití funkce, povolení funkce ověřování tokenů a povolení dalších funkcí souvisejících s ověřováním tokenů. Další informace naleznete v [tématu Rules engine reference](cdn-rules-engine-reference.md).

   1. Vyberte existující pravidlo nebo vytvořte nové pravidlo pro definování datového zdroje nebo cesty, pro kterou chcete použít ověřování tokenu. 
   2. Chcete-li povolit ověřování tokenů u pravidla, vyberte v seznamu **Funkce** možnost **[Token Auth](cdn-verizon-premium-rules-engine-reference-features.md#token-auth)** a pak vyberte **Povoleno**. Pokud aktualizujete pravidlo, vyberte **aktualizovat** nebo **Přidat,** pokud vytváříte pravidlo.
        
      ![Příklad ověřování tokenů modulu pravidel CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. V modulu pravidel můžete také povolit další funkce související s ověřováním tokenů. Chcete-li povolit některou z následujících funkcí, vyberte ji ze seznamu **Funkce** a pak vyberte **Možnost Povoleno**.
    
   - **[Kód odmítnutí ověření tokenu](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-denial-code)**: Určuje typ odpovědi, která je vrácena uživateli při odepření požadavku. Zde nastavená pravidla přepíší kód odpovědi nastavený v části **Vlastní zpracování odmítnutí** na stránce ověřování založené na tokenech.

   - **[Token Umítat ignorovat url případ:](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-ignore-url-case)** Určuje, zda adresa URL použitá k ověření tokenu je malá a velká písmena.

   - **[Parametr token ututh](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-parameter)**: Přejmenuje parametr řetězce dotazu tokenu, který se zobrazí v požadované adrese URL. 
        
     ![Příklad nastavení ověřování tokenu tokenu modulu pravidel CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Token můžete přizpůsobit přístupem ke zdrojovému kódu na [GitHubu](https://github.com/VerizonDigital/ectoken).
   Mezi dostupné jazyky patří:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Funkce Azure CDN a ceny poskytovatelů

Informace o funkcích naleznete v [tématu Azure CDN product features](cdn-features.md). Informace o cenách naleznete v [tématu Content Delivery Network pricing](https://azure.microsoft.com/pricing/details/cdn/).
