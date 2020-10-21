---
title: Kurz – konfigurace HTTPS pro vlastní doménu pro služby Azure front-dveří | Microsoft Docs
description: V tomto kurzu se dozvíte, jak povolit a zakázat protokol HTTPS v konfiguraci front-dveří Azure pro vlastní doménu.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: duau
ms.openlocfilehash: 6db01531a34e936c59e5aa01a5c37f3a2ccf7345
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277719"
---
# <a name="tutorial-configure-https-on-a-front-door-custom-domain"></a>Kurz: Konfigurace HTTPS pro vlastní doménu Front Dooru

V tomto kurzu se dozvíte, jak povolit protokol HTTPS pro vlastní doménu přidruženou ke Front Dooru v části hostitelů front-endu. Použitím protokolu HTTPS pro vlastní doménu (například https:\//www.contoso.com) zajistíte zabezpečené doručování citlivých dat prostřednictvím šifrování TLS/SSL při posílání přes internet. Když se webový prohlížeč připojí k webu přes HTTPS, ověří certifikát zabezpečení webu a to, že je vydán legitimní certifikační autoritou. Tento proces zajišťuje zabezpečení a chrání vaše webové aplikace před útoky.

Přední dveře Azure ve výchozím nastavení podporují protokol HTTPS pro výchozí název hostitele front-dveří. Například pokud vytvoříte přední dvířka (například `https://contoso.azurefd.net` ), protokol HTTPS je automaticky povolen pro požadavky vytvořené na `https://contoso.azurefd.net` . Jakmile ale začleníte vlastní doménu „www.contoso.com“, bude potřeba pro tohoto hostitele front-endu povolit HTTPS dodatečně.   

Mezi klíčové atributy vlastní funkce HTTPS patří mimo jiné:

- Žádné další náklady: Získání a obnovení certifikátů je bezplatné a za provoz protokolu HTTPS se neplatí žádné další poplatky. 

- Jednoduché povolení: Na webu [Azure Portal](https://portal.azure.com) je k dispozici zřízení jedním kliknutím. K povolení této funkce můžete použít také rozhraní REST API nebo jiné vývojářské nástroje.

- Kompletní správa certifikátů je dostupná: Veškeré nákupy a správu certifikátů zajišťujete sami. Certifikáty se zřizují automaticky a před vypršením platnosti se automaticky obnovují. Tím odpadá riziko přerušení služby kvůli vypršení platnosti certifikátu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Povolit protokol HTTPS pro vlastní doménu
> - Použít certifikát spravovaný službou AFD 
> - Použijte vlastní certifikát, to znamená vlastní certifikát TLS/SSL.
> - Ověření domény
> - Zákaz protokolu HTTPS pro vlastní doménu


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

Před dokončením kroků v tomto kurzu musíte nejprve vytvořit Front Door s minimálně jednou začleněnou vlastní doménou. Další informace najdete v [kurzu přidání vlastní domény do Front Dooru](front-door-custom-domain.md).

## <a name="tlsssl-certificates"></a>Certifikáty TLS/SSL

Pokud chcete protokol HTTPS povolit pro bezpečné doručování obsahu na vlastní doméně front-dveří, musíte použít certifikát TLS/SSL. Můžete použít certifikát, který je spravovaný přes službu Azure front-dveří, nebo použít vlastní certifikát.


### <a name="option-1-default-use-a-certificate-managed-by-front-door"></a>Možnost 1 (výchozí): Použití certifikátu spravovaného službou Front Door

Když použijete certifikát, který spravuje přední dvířka Azure, může být funkce HTTPS zapnutá jenom několika kliknutími. Přední dveře Azure zcela zpracovávají úlohy správy certifikátů, jako je například nákup a obnovení. Jakmile funkci povolíte, proces se okamžitě zahájí. Pokud už je vlastní doména namapovaná na výchozího hostitele front-endu Front Dooru (`{hostname}.azurefd.net`), není potřeba žádná další akce. Front Door postup zpracuje a dokončí vaši žádost automaticky. Pokud je ale vaše vlastní doména namapovaná jinde, musíte vlastnictví domény ověřit prostřednictvím e-mailu.

Pokud chcete povolit HTTPS pro vlastní doménu, postupujte následovně:

1. Na webu [Azure Portal](https://portal.azure.com) přejděte na profil **Front Door**.

2. V seznamu hostitelů front-endu vyberte vlastní doménu, u které chcete povolit HTTPS.

3. V části **HTTPS pro vlastní doménu** klikněte na **Povoleno** a jako zdroj certifikátu vyberte **Front Door managed** (Spravováno službou Front Door).

4. Klikněte na Uložit.

5. Pokračujte k části [Ověření domény](#validate-the-domain).

> [!NOTE]
> U spravovaných certifikátů AFD se vynutilo omezení počtu znaků DigiCert 64. Pokud dojde k překročení tohoto limitu, ověření se nezdaří.


### <a name="option-2-use-your-own-certificate"></a>Možnost 2: Použití vlastního certifikátu

K povolení funkce HTTPS můžete použít vlastní certifikát. Tento proces se provádí prostřednictvím integrace s Azure Key Vault, která vám umožní bezpečně ukládat vaše certifikáty. Přední dveře Azure používají tento zabezpečený mechanismus k získání certifikátu a vyžadují několik dalších kroků. Při vytváření certifikátu TLS/SSL ho musíte vytvořit s povolenou certifikační autoritou (CA). Pokud použijete nepovolenou certifikační autoritu, vaše žádost se odmítne. Seznam povolených certifikačních autorit najdete v tématu [povolené certifikační autority pro povolení vlastního protokolu HTTPS na frontách Azure](front-door-troubleshoot-allowed-ca.md).

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Příprava účtu a certifikátu Azure Key Vault
 
1. Azure Key Vault: Musíte mít účet Azure Key Vault běžící v rámci stejného předplatného jako Front Door, pro který chcete vlastní HTTPS povolit. Pokud účet Azure Key Vault nemáte, vytvořte ho.

> [!WARNING]
> Přední dveře Azure v současné době podporují jenom Key Vault účty ve stejném předplatném, jako je konfigurace front-dveří. Pokud vyberete účet v rámci jiného předplatného, dojde k chybě.

2. Certifikáty Azure Key Vault: Pokud už certifikát máte, můžete ho nahrát přímo do vašeho účtu Azure Key Vault, nebo můžete vytvořit nový certifikát přímo prostřednictvím služby Azure Key Vault z jedné z certifikačních autorit, se kterými se Azure Key Vault integruje. Nahrajte certifikát jako objekt **certifikátu** místo **tajného klíče**.

> [!NOTE]
> U vlastního certifikátu TLS/SSL nepodporuje přední dveře certifikáty s algoritmy kryptografie EC.

#### <a name="register-azure-front-door"></a>Registrovat přední dveře Azure

Zaregistrujte instanční objekt pro služby Azure front-dveří jako aplikaci ve vašem Azure Active Directory prostřednictvím PowerShellu.

> [!NOTE]
> Tato akce vyžaduje globální oprávnění správce a je nutné ji provést pouze **jednou** pro každého tenanta.

1. V případě potřeby nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) v PowerShellu na místním počítači.

2. V PowerShellu spusťte následující příkaz:

     `New-AzADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Udělení přístupu k trezoru klíčů pro Azure na přední dveře
 
Udělte pro přístup k certifikátům ve vašem účtu Azure Key Vault oprávnění pro přední dveře Azure.

1. V účtu trezoru klíčů vyberte v části Nastavení možnost **Zásady přístupu**, pak vyberte **Přidat novou** a vytvořte novou zásadu.

2. V části **Výběr objektu zabezpečení** vyhledejte **ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037** a vyberte **Microsoft.Azure.Frontdoor**. Klikněte na **Vybrat**.

3. V části **oprávnění tajného**kódu vyberte **získat** , pokud chcete, aby přední dvířka získala certifikát.

4. V části **oprávnění certifikátu**vyberte **získat** a umožněte tak, aby přední dvířka získala certifikát.

5. Vyberte **OK**. 

    Přední dveře Azure teď můžou získat přístup k tomuto Key Vault a certifikáty, které jsou uložené v tomto Key Vault.
 
#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Vyberte certifikát pro nasazení na přední dveře Azure.
 
1. Vraťte se na Front Door na portálu. 

2. V seznamu vlastních domén vyberte vlastní doménu, pro kterou chcete povolit HTTPS.

    Zobrazí se stránka **Vlastní doména**.

3. V části Typ správy certifikátu vyberte **Použít vlastní certifikát**. 

4. Přední dveře Azure vyžadují, aby bylo předplatné Key Vault stejné jako pro vaše přední dveře. Vyberte trezor klíčů, certifikát (tajný kód) a verzi certifikátu.

    Přední dvířka Azure obsahují následující informace: 
    - Účty trezoru klíčů pro ID vašeho předplatného 
    - Certifikáty (tajné kódy) v rámci vybraného trezoru klíčů 
    - Dostupné verze certifikátu 
 
5. Při použití vlastního certifikátu se ověření domény nevyžaduje. Pokračujte k části [Čekání na rozšíření](#wait-for-propagation).

## <a name="validate-the-domain"></a>Ověření domény

Pokud už používáte vlastní doménu, která se mapuje na váš vlastní koncový bod pomocí záznamu CNAME, nebo používáte vlastní certifikát, pokračujte k tématu  
[Vlastní doména se mapuje na Front Door](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record). Jinak, pokud už položka záznamu CNAME pro vaši doménu neexistuje nebo obsahuje subdoménu afdverify, pokračujte k části [Vlastní doména se nemapuje na Front Door](#custom-domain-is-not-mapped-to-your-front-door).

### <a name="custom-domain-is-mapped-to-your-front-door-by-a-cname-record"></a>Vlastní doména se mapuje na Front Door pomocí záznamu CNAME

Při přidávání vlastní domény do hostitelů Front Dooru jste v tabulce DNS vašeho doménového registrátora vytvořili záznam CNAME, kterým jste vlastní doménu namapovali na výchozí název hostitele .azurefd.net pro tento Front Door. Pokud tento záznam stále existuje a neobsahuje subdoménu afdverify, certifikační autorita DigiCert ho použije k automatickému ověření vlastnictví vaší vlastní domény. 

Při použití vlastního certifikátu se ověření domény nevyžaduje.

Záznam CNAME by měl mít následující formát, kde *Název* je název vaší vlastní domény a *Hodnota* je výchozí název hostitele .azurefd.net vašeho Front Dooru:

| Název            | Typ  | Hodnota                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azurefd.net |

Další informace o záznamech CNAME najdete v tématu popisujícím [vytvoření záznamu DNS CNAME](../cdn/cdn-map-content-to-custom-domain.md).

Pokud je váš záznam CNAME ve správném formátu, DigiCert automaticky ověří váš název vlastní domény a vytvoří pro váš název domény vyhrazený certifikát. DigiCert vám neodešle ověřovací e-mail a vy nebudete muset potvrzovat svou žádost. Certifikát je platný jeden rok a před vypršením jeho platnosti se obnoví jeho platnost. Pokračujte k části [Čekání na rozšíření](#wait-for-propagation). 

Automatické ověření trvá obvykle několik minut. Pokud se vaše doména neověří do hodiny, otevřete lístek podpory.

>[!NOTE]
>Pokud máte záznam CAA (Certificate Authority Authorization) pro vašeho poskytovatele DNS, musí jako platnou certifikační autoritu zahrnovat DigiCert. Záznam CAA umožňuje vlastníkům domén určit u poskytovatelů DNS, které certifikační autority mají oprávnění k vystavování certifikátů pro jejich domény. Pokud certifikační autorita přijme objednávku na certifikát pro doménu se záznamem CAA a tato certifikační autorita není uvedená jako autorizovaný vystavitel certifikátů, nebude moci vystavit certifikát pro danou doménu nebo subdoménu. Informace o správě záznamů CAA najdete v tématu [Správa záznamů CAA](https://support.dnsimple.com/articles/manage-caa-record/). Nástroj pro práci se záznamy CAA najdete tady: [CAA Record Helper](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-front-door"></a>Vlastní doména se nemapuje na Front Door

Pokud položka záznamu CNAME pro váš koncový bod už neexistuje nebo obsahuje subdoménu afdverify, postupujte podle zbývajících pokynů v tomto kroku.

Po povolení HTTPS pro vlastní doménu certifikační autorita DigiCert ověří vlastnictví vaší domény tak, že kontaktuje žadatele o registraci na základě informací o žadateli o registraci v registru [WHOIS](http://whois.domaintools.com/) domény. Kontakt proběhne přes e-mailovou adresu (ve výchozím nastavení) nebo telefonní číslo uvedené v registraci WHOIS. Nejprve je potřeba provést ověření domény, a teprve pak se protokol HTTPS pro vaši vlastní doménu aktivuje. Na schválení domény máte šest pracovních dnů. Žádosti, které se nepotvrdí do šesti pracovních dnů, se automaticky zruší. 

![Záznam WHOIS](./media/front-door-custom-domain-https/whois-record.png)

DigiCert odešle ověřovací e-mail také na další e-mailové adresy. Pokud jsou informace o žadateli o registraci v registru WHOIS privátní, ujistěte se, že můžete provést schválení přímo z některé z následujících adres:

admin@&lt;název_vaší_domény.com&gt;  
administrator@&lt;název_vaší_domény.com&gt;  
webmaster@&lt;název_vaší_domény.com&gt;  
hostmaster@&lt;název_vaší_domény.com&gt;  
postmaster@&lt;název_vaší_domény.com&gt;  

Během několika minut byste měli obdržet podobný e-mail jako v následujícím příkladu s výzvou ke schválení žádosti. Pokud používáte filtr spamu, přidejte admin@digicert.com ho do seznamu povolených. Pokud e-mail neobdržíte do 24 hodin, kontaktujte podporu Microsoftu.

Po kliknutí na odkaz na schválení budete přesměrováni na online formulář pro schválení. Postupujte podle pokynů ve formuláři. Máte na výběr dvě možnosti ověření:

- Můžete schválit všechny budoucí objednávky zadané přes stejný účet a pro stejnou kořenovou doménu, například contoso.com. Tento přístup se doporučuje v případě, že pro stejnou kořenovou doménu plánujete přidat další vlastní domény.

- Můžete schválit pouze konkrétní název hostitele použitý v této žádosti. Další požadavky budou vyžadovat dodatečné schválení.

Po schválení DigiCert dokončí vytvoření certifikátu pro váš název vlastní domény. Certifikát je platný jeden rok a před vypršením jeho platnosti se obnoví jeho platnost.

## <a name="wait-for-propagation"></a>Čekání na rozšíření

Po ověření názvu domény může aktivace funkce HTTPS pro vlastní doménu trvat 6 až 8 hodin. Po dokončení tohoto procesu se stav HTTPS na webu Azure Portal nastaví na **Povoleno** a čtyři kroky operace v dialogovém okně vlastní domény se označí jako dokončené. Vaše vlastní doména je teď připravená k použití HTTPS.

### <a name="operation-progress"></a>Průběh operace

Následující tabulka ukazuje průběh operace, která proběhne při povolení HTTPS. Po povolení HTTPS se v dialogovém okně vlastní domény zobrazí čtyři kroky operace. Když se jednotlivé kroky aktivují, zobrazí se pod nimi další podrobnosti o dílčích krocích. Ne všechny tyto dílčí kroky se provedou. Po úspěšném dokončení kroku se vedle něj zobrazí zelená značka zaškrtnutí. 

| Krok operace | Podrobnosti o dílčím kroku operace | 
| --- | --- |
| 1. Odesílání žádosti | Odesílání žádosti |
| | Vaše žádost o HTTPS se právě odesílá. |
| | Vaše žádost o HTTPS se úspěšně odeslala. |
| 2. Ověření domény | Pokud je doména pomocí záznamu CNAME namapovaná na výchozí název hostitele .azurefd.net vašeho Front Dooru, ověří se automaticky. Jinak se na e-mail uvedený v záznamu o registraci vaší domény (žadatel o registraci v registru WHOIS) odešle žádost o ověření. Ověřte doménu co nejdříve. |
| | Vaše vlastnictví domény se úspěšně ověřilo. |
| | Platnost požadavku na ověření vlastnictví domény vypršela (zákazník pravděpodobně neodpověděl ve lhůtě 6 dní). HTTPS se pro vaši doménu nepovolí. * |
| | Požadavek na ověření vlastnictví domény byl zamítnut zákazníkem. HTTPS se pro vaši doménu nepovolí. * |
| 3. Zřizování certifikátu | Certifikační autorita momentálně vystavuje certifikát nutný pro povolení HTTPS pro vaši doménu. |
| | Certifikát byl vystaven a momentálně se nasazuje pro Front Door. Tento proces může trvat až jednu hodinu. |
| | Certifikát se pro Front Door nasadil úspěšně. |
| 4. Hotovo | Protokol HTTPS se ve vaší doméně úspěšně povolil. |

\* Tato zpráva se zobrazí pouze v případě, že dojde k chybě. 

Pokud před odesláním žádosti dojde k chybě, zobrazí se následující chybová zpráva:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

1. *Kdo je poskytovatel certifikátu a jaký typ certifikátu se používá?*

    Pro vlastní doménu se používá jediný (vyhrazený) certifikát vydaný společností DigiCert. 

2. *Používáte protokol TLS/SSL založený na IP nebo SNI?*

    Přední dveře Azure používají SNI TLS/SSL.

3. *Co když neobdržím e-mail pro ověření domény od DigiCert?*

    Pokud pro svou vlastní doménu máte záznam CNAME, který odkazuje přímo na název hostitele vašeho koncového bodu (a nepoužíváte název subdomény afdverify), žádný e-mail pro ověření domény neobdržíte. Ověření proběhne automaticky. Jinak, pokud záznam CNAME nemáte a neobdrželi jste e-mail během 24 hodin, kontaktujte podporu Microsoftu.

4. *Je používání certifikátu SAN méně bezpečné než vyhrazený certifikát?*
    
    Certifikát SAN využívá stejné standardy šifrování a zabezpečení jako vyhrazený certifikát. Všechny vydané certifikáty TLS/SSL používají pro rozšířené zabezpečení serveru SHA-256.

5. *Potřebuji záznam CAA (Certificate Authority Authorization) pro svého poskytovatele DNS?*

    Ne, záznam CAA (Certificate Authority Authorization) se v současné době nevyžaduje. Pokud ho však máte, musí jako platnou certifikační autoritu zahrnovat DigiCert.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste pro vlastní doménu povolili protokol HTTPS. Pokud už na vlastní doméně nechcete používat HTTPS, můžete HTTPS zakázat provedením následujících kroků:

### <a name="disable-the-https-feature"></a>Zákaz funkce HTTPS 

1. V [Azure Portal](https://portal.azure.com)přejděte do konfigurace front- **dveří Azure** .

2. V seznamu hostitelů front-endu klikněte na vlastní doménu, pro kterou chcete zakázat HTTPS.

3. Kliknutím na možnost **Zakázáno** zakažte HTTPS a potom klikněte na **Uložit**.

### <a name="wait-for-propagation"></a>Čekání na rozšíření

Po zákazu funkce HTTPS vlastní domény může trvat 6 až 8 hodin, než se změna projeví. Po dokončení tohoto procesu se stav HTTPS na webu Azure Portal nastaví na **Zakázáno** a tři kroky operace v dialogovém okně vlastní domény se označí jako dokončené. Vaše vlastní doména už nemůže používat HTTPS.

#### <a name="operation-progress"></a>Průběh operace

Následující tabulka ukazuje průběh operace, která proběhne při zákazu HTTPS. Po zákazu HTTPS se v dialogovém okně vlastní domény zobrazí tři kroky operace. Když se jednotlivé kroky aktivují, zobrazí se pod nimi další podrobnosti. Po úspěšném dokončení kroku se vedle něj zobrazí zelená značka zaškrtnutí. 

| Průběh operace | Podrobnosti o operaci | 
| --- | --- |
| 1. Odesílání žádosti | Odesílání vaší žádosti |
| 2. Zrušení zřízení certifikátu | Odstraňování certifikátu |
| 3. Hotovo | Certifikát odstraněn |

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Nahrajte certifikát do Key Vault.
* Ověří doménu.
* Pro vlastní doménu povolte HTTPS.

Pokud se chcete dozvědět, jak nastavit zásady geografického filtrování pro vaše přední dveře, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Nastavení zásad geografického filtrování](front-door-geo-filtering.md)