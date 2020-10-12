---
title: Koupit vlastní název domény
description: Naučte se koupit doménu App Service a použít ji jako vlastní doménu pro Azure App Service vaší aplikace.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: 0111da00962f267387e66978952e8a7c9f5d7308
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90970048"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Nákup vlastního názvu domény pro Azure App Service

App Service domény jsou domény nejvyšší úrovně, které se spravují přímo v Azure. Usnadňují správu vlastních domén pro [Azure App Service](overview.md). V tomto kurzu se dozvíte, jak koupit doménu App Service a přiřadit k Azure App Service názvy DNS.

Informace o virtuálním počítači nebo Azure Storage Azure najdete v tématu [přiřazení App Service domény k virtuálnímu počítači Azure nebo Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). Cloud Services najdete v tématu [Konfigurace vlastního názvu domény pro cloudovou službu Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* [Vytvořit plán služby App Service](./index.yml) nebo použít aplikaci, kterou jste vytvořili pro účely jiného kurzu.
* [Odeberte limit útraty ve vašem předplatném](../cost-management-billing/manage/spending-limit.md#remove). Nemůžete nakupovat App Service domén s kredity bezplatného předplatného.

## <a name="prepare-the-app"></a>Příprava aplikace

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Pokud chcete používat vlastní domény v Azure App Service, musí být [plán App Service](https://azure.microsoft.com/pricing/details/app-service/) vaší aplikace placenou úrovní (**Shared**, **Basic**, **Standard**nebo **Premium**). V tomto kroku se ujistěte, že je aplikace v podporované cenové úrovni.

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

:::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="Snímek obrazovky vlevo v navigační nabídce stránky aplikace s vybraným stupněm navýšení (App Servicem plánem)":::

Pokud App Service plán není ve vrstvě **F1** , zavřete stránku **horizontálního navýšení kapacity** a přejděte k [nákupu domény](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

Vyberte některou z placených úrovní (**D1**, **B1**, **B2**, **B3** nebo kteroukoli úroveň v kategorii **Produkční**). Další možnosti se zobrazí po kliknutí na odkaz **Zobrazit další možnosti**.

Klikněte na **Použít**.

:::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="Snímek obrazovky vlevo v navigační nabídce stránky aplikace s vybraným stupněm navýšení (App Servicem plánem)":::

Až se zobrazí následující oznámení, operace škálování je dokončená.

![Potvrzení operace škálování](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Koupit doménu

### <a name="pricing-information"></a>Informace o cenách
Informace o cenách Azure App Servicech doménách najdete na [stránce s cenami App Service](https://azure.microsoft.com/pricing/details/app-service/windows/) a posuňte se dolů na App Service doménu.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure
Otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se pomocí svého účtu Azure.

### <a name="launch-buy-domains"></a>Spustit nákupní domény
Na kartě **App Services** klikněte na název vaší aplikace, vyberte **Nastavení**a pak vyberte **vlastní domény** .
   
![Snímek obrazovky zobrazující zvýrazněné vlastní domény](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Na stránce **vlastní domény** klikněte na **koupit doménu**.

![Snímek obrazovky zobrazující zvýrazněnou nákupní doménu](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Pokud nemůžete zobrazit část **App Service domény** , musíte odebrat limit útraty ve vašem účtu Azure (viz [požadavky](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Konfigurace nákupu domény

Na stránce **App Service doména** zadejte do pole **Hledat doménu** název domény, kterou chcete koupit, a zadejte `Enter` . Navrhované dostupné domény se zobrazují hned pod textovým polem. Vyberte jednu nebo více domén, které chcete koupit.

![Snímek obrazovky, který zobrazuje vyhledávací pole Hledat doménu](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Následující [domény nejvyšší úrovně](https://wikipedia.org/wiki/Top-level_domain) jsou podporovány App Service doménami: _com_, _net_, _co.UK_, _org_, _nl_, _in_,, _biz_ _org.UK_a _co.in_.
>
>

Klikněte na **kontaktní informace** a vyplňte formulář kontaktní informace v doméně. Po dokončení klikněte na tlačítko **OK** , čímž se vrátíte na stránku App Service doména.

Je důležité vyplnit všechna povinná pole co nejvíc přesností. Nesprávná data pro kontaktní údaje můžou mít za následek neúspěšné zakoupení domén.

V dalším kroku vyberte požadované možnosti pro vaši doménu. Vysvětlení najdete v následující tabulce:

| Nastavení | Navrhovaná hodnota | Description |
|-|-|-|
|Privacy protection | Povolit | Přihlaste se k ochraně osobních údajů, která je zahrnutá v ceně nákupu _zdarma_. Některé domény nejvyšší úrovně jsou spravovány registrátory, které nepodporují ochranu osobních údajů, a jsou uvedeny na stránce **Ochrana osobních údajů** . |
| Přiřadit výchozí názvy hostitelů | **www** a **\@** | V případě potřeby vyberte požadované vazby hostitele. Po dokončení operace nákupu domény může být aplikace dostupná na vybraných hostitelských hostitelích. Pokud je aplikace za [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), nevidíte možnost přiřadit kořenovou doménu (@), protože Traffic Manager nepodporuje záznamy. Po dokončení nákupu domény můžete provádět změny v přiřazeních hostitelů. |

### <a name="accept-terms-and-purchase"></a>Přijmout podmínky a koupit

Kliknutím na **právní doložku** si Projděte podmínku a poplatky a pak klikněte na **koupit**.

> [!NOTE]
> App Service domény používají GoDaddy k registraci domény a Azure DNS k hostování domén. Kromě registračního poplatku domény se účtují poplatky za využití Azure DNS použít. Informace najdete v tématu [Azure DNS ceny](https://azure.microsoft.com/pricing/details/dns/).
>
>

Zpátky na stránce **App Service doména** klikněte na **OK**. V průběhu operace se zobrazí následující oznámení:

![Snímek obrazovky zobrazující zprávu o probíhajícím ověřování](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![Snímek obrazovky zobrazující oznámení o úspěšnosti nákupu](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Testování názvů hostitelů

Pokud jste aplikaci přiřadili výchozí názvy hostitelů, zobrazí se vám také oznámení o úspěšnosti pro každý vybraný název hostitele.

![Snímek obrazovky, který zobrazuje oznámení o úspěšnosti pro každý vybraný název hostitele.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Vybrané názvy hostitelů se zobrazí také na stránce **vlastní domény** v části **vlastní názvy hostitelů** .

![Snímek obrazovky, který zobrazuje vybrané názvy hostitelů v části vlastní názvy hostitelů na stránce vlastní domény.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> **Nezabezpečený** popisek pro vaši vlastní doménu znamená, že ještě není svázán s certifikátem TLS/SSL, a v závislosti na prohlížeči obdrží v závislosti na prohlížeči chybu nebo upozornění v jakémkoli požadavku HTTPS z prohlížeče do vaší vlastní domény. Pokud chcete nakonfigurovat vazbu TLS, přečtěte si téma [zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service](configure-ssl-bindings.md).
>

Chcete-li otestovat názvy hostitelů, přejděte k uvedeným názvům hostitelů v prohlížeči. V příkladu na předchozím snímku obrazovky zkuste přejít na _kontoso.NET_ a na _webové \. kontoso.NET_.

## <a name="assign-hostnames-to-app"></a>Přiřazení názvů hostitelů k aplikaci

Pokud se rozhodnete nepřiřadit k aplikaci jeden nebo více výchozích názvů hostitelů během procesu nákupu, nebo pokud potřebujete přiřadit název hostitele, který není uveden, můžete název hostitele přiřadit kdykoli.

Můžete také přiřadit názvy hostitelů v doméně App Service do jakékoli jiné aplikace. Postup závisí na tom, jestli App Service doména a aplikace patří do stejného předplatného.

- Jiné předplatné: namapujte vlastní záznamy DNS z App Service domény do aplikace, jako je externě zakoupená doména. Informace o přidání vlastních názvů DNS do domény App Service najdete v tématu [Správa vlastních záznamů DNS](#custom). Pokud chcete namapovat externí zakoupenou doménu na aplikaci, přečtěte si téma [Mapování existujícího vlastního názvu DNS na Azure App Service](app-service-web-tutorial-custom-domain.md). 
- Stejné předplatné: použijte následující postup.

### <a name="launch-add-hostname"></a>Spustit přidání názvu hostitele
Na stránce **App Services** vyberte název aplikace, ke které chcete přiřadit názvy hostitelů, vyberte **Nastavení**a pak vyberte **vlastní domény**.

![Snímek obrazovky zobrazující zvýrazněné vlastní domény](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Ujistěte se, že je Vaše zakoupená doména uvedená v části **App Service domény** , ale nevybírejte ji. 

![Snímek obrazovky, který zobrazuje vaši zakoupenou doménu v části App Service domény.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Na stránce **vlastní domény** aplikace se zobrazí všechny App Service domény ve stejném předplatném. Pokud je vaše doména v předplatném aplikace, ale nemůžete ji zobrazit na stránce **vlastní domény** aplikace, zkuste znovu otevřít stránku **vlastní domény** nebo aktualizovat webovou stránku. Také si přečtěte oznámení v oznámení v horní části Azure Portal pro průběh nebo selhání vytvoření.
>
>

Vyberte **Přidat název hostitele**.

### <a name="configure-hostname"></a>Konfigurovat název hostitele
V dialogovém okně **Přidat název hostitele** zadejte plně kvalifikovaný název domény vaší App Service domény nebo jakékoli subdomény. Například:

- kontoso.net
- Webová \. kontoso.NET
- abc.kontoso.net

Po dokončení vyberte **ověřit**. Typ záznamu názvu hostitele je automaticky vybrán.

Vyberte **Přidat název hostitele**.

Po dokončení operace se pro přiřazený název hostitele zobrazí oznámení o úspěchu.  

![Snímek obrazovky, který zobrazuje oznámení o úspěšnosti pro přiřazený název hostitele.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Zavřít přidání názvu hostitele
Na stránce **Přidat název hostitele** přiřaďte aplikaci libovolný další název hostitele podle potřeby. Po dokončení zavřete stránku **Přidat název hostitele** .

Teď byste měli vidět nově přiřazené názvy hostitelů na stránce **vlastní domény** vaší aplikace.

![Snímek obrazovky zobrazující nově přiřazené názvy hostitelů na stránce vlastní domény vaší aplikace](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Testování názvů hostitelů

V prohlížeči přejděte k uvedeným názvům hostitelů. V příkladu na předchozím snímku obrazovky zkuste přejít na _ABC.kontoso.NET_.

## <a name="renew-the-domain"></a>Obnovení domény

App Service doména, kterou jste zakoupili, je platná po dobu jednoho roku od zakoupení. Ve výchozím nastavení je doména nakonfigurovaná tak, aby se automaticky obnovila tak, že si zaznamenáme způsob platby na další rok. Název domény můžete ručně obnovit.

Pokud chcete vypnout automatické obnovování nebo pokud chcete, aby se doména obnovila ručně, postupujte podle kroků uvedených tady.

Na kartě **App Services** klikněte na název vaší aplikace, vyberte **Nastavení**a pak vyberte **vlastní domény**.

![Snímek obrazovky zobrazující zvýrazněné vlastní domény](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

V části **App Service domény** vyberte doménu, kterou chcete nakonfigurovat.

![Snímek obrazovky, který zobrazuje vaši zakoupenou doménu v části App Service domény.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

V levém navigačním panelu domény vyberte **obnovení domény**. Pokud chcete zastavit obnovení domény automaticky, vyberte **vypnout**a pak **Uložit**.

![Snímek obrazovky, který ukazuje možnost automaticky obnovit vaši doménu.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Chcete-li ručně obnovit doménu, vyberte možnost **obnovit doménu**. Toto tlačítko je ale aktivní až [90 dní před vypršením platnosti domény](#when-domain-expires).

Pokud je obnovení domény úspěšné, obdržíte e-mailové oznámení během 24 hodin.

## <a name="when-domain-expires"></a>Po vypršení platnosti domény

Azure se týká vypršení platnosti nebo App Service domén s vypršenou platností následujícím způsobem:

* Pokud je automatické obnovení zakázané: 90 dní před vypršením platnosti domény, pošle se vám e-mail s oznámením o prodloužení a na portálu se aktivuje tlačítko **prodloužit doménu** .
* Pokud je povoleno automatické obnovení: dne po datu vypršení platnosti domény, Azure se pokusí vyfakturovat za obnovení názvu domény.
* Pokud při automatickém obnovování dojde k chybě (například vypršela platnost karty na soubor) nebo pokud je automatické obnovení zakázané a pokud povolíte vypršení platnosti domény, Azure vás upozorní na vypršení platnosti domény a parky, které mají název vaší domény. Doménu můžete [obnovit ručně](#renew-the-domain) .
* V 4.12. dnech dne po vypršení platnosti vám Azure pošle další e-maily s oznámením. Doménu můžete [obnovit ručně](#renew-the-domain) .
* 19 den po vypršení platnosti zůstane doména pozastavená, ale bude se řídit poplatkem za uplatnění. Můžete zavolat zákaznickou podporu a prodloužit název domény, a to v souladu s platnými poplatky za obnovení a uplatnění.
* Od 25 dnů po vypršení platnosti Azure zaznamená vaši doménu do aukce za aukci pomocí služby obor názvů domény. Můžete zavolat zákaznickou podporu a prodloužit název domény, a to v souladu s platnými poplatky za obnovení a uplatnění.
* Po uplynutí 30 dnů po vypršení platnosti už nebudete moci uplatnit svoji doménu.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Správa vlastních záznamů DNS

V Azure se záznamy DNS pro App Service doméně spravují pomocí [Azure DNS](https://azure.microsoft.com/services/dns/). Můžete přidávat, odebírat a aktualizovat záznamy DNS stejně jako u externě koupené domény.

### <a name="open-app-service-domain"></a>Otevřít App Service doménu

V Azure Portal v nabídce vlevo vyberte **všechny služby**  >  **App Service domény**.

![Snímek obrazovky, který ukazuje, kde získat přístup k App Service doménám](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Vyberte doménu, kterou chcete spravovat. 

### <a name="access-dns-zone"></a>Přístup k zóně DNS

V nabídce vlevo v doméně vyberte **zónu DNS**.

![Snímek obrazovky, který ukazuje, kde vybrat zónu DNS.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Tato akce otevře stránku [zóny DNS](../dns/dns-zones-records.md) vaší App Service domény v Azure DNS. Informace o tom, jak upravit záznamy DNS, najdete v tématu [správa zóny DNS v Azure Portal](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Zrušit nákup (odstranit doménu)

Po zakoupení App Service domény budete mít za pět dní, abyste zrušili nákup pro plnou refundaci. Po pěti dnech můžete doménu App Service odstranit, ale nemůžete ji vrátit.

### <a name="open-app-service-domain"></a>Otevřít App Service doménu

V Azure Portal v nabídce vlevo vyberte **všechny služby**  >  **App Service domény**.

![Snímek obrazovky, který ukazuje, kde získat přístup k App Service doménám](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Vyberte doménu, kterou chcete zrušit nebo odstranit. 

### <a name="delete-hostname-bindings"></a>Odstranit vazby hostname

V nabídce vlevo v doméně vyberte možnost **vazby názvů hostitelů**. Tady jsou uvedené vazby názvů hostitelů ze všech služeb Azure.

![Snímek obrazovky zobrazující stránku vazeb názvu hostitele](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Doménu App Service nelze odstranit, dokud nebudou odstraněny všechny vazby názvů hostitelů.

Odstraňte všechny vazby hostitele výběrem **...**  >  **Odstraňte**. Po odstranění všech vazeb vyberte **Save (Uložit**).

![Snímek obrazovky, který ukazuje, kde odstranit vazby hostitele.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Zrušit nebo odstranit

V nabídce vlevo v doméně vyberte **Přehled**. 

Pokud neuplynula doba zrušení u koupené domény, vyberte **zrušit nákup**. V opačném případě se místo toho zobrazí tlačítko pro **odstranění** . Chcete-li odstranit doménu bez náhrady, vyberte možnost **Odstranit**.

![Snímek obrazovky, který ukazuje, kde odstranit nebo zrušit zakoupenou doménu.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Pro potvrzení operace vyberte **Ano**.

Po dokončení operace se doména uvolní z vašeho předplatného a bude k dispozici pro kohokoli zakoupení. 

## <a name="direct-default-url-to-a-custom-directory"></a>Směrování výchozí adresy URL do vlastního adresáře

Ve výchozím nastavení služba App Service směruje webové požadavky do kořenového adresáře kódu vaší aplikace. Pokud je chcete směrovat do podadresáře, například `public` , přečtěte si téma [přesměrování výchozí adresy URL na vlastní adresář](app-service-web-tutorial-custom-domain.md#virtualdir).