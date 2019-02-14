---
title: Kurz – Konfigurace HTTPS pro vlastní doménu Azure CDN | Microsoft Docs
description: V tomto kurzu se dozvíte, jak povolit a zakázat HTTPS pro vlastní doménu koncového bodu Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/18/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: c9fb24cd6bcfae273c5430eee8d1fa8df58c6202
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236307"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Kurz: Konfigurace HTTPS pro vlastní doménu Azure CDN

V tomto kurzu se dozvíte, jak povolit protokol HTTPS pro vlastní doménu přidruženou ke koncovému bodu služby Azure CDN. Použitím protokolu HTTPS pro vlastní doménu (například https:\//www.contoso.com) zajistíte zabezpečené doručování citlivých dat prostřednictvím šifrování TLS/SSL při posílání přes internet. Když se webový prohlížeč připojí k webu přes HTTPS, ověří certifikát zabezpečení webu a to, že je vydán legitimní certifikační autoritou. Tento proces zajišťuje zabezpečení a chrání vaše webové aplikace před útoky.

Azure CDN ve výchozím nastavení podporuje HTTPS pro název hostitele koncového bodu CDN. Pokud například vytvoříte koncový bod CDN (například https:\//contoso.azureedge.net), HTTPS se automaticky povolí.  

Mezi klíčové atributy vlastní funkce HTTPS patří mimo jiné:

- Bez dalších poplatků: Neexistují žádné náklady pro získání certifikátů nebo obnovení a bez dalších nákladů pro provoz protokolu HTTPS. Platíte pouze za GB odchozího provozu mimo síť CDN.

- Jednoduché povolení: Je k dispozici zřízení jedním kliknutím [webu Azure portal](https://portal.azure.com). K povolení této funkce můžete použít také rozhraní REST API nebo jiné vývojářské nástroje.

- Kompletní správu certifikátů je k dispozici: Všechny certifikátů zajišťujete a management se postará služba za vás. Certifikáty se zřizují automaticky a před vypršením platnosti se automaticky obnovují. Tím odpadá riziko přerušení služby kvůli vypršení platnosti certifikátu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Povolit protokol HTTPS pro vlastní doménu
> - Použít certifikát spravovaný CDN 
> - Použít vlastní certifikát
> - Ověření domény
> - Zakázat protokol HTTPS pro vlastní doménu

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Před dokončením kroků v tomto kurzu musíte nejprve vytvořit profil CDN a alespoň jeden koncový bod CDN. Další informace najdete v tématu [rychlý start: Vytvoření koncového bodu a profilu Azure CDN](cdn-create-new-endpoint.md).

Kromě toho musíte ke koncovému bodu CDN přidružit vlastní doménu Azure CDN. Další informace najdete v tématu [kurzu: Přidání vlastní domény do koncového bodu Azure CDN](cdn-map-content-to-custom-domain.md)

---

## <a name="ssl-certificates"></a>Certifikáty SSL
Pokud chcete povolit protokol HTTPS, abyste mohli zabezpečeně dodávat obsah na vlastní doménu služby Azure CDN, musíte použít certifikát SSL. Můžete použít certifikát, který je spravovaný službou Azure CDN, nebo můžete použít svůj vlastní certifikát.


# <a name="option-1-default-enable-https-with-a-cdn-managed-certificatetaboption-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Možnost 1 (výchozí): Povolení HTTPS pomocí certifikátu spravovat CDN](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Když použijete certifikát spravovaný CDN, můžete funkci HTTPS zapnout několika kliknutími. Azure CDN se kompletně postará o úlohy správy certifikátů, jako je třeba nákup nebo obnovení. Jakmile funkci povolíte, proces se okamžitě zahájí. Pokud už je vlastní doména namapovaná na koncový bod CDN, není potřeba žádná další akce. Azure CDN postup zpracuje a dokončí vaši žádost automaticky. Pokud je ale vaše vlastní doména namapovaná jinde, musíte vlastnictví domény ověřit prostřednictvím e-mailu.

Pokud chcete povolit HTTPS pro vlastní doménu, postupujte následovně:

1. V [webu Azure portal](https://portal.azure.com), přejděte do vaší **Azure CDN Standard od společnosti Microsoft**, **Azure CDN Standard od Akamai**, **Azure CDN Standard od Verizonu** nebo **Azure CDN Premium od Verizonu** profilu.

2. V seznamu koncových bodů CDN vyberte koncový bod obsahující vaši vlastní doménu.

    ![Seznam koncových bodů](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Zobrazí se stránka **Koncový bod**.

3. V seznamu vlastních domén vyberte vlastní doménu, pro kterou chcete povolit HTTPS.

    ![Seznam vlastních domén](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Zobrazí se stránka **Vlastní doména**.

4. V části Typ správy certifikátu vyberte **Spravováno sítí CDN**.

5. Výběrem možnosti **Zapnuto** povolte HTTPS.

    ![Stav HTTPS pro vlastní doménu](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

6. Pokračujte k části [Ověření domény](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificatetaboption-2-enable-https-with-your-own-certificate"></a>[Option 2: Povolení protokolu HTTPS s vlastním certifikátem](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Tato možnost je dostupná jenom u profilů **Azure CDN Standard od Microsoftu**. 
>
 
K povolení funkce HTTPS můžete použít vlastní certifikát. Tento proces se provádí prostřednictvím integrace s Azure Key Vault, která vám umožní bezpečně ukládat vaše certifikáty. Azure CDN používá tento zabezpečený mechanismus k získání vašeho certifikátu a vyžaduje několik dalších kroků. Když vytváříte svůj certifikát SSL, musíte ho vytvořit s povolenou certifikační autoritou (CA). Pokud použijete nepovolenou certifikační autoritu, vaše žádost se odmítne. Seznam povolených certifikační autority najdete v tématu s popisem [povolených certifikačních autorit pro umožnění vlastního HTTPS v Azure CDN](cdn-troubleshoot-allowed-ca.md).

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Příprava účtu a certifikátu Azure Key Vault
 
1. Azure Key Vault: Musíte mít spuštěný účet služby Azure Key Vault ve stejném předplatném jako profil Azure CDN a koncové body CDN, které chcete povolit vlastní protokol HTTPS. Pokud účet Azure Key Vault nemáte, vytvořte ho.
 
2. Azure Key Vault certificates: Pokud už máte certifikát, nahrajte ho přímo do vašeho účtu služby Azure Key Vault nebo vytvořit nový certifikát přímo prostřednictvím Azure Key Vault z některého z partnerů certifikační autority služby Azure Key Vault se integruje s. 

### <a name="register-azure-cdn"></a>Registrace Azure CDN

Zaregistrujte Azure CDN jako aplikaci v Azure Active Directory pomocí PowerShellu.

1. V případě potřeby nainstalujte [prostředí Azure PowerShell](/powershell/azure/install-az-ps) na místním počítači.

2. V PowerShellu spusťte následující příkaz:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`

    ![Registrace Azure CDN v PowerShellu](./media/cdn-custom-ssl/cdn-register-powershell.png)
              

### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Udělení přístupu k trezoru klíčů pro Azure CDN
 
Udělte Azure CDN oprávnění přistupovat k certifikátům (tajným kódům) ve vašem účtu Azure Key Vault.

1. V účtu trezoru klíčů vyberte v části Nastavení možnost **Zásady přístupu**, pak vyberte **Přidat novou** a vytvořte novou zásadu.

    ![Vytvoření nové zásady přístupu](./media/cdn-custom-ssl/cdn-new-access-policy.png)

2. V části **Výběr objektu zabezpečení** vyhledejte **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** a zvolte **Microsoft.Azure.Cdn**. Klikněte na **Vybrat**.

    ![Nastavení zásad přístupu](./media/cdn-custom-ssl/cdn-access-policy-settings.png)

3. V části **Oprávnění na základě tajných kódů** vyberte **Získat** a povolte, aby síť CDN mohla tato oprávnění provádět za účelem získání a zobrazení těchto certifikátů. 

4. Vyberte **OK**. 

    Azure CDN teď může přistupovat k tomuto trezoru klíčů a certifikátům (tajným kódům), které jsou v tomto trezoru klíčů uloženy.
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Výběr certifikátu k nasazení pro Azure CDN
 
1. Vraťte se zpět na portál Azure CDN a vyberte profil a koncový bod CDN, pro které chcete vlastní HTTPS povolit. 

2. V seznamu vlastních domén vyberte vlastní doménu, pro kterou chcete povolit HTTPS.

    Zobrazí se stránka **Vlastní doména**.

3. V části Typ správy certifikátu vyberte **Použít vlastní certifikát**. 

    ![Konfigurace certifikátu](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Vyberte trezor klíčů, certifikát (tajný kód) a verzi certifikátu.

    Azure CDN zobrazí následující informace: 
    - Účty trezoru klíčů pro ID vašeho předplatného 
    - Certifikáty (tajné kódy) v rámci vybraného trezoru klíčů 
    - Dostupné verze certifikátu 
 
5. Výběrem možnosti **Zapnuto** povolte HTTPS.
  
6. Při použití vlastního certifikátu se ověření domény nevyžaduje. Pokračujte k části [Čekání na rozšíření](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>Ověření domény

Pokud už používáte vlastní doménu, která se mapuje na váš vlastní koncový bod pomocí záznamu CNAME, nebo používáte vlastní certifikát, pokračujte k tématu  
[Vlastní doména se mapuje na koncový bod CDN](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). Jinak, pokud už položka záznamu CNAME pro váš koncový bod neexistuje nebo obsahuje subdoménu cdnverify, pokračujte k tématu [Vlastní doména se nemapuje na koncový bod CDN](#custom-domain-is-not-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Vlastní doména se mapuje na koncový bod CDN pomocí záznamu CNAME

Při přidávání vlastní domény do vašeho koncového bodu jste v tabulce DNS vašeho doménového registrátora vytvořili záznam CNAME, kterým jste vlastní doménu namapovali na název hostitele koncového bodu CDN. Pokud tento záznam CNAME stále existuje a neobsahuje subdoménu cdnverify, certifikační autorita DigiCert ho použije k automatickému ověření vlastnictví vaší vlastní domény. 

Při použití vlastního certifikátu se ověření domény nevyžaduje.

Váš záznam CNAME by měl mít následující formát, kde *Název* je název vaší vlastní domény a *Hodnota* je název hostitele vašeho koncového bodu CDN:

| Název            | Typ  | Hodnota                 |
|-----------------|-------|-----------------------|
| www.contoso.com | CNAME | contoso.azureedge.net |

Další informace o záznamech CNAME najdete v tématu popisujícím [vytvoření záznamu DNS CNAME](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain).

Pokud je váš záznam CNAME ve správném formátu, DigiCert automaticky ověří váš název vlastní domény a vytvoří pro váš název domény vyhrazený certifikát. DigiCert vám neodešle ověřovací e-mail a vy nebudete muset potvrzovat svou žádost. Certifikát je platný jeden rok a před vypršením platnosti se automaticky obnoví. Pokračujte k části [Čekání na rozšíření](#wait-for-propagation). 

Automatické ověření trvá obvykle několik minut. Pokud se vaše doména neověří do hodiny, otevřete lístek podpory.

>[!NOTE]
>Pokud máte záznam CAA (Certificate Authority Authorization) pro vašeho poskytovatele DNS, musí jako platnou certifikační autoritu zahrnovat DigiCert. Záznam CAA umožňuje vlastníkům domén určit u poskytovatelů DNS, které certifikační autority mají oprávnění k vystavování certifikátů pro jejich domény. Pokud certifikační autorita přijme objednávku na certifikát pro doménu se záznamem CAA a tato certifikační autorita není uvedená jako autorizovaný vystavitel certifikátů, nebude moci vystavit certifikát pro danou doménu nebo subdoménu. Informace o správě záznamů CAA najdete v tématu [Správa záznamů CAA](https://support.dnsimple.com/articles/manage-caa-record/). Nástroj pro práci se záznamy CAA najdete tady: [CAA Record Helper](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>Vlastní doména se nemapuje na koncový bod CDN

Pokud položka záznamu CNAME pro váš koncový bod už neexistuje nebo obsahuje subdoménu cdnverify, postupujte podle zbývajících pokynů v tomto kroku.

>[!NOTE]
>E-mailová ověření vlastní domény vlastnictví není momentálně k dispozici pro **Azure CDN od Akamai** profily. Tato funkce je aktuálně v našem seznamu nevyřízených věcí. 

Po povolení HTTPS pro vlastní doménu certifikační autorita DigiCert ověří vlastnictví vaší domény tak, že kontaktuje žadatele o registraci na základě informací o žadateli o registraci v registru [WHOIS](http://whois.domaintools.com/) domény. Kontakt proběhne přes e-mailovou adresu (ve výchozím nastavení) nebo telefonní číslo uvedené v registraci WHOIS. Nejprve je potřeba provést ověření domény, a teprve pak se protokol HTTPS pro vaši vlastní doménu aktivuje. Na schválení domény máte šest pracovních dnů. Žádosti, které se nepotvrdí do šesti pracovních dnů, se automaticky zruší. 

![Záznam WHOIS](./media/cdn-custom-ssl/whois-record.png)

DigiCert odešle ověřovací e-mail také na další e-mailové adresy. Pokud jsou informace o žadateli o registraci v registru WHOIS privátní, ujistěte se, že můžete provést schválení přímo z některé z následujících adres:

admin@&lt;název_vaší_domény.com&gt;  
administrator@&lt;název_vaší_domény.com&gt;  
webmaster@&lt;název_vaší_domény.com&gt;  
hostmaster@&lt;název_vaší_domény.com&gt;  
postmaster@&lt;název_vaší_domény.com&gt;  

Během několika minut byste měli obdržet podobný e-mail jako v následujícím příkladu s výzvou ke schválení žádosti. Pokud používáte filtr proti spamu, přidejte v něm adresu admin@digicert.com na seznam povolených. Pokud e-mail neobdržíte do 24 hodin, kontaktujte podporu Microsoftu.
    
![E-mail pro ověření domény](./media/cdn-custom-ssl/domain-validation-email.png)

Po kliknutí na odkaz na schválení budete přesměrování na následující online formulář pro schválení: 
    
![Formulář pro ověření domény](./media/cdn-custom-ssl/domain-validation-form.png)

Postupujte podle pokynů ve formuláři. Máte na výběr dvě možnosti ověření:

- Můžete schválit všechny budoucí objednávky zadané přes stejný účet a pro stejnou kořenovou doménu, například contoso.com. Tento přístup se doporučuje v případě, že pro stejnou kořenovou doménu plánujete přidat další vlastní domény.

- Můžete schválit pouze konkrétní název hostitele použitý v této žádosti. Další požadavky budou vyžadovat dodatečné schválení.

Po schválení DigiCert dokončí vytvoření certifikátu pro váš název vlastní domény. Certifikát je platný jeden rok a před vypršením platnosti se automaticky obnoví.

## <a name="wait-for-propagation"></a>Čekání na rozšíření

Po ověření názvu domény může aktivace funkce HTTPS pro vlastní doménu trvat 6 až 8 hodin. Po dokončení tohoto procesu se stav HTTPS na webu Azure Portal nastaví na **Povoleno** a čtyři kroky operace v dialogovém okně vlastní domény se označí jako dokončené. Vaše vlastní doména je teď připravená k použití HTTPS.

![Dialogové okno pro povolení HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Průběh operace

Následující tabulka ukazuje průběh operace, která proběhne při povolení HTTPS. Po povolení HTTPS se v dialogovém okně vlastní domény zobrazí čtyři kroky operace. Když se jednotlivé kroky aktivují, zobrazí se pod nimi další podrobnosti o dílčích krocích. Ne všechny tyto dílčí kroky se provedou. Po úspěšném dokončení kroku se vedle něj zobrazí zelená značka zaškrtnutí. 

| Krok operace | Podrobnosti o dílčím kroku operace | 
| --- | --- |
| 1. Odesílání žádosti | Odesílání žádosti |
| | Vaše žádost o HTTPS se právě odesílá. |
| | Vaše žádost o HTTPS se úspěšně odeslala. |
| 2. Ověření domény | Doména ser ověří automaticky, pokud se pomocí záznamu CNAME mapuje na koncový bod CDN. Jinak se na e-mail uvedený v záznamu o registraci vaší domény (žadatel o registraci v registru WHOIS) odešle žádost o ověření. Ověřte doménu co nejdříve. |
| | Vaše vlastnictví domény se úspěšně ověřilo. |
| | Platnost požadavku na ověření vlastnictví domény vypršela (zákazník pravděpodobně neodpověděl ve lhůtě 6 dní). HTTPS se pro vaši doménu nepovolí. * |
| | Požadavek na ověření vlastnictví domény byl zamítnut zákazníkem. HTTPS se pro vaši doménu nepovolí. * |
| 3. Zřizování certifikátu | Certifikační autorita momentálně vystavuje certifikát nutný pro povolení HTTPS pro vaši doménu. |
| | Certifikát byl vystaven a momentálně se nasazuje do sítě CDN. Může to trvat až 6 hodin. |
| | Certifikát se úspěšně nasadil do sítě CDN. |
| 4. Hotovo | Protokol HTTPS se ve vaší doméně úspěšně povolil. |

\* Tato zpráva se zobrazí pouze v případě, že dojde k chybě. 

Pokud před odesláním žádosti dojde k chybě, zobrazí se následující chybová zpráva:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Vyčištění prostředků – zákaz HTTPS

V předchozích krocích jste pro vlastní doménu povolili protokol HTTPS. Pokud už na vlastní doméně nechcete používat HTTPS, můžete HTTPS zakázat provedením následujících kroků:

### <a name="disable-the-https-feature"></a>Zákaz funkce HTTPS 

1. Na webu [Azure Portal](https://portal.azure.com) přejděte do svého profilu **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Verizonu** nebo **Azure CDN Premium od Verizonu**.

2. V seznamu koncových bodů klikněte na koncový bod obsahující vaši vlastní doménu.

3. Klikněte na vlastní doménu, pro kterou chcete zakázat HTTPS.

    ![Seznam vlastních domén](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Kliknutím na možnost **Vypnuto** zakažte HTTPS a pak klikněte na **Použít**.

    ![Dialogové okno HTTPS pro vlastní doménu](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Čekání na rozšíření

Po zákazu funkce HTTPS vlastní domény může trvat 6 až 8 hodin, než se změna projeví. Po dokončení tohoto procesu se stav HTTPS na webu Azure Portal nastaví na **Zakázáno** a tři kroky operace v dialogovém okně vlastní domény se označí jako dokončené. Vaše vlastní doména už nemůže používat HTTPS.

![Dialogové okno pro zákaz HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Průběh operace

Následující tabulka ukazuje průběh operace, která proběhne při zákazu HTTPS. Po zákazu HTTPS se v dialogovém okně vlastní domény zobrazí tři kroky operace. Když se jednotlivé kroky aktivují, zobrazí se pod nimi další podrobnosti. Po úspěšném dokončení kroku se vedle něj zobrazí zelená značka zaškrtnutí. 

| Průběh operace | Podrobnosti o operaci | 
| --- | --- |
| 1. Odesílání žádosti | Odesílání vaší žádosti |
| 2. Zrušení zřízení certifikátu | Odstraňování certifikátu |
| 3. Hotovo | Certifikát odstraněn |

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

1. *Kdo je poskytovatel certifikátu a jaký typ certifikátu se používá?*

    U **Azure CDN od Verizonu** i **Azure CDN od Microsoftu** slouží pro vaši vlastní doménu vyhrazený/jediný certifikát od Digicertu. 

2. *Používáte protokol TLS/SSL založený na IP nebo SNI?*

    **Azure CDN od Verizonu** i **Azure CDN Standard od Microsoftu** používají SNI TLS/SSL.

3. *Co když neobdržím e-mail pro ověření domény od DigiCert?*

    Pokud pro svou vlastní doménu máte záznam CNAME, který odkazuje přímo na název hostitele vašeho koncového bodu (a nepoužíváte název subdomény cdnverify), žádný e-mail pro ověření domény neobdržíte. Ověření proběhne automaticky. Jinak, pokud záznam CNAME nemáte a neobdrželi jste e-mail během 24 hodin, kontaktujte podporu Microsoftu.

4. *Je používání certifikátu SAN méně bezpečné než vyhrazený certifikát?*
    
    Certifikát SAN využívá stejné standardy šifrování a zabezpečení jako vyhrazený certifikát. Všechny vystavené certifikáty SSL k vylepšení zabezpečení serveru využívají šifrování SHA-256.

5. *Potřebuji záznam CAA (Certificate Authority Authorization) pro svého poskytovatele DNS?*

    Ne, záznam CAA (Certificate Authority Authorization) se v současné době nevyžaduje. Pokud ho však máte, musí jako platnou certifikační autoritu zahrnovat DigiCert.

6. *20. června 2018 začala síť Azure CDN od Verizonu ve výchozím nastavení používat vyhrazený certifikát s SNI TLS/SSL. Co se stane s mými existujícími vlastními doménami, které používají certifikát SAN (Subject Alternative Names, alternativní názvy subjektů) a protokol TLS/SSL založený na IP adrese?*

    Vaše stávající domény se budou v nadcházejících měsících postupně migrovat na jediný certifikát, pokud Microsoft dojde analýzou k tomu, že do vaší aplikace přicházejí jenom žádosti klientů SNI. Pokud Microsoft zjistí, že do vaší aplikace přicházejí žádosti klientů jiných než SNI, zůstanou domény v certifikátu SAN s protokolem TLS/SSL založeném na IP adrese. V žádném případě nedojde k přerušení poskytování vaší služby nebo podpory pro žádosti vašich klientů bez ohledu na to, jestli jsou tyto žádosti SNI nebo ne.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> - Povolit protokol HTTPS pro vlastní doménu
> - Použít certifikát spravovaný CDN 
> - Použít vlastní certifikát
> - Ověřit doménu
> - Zakázat protokol HTTPS pro vlastní doménu

V dalším kurzu se dozvíte, jak na koncovém bodu CDN nakonfigurovat ukládání do mezipaměti.

> [!div class="nextstepaction"]
> [Kurz: Nastavte pravidla ukládání do mezipaměti Azure CDN](cdn-caching-rules-tutorial.md)

