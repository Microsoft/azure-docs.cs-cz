---
title: Nákup vlastního názvu domény v Azure – službu App Service
description: Informace o možnostech nákupu názvu vlastní domény s webovou aplikací ve službě Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: d47c560515a13efa8346974e828f14f9a15f0e4a
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53730922"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Nákup vlastního názvu domény pro službu Azure App Service

Domény služby App Service (preview) jsou domény nejvyšší úrovně, které se spravují přímo v Azure. Jejich usnadňují spravování vlastních domén pro [služby Azure App Service](overview.md). V tomto kurzu se dozvíte, jak koupit doménu služby App Service a přiřadit názvy DNS do služby Azure App Service.

Pro virtuální počítač Azure nebo Azure Storage, najdete v článku [doména přiřadit služby App Service na virtuálním počítači Azure nebo Azure Storage](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/). Cloud Services, najdete v části [konfigurace vlastního názvu domény pro cloudovou službu Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* [Vytvořit plán služby App Service](/azure/app-service/) nebo použít aplikaci, kterou jste vytvořili pro účely jiného kurzu.
* [Odebrat limit útraty pro vaše předplatné](../billing/billing-spending-limit.md#remove). Nelze nakupovat domény služby App Service s kredity bezplatné předplatné.

## <a name="prepare-the-app"></a>Příprava aplikace

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Použití vlastních domén v Azure App Service, aplikaci prvku [plán služby App Service](https://azure.microsoft.com/pricing/details/app-service/) musí být placenou úroveň (**Shared**, **základní**, **standardní**, nebo  **Premium**). V tomto kroku Ujistěte se, že aplikace je na podporované cenové úrovni.

### <a name="sign-in-to-azure"></a>Přihlásit se k Azure

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

Pokud není v plánu služby App Service **F1** vrstvy, zavřete **vertikálně navýšit kapacitu** stránce a přeskočte k [koupit doménu](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

Vyberte některou z placených úrovní (**D1**, **B1**, **B2**, **B3** nebo kteroukoli úroveň v kategorii **Produkční**). Další možnosti se zobrazí po kliknutí na odkaz **Zobrazit další možnosti**.

Klikněte na tlačítko **Použít**.

![Kontrola cenové úrovně](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Až se zobrazí následující oznámení, operace škálování je dokončená.

![Potvrzení operace škálování](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Koupit domény

### <a name="pricing-information"></a>Informace o cenách
Informace o cenách na domény služby Azure App Service, najdete [stránku App Service – ceny](https://azure.microsoft.com/pricing/details/app-service/windows/) a posuňte se dolů doména App Service.

### <a name="sign-in-to-azure"></a>Přihlásit se k Azure
Otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se pomocí svého účtu Azure.

### <a name="launch-buy-domains"></a>Spusťte nákup domén
V **App Services** kartu, klikněte na název vaší aplikace, vyberte **nastavení**a pak vyberte **vlastní domény**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

V **vlastní domény** klikněte na **koupit domény**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Není-li v **domén služby App Service** části, je třeba odebrat limit útraty u vašeho účtu Azure (viz [požadavky](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Konfigurace zakoupení domény

V **doména App Service** stránku, **hledat domény** zadejte název domény, které chcete koupit a zadejte `Enter`. Navržené domény k dispozici jsou uvedeny přímo pod textové pole. Vyberte jednu nebo více domén, které chcete koupit.
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Následující [domény nejvyšší úrovně](https://wikipedia.org/wiki/Top-level_domain) domény služby App Service podporuje: _com_, _net_, _co.uk_, _organizace_, _nl_, _v_, _biz_, _org.uk_, a _co.in_.
>
>

Klikněte na tlačítko **kontaktní údaje** a vyplňte formulář kontaktní údaje domény. Až budete hotovi, klikněte na tlačítko **OK** se vrátíte na stránku doména App Service.
   
Je důležité, vyplňte všechna povinná pole s co nejvíce přesnost nejvíce. Nesprávná data kontaktní informace může způsobit selhání při nákupu domény. 

Potom vyberte požadované možnosti pro vaši doménu. V následující tabulce najdete vysvětlení:

| Nastavení | Navrhovaná hodnota | Popis |
|-|-|-|
|Ochrana osobních údajů | Povolení | Vyjádřit výslovný souhlas pro "Ochrany osobních údajů", který je součástí ceny nákupu _zdarma_. Některé domény nejvyšší úrovně se spravují přes registrátorů, které nepodporují ochranu osobních údajů a jsou uvedeny na **ochranu osobních údajů** stránky. |
| Přiřadit výchozí hostitelské názvy | **WWW** a **@** | Vyberte požadovaný název hostitele vazeb v případě potřeby. Po dokončení operace nákupu domény vaší aplikace je přístupný na vybrané názvy hostitelů. Pokud je aplikace za [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), nevidíte možnost přiřadit kořenovou doménu (@), protože nemá podporu záznamy O Traffic Manageru. Můžete provedete změny přiřazení názvu hostitele po dokončení nákupu domény. |

### <a name="accept-terms-and-purchase"></a>Přijměte podmínky a nákup

Klikněte na tlačítko **právní podmínky** přečtěte si podmínky a náklady a pak klikněte na **koupit**.

> [!NOTE]
> Domény služby App Service použít Azure DNS k hostování domény. Kromě registrace poplatek domény se platí poplatky za používání pro Azure DNS. Informace najdete v tématu [ceny služby Azure DNS](https://azure.microsoft.com/pricing/details/dns/).
>
>

Zpátky **doména App Service** klikněte na **OK**. Když probíhá operace, se zobrazí následující oznámení:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Testování názvy hostitelů

Pokud výchozí názvy hostitelů přiřazenými do vaší aplikace, se také zobrazí oznámení úspěch pro každý vybraný název hostitele. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Zobrazí také názvy vybraných hostitelů v **vlastní domény** stránku, **vlastní názvy hostitelů** oddílu. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

Chcete-li otestovat názvy hostitelů, přejděte na uvedené názvy hostitelů v prohlížeči. V příkladu v předchozím snímku obrazovky, zkuste přejít na stránku _kontoso.net_ a _www.kontoso.net_.

## <a name="assign-hostnames-to-app"></a>Názvy hostitelů přiřadit aplikace

Pokud se rozhodnete přiřadit výchozí hostitelské názvy do vaší aplikace během procesu nákupu nebo pokud je potřeba přiřadit název hostitele není uvedený, můžete kdykoli přiřadit název hostitele na.

Názvy hostitelů v doména App Service můžete také přiřadit jakoukoli jinou aplikaci. Postup závisí na tom, jestli se doména App Service a aplikaci patřily do stejného předplatného.

- Jiné předplatné: Mapování vlastních záznamů DNS z doména App Service do aplikace, jako jsou externě zakoupené domény. Informace o přidání vlastní názvy DNS doména služby App Service najdete v tématu [spravovat vlastní záznamy DNS](#custom). Mapování externí domény zakoupené na aplikaci, najdete v článku [mapování existujícího vlastního názvu DNS do služby Azure App Service](app-service-web-tutorial-custom-domain.md). 
- Stejného předplatného: Pomocí následujícího postupu.

### <a name="launch-add-hostname"></a>Spuštění přidat název hostitele
V **App Services** vyberte název aplikace, kterou chcete přiřadit názvů hostitelů, vyberte **nastavení**a pak vyberte **vlastní domény**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Ujistěte se, že vaše zakoupené doména uvedena v **domén služby App Service** části, ale nevybírejte ho. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Všechny domén služby App Service ve stejném předplatném jsou uvedeny v aplikace **vlastní domény** stránky. Pokud se vaše doména je v předplatném aplikace, ale nevidíte ji v aplikaci prvku **vlastní domény** stránce, zkuste to znova otevřít **vlastní domény** stránce nebo aktualizujte webovou stránku. Zkontrolujte taky, oznámení zvonku v horní části webu Azure portal pro průběh nebo vytvoření chyby.
>
>

Vyberte **Přidat název hostitele**.

### <a name="configure-hostname"></a>Konfigurace názvu hostitele
V **přidat název hostitele** dialogové okno, zadejte plně kvalifikovaný název vaše doména App Service nebo jakákoli subdoména. Příklad:

- kontoso.net
- www.kontoso.net
- abc.kontoso.net

Až budete hotovi, vyberte **ověřit**. Typ záznamu názvu hostitele se vybere automaticky za vás.

Vyberte **Přidat název hostitele**.

Po dokončení operace se zobrazí oznámení úspěch přiřazené názvu hostitele.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Zavřete přidat název hostitele
V **přidat název hostitele** stránky, další název hostitele přiřadit aplikace podle potřeby. Až budete hotovi, zavřete **přidat název hostitele** stránky.

Teď byste měli vidět nově přiřazené hostname(s) ve vaší aplikaci **vlastní domény** stránky.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Testování názvy hostitelů

Přejděte na uvedené názvy hostitelů v prohlížeči. V příkladu v předchozím snímku obrazovky, zkuste přejít na stránku _abc.kontoso.net_.

## <a name="renew-the-domain"></a>Obnovit doménu

Doména služby App Service, které jste si zakoupili je platný jeden rok od okamžiku nákupu. Ve výchozím nastavení doména nenakonfiguruje automaticky obnovit tím vaším způsobem platby pro příští rok. Pokud chcete vypnout automatické obnovení, nebo pokud chcete ručně obnovit vaší domény, postupujte podle kroků v tomto poli.

V **App Services** kartu, klikněte na název vaší aplikace, vyberte **nastavení**a pak vyberte **vlastní domény**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

V **domén služby App Service** části, vyberte doménu, kterou chcete konfigurovat.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

V levém navigačním panelu domény, vyberte **obnovení domény**. Chcete-li zastavit automatické prodlužování platnosti vaší domény, vyberte **vypnout**a potom **Uložit**. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Chcete-li ručně obnovit vaši doménu, vyberte **obnovení domény**. Nicméně toto tlačítko není aktivní až 90 dní před vypršením platnosti domény.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Správa vlastních záznamů DNS

V Azure, záznamy DNS pro domény služby App Service se spravují pomocí [Azure DNS](https://azure.microsoft.com/services/dns/). Můžete přidat, odebrat a aktualizovat záznamy DNS, stejně jako pro externě zakoupené doménu.

### <a name="open-app-service-domain"></a>Doména otevřít služby App Service

Na webu Azure Portal, v levé nabídce vyberte **všechny služby** > **domén služby App Service**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Vyberte doménu, ke správě. 

### <a name="access-dns-zone"></a>Přístup k zóně DNS

V nabídce vlevo doménu, vyberte **zónu DNS**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Tato akce otevře [zónu DNS](../dns/dns-zones-records.md) stránky vaše doména App Service v Azure DNS. Informace o tom, jak upravit záznamů DNS najdete v tématu [jak spravovat zóny DNS na webu Azure Portal](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Zrušit nákup (odstranění domény)

Když zakoupíte doména App Service, máte pět dní zrušit svůj nákup si vrátit celou částku. Za pět dní můžete odstranit doména App Service ale nemůže přijímat refundaci.

### <a name="open-app-service-domain"></a>Doména otevřít služby App Service

Na webu Azure Portal, v levé nabídce vyberte **všechny služby** > **domén služby App Service**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Vyberte domény, ke které chcete zrušit nebo odstranit. 

### <a name="delete-hostname-bindings"></a>Odstranění vazby názvu hostitele

V nabídce vlevo doménu, vyberte **vazby názvu hostitele**. Tady jsou uvedené vazby názvu hostitele ze všech služeb Azure.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Doména App Service nejde odstranit, dokud se odstraní všechny vazby názvu hostitele.

Odstranit vazbu každý název hostitele tak, že vyberete **...**   >  **Odstranit**. Když se odstraní všechny vazby, vyberte **Uložit**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Zrušit nebo odstranit

V nabídce vlevo doménu, vyberte **přehled**. 

Pokud limit zrušení na zakoupené domény nebyl vypršel, vyberte **zrušit nákup**. V opačném případě se zobrazí **odstranit** tlačítko místo. Chcete-li odstranit doménu bez refundaci, vyberte **odstranit**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Potvrďte operaci vybrat **Ano**.

Po dokončení operace se doména je z vašeho předplatného vydané a dostupné pro každého, kdo zopakovat nákup. 

## <a name="direct-default-url-to-a-custom-directory"></a>Směrování výchozí adresy URL do vlastního adresáře

Ve výchozím nastavení služba App Service směruje webové požadavky do kořenového adresáře kódu vaší aplikace. Pro přesměrování je na podadresáře, například `public`, naleznete v tématu [směrování výchozí adresy URL do vlastního adresáře](app-service-web-tutorial-custom-domain.md#virtualdir).

## <a name="more-resources"></a>Další zdroje informací

[NEJČASTĚJŠÍ DOTAZY: Doména App Service (preview) a vlastní domény](https://blogs.msdn.microsoft.com/appserviceteam/2017/08/08/faq-app-service-domain-preview-and-custom-domains/)
