---
title: Kurz – konfigurace HTTPS pro Azure CDN vlastní doménu | Microsoft Docs
description: V tomto kurzu se dozvíte, jak povolit a zakázat HTTPS na vlastní doméně Azure CDN koncového bodu.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/1/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 5506e4e4bb41725fd8791d3c6e47bb0b94584923
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959437"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Kurz: Konfigurace HTTPS u Azure CDN vlastní domény

V tomto kurzu se dozvíte, jak povolit protokol HTTPS pro vlastní doménu, která je přidružená k Azure CDNmu koncovému bodu. Pomocí protokolu HTTPS ve vlastní doméně (například https: \//www. contoso. com) zajistíte zabezpečenou doručování citlivých dat prostřednictvím šifrování TLS/SSL při posílání přes Internet. Pokud je webový prohlížeč připojen k webu prostřednictvím protokolu HTTPS, ověří certifikát zabezpečení webu a ověří, zda je vydaný oprávněnou certifikační autoritou. Tento proces zajišťuje zabezpečení a chrání vaše webové aplikace před útoky.

Ve výchozím nastavení Azure CDN podporuje HTTPS u názvu hostitele koncového bodu CDN. Pokud třeba vytvoříte koncový bod CDN (například https: \//contoso. azureedge. NET), protokol HTTPS se automaticky povolí.  

Mezi klíčové atributy vlastní funkce HTTPS patří:

- Žádné další poplatky: pro získání a obnovení certifikátu se neúčtují žádné náklady ani žádné další poplatky za přenosy HTTPS. Platíte jenom za GB odchozích dat ze sítě CDN.

- Jednoduché povolení: zřizování jedním kliknutím je k dispozici z [Azure Portal](https://portal.azure.com). Tuto funkci můžete povolit také pomocí REST API nebo jiných vývojářských nástrojů.

- Kompletní správa certifikátů je k dispozici: pro vás bude zpracována veškerá zakázka a Správa certifikátů. Certifikáty se automaticky zřídí a obnovují před vypršením platnosti, což odstraní rizika přerušení služby z důvodu vypršení platnosti certifikátu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Povolte protokol HTTPS ve vlastní doméně.
> - Použití certifikátu spravovaného CDN 
> - Použití vlastního certifikátu
> - Ověřit doménu
> - Zakažte protokol HTTPS ve vlastní doméně.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Než budete moct dokončit kroky v tomto kurzu, musíte nejdřív vytvořit profil CDN a aspoň jeden koncový bod CDN. Další informace najdete v tématu [rychlý Start: vytvoření profilu Azure CDN a koncového bodu](cdn-create-new-endpoint.md).

Kromě toho musíte do koncového bodu CDN přidružit Azure CDN vlastní doménu. Další informace najdete v tématu [kurz: Přidání vlastní domény do koncového bodu Azure CDN.](cdn-map-content-to-custom-domain.md) 

> [!IMPORTANT]
> Certifikáty spravované CDN nejsou pro kořenové nebo vrcholové domény k dispozici. Pokud je vaše Azure CDN vlastní doména kořenovou nebo vrcholovou doménou, musíte použít funkci Přineste si vlastní certifikát. 
>

---

## <a name="ssl-certificates"></a>Certifikáty SSL
Pokud chcete protokol HTTPS povolit pro bezpečné doručování obsahu do Azure CDN vlastní domény, musíte použít certifikát SSL. Můžete použít certifikát, který se spravuje Azure CDN nebo použijte vlastní certifikát.


# <a name="option-1-default-enable-https-with-a-cdn-managed-certificatetaboption-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Možnost 1 (výchozí): povolení protokolu HTTPS pomocí certifikátu spravovaného CDN](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Když použijete certifikát spravovaný přes CDN, funkce HTTPS se dá zapnout jenom několika kliknutími. Azure CDN zcela zpracovává úlohy správy certifikátů, jako je například nákup a obnovení. Po povolení této funkce se proces spustí okamžitě. Pokud je vlastní doména už namapovaná na koncový bod CDN, nevyžaduje se žádná další akce. Azure CDN zpracuje kroky a automaticky dokončí požadavek. Pokud je ale vaše vlastní doména namapovaná jinde, musíte k ověření vlastnictví domény použít e-mail.

Pokud chcete povolit protokol HTTPS u vlastní domény, postupujte podle těchto kroků:

1. V [Azure Portal](https://portal.azure.com)přejděte na **Azure CDN Standard od Microsoftu**, **Azure CDN Standard z Akamai**, **Azure CDN Standard od Verizon** nebo **Azure CDN Premium od profilu Verizon** .

2. V seznamu koncových bodů CDN vyberte koncový bod obsahující vaši vlastní doménu.

    ![Seznam koncových bodů](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Zobrazí se stránka **koncový bod** .

3. V seznamu vlastních domén vyberte vlastní doménu, pro kterou chcete povolit protokol HTTPS.

    ![Seznam vlastních domén](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Zobrazí se stránka **vlastní doména** .

4. V části typ správy certifikátů vyberte **spravovaná síť CDN**.

5. Pokud chcete povolit protokol HTTPS, vyberte **zapnuto** .

    ![Stav HTTPS vlastní domény](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

6. Pokračujte a [Ověřte doménu](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificatetaboption-2-enable-https-with-your-own-certificate"></a>[Možnost 2: povolení HTTPS s vlastním certifikátem](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Tato možnost je k dispozici pouze u **Azure CDN od společnosti Microsoft** a **Azure CDN z profilů Verizon** . 
>
 
K povolení funkce HTTPS můžete použít vlastní certifikát. Tento proces se provádí prostřednictvím integrace s Azure Key Vault, která umožňuje bezpečné ukládání certifikátů. Azure CDN používá tento zabezpečený mechanismus k získání certifikátu a vyžaduje několik dalších kroků. Když vytvoříte certifikát SSL, musíte ho vytvořit s povolenou certifikační autoritou (CA). V opačném případě, pokud použijete nepovolenou certifikační autoritu, vaše žádost se odmítne. Seznam povolených certifikačních autorit najdete v části [povolené certifikační autority pro povolení vlastního protokolu HTTPS v Azure CDN](cdn-troubleshoot-allowed-ca.md). Pro **Azure CDN z Verizon**bude přijata jakákoli platná certifikační autorita. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Příprava účtu a certifikátu Azure Key trezoru
 
1. Azure Key Vault: musíte mít spuštěný účet Azure Key Vault ve stejném předplatném, jako je profil Azure CDN, a koncové body CDN, u kterých chcete povolit vlastní protokol HTTPS. Vytvořte účet Azure Key Vault, pokud ho ještě nemáte.
 
2. Azure Key Vault certifikáty: Pokud už certifikát máte, můžete ho nahrát přímo na účet Azure Key Vault nebo můžete vytvořit nový certifikát přímo prostřednictvím Azure Key Vault z jedné z partnerských autorit, které Azure Key Vault integruje s. 

### <a name="register-azure-cdn"></a>Registrovat Azure CDN

Zaregistrujte Azure CDN jako aplikaci v Azure Active Directory prostřednictvím PowerShellu.

1. V případě potřeby nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) na svém místním počítači.

2. V prostředí PowerShell spusťte následující příkaz:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`

    ![Registrace Azure CDN v PowerShellu](./media/cdn-custom-ssl/cdn-register-powershell.png)
              

### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Udělení Azure CDN k vašemu trezoru klíčů
 
Udělte Azure CDN oprávnění pro přístup k certifikátům (tajným) ve vašem účtu Azure Key Vault.

1. V účtu trezoru klíčů v části nastavení vyberte **zásady přístupu**a pak vyberte **Přidat nový** . vytvoří se nová zásada.

    ![Vytvořit nové zásady přístupu](./media/cdn-custom-ssl/cdn-new-access-policy.png)

2. V **možnosti vybrat objekt zabezpečení**vyhledejte **205478C0-BD83-4e1b-a9d6-db63a3e1e1c8**a zvolte **Microsoft. Azure. CDN**. Klikněte na **Vybrat**.

    ![Nastavení zásad přístupu](./media/cdn-custom-ssl/cdn-access-policy-settings.png)

3. Vyberte **oprávnění certifikátu**a potom zaškrtněte políčka pro možnost **získat** a **seznam** , aby síť CDN mohla provést tato oprávnění k získání a výpisu certifikátů.

4. Vyberte **OK**. 

    Azure CDN teď mají přístup k tomuto trezoru klíčů a k certifikátům (tajným klíčům) uloženým v tomto trezoru klíčů.
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Vyberte certifikát pro nasazení Azure CDN
 
1. Vraťte se na portál Azure CDN a vyberte profil a koncový bod CDN, u kterých chcete povolit vlastní HTTPS. 

2. V seznamu vlastních domén vyberte vlastní doménu, pro kterou chcete povolit protokol HTTPS.

    Zobrazí se stránka **vlastní doména** .

3. V části typ správy certifikátů vyberte **použít vlastní certifikát**. 

    ![Konfigurace certifikátu](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Vyberte Trezor klíčů, certifikát (tajný kód) a verzi certifikátu.

    Azure CDN jsou uvedené následující informace: 
    - Účty trezoru klíčů pro ID předplatného. 
    - Certifikáty (tajné klíče) v rámci vybraného trezoru klíčů. 
    - Dostupné verze certifikátu. 
 
5. Pokud chcete povolit protokol HTTPS, vyberte **zapnuto** .
  
6. Pokud používáte vlastní certifikát, ověření domény se nevyžaduje. Pokračujte [v čekání na šíření](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>Ověřit doménu

Pokud už používáte vlastní doménu, která je namapovaná na váš vlastní koncový bod pomocí záznamu CNAME nebo vlastního certifikátu, přejděte k  
[Vlastní doména je namapovaná na koncový bod CDN](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). Jinak, pokud položka záznamu CNAME pro váš koncový bod už neexistuje nebo obsahuje subdoménu cdnverify, přejděte k [vlastní doméně, která není namapovaná na váš koncový bod CDN](#custom-domain-is-not-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Vlastní doména se mapuje na koncový bod CDN pomocí záznamu CNAME.

Po přidání vlastní domény do koncového bodu jste v tabulce DNS vašeho registrátora domény vytvořili záznam CNAME, který ho namapuje na název hostitele koncového bodu CDN. Pokud tento záznam CNAME stále existuje a neobsahuje subdoménu cdnverify, certifikační autorita DigiCert ho použije k automatickému ověření vlastnictví vlastní domény. 

Pokud používáte vlastní certifikát, ověření domény se nevyžaduje.

Záznam CNAME by měl být v následujícím formátu, kde *název* vlastní domény a *hodnota* je název hostitele koncového bodu CDN:

| Name            | Typ  | Hodnota                 |
|-----------------|-------|-----------------------|
| < www. contoso. com > | ZÁZNAMU | contoso.azureedge.net |

Další informace o záznamech CNAME najdete v tématu [Vytvoření záznamu DNS CNAME](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain).

Pokud je váš záznam CNAME ve správném formátu, DigiCert automaticky ověří vlastní název domény a vytvoří vyhrazený certifikát pro název domény. DigitCert vám nepošle ověřovací e-mail a vy nebudete muset žádost schválit. Certifikát je platný jeden rok a před vypršením platnosti se automaticky obnoví. Pokračujte [v čekání na šíření](#wait-for-propagation). 

Automatické ověřování obvykle trvá několik hodin. Pokud se vaše doména neověří za 24 hodin, otevřete lístek podpory.

>[!NOTE]
>Pokud máte záznam o autorizaci certifikační autority (CAA) s vaším poskytovatelem DNS, musí jako platnou certifikační autoritu zahrnovat DigiCert. CAA záznam umožňuje vlastníkům domény určit jejich poskytovatele DNS, které CA mají oprávnění k vystavování certifikátů pro svou doménu. Pokud certifikační autorita obdrží objednávku pro certifikát pro doménu, která má záznam CAA a tato certifikační autorita není uvedená jako autorizovaný Vydavatel, je zakázáno vystavit certifikát pro tuto doménu nebo subdoménu. Informace o správě záznamů CAA najdete v tématu [Správa záznamů CAA](https://support.dnsimple.com/articles/manage-caa-record/). Nástroj pro záznam CAA najdete v tématu [Pomocník pro záznam CAA](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>Vlastní doména není namapovaná na váš koncový bod CDN.

>[!NOTE]
>Pokud používáte **Azure CDN z Akamai**, měli byste nastavit tento záznam CNAME tak, aby umožňoval automatizované ověřování domény. "_acme-Challenge. &lt;custom doména název hostitele @ no__t-1-> CNAME-> &lt;custom Domain hostname&gt;.ak-acme-challenge.azureedge.net"

Pokud položka záznamu CNAME obsahuje subdoménu cdnverify, postupujte podle zbývajících pokynů v tomto kroku.

DigiCert odešle e-mail s ověřovacím e-mailem na následující e-mailové adresy. Ověřte, že můžete schvalovat přímo z jedné z následujících adres:

Správce @ @no__t -0your-Domain-Name. com @ no__t-1  
Správce @ @no__t -0your-Domain-Name. com @ no__t-1  
správce webového serveru @ @no__t -0your-Domain-Name. com @ no__t-1  
hostmaster @ @no__t -0your-Domain-Name. com @ no__t-1  
postmaster @ @no__t -0your-Domain-Name. com @ no__t-1  

Měli byste obdržet e-mail během několika minut, podobně jako v následujícím příkladu, který vás požádá o schválení žádosti. Pokud používáte filtr spamu, přidejte verification@digicert.com do seznamu povolených položek. Pokud neobdržíte e-mail během 24 hodin, kontaktujte podporu Microsoftu.
    
![E-mail pro ověření domény](./media/cdn-custom-ssl/domain-validation-email.png)

Po kliknutí na odkaz na schválení budete přesměrováni na následující formulář pro online schválení: 
    
![Formulář ověřování domény](./media/cdn-custom-ssl/domain-validation-form.png)

Postupujte podle pokynů ve formuláři; máte dvě možnosti ověřování:

- Všechny budoucí objednávky, které jsou umístěné přes stejný účet, můžete schvalovat pro stejnou kořenovou doménu. například contoso.com. Tento přístup se doporučuje, pokud plánujete přidat další vlastní domény pro stejnou kořenovou doménu.

- Můžete schválit jenom konkrétní název hostitele použitý v této žádosti. Pro následné požadavky je vyžadováno dodatečné schválení.

Po schválení dokončí DigiCert vytvoření certifikátu pro vlastní název domény. Certifikát je platný jeden rok a před vypršením jeho platnosti se automaticky obnoví.

## <a name="wait-for-propagation"></a>Počkat na šíření

Po ověření názvu domény může trvat až 6-8 hodin, než se aktivuje funkce HTTPS vlastní domény. Po dokončení procesu je vlastní stav HTTPS v Azure Portal nastaven na **povoleno** a čtyři kroky operace v dialogovém okně vlastní doména jsou označeny jako úplné. Vaše vlastní doména je teď připravená k použití protokolu HTTPS.

![Dialogové okno Povolit protokol HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Průběh operace

Následující tabulka ukazuje průběh operace, která nastane, když povolíte protokol HTTPS. Po povolení protokolu HTTPS se v dialogovém okně vlastní doména zobrazí čtyři kroky operace. Jak je každý krok aktivní, v průběhu tohoto kroku se zobrazí další podrobnosti o dalších krocích. Ne všechny tyto podkroky budou provedeny. Po úspěšném dokončení kroku se vedle něho zobrazí zelený symbol zaškrtnutí. 

| Krok operace | Podrobnosti o podkroku operace | 
| --- | --- |
| 1 odeslání žádosti | Odesílá se žádost. |
| | Vaše žádost o HTTPS se posílá. |
| | Vaše žádost o HTTPS se úspěšně odeslala. |
| 2 ověření domény | Doména je automaticky ověřena, pokud je záznam CNAME mapován na koncový bod CDN. V opačném případě se žádost o ověření pošle e-mailu uvedenému v registračním záznamu vaší domény (WHOIS základě). Ověřte prosím co nejdříve doménu. |
| | Vaše vlastnictví domény se úspěšně ověřilo. |
| | Platnost žádosti o ověření vlastnictví domény vypršela (zákazník nejspíš nereagoval do 6 dnů). Protokol HTTPS nebude ve vaší doméně povolený. * |
| | Požadavek na ověření vlastnictví domény byl zamítnut zákazníkem. Protokol HTTPS nebude ve vaší doméně povolený. * |
| 3 zřizování certifikátů | Certifikační autorita aktuálně vydává certifikát potřebný k povolení protokolu HTTPS ve vaší doméně. |
| | Certifikát se vystavil a v tuto chvíli se nasazuje do sítě CDN. Může to trvat až 6 hodin. |
| | Certifikát se úspěšně nasadil do sítě CDN. |
| 4 dokončení | Protokol HTTPS se ve vaší doméně úspěšně povolil. |

\* Tato zpráva se zobrazí, pokud nedošlo k chybě. 

Pokud před odesláním požadavku dojde k chybě, zobrazí se následující chybová zpráva:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Vyčištění prostředků – zakázání HTTPS

V předchozích krocích jste povolili protokol HTTPS ve vlastní doméně. Pokud už nechcete používat vlastní doménu s protokolem HTTPS, můžete protokol HTTPS vypnout provedením kroků následujících:

### <a name="disable-the-https-feature"></a>Zakázat funkci HTTPS 

1. V [Azure Portal](https://portal.azure.com)přejděte na **Azure CDN Standard od Microsoftu**, **Azure CDN Standard z Verizon** nebo **Azure CDN Premium od profilu Verizon** .

2. V seznamu koncových bodů klikněte na koncový bod obsahující vaši vlastní doménu.

3. Klikněte na vlastní doménu, pro kterou chcete zakázat protokol HTTPS.

    ![Seznam vlastních domén](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Kliknutím na **vypnuto** zakažte https a pak klikněte na **použít**.

    ![Vlastní dialog HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Počkat na šíření

Po zakázání funkce HTTPS vlastní domény může trvat až 6-8 hodin, než se projeví. Po dokončení procesu se vlastní stav HTTPS v Azure Portal nastaví na **zakázáno** a tři kroky operace v dialogovém okně vlastní domény se označí jako dokončené. Vaše vlastní doména už nemůže používat protokol HTTPS.

![Zakázat dialog HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Průběh operace

Následující tabulka ukazuje průběh operace, která nastane, když zakážete protokol HTTPS. Po zakázání protokolu HTTPS se v dialogovém okně vlastní doména zobrazí tři kroky operace. Po aktivaci každého kroku se zobrazí další podrobnosti v kroku. Po úspěšném dokončení kroku se vedle něho zobrazí zelený symbol zaškrtnutí. 

| Průběh operace | Podrobnosti operace | 
| --- | --- |
| 1 odeslání žádosti | Odesílá se žádost. |
| 2\. zrušení zřízení certifikátu | Odstraňování certifikátu |
| 3 dokončení | Certifikát se odstranil. |

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

1. *Kdo je poskytovatel certifikátu a jaký typ certifikátu se používá?*

    Pro **Azure CDN z Verizon** a **Azure CDN od Microsoftu**se pro vaši vlastní doménu používá vyhrazený/jeden certifikát, který poskytuje DigiCert. 

2. *Používáte protokol TLS/SSL založený na protokolu IP nebo SNI?*

    Jak **Azure CDN z Verizon** a **Azure CDN Standard od Microsoftu** používají sni TLS/SSL.

3. *Co když mi e-maily s ověřením domény neobdrží z DigiCert?*

    Pokud máte záznam CNAME pro vaši vlastní doménu, která odkazuje přímo na název hostitele koncového bodu (a nepoužíváte název subdomény cdnverify), nebudete dostávat e-maily pro ověření domény. Automatické ověřování probíhá. Jinak, pokud nemáte záznam CNAME a nedostali jste e-mailovou zprávu během 24 hodin, kontaktujte podporu Microsoftu.

4. *Používá se certifikát sítě SAN méně bezpečný než vyhrazený certifikát?*
    
    Certifikát sítě SAN dodržuje stejné standardy šifrování a zabezpečení jako vyhrazený certifikát. Všechny vydané certifikáty SSL používají SHA-256 pro rozšířené zabezpečení serveru.

5. *Potřebuji záznam o autorizaci certifikační autority se zprostředkovatelem DNS?*

    Ne, autorizační záznam certifikační autority se momentálně nevyžaduje. Pokud ho ale máte, musí jako platnou certifikační autoritu zahrnovat DigiCert.

6. *20. června 2018 Azure CDN od Verizon zahájil používání vyhrazeného certifikátu s SNI TLS/SSL ve výchozím nastavení. Co se stane se stávajícími vlastními doménami pomocí certifikátu sítě SAN a protokolu TLS/SSL založeného na protokolu IP?*

    V nadcházejících měsících se vaše stávající domény postupně migrují do jednoho certifikátu, pokud Microsoft analyzuje, že se do vaší aplikace provedou pouze požadavky klienta SNI. Pokud společnost Microsoft zjistí, že v rámci vaší aplikace nějaké požadavky klientů neSNI, budou vaše domény zůstat v certifikátu sítě SAN s protokolem TLS/SSL založeným na protokolu IP. Bez ohledu na to, jestli jsou tyto požadavky SNI nebo SNI, nedojde v žádném případě k žádnému přerušení vaší služby ani podpoře vašich klientských požadavků.

7. *Jak fungují obnovení certifikátů pomocí funkce Přineste si vlastní certifikát?*

    Pokud chcete zajistit, aby byl novější certifikát nasazený v infrastruktuře PoP, jednoduše nahrajte nový certifikát do trezoru klíčů Azure a potom v nastavení SSL na Azure CDN zvolte nejnovější verzi certifikátu a pak stiskněte Uložit. Azure CDN se pak propogate nový aktualizovaný certifikát. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak:

> [!div class="checklist"]
> - Povolte protokol HTTPS ve vlastní doméně.
> - Použití certifikátu spravovaného CDN 
> - Použití vlastního certifikátu
> - Ověřte doménu.
> - Zakažte protokol HTTPS ve vlastní doméně.

Přejděte k dalšímu kurzu, kde se dozvíte, jak nakonfigurovat ukládání do mezipaměti pro koncový bod CDN.

> [!div class="nextstepaction"]
> [Kurz: nastavení pravidel ukládání Azure CDN do mezipaměti](cdn-caching-rules-tutorial.md)

