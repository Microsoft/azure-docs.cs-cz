---
title: Zakoupení vlastního názvu domény
description: Přečtěte si, jak koupit doménu služby App Service a použít ji jako vlastní doménu pro vaši aplikaci Azure App Service.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: afb40d0f3681bc02351e43166fccfaafe7741128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259276"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Nákup vlastního názvu domény pro Azure App Service

Domény služby App Service jsou domény nejvyšší úrovně, které se spravují přímo v Azure. Usnadňují správu vlastních domén pro [službu Azure App Service](overview.md). Tento kurz ukazuje, jak koupit doménu služby App Service a přiřadit názvy DNS službě Azure App Service.

V případě virtuálního počítače Azure nebo úložiště Azure najdete v tématu [Přiřazení domény služby App Service k virtuálnímu počítači Azure nebo Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). V případě cloudových služeb [najdete v tématu Konfigurace vlastního názvu domény pro cloudovou službu Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* [Vytvořit plán služby App Service](/azure/app-service/) nebo použít aplikaci, kterou jste vytvořili pro účely jiného kurzu.
* [Odeberte limit útraty u předplatného](../cost-management-billing/manage/spending-limit.md#remove). Domény služby App Service nelze zakoupit s bezplatnými kredity předplatného.

## <a name="prepare-the-app"></a>Příprava aplikace

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Aby bylo možné používat vlastní domény ve službě Azure App Service, musí být [plán služby App Service](https://azure.microsoft.com/pricing/details/app-service/) vaší aplikace placenou úrovní (**sdílená**, **základní**, **standardní**nebo **prémiová).** V tomto kroku se ujistěte, že aplikace je v podporované cenové úrovni.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Přechod do aplikace na webu Azure Portal

V levé nabídce vyberte **App Services** a pak vyberte název aplikace.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/select-app.png)

Zobrazí se stránka pro správu aplikace App Service.  

### <a name="check-the-pricing-tier"></a>Kontrola cenové úrovně

V levém navigačním panelu na stránce aplikace se posuňte do části **Nastavení** a vyberte **Vertikálně navýšit kapacitu (plán služby App Service)**.

![Nabídka Vertikálně navýšit kapacitu](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Aktuální úroveň aplikace je zvýrazněná modrým ohraničením. Zkontrolujte, že aplikace není na úrovni **F1**. Vlastní DNS se na úrovni **F1** nepodporuje. 

![Kontrola cenové úrovně](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Pokud plán služby App Service není ve vrstvě **F1,** zavřete stránku **Vertika nahoru** a přeskočte na [nákup domény](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

Vyberte některou z placených úrovní (**D1**, **B1**, **B2**, **B3** nebo kteroukoli úroveň v kategorii **Produkční**). Další možnosti se zobrazí po kliknutí na odkaz **Zobrazit další možnosti**.

Klikněte na **Použít**.

![Kontrola cenové úrovně](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Až se zobrazí následující oznámení, operace škálování je dokončená.

![Potvrzení operace škálování](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Koupit doménu

### <a name="pricing-information"></a>Informace o cenách
Informace o cenách v doménách služby Azure App Service najdete na [stránce Ceny služby App Service](https://azure.microsoft.com/pricing/details/app-service/windows/) a přejděte dolů na Doménu služby App Service.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure
Otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se pomocí svého účtu Azure.

### <a name="launch-buy-domains"></a>Spustit koupit domény
Na kartě **Služby aplikací** klikněte na název aplikace, vyberte **Nastavení**a pak vyberte **Vlastní domény.**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Na stránce **Vlastní domény** klikněte na **Koupit doménu**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Pokud se vám část **Domény služby App Service** nezobrazuje, musíte odebrat limit útraty na vašem účtu Azure (viz [Požadavky).](#prerequisites)
>
>

### <a name="configure-the-domain-purchase"></a>Konfigurace nákupu domény

Na stránce **Doména služby App Service** zadejte do pole **Hledat doménu** název domény, který chcete koupit a zadat `Enter`. Navrhované dostupné domény jsou zobrazeny těsně pod textovým polem. Vyberte jednu nebo více domén, které chcete koupit.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> [Doménami služby App](https://wikipedia.org/wiki/Top-level_domain) Service podporují následující domény nejvyšší úrovně: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_a _co.in_.
>
>

Klikněte na **kontaktní informace** a vyplňte formulář kontaktních informací domény. Po dokončení se kliknutím na **TLAČÍTKO OK** vraťte na stránku Domény služby App Service.

Je důležité, abyste vyplnili všechna požadovaná pole co nejpřesněji. Nesprávná data pro kontaktní informace může mít za následek selhání nákupu domén.

Dále vyberte požadované možnosti pro vaši doménu. Vysvětlení naleznete v následující tabulce:

| Nastavení | Navrhovaná hodnota | Popis |
|-|-|-|
|Privacy protection | Povolení | Přihlaste se k "ochraně soukromí", která je zahrnuta v kupní ceně _zdarma_. Některé domény nejvyšší úrovně jsou spravovány registrátory, kteří nepodporují ochranu osobních údajů, a jsou uvedeny na stránce **Ochrany osobních údajů.** |
| Přiřazení výchozích názvů hostitelů | **www** a**\@** | V případě potřeby vyberte požadované vazby název hostitele. Po dokončení operace nákupu domény je vaše aplikace přístupná na vybraných jménech hostitelů. Pokud je aplikace za [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), nevidíte možnost přiřadit kořenovou doménu (@), protože Traffic Manager nepodporuje záznamy A. Po dokončení nákupu domény můžete v přiřazení chod názvu hostitele provést změny. |

### <a name="accept-terms-and-purchase"></a>Přijmout podmínky a zakoupit

Kliknutím na **Právní podmínky** zkontrolujte podmínky a poplatky a potom klikněte na **Koupit**.

> [!NOTE]
> Domény služby App Service používají K hostování domén GoDaddy a Azure DNS. Kromě registračního poplatku za doménu se účtují poplatky za využití služby Azure DNS. Další informace naleznete v [tématu Azure DNS Pricing](https://azure.microsoft.com/pricing/details/dns/).
>
>

Na stránce **Doména služby App Service** klepněte na tlačítko **OK**. Během operace se zobrazí následující oznámení:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Otestujte názvy hostitelů

Pokud jste aplikaci přiřadili výchozí názvy hostitelů, zobrazí se také oznámení o úspěchu pro každý vybraný název hostitele.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Vybrané názvy **hostitelů** se zobrazí také na stránce Vlastní domény v části **Vlastní názvy hostitelů.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> Popisek **Není zabezpečený** pro vaši vlastní doménu znamená, že ještě není vázán na certifikát SSL a jakýkoli požadavek HTTPS z prohlížeče do vaší vlastní domény obdrží chybu nebo upozornění v závislosti na prohlížeči. Pokud chcete nakonfigurovat vazbu SSL, přečtěte si informace [o zabezpečení vlastního názvu DNS s vazbou SSL ve službě Azure App Service](configure-ssl-bindings.md).
>

Chcete-li otestovat názvy hostitelů, přejděte na uvedené názvy hostitelů v prohlížeči. V příkladu na předchozím snímku obrazovky zkuste přecházet na _kontoso.net_ a _www\.kontoso.net_.

## <a name="assign-hostnames-to-app"></a>Přiřazení názvů hostitelů k aplikaci

Pokud se rozhodnete aplikaci během procesu nákupu nepřiřazovat jeden nebo více výchozích názvů hostitelů nebo pokud potřebujete přiřadit název hostitele, který není v seznamu uveden, můžete kdykoli přiřadit název hostitele.

Názvy hostitelů v doméně služby App Service můžete také přiřadit jakékoli jiné aplikaci. Postup závisí na tom, jestli doména služby App Service a aplikace patří do stejného předplatného.

- Jiné předplatné: Mapování vlastních záznamů DNS z domény služby App Service do aplikace jako externě zakoupené domény. Informace o přidávání vlastních názvů DNS do domény služby App Service naleznete v [tématu Správa vlastních záznamů DNS](#custom). Pokud chcete namapovat externí zakoupenou doménu na aplikaci, přečtěte si [témat u tématu Mapování existujícího vlastního názvu DNS na Službu Azure App Service](app-service-web-tutorial-custom-domain.md). 
- Stejné předplatné: Použijte následující kroky.

### <a name="launch-add-hostname"></a>Spustit add hostname
Na stránce **Služby aplikací** vyberte název aplikace, které chcete přiřadit názvy **hostitelů,** vyberte **Nastavení**a pak vyberte Vlastní domény .

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Ujistěte se, že vaše zakoupená doména je uvedená v části **Domény služby App Service,** ale nevybírejte ji. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Všechny domény služby App Service ve stejném předplatném se zobrazují na stránce **Vlastní domény** aplikace. Pokud je vaše doména v předplatném aplikace, ale nevidíte ji na stránce **Vlastní domény** aplikace, zkuste znovu otevřít stránku **Vlastní domény** nebo aktualizujte webovou stránku. Zkontrolujte také upozornění zvonek v horní části portálu Azure pro průběh nebo vytváření selhání.
>
>

Vyberte **Přidat název hostitele**.

### <a name="configure-hostname"></a>Konfigurovat název hostitele
V dialogovém okně **Přidat název hostitele** zadejte plně kvalifikovaný název domény služby App Service domain nebo jakékoli subdomény. Například:

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

Po dokončení vyberte **Ověřit**. Typ záznamu názvu hostitele je automaticky vybrán za vás.

Vyberte **Přidat název hostitele**.

Po dokončení operace se zobrazí oznámení o úspěchu pro přiřazený název hostitele.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Zavřít add název hostitele
Na stránce **Přidat název hostitele** přiřaďte aplikaci podle potřeby jakýkoli jiný název hostitele. Po dokončení zavřete stránku **Přidat název hostitele.**

Nově přiřazené názvy hostitelů byste teď měli vidět na stránce **Vlastní domény** aplikace.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Otestujte názvy hostitelů

Přejděte na uvedené názvy hostitelů v prohlížeči. V příkladu na předchozím snímku obrazovky zkuste navigovat na _abc.kontoso.net_.

## <a name="renew-the-domain"></a>Obnovení domény

Doména služby App Service, kterou jste si koupili, je platná po dobu jednoho roku od okamžiku nákupu. Ve výchozím nastavení je doména nakonfigurována tak, aby se automaticky obnovovala účtováním způsobu platby pro příští rok. Název domény můžete obnovit ručně.

Pokud chcete automatické obnovení vypnout nebo chcete doménu obnovit ručně, postupujte podle těchto kroků.

Na kartě **Služby aplikací** klikněte na název aplikace, vyberte **Nastavení**a potom vyberte **Vlastní domény**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

V části **Domény služby App Service** vyberte doménu, kterou chcete konfigurovat.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

V levém navigačním panelu domény vyberte **možnost Obnovení domény**. Chcete-li zastavit automatické obnovování domény, vyberte **možnost Vypnout**a potom **uložit**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Chcete-li doménu obnovit ručně, vyberte **možnost Obnovit doménu**. Toto tlačítko však není aktivní až [90 dní před vypršením platnosti domény](#when-domain-expires).

Pokud je obnovení domény úspěšné, obdržíte e-mailové oznámení do 24 hodin.

## <a name="when-domain-expires"></a>Když vyprší platnost domény

Azure se zabývá doménami s vypršením platnosti nebo vypršením platnosti služby App Service následujícím způsobem:

* Pokud je automatické obnovení zakázáno: 90 dní před vypršením platnosti domény vám bude odeslán e-mail s oznámením o obnovení a na portálu se aktivuje tlačítko **Obnovit doménu.**
* Pokud je povoleno automatické obnovení: Den po datu vypršení platnosti domény se vám Azure pokusí účtovat fakturu za obnovení názvu domény.
* Pokud dojde k chybě během automatického obnovení (například platnost karty v souboru vypršela) nebo pokud je automatické obnovení zakázáno a povolíte vypršení platnosti domény, Azure vás upozorní na vypršení platnosti domény a zaparkuje název vaší domény. Doménu můžete [obnovit ručně.](#renew-the-domain)
* Ve 4. a 12. den po vypršení platnosti vám Azure pošle další e-maily s oznámením. Doménu můžete [obnovit ručně.](#renew-the-domain)
* 19. den po vypršení platnosti zůstane vaše doména pozastavena, ale bude podléhat poplatku za uplatnění. Můžete zavolat na zákaznickou podporu a obnovit název domény, a to v závislosti na příslušných poplatcích za obnovení a uplatnění.
* 25. den po vypršení platnosti Azure umístí vaši doménu do aukce pomocí služby aukce doménových jmen. Můžete zavolat na zákaznickou podporu a obnovit název domény, a to v závislosti na příslušných poplatcích za obnovení a uplatnění.
* Třicátý den po vypršení platnosti již nebudete moci uplatnit svou doménu.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Správa vlastních záznamů DNS

V Azure se záznamy DNS pro doménu služby App Service spravují pomocí [Azure DNS](https://azure.microsoft.com/services/dns/). Záznamy DNS můžete přidávat, odebírat a aktualizovat stejně jako u externě zakoupené domény.

### <a name="open-app-service-domain"></a>Otevřít doménu služby App Service

Na portálu Azure v levé nabídce vyberte Všechny**domény aplikačních služeb** **služeb** > .

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Vyberte doménu, kterou chcete spravovat. 

### <a name="access-dns-zone"></a>Přístup k zóně DNS

V levé nabídce domény vyberte **zónu DNS**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Tato akce otevře stránku [zóny DNS](../dns/dns-zones-records.md) domény služby App Service v Azure DNS. Informace o tom, jak upravovat záznamy DNS, najdete [v tématu Správa zón DNS na webu Azure Portal](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Zrušit nákup (odstranit doménu)

Po zakoupení domény služby App Service máte pět dní na zrušení nákupu za plnou náhradu. Po pěti dnech můžete doménu služby App Service odstranit, ale nemůžete obdržet refundaci.

### <a name="open-app-service-domain"></a>Otevřít doménu služby App Service

Na portálu Azure v levé nabídce vyberte Všechny**domény aplikačních služeb** **služeb** > .

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Vyberte doménu, kterou chcete zrušit nebo odstranit. 

### <a name="delete-hostname-bindings"></a>Odstranit vazby názvů hostname

V levé nabídce domény vyberte **Vazby Hostname**. Tady jsou uvedeny vazby názvu hostitele ze všech služeb Azure.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Doménu služby App Service nelze odstranit, dokud nebudou odstraněny všechny vazby názvu hostitele.

Odstranit každou vazbu názvu hostitele výběrem **...**  >  **Odstranit**. Po odstranění všech vazeb vyberte **uložit**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Zrušit nebo odstranit

V levé nabídce domény vyberte **Přehled**. 

Pokud neuplynula doba zrušení na zakoupené doméně, vyberte **zrušit nákup**. V opačném případě se místo toho zobrazí tlačítko **Odstranit.** Chcete-li odstranit doménu bez vrácení peněz, vyberte **odstranit**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Chcete-li operaci potvrdit, vyberte **možnost Ano**.

Po dokončení operace je doména uvolněna z vašeho předplatného a je k dispozici pro každého, kdo si může znovu zakoupit. 

## <a name="direct-default-url-to-a-custom-directory"></a>Směrování výchozí adresy URL do vlastního adresáře

Ve výchozím nastavení služba App Service směruje webové požadavky do kořenového adresáře kódu vaší aplikace. Chcete-li je nasměrovat do `public`podadresáře, například , přečtěte si informace [o tom, že je adresa URL je nastavena na vlastní adresář](app-service-web-tutorial-custom-domain.md#virtualdir).
