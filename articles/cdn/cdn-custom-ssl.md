---
title: 'Kurz: Konfigurace HTTPS pro vlastní doménu Azure CDN'
description: V tomto kurzu se dozvíte, jak povolit a zakázat HTTPS pro vlastní doménu koncového bodu Azure CDN.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: c4ad270b989e0e212c1d362ae4bfafc91fe07f3e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943579"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Kurz: Konfigurace HTTPS pro vlastní doménu Azure CDN

V tomto kurzu se dozvíte, jak povolit protokol HTTPS pro vlastní doménu přidruženou ke koncovému bodu služby Azure CDN. 

Protokol HTTPS ve vaší vlastní doméně (například https: \/ /www.contoso.com) zajišťuje zabezpečený doručování citlivých dat přes protokol TLS/SSL. Pokud je webový prohlížeč připojen pomocí protokolu HTTPS, prohlížeč ověří certifikát webu. Prohlížeč je ověří, že je vydaný oprávněnou certifikační autoritou. Tento proces zajišťuje zabezpečení a chrání vaše webové aplikace před útoky.

Azure CDN ve výchozím nastavení podporuje HTTPS pro název hostitele koncového bodu CDN. Pokud například vytvoříte koncový bod CDN (například https:\//contoso.azureedge.net), HTTPS se automaticky povolí.  

Mezi klíčové atributy vlastní funkce HTTPS patří mimo jiné:

- Žádné další poplatky: neúčtují se žádné náklady na pořízení a obnovení certifikátů ani žádné další poplatky za přenosy přes protokol HTTPS. Platíte pouze za GB odchozího provozu mimo síť CDN.

- Jednoduché povolení: Na webu [Azure Portal](https://portal.azure.com) je k dispozici zřízení jedním kliknutím. K povolení této funkce můžete použít také rozhraní REST API nebo jiné vývojářské nástroje.

- Je k dispozici kompletní správa certifikátů: 
    * Veškerá zakázka a Správa certifikátů je zpracovávána za vás. 
    * Certifikáty se automaticky zřídí a obnoví před vypršením platnosti.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Povolit protokol HTTPS pro vlastní doménu
> - Použít certifikát spravovaný CDN 
> - Použít vlastní certifikát
> - Ověření domény
> - Zakázat protokol HTTPS pro vlastní doménu

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Než budete moct dokončit kroky v tomto kurzu, vytvořte profil CDN a aspoň jeden koncový bod CDN. Další informace najdete v tématu [Rychlý start: Vytvoření profilu a koncového bodu Azure CDN](cdn-create-new-endpoint.md).

Přidružit Azure CDN vlastní doménu na koncový bod CDN. Další informace najdete v tématu [kurz: Přidání vlastní domény do koncového bodu Azure CDN](cdn-map-content-to-custom-domain.md).

> [!IMPORTANT]
> Certifikáty spravované CDN nejsou pro kořenové nebo vrcholové domény k dispozici. Pokud je vaše Azure CDN vlastní doména kořenovou nebo vrcholovou doménou, musíte použít funkci Přineste si vlastní certifikát. 
>

---

## <a name="tlsssl-certificates"></a>Certifikáty TLS/SSL

Pokud chcete povolit protokol HTTPS ve vlastní doméně Azure CDN, použijte certifikát TLS/SSL. Rozhodnete se použít certifikát, který je spravovaný Azure CDN nebo použijte certifikát.

# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[1. možnost (výchozí): Povolení protokolu HTTPS s certifikátem spravovaným CDN](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Azure CDN zpracovává úlohy správy certifikátů, jako je například nákup a obnovení. Jakmile funkci povolíte, proces se okamžitě zahájí. 

Pokud je vlastní doména už namapovaná na koncový bod CDN, nemusíte dělat nic dalšího. Azure CDN postup zpracuje a dokončí vaši žádost automaticky.

Pokud je vaše vlastní doména namapovaná jinde, použijte k ověření vlastnictví domény e-mail.

Pokud chcete povolit HTTPS pro vlastní doménu, postupujte následovně:

1. Pokud chcete najít certifikát, který spravuje vaše Azure CDN, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **profily CDN**. 

2. Vyberte svůj profil:
    * **Azure CDN Standard od Microsoftu**
    * **Azure CDN Standard z Akamai**
    * **Azure CDN Standard z Verizon**
    * **Azure CDN Premium z Verizon**

3. V seznamu koncových bodů CDN vyberte koncový bod obsahující vaši vlastní doménu.

    ![Seznam koncových bodů](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Zobrazí se stránka **Koncový bod**.

4. V seznamu vlastních domén vyberte vlastní doménu, pro kterou chcete povolit HTTPS.

    ![Snímek obrazovky zobrazující stránku vlastní doména s možností použít vlastní certifikát](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Zobrazí se stránka **Vlastní doména**.

5. V části Typ správy certifikátu vyberte **Spravováno sítí CDN**.

6. Výběrem možnosti **Zapnuto** povolte HTTPS.

    ![Stav HTTPS pro vlastní doménu](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. Pokračujte [v ověřování domény](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificate"></a>[2. možnost: Povolení protokolu HTTPS s vlastním certifikátem](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Tato možnost je k dispozici pouze u **Azure CDN od společnosti Microsoft** a **Azure CDN z profilů Verizon** . 
>
 
K povolení funkce HTTPS můžete použít vlastní certifikát. Tento proces se provádí prostřednictvím integrace s Azure Key Vault, která vám umožní bezpečně ukládat vaše certifikáty. Azure CDN používá tento zabezpečený mechanismus k získání certifikátu a vyžaduje několik dalších kroků. Při vytváření certifikátu TLS/SSL ho musíte vytvořit s povolenou certifikační autoritou (CA). Pokud použijete nepovolenou certifikační autoritu, vaše žádost se odmítne. Seznam povolených certifikační autority najdete v tématu s popisem [povolených certifikačních autorit pro umožnění vlastního HTTPS v Azure CDN](cdn-troubleshoot-allowed-ca.md). Pro **Azure CDN z Verizon** bude přijata jakákoli platná certifikační autorita. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Příprava účtu a certifikátu Azure Key Vault
 
1. Azure Key Vault: Musíte mít účet Azure Key Vault běžící v rámci stejného předplatného jako profil Azure CDN a koncové body CDN, pro které chcete vlastní HTTPS povolit. Pokud účet Azure Key Vault nemáte, vytvořte ho.
 
2. Azure Key Vault certifikáty: Pokud máte certifikát, nahrajte ho přímo na účet Azure Key Vault. Pokud certifikát nemáte, vytvořte nový certifikát přímo prostřednictvím Azure Key Vault.

### <a name="register-azure-cdn"></a>Registrace Azure CDN

Zaregistrujte Azure CDN jako aplikaci v Azure Active Directory pomocí PowerShellu.

1. V případě potřeby nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) na svém místním počítači.

2. V PowerShellu spusťte následující příkaz:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`
    > [!NOTE]
    > **205478c0-BD83-4e1b-a9d6-db63a3e1e1c8** je instanční objekt pro **Microsoft. AzureFrontDoor-CDN**.

    ```bash  
    New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"

    Secret                : 
    ServicePrincipalNames : {205478c0-bd83-4e1b-a9d6-db63a3e1e1c8, 
                                https://microsoft.onmicrosoft.com/033ce1c9-f832-4658-b024-ef1cbea108b8}
    ApplicationId         : 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8
    ObjectType            : ServicePrincipal
    DisplayName           : Microsoft.AzureFrontDoor-Cdn
    Id                    : c87be08f-686a-4d9f-8ef8-64707dbd413e
    Type                  :
    ```
### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Udělení přístupu k trezoru klíčů pro Azure CDN
 
Udělte Azure CDN oprávnění přistupovat k certifikátům (tajným kódům) ve vašem účtu Azure Key Vault.

1. V trezoru klíčů v části **Nastavení** vyberte **zásady přístupu**. V pravém podokně vyberte **+ Přidat zásady přístupu**:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-new-access-policy.png" alt-text="Vytvoření zásad přístupu trezoru klíčů pro CDN" border="true":::

2. Na stránce **Přidat zásady přístupu** vyberte možnost **Žádná vybraná** vedle **výběru objektu zabezpečení**. Na **Hlavní** stránce zadejte **205478c0-BD83-4e1b-a9d6-db63a3e1e1c8**. Vyberte **Microsoft. AzureFrontdoor-CDN**.  Zvolte **Vybrat**:

2. V **možnosti vybrat objekt zabezpečení** vyhledejte **205478C0-BD83-4e1b-a9d6-db63a3e1e1c8** a zvolte **Microsoft. AzureFrontDoor-CDN**. Zvolte **Vybrat**.

    :::image type="content" source="./media/cdn-custom-ssl/cdn-access-policy-settings.png" alt-text="Vyberte instanční objekt Azure CDN" border="true":::
    
3. Vyberte **oprávnění certifikátu**. Zaškrtněte políčka pro **získat** a **seznam** , aby se povolily oprávnění CDN k získání a výpisu certifikátů.

4. Vyberte **oprávnění tajného** kódu. Zaškrtněte políčka pro **získat** a **seznam** , aby oprávnění CDN umožňovala získat a zobrazit seznam tajných kódů:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-vault-permissions.png" alt-text="Vyberte oprávnění pro CDN do trezoru klíčů." border="true":::

5. Vyberte **Přidat**. 

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
  
6. Pokud používáte certifikát, ověření domény se nevyžaduje. Nadále [čekat na šíření](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>Ověření domény

Pokud máte vlastní doménu namapovanou na vlastní koncový bod pomocí záznamu CNAME nebo vlastníte certifikát, pokračujte na [vlastní doménu namapovanou na koncový bod CDN](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). 

Jinak, pokud položka záznamu CNAME pro váš koncový bod už neexistuje nebo obsahuje subdoménu cdnverify, pokračujte do [vlastní domény, která není namapovaná na váš koncový bod CDN](#custom-domain-isnt-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Vlastní doména se mapuje na koncový bod CDN pomocí záznamu CNAME

Po přidání vlastní domény do koncového bodu jste vytvořili záznam CNAME v registrátoru domény DNS namapovaném na název hostitele koncového bodu CDN. 

Pokud tento záznam CNAME stále existuje a neobsahuje subdoménu cdnverify, certifikační autorita DigiCert ho použije k automatickému ověření vlastnictví vlastní domény. 

Pokud používáte vlastní certifikát, ověření domény se nevyžaduje.

Záznam CNAME by měl být v následujícím formátu:

* *Název* je vlastní název domény.
* *Hodnota* je název hostitele koncového bodu CDN.

| Název            | Typ  | Hodnota                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

Další informace o záznamech CNAME najdete v tématu popisujícím [vytvoření záznamu DNS CNAME](./cdn-map-content-to-custom-domain.md).

Pokud je váš záznam CNAME ve správném formátu, DigiCert automaticky ověří vlastní název domény a vytvoří certifikát pro vaši doménu. DigiCert vám neodešle ověřovací e-mail a vy nebudete muset potvrzovat svou žádost. Certifikát je platný jeden rok a před vypršením jeho platnosti se obnoví jeho platnost. Nadále [čekat na šíření](#wait-for-propagation). 

Automatické ověřování obvykle trvá několik hodin. Pokud se vaše doména neověří za 24 hodin, otevřete lístek podpory.

>[!NOTE]
>Pokud máte záznam CAA (Certificate Authority Authorization) pro vašeho poskytovatele DNS, musí jako platnou certifikační autoritu zahrnovat DigiCert. Záznam CAA umožňuje vlastníkům domén určit u poskytovatelů DNS, které certifikační autority mají oprávnění k vystavování certifikátů pro jejich domény. Pokud certifikační autorita přijme objednávku na certifikát pro doménu se záznamem CAA a tato certifikační autorita není uvedená jako autorizovaný vystavitel certifikátů, nebude moci vystavit certifikát pro danou doménu nebo subdoménu. Informace o správě záznamů CAA najdete v tématu [Správa záznamů CAA](https://support.dnsimple.com/articles/manage-caa-record/). Nástroj pro práci se záznamy CAA najdete tady: [CAA Record Helper](https://sslmate.com/caa/).

### <a name="custom-domain-isnt-mapped-to-your-cdn-endpoint"></a>Vlastní doména není namapovaná na váš koncový bod CDN.

>[!NOTE]
>Pokud používáte **Azure CDN z Akamai**, měli byste nastavit tento záznam CNAME tak, aby umožňoval automatizované ověřování domény. "_acme – výzva. &lt; název hostitele vlastní domény &gt; – > CNAME – > &lt; vlastní doména hostname &gt; . ak-Acme-Challenge.azureedge.NET "

Pokud položka záznamu CNAME obsahuje subdoménu cdnverify, postupujte podle zbývajících pokynů v tomto kroku.

DigiCert odešle e-mail s ověřovacím e-mailem na následující e-mailové adresy. Ověřte, že můžete schvalovat přímo z jedné z následujících adres:

* **admin@your-domain-name.com** 
* **administrator@your-domain-name.com** 
* **webmaster@your-domain-name.com** 
* **hostmaster@your-domain-name.com**  
* **postmaster@your-domain-name.com**  

Měli byste obdržet e-mail za několik minut, než žádost schválíte. V případě, že používáte filtr spamu, přidejte verification@digicert.com do seznamu povolených. Pokud e-mail neobdržíte do 24 hodin, kontaktujte podporu Microsoftu.
    
![E-mail pro ověření domény](./media/cdn-custom-ssl/domain-validation-email.png)

Když vyberete odkaz pro schválení, budete přesměrováni na následující formulář pro online schválení: 
    
![Formulář pro ověření domény](./media/cdn-custom-ssl/domain-validation-form.png)

Postupujte podle pokynů ve formuláři. Máte na výběr dvě možnosti ověření:

- Můžete schválit všechny budoucí objednávky zadané přes stejný účet a pro stejnou kořenovou doménu, například contoso.com. Tento přístup se doporučuje, pokud plánujete přidat další vlastní domény pro stejnou kořenovou doménu.

- Můžete schválit pouze konkrétní název hostitele použitý v této žádosti. Pro pozdější požadavky se vyžaduje jiné schválení.

Po schválení DigiCert dokončí vytvoření certifikátu pro váš název vlastní domény. Certifikát je platný jeden rok a před vypršením jeho platnosti se obnoví jeho platnost.

## <a name="wait-for-propagation"></a>Čekání na rozšíření

Po ověření názvu domény může aktivace funkce HTTPS pro vlastní doménu trvat 6 až 8 hodin. Po dokončení procesu se změní stav vlastního protokolu HTTPS v Azure Portal na **povoleno**. Čtyři kroky operace v dialogovém okně vlastní doména jsou označeny jako úplné. Vaše vlastní doména je teď připravená k použití HTTPS.

![Dialogové okno pro povolení HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Průběh operace

Následující tabulka ukazuje průběh operace, která proběhne při povolení HTTPS. Po povolení HTTPS se v dialogovém okně vlastní domény zobrazí čtyři kroky operace. Jak je každý krok aktivní, v průběhu tohoto kroku se zobrazí další podrobnosti o podkrokech. Ne všechny tyto dílčí kroky se provedou. Po úspěšném dokončení kroku se vedle něj zobrazí zelená značka zaškrtnutí. 

| Krok operace | Podrobnosti o dílčím kroku operace | 
| --- | --- |
| 1. Odesílání žádosti | Odesílání žádosti |
| | Vaše žádost o HTTPS se právě odesílá. |
| | Vaše žádost o HTTPS se úspěšně odeslala. |
| 2. Ověření domény | Doména se ověří automaticky, pokud je CNAME namapované na koncový bod CDN. Jinak se na e-mail uvedený v záznamu o registraci vaší domény (žadatel o registraci v registru WHOIS) odešle žádost o ověření.|
| | Vaše vlastnictví domény se úspěšně ověřilo. |
| | Platnost požadavku na ověření vlastnictví domény vypršela (zákazník pravděpodobně neodpověděl ve lhůtě 6 dní). Protokol HTTPS nebude ve vaší doméně povolený. * |
| | Požadavek na ověření vlastnictví domény byl zamítnut zákazníkem. Protokol HTTPS nebude ve vaší doméně povolený. * |
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

V této části se dozvíte, jak zakázat HTTPS pro vaši vlastní doménu.

### <a name="disable-the-https-feature"></a>Zákaz funkce HTTPS 

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **profily CDN**. 

2. Vyberte **Azure CDN Standard od Microsoftu**, **Azure CDN Standard od Verizon** nebo **Azure CDN Premium ze profilu Verizon** .

3. V seznamu koncových bodů vyberte koncový bod obsahující vaši vlastní doménu.

4. Vyberte vlastní doménu, pro kterou chcete zakázat protokol HTTPS.

    ![Seznam vlastních domén](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. Zvolte **vypnuto** , pokud chcete protokol HTTPS zakázat, a pak vyberte **použít**.

    ![Dialogové okno HTTPS pro vlastní doménu](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Čekání na rozšíření

Po zákazu funkce HTTPS vlastní domény může trvat 6 až 8 hodin, než se změna projeví. Po dokončení procesu se změní stav vlastního protokolu HTTPS v Azure Portal na **zakázáno**. Tři kroky operace v dialogovém okně vlastní doména jsou označeny jako úplné. Vaše vlastní doména už nemůže používat HTTPS.

![Dialogové okno pro zákaz HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Průběh operace

Následující tabulka ukazuje průběh operace, která proběhne při zákazu HTTPS. Po zakázání protokolu HTTPS se v dialogovém okně vlastní doména zobrazí tři kroky operace. Když se krok aktivuje, zobrazí se v kroku podrobnosti. Po úspěšném dokončení kroku se vedle něj zobrazí zelená značka zaškrtnutí. 

| Průběh operace | Podrobnosti o operaci | 
| --- | --- |
| 1. Odesílání žádosti | Odesílání vaší žádosti |
| 2. Zrušení zřízení certifikátu | Odstraňování certifikátu |
| 3. Hotovo | Certifikát odstraněn |

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

1. *Kdo je poskytovatel certifikátu a jaký typ certifikátu se používá?*

    Vyhrazený certifikát, který poskytuje DigiCert, se používá pro vlastní doménu pro:
    
    * **Azure CDN z Verizon**
    * **Azure CDN od Microsoftu**

2. *Používáte protokol TLS/SSL založený na IP nebo SNI?*

    **Azure CDN od Verizonu** i **Azure CDN Standard od Microsoftu** používají SNI TLS/SSL.

3. *Co když neobdržím e-mail pro ověření domény od DigiCert?*

    Pokud nepoužíváte subdoménu *cdnverify* a položka CNAME je určena pro název hostitele koncového bodu, neobdržíte e-mailem pro ověření domény.
     
    Ověření proběhne automaticky. Jinak, pokud záznam CNAME nemáte a neobdrželi jste e-mail během 24 hodin, kontaktujte podporu Microsoftu.

4. *Je používání certifikátu SAN méně bezpečné než vyhrazený certifikát?*
    
    Certifikát SAN využívá stejné standardy šifrování a zabezpečení jako vyhrazený certifikát. Všechny vydané certifikáty TLS/SSL používají pro rozšířené zabezpečení serveru SHA-256.

5. *Potřebuji záznam CAA (Certificate Authority Authorization) pro svého poskytovatele DNS?*

    Autorizační záznam certifikační autority není aktuálně požadován. Pokud ho však máte, musí jako platnou certifikační autoritu zahrnovat DigiCert.

6. *20. června 2018 Azure CDN od Verizon zahájil používání vyhrazeného certifikátu s SNI TLS/SSL ve výchozím nastavení. Co se stane se stávajícími vlastními doménami pomocí certifikátu sítě SAN a protokolu TLS/SSL založeného na protokolu IP?*

    Vaše stávající domény se budou v nadcházejících měsících postupně migrovat na jediný certifikát, pokud Microsoft dojde analýzou k tomu, že do vaší aplikace přicházejí jenom žádosti klientů SNI. 
    
    Pokud se zjistí, že se neSNI klienti, vaše domény zůstanou v certifikátu sítě SAN s protokolem TLS/SSL založeným na protokolu IP. Požadavky na vaše služby nebo klienty, které nejsou SNI, jsou neovlivněné.

7. *Jak fungují obnovení certifikátů pomocí funkce Přineste si vlastní certifikát?*

    Pokud chcete zajistit, aby byl novější certifikát nasazený v infrastruktuře PoP, nahrajte nový certifikát do služby Azure webrecovery. V nastavení TLS na Azure CDN zvolte nejnovější verzi certifikátu a vyberte Uložit. Azure CDN pak rozšíří nový aktualizovaný certifikát. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> - Povolit protokol HTTPS pro vlastní doménu
> - Použít certifikát spravovaný CDN 
> - Použít vlastní certifikát
> - Ověřit doménu
> - Zakázat protokol HTTPS pro vlastní doménu

V dalším kurzu se dozvíte, jak na koncovém bodu CDN nakonfigurovat ukládání do mezipaměti.

> [!div class="nextstepaction"]
> [Kurz: Nastavení pravidel ukládání do mezipaměti Azure CDN](cdn-caching-rules-tutorial.md)