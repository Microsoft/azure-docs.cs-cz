---
title: Zabezpečení prostředků Azure CDN s ověřováním tokenu | Microsoft Docs
description: Naučte se používat ověřování pomocí tokenu k zabezpečení přístupu k prostředkům Azure CDN.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mazha
ms.openlocfilehash: 21ef06f37e6840df08b1477f9c0ff24f6e15d1a3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778016"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Zabezpečení Azure CDN prostředků pomocí ověřování tokenů

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Přehled

Ověřování tokenu je mechanismus, který umožňuje zabránit službě Azure Content Delivery Network (CDN) poskytovat prostředky neautorizovaným klientům. Ověřování tokenu se obvykle provádí, aby se zabránilo *hotlinking* obsahu, ve kterém jiný web, jako je například panel zpráv, používá vaše prostředky bez oprávnění. Hotlinking může mít dopad na poplatky za doručování obsahu. Povolením ověřování tokenu pro CDN se požadavky ověřují pomocí serveru CDN Edge, než CDN obsah doručí. 

## <a name="how-it-works"></a>Jak to funguje

Ověřování tokenu ověřuje, že požadavky jsou vygenerovány důvěryhodnou lokalitou, protože vyžadují, aby žádosti obsahovaly hodnotu tokenu, která obsahuje kódované informace o žadateli. Obsah je obsluhován žadateli pouze v případě, že kódované informace splňují požadavky. v opačném případě jsou požadavky zamítnuty. Požadavky můžete nastavit pomocí jednoho nebo několika následujících parametrů:

- Země/oblast: povoluje nebo zakazuje žádosti, které pocházejí ze zemí nebo oblastí určených jejich [kódem země/oblasti](/previous-versions/azure/mt761717(v=azure.100)).
- Adresa URL: povoluje pouze požadavky, které odpovídají zadanému prostředku nebo cestě.
- Hostitel: povoluje nebo zamítá žádosti, které používají zadané hostitele v hlavičce požadavku.
- Odkazující na adresu: povoluje nebo zakazuje požadavek ze zadaného odkazujícího serveru.
- IP adresa: povolí jenom požadavky, které pocházejí z konkrétní IP adresy nebo podsítě IP.
- Protokol: povoluje nebo zamítá žádosti na základě protokolu použitého k vyžádání obsahu.
- Čas vypršení platnosti: přiřaďte datum a časové období, abyste zajistili, že odkaz zůstane platný jenom za dobu omezeného časového období.

Další informace najdete v podrobných příkladech konfigurace pro každý parametr v tématu [nastavení ověřování tokenu](#setting-up-token-authentication).

>[!IMPORTANT] 
> Pokud je povolena autorizace tokenu pro jakoukoli cestu k tomuto účtu, je režim standardní mezipaměti jediným režimem, který lze použít pro ukládání řetězců dotazu do mezipaměti. Další informace najdete v tématu [Řízení chování Azure CDN při ukládání řetězců dotazu do mezipaměti](cdn-query-string-premium.md).

## <a name="reference-architecture"></a>Referenční architektura

Následující diagram pracovního postupu popisuje, jak CDN používá ověřování pomocí tokenu pro práci s vaší webovou aplikací.

![Pracovní postup ověřování tokenu CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Logika ověření tokenu na koncovém bodu CDN
    
Následující vývojový diagram popisuje, jak Azure CDN ověřuje požadavek klienta, když je na koncovém bodu CDN nakonfigurováno ověřování tokenu.

![Logika ověřování tokenu CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Nastavení ověřování tokenu

1. V [Azure Portal](https://portal.azure.com)přejděte na svůj profil CDN a pak vyberte **Spravovat** a spusťte doplňkový portál.

    ![Tlačítko Spravovat profil CDN](./media/cdn-token-auth/cdn-manage-btn.png)

2. Najeďte myší na **velkou velikost protokolu HTTP** a pak v informačním rámečku Vyberte **ověřování pomocí tokenu** . Pak můžete nastavit šifrovací klíč a parametry šifrování následujícím způsobem:

   1. Vytvořte jeden nebo více šifrovacích klíčů. Šifrovací klíč rozlišuje velká a malá písmena a může obsahovat libovolnou kombinaci alfanumerických znaků. Žádný jiný typ znaků, včetně mezer, není povolen. Maximální délka je 250 znaků. Aby bylo zajištěno, že šifrovací klíče jsou náhodné, doporučujeme je vytvořit pomocí [nástroje OpenSSL](https://www.openssl.org/). 

      Nástroj OpenSSL má následující syntaxi:

      ```rand -hex <key length>```

      Příklad:

      ```OpenSSL> rand -hex 32``` 

      Chcete-li se vyhnout výpadkům, vytvořte primární i záložní klíč. Záložní klíč poskytuje při aktualizaci primárního klíče nepřerušovaný přístup k vašemu obsahu.
    
   2. Do pole **primární klíč** zadejte jedinečný šifrovací klíč a volitelně zadejte do pole **záložní klíč** záložní klíč.

   3. Vyberte minimální verzi šifrování pro každý klíč ze seznamu **Minimální verze šifrování** a pak vyberte **aktualizovat** :
      - **V2** : označuje, že klíč lze použít ke generování tokenů verze 2,0 a 3,0. Tuto možnost použijte jenom v případě, že přecházíte z starší verze šifrovacího klíče 2,0 na klíč verze 3,0.
      - **V3** : (doporučeno) znamená, že klíč lze použít pouze k vygenerování tokenů verze 3,0.

      ![Klíč nastavení ověření tokenu CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. K nastavení parametrů šifrování a vygenerování tokenu použijte nástroj pro šifrování. Pomocí nástroje pro šifrování můžete povolit nebo zamítnout žádosti na základě času vypršení platnosti, země/oblasti, odkazujícího serveru, protokolu a IP adresy klienta (v libovolné kombinaci). I když neexistuje žádné omezení počtu a kombinace parametrů, které lze kombinovat, aby bylo možné vytvořit token, je celková délka tokenu omezena na 512 znaků. 

      ![Nástroj pro šifrování CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      V části **šifrovaný nástroj** zadejte hodnoty pro jeden nebo více následujících parametrů šifrování: 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Název parametru</th> 
      >   <th>Description</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Přiřadí k tokenu čas vypršení platnosti, po jehož uplynutí vyprší platnost tokenu. Žádosti odeslané po uplynutí doby platnosti jsou zamítnuté. Tento parametr používá časové razítko systému UNIX, které vychází z počtu sekund od standardní epocha systému UNIX `1/1/1970 00:00:00 GMT` . (Můžete použít online nástroje k převodu mezi standardním časem a časem v systému UNIX.)> 
      >    Například pokud chcete, aby vyprší platnost tokenu v `12/31/2016 12:00:00 GMT` , zadejte hodnotu časového razítka systému UNIX, `1483185600` . 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Umožňuje přizpůsobit tokeny konkrétnímu prostředku nebo cestě. Omezuje přístup k žádostem, jejichž adresa URL začíná konkrétní relativní cestou. V adresách URL se rozlišují malá a velká písmena. Zadáním více cest oddělte každou cestu čárkou. Nepřidávat mezery V závislosti na vašich požadavcích můžete nastavit různé hodnoty tak, aby poskytovaly různé úrovně přístupu.> 
      >    Například pro adresu URL `http://www.mydomain.com/pictures/city/strasbourg.png` jsou povoleny tyto požadavky pro následující vstupní hodnoty: 
      >    <ul>
      >       <li>Vstupní hodnota `/` : jsou povoleny všechny požadavky.</li>
      >       <li>Vstupní hodnota `/pictures` : jsou povoleny následující požadavky: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Vstupní hodnota `/pictures/` : jsou povoleny pouze požadavky obsahující `/pictures/` cestu. Například, `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Vstupní hodnota `/pictures/city/strasbourg.png` : jsou povoleny pouze žádosti pro tuto konkrétní cestu a prostředek.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Povoluje pouze žádosti, které pocházejí z jedné nebo více zadaných zemí nebo oblastí. Žádosti, které pocházejí ze všech ostatních zemí nebo oblastí, jsou odepřeny. Pro každou zemi nebo oblast použijte dvoumístné [číslo země/oblasti ISO 3166](/previous-versions/azure/mt761717(v=azure.100)) a každou z nich oddělte čárkou. Nepřidávejte mezeru. Pokud například chcete přístup udělit jenom z USA a Francie, zadejte `US,FR` .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Odmítá žádosti, které pocházejí z jedné nebo více zadaných zemí nebo oblastí. Žádosti, které pocházejí ze všech ostatních zemí nebo oblastí, jsou povoleny. Implementace je stejná jako parametr <b>ec_country_allow</b> . Pokud je kód země nebo oblasti přítomen jak v parametrech <b>ec_country_allow</b> , tak <b>ec_country_deny</b> , má parametr <b>ec_country_allow</b> přednost.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Povoluje pouze požadavky ze zadaného odkazujícího serveru. Odkazující strana identifikuje adresu URL webové stránky, která je propojena s požadovaným prostředkem. Nezahrnujte protokol do hodnoty parametru.> 
      >    Jsou povoleny následující typy vstupů:
      >    <ul>
      >       <li>Název hostitele nebo název hostitele a cesta.</li>
      >       <li>Více odkazujících. Chcete-li přidat více odkazujících serverů, oddělte každý odkazující znak čárkou. Nepřidávejte mezeru. Pokud zadáte hodnotu odkazujícího serveru, ale informace o odkazujícím serveru se v požadavku z důvodu konfigurace prohlížeče neodesílají, ve výchozím nastavení je požadavek odepřen.</li> 
      >       <li>Žádosti s informacemi o chybějících nebo prázdných odkazujících prodaných. Ve výchozím nastavení parametr <b>ec_ref_allow</b> zablokuje tyto typy požadavků. Pokud chcete tyto požadavky použít, zadejte text, "chybějící", nebo zadejte prázdnou hodnotu (pomocí koncové čárky).</li> 
      >       <li>Subdomén. Chcete-li, aby byly subdomény povoleny, zadejte hvězdičku ( \* ). Například pro povolení všech subdomén v `contoso.com` Zadejte `*.contoso.com` .</li>
      >    </ul> 
      >    Chcete-li například udělit přístup pro požadavky z `www.contoso.com` , všechny subdomény v rámci `contoso2.com` a požadavky s prázdnými nebo chybějícími odkazujícími servery, zadejte `www.contoso.com,*.contoso.com,missing` .</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Odmítá žádosti od zadaného odkazujícího serveru. Implementace je stejná jako parametr <b>ec_ref_allow</b> . Pokud je odkazující objekt přítomen v parametrech <b>ec_ref_allow</b> i <b>ec_ref_deny</b> , má parametr <b>ec_ref_allow</b> přednost.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Povoluje pouze požadavky ze zadaného protokolu. Platné hodnoty jsou `http` , `https` nebo `http,https` .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Odmítne požadavky ze zadaného protokolu. Implementace je stejná jako parametr <b>ec_proto_allow</b> . Pokud je protokol přítomen v parametrech <b>ec_proto_allow</b> i <b>ec_proto_deny</b> , má parametr <b>ec_proto_allow</b> přednost.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Omezí přístup k IP adrese zadaného žadatele. Podporují se adresy IPV4 i IPV6. Můžete zadat jednu nebo více žádostí o IP adresu, které jsou přidružené k určité podsíti. Například `11.22.33.0/22` umožňuje žádost z IP adres 11.22.32.1 na 11.22.35.254.</td>
      > </tr>
      > </table>

   5. Po dokončení zadávání hodnot parametrů šifrování vyberte klíč, který chcete zašifrovat (Pokud jste vytvořili primární a záložní klíč) z **klíče k zašifrování** seznamu.
    
   6. Vyberte verzi šifrování ze seznamu **verze šifrování** : **v2** pro verzi 2 nebo **V3** pro verzi 3 (doporučeno). 

   7. Pro vygenerování tokenu vyberte **Šifrovat** .

      Po vygenerování tokenu se tato zpráva zobrazí v poli **vygenerovaného tokenu** . Pokud chcete token použít, přidejte ho jako řetězec dotazu na konec souboru v cestě URL. Například, `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Volitelně otestujte token pomocí nástroje dešifrujte, abyste mohli zobrazit parametry tokenu. Vložte hodnotu token do pole **token k dešifrování** . Vyberte šifrovací klíč, který se použije ze seznamu **klíč k dešifrování** , a pak vyberte **dešifrovat** .

      Po dešifrování tokenu se jeho parametry zobrazí v poli **původní parametry** .

   9. Volitelně můžete přizpůsobit typ kódu odpovědi, který se vrátí, když je žádost zamítnuta. Vyberte **povoleno** a pak vyberte kód odpovědi ze seznamu **kódu odpovědi** . **Název záhlaví** je automaticky nastaven na **umístění** . Vyberte **Uložit** pro implementaci nového kódu odpovědi. U některých kódů odpovědí musíte zadat také adresu URL chybové stránky v poli **hodnota hlavičky** . Ve výchozím nastavení je vybrán kód odpovědi **403** (zakázáno). 

3. V části **velký protokol HTTP** vyberte **modul pravidel** . Použijete modul pravidel k definování cest pro použití funkce, povolení funkce ověřování tokenu a povolení dalších možností souvisejících s ověřováním tokenů. Další informace najdete v tématu [Referenční příručka k modulům pravidel](./cdn-verizon-premium-rules-engine-reference.md).

   1. Vyberte existující pravidlo nebo vytvořte nové pravidlo k definování prostředku nebo cesty, pro kterou chcete použít ověřování pomocí tokenu. 
   2. Pokud chcete pro pravidlo Povolit ověřování tokenu, vyberte v seznamu **funkce** možnost **[ověření tokenu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm)** a pak vyberte **povoleno** . **Pokud aktualizujete pravidlo nebo pokud** vytváříte pravidlo, vyberte **aktualizovat** .
        
      ![Příklad povolení ověřování tokenu modulu CDN Rules](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. V modulu pravidel můžete také povolit další funkce související s ověřováním tokenu. Pokud chcete povolit některou z následujících funkcí, vyberte ji ze seznamu **funkce** a pak vyberte **povoleno** .
    
   - **[Odepření kódu ověření tokenu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm)** : Určuje typ odpovědi, která se vrátí uživateli, když je žádost zamítnutá. Sada pravidel je popsána na stránce ověřování na základě tokenu, která je nastavená v části **vlastní nařízení zamítnutí** .

   - **[Případ ignorování adresy URL pro ověření tokenu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm)** : Určuje, jestli adresa URL použitá k ověření tokenu rozlišuje velká a malá písmena.

   - **[Parametr auth tokenu](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm)** : přejmenuje parametr řetězce dotazu auth tokenu, který se zobrazí na požadované adrese URL. 
        
     ![Příklad nastavení ověřování tokenu modulu CDN Rules](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Svůj token můžete přizpůsobit přístupem ke zdrojovému kódu na [GitHubu](https://github.com/VerizonDigital/ectoken).
   K dispozici jsou tyto jazyky:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python    

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN funkce a ceny zprostředkovatele

Informace o funkcích najdete v tématu [Azure CDN funkce produktu](cdn-features.md). Informace o cenách najdete v tématu [Content Delivery Network ceny](https://azure.microsoft.com/pricing/details/cdn/).