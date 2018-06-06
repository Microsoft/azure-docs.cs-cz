---
title: Nákup vlastního názvu domény pro Azure Web Apps
description: Zjistěte, jak koupit vlastní doménu s webovou aplikaci v Azure App Service.
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
ms.openlocfilehash: 16a6d8e34bdec6e597c5f04faf421d387a877bff
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714880"
---
# <a name="buy-a-custom-domain-name-for-azure-web-apps"></a>Nákup vlastního názvu domény pro Azure Web Apps

Domény nejvyšší úrovně, které jsou spravovány přímo v Azure jsou domény služby App Service (preview). Se snadno spravovat vlastní domény pro [Azure Web Apps](app-service-web-overview.md). V tomto kurzu se dozvíte, jak zakoupit domény služby App Service a názvy DNS přiřadit Azure Web Apps.

Tento článek je pro službu Azure App Service (webové aplikace, aplikace API, mobilní aplikace, Logic Apps). Virtuální počítač Azure nebo Azure Storage, najdete v části [domény přiřadit aplikační služby na virtuálním počítači Azure nebo Azure Storage](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/). Cloudové služby, najdete v části [konfigurace vlastního názvu domény pro cloudové služby Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* [Vytvořit plán služby App Service](/azure/app-service/) nebo použít aplikaci, kterou jste vytvořili pro účely jiného kurzu.
* [Odeberte limit útraty vaše předplatné](../billing/billing-spending-limit.md#remove). Služby App Service domén s kredity bezplatné předplatné nejde koupit.

## <a name="prepare-the-app"></a>Příprava aplikace

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Použít vlastní domény ve službě Azure Web Apps, webové aplikace na [plán služby App Service](https://azure.microsoft.com/pricing/details/app-service/) musí být placené vrstvy (**sdílené**, **základní**, **standardní**, nebo  **Premium**). V tomto kroku je třeba zkontrolovat, že webová aplikace je v podporovaném cenová úroveň.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Přechod do aplikace na webu Azure Portal

V levé nabídce vyberte **App Services** a pak vyberte název aplikace.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/select-app.png)

Zobrazí se stránka pro správu aplikace App Service.  

### <a name="check-the-pricing-tier"></a>Kontrola cenové úrovně

V levém navigačním panelu na stránce aplikace se posuňte do části **Nastavení** a vyberte **Vertikálně navýšit kapacitu (plán služby App Service)**.

![Nabídka Vertikálně navýšit kapacitu](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Aktuální úroveň aplikace je zvýrazněná modrým ohraničením. Zkontrolujte, zda není v aplikaci **F1** vrstvy. Vlastní DNS není podporována v **F1** vrstvy. 

![Kontrola cenové úrovně](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Pokud plán služby App Service se nenachází ve **F1** vrstvy, zavřete **škálovat** stránky a přejít na [koupit domény](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

Vyberte některé z vrstvy a bezplatnou (**D1**, **B1**, **B2**, **B3**, nebo všechny vrstvy v **produkční** kategorie). Další možnosti, klikněte na tlačítko **najdete v části Další možnosti**.

Klikněte na tlačítko **Použít**.

![Kontrola cenové úrovně](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Až se zobrazí následující oznámení, operace škálování je dokončená.

![Potvrzení operace škálování](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Kupte si domény

### <a name="sign-in-to-azure"></a>Přihlášení k Azure
Otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se pomocí svého účtu Azure.

### <a name="launch-buy-domains"></a>Spusťte koupit domén
V **webové aplikace** , klikněte na název vaší webové aplikaci, vyberte **nastavení**a potom vyberte **vlastní domény**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

V **vlastní domény** klikněte na tlačítko **koupit domény**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Pokud nevidíte **doménami aplikací služby** části, je třeba odebrat limit útraty na účtu Azure (v tématu [požadavky](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Konfigurace domény nákupu

V **aplikace služby domény** stránky v **hledat domény** pole, zadejte název domény, které chcete koupit a zadejte `Enter`. Navržené domény k dispozici se zobrazí pod textové pole. Vyberte jednu nebo více domén, které chcete koupit.
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Následující [domény nejvyšší úrovně](https://wikipedia.org/wiki/Top-level_domain) jsou podporovány doménami služby App Service: _com_, _net_, _co.uk_, _org_, _nl_, _v_, _biz_, _org.uk_, a _co.in_.
>
>

Klikněte **kontaktní údaje** a vyplňte formulář domény kontaktní informace. Po dokončení klikněte na tlačítko **OK** vrátit na stránku služby domény aplikace.
   
Je důležité, vyplňte všechna povinná pole s tolik přesnost míře. Nesprávná data kontaktní informace může způsobit selhání přikoupení domén. 

Potom vyberte požadované možnosti pro vaši doménu. V následující tabulce najdete vysvětlení:

| Nastavení | Navrhovaná hodnota | Popis |
|-|-|-|
|Ochrana osobních údajů | Povolení | Vyjádřit výslovný souhlas pro "Ochrany osobních údajů", který je součástí kupní ceny _zdarma_. Některé domény nejvyšší úrovně spravuje registrátorů, které nepodporují ochrany osobních údajů a jsou uvedeny na **ochrany osobních údajů** stránky. |
| Přiřadit výchozí hostitelské názvy | **Webová** a **@** | Vazby požadovaným názvem hostitele, vyberte v případě potřeby. Po dokončení nákupu operace domény vaší webové aplikace jsou přístupné na vybrané názvy hostitelů. Pokud webová aplikace je za [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), nevidíte možnost k přidělení kořenové domény (@), protože nemá podporu záznamů A Traffic Manager. Vám po dokončení nákupu domény provádět změny přiřazení názvu hostitele. |

### <a name="accept-terms-and-purchase"></a>Přijmout podmínky a nákupu

Klikněte na tlačítko **právní podmínky** přečtěte si podmínky a že poplatky a pak klikněte na **koupit**.

> [!NOTE]
> Aplikační služby domény použít Azure DNS k hostování domény. Kromě poplatek za zápis domain poplatky za používání pro Azure DNS použít. Informace najdete v tématu [Azure DNS ceny](https://azure.microsoft.com/pricing/details/dns/).
>
>

Zpět v **aplikace služby domény** klikněte na tlačítko **OK**. Když probíhá operace, zobrazí se následující oznámení:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Testování názvy hostitelů

Pokud jste přiřadili výchozí názvy hostitelů do vaší webové aplikace, je také zobrazit upozornění na úspěch pro každý vybraný název hostitele. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Zobrazí vybraný názvy hostitelů v **vlastní domény** stránky v **vlastní názvy hostitelů** části. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

Chcete-li otestovat názvy hostitelů, přejděte na uvedené názvy hostitelů v prohlížeči. V příkladu v předchozí snímek obrazovky, zkuste přejdete na _kontoso.net_ a _www.kontoso.net_.

## <a name="assign-hostnames-to-web-app"></a>Přiřadit názvy hostitelů do webové aplikace

Pokud se rozhodnete přiřadit jeden nebo více názvy hostitelů výchozí webové aplikace během procesu nákupu, nebo pokud je třeba přiřadit název hostitele není uvedený, můžete kdykoli přiřadit název hostitele v.

Názvy hostitelů v doméně služby aplikaci můžete přiřadit také do jiné webové aplikace. Postup závisí na tom, jestli domény služby, aplikace a webové aplikace patří do stejného předplatného.

- Jiné předplatné: namapovat vlastní záznamy DNS od domény služby aplikace do webové aplikace jako externě zakoupené domény. Informace o přidání vlastní názvy DNS do domény služby App Service najdete v tématu [vlastní záznamy DNS spravovat](#custom). Mapování externí zakoupené domény do webové aplikace naleznete v tématu [mapovat existující vlastní název DNS pro službu Azure Web Apps](app-service-web-tutorial-custom-domain.md). 
- Stejného předplatného: použijte následující postup.

### <a name="launch-add-hostname"></a>Spuštění přidat název hostitele
V **App Services** vyberte název vaší webové aplikace, kterou chcete přiřadit názvů hostitelů, vyberte **nastavení**a potom vyberte **vlastní domény**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Ujistěte se, že zakoupené domény, je uvedena ve **doménami aplikací služby** tématu, ale nezaškrtnete políčko. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Všechny aplikace služby domény v rámci stejného předplatného jsou uvedeny ve webové aplikaci **vlastní domény** stránky. Pokud vaše doména je v rámci předplatného webové aplikace, ale nejde ji zobrazit ve webové aplikaci **vlastní domény** stránky, zkuste to znovu **vlastní domény** stránky nebo obnovení webové stránky. Zkontrolujte také, oznámení zvonku v horní části portálu Azure průběh nebo vytvoření chyby.
>
>

Vyberte **Přidat název hostitele**.

### <a name="configure-hostname"></a>Nakonfigurujte název hostitele
V **přidat název hostitele** dialogové okno, zadejte plně kvalifikovaný název domény vaší domény služby aplikace nebo jakékoli subdomény. Příklad:

- kontoso.net
- www.kontoso.net
- abc.kontoso.net

Po dokončení vyberte **ověřením**. Typ záznamu název hostitele je automaticky vybrána pro vás.

Vyberte **Přidat název hostitele**.

Po dokončení operace se zobrazí upozornění na úspěch přiřazené název hostitele.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Zavřete přidat název hostitele
V **přidat název hostitele** přiřaďte jiné název hostitele do webové aplikace, podle potřeby. Po dokončení zavřete **přidat název hostitele** stránky.

Teď byste měli vidět nově přiřazené hostname(s) ve vaší aplikaci **vlastní domény** stránky.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Testování názvy hostitelů

Přejděte do seznamu názvy hostitelů v prohlížeči. V příkladu v předchozí snímek obrazovky, zkuste přejdete na _abc.kontoso.net_.

## <a name="renew-the-domain"></a>Obnovit

Doména služby aplikace, které jste si zakoupili je platný jeden rok od nákupu. Ve výchozím nastavení doména nenakonfiguruje na Obnovit automaticky tak, že váš způsob platby poplatků za do následujícího roku. Pokud chcete vypnout automatické obnovení, nebo pokud chcete ručně obnovit vaší domény, postupujte podle kroků v tomto poli.

V **webové aplikace** , klikněte na název vaší webové aplikaci, vyberte **nastavení**a potom vyberte **vlastní domény**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

V **doménami aplikací služby** část, vyberte doménu, kterou chcete konfigurovat.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

V levém navigačním domény, vyberte **obnovení domény**. Chcete-li zastavit automaticky obnovují doménu, vyberte **vypnout**a potom **Uložit**. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Chcete-li ručně obnovit doménu, vyberte **obnovení domény**. Ale toto tlačítko není aktivní až 90 dní před vypršením platnosti domény.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Spravovat vlastní záznamy DNS

V Azure, záznamy DNS pro doménu služby App Service se spravují pomocí [Azure DNS](https://azure.microsoft.com/services/dns/). Můžete přidat, odebrat a aktualizovat záznamy DNS, stejně jako pro externě zakoupené doménu.

### <a name="open-app-service-domain"></a>Otevřete aplikaci služby domény

Na portálu Azure v levé nabídce vyberte **všechny služby** > **doménami aplikací služby**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Vyberte doménu pro správu. 

### <a name="access-dns-zone"></a>Zóna DNS přístup

V levé nabídce domény, vyberte **zónu DNS**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Tato akce otevře [zónu DNS](../dns/dns-zones-records.md) stránku vaší domény služby aplikace v Azure DNS. Informace o tom, jak upravit záznamy DNS najdete v tématu [správa zóny DNS na webu Azure portal](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Zrušit nákup (odstranit domény)

Po zakoupení služby doména aplikace máte zrušit nákup pro vrátit celou částku pět dní. Po pět dní můžete odstranit doménu služby aplikace ale nemůže přijímat náhrada.

### <a name="open-app-service-domain"></a>Otevřete aplikaci služby domény

Na portálu Azure v levé nabídce vyberte **všechny služby** > **doménami aplikací služby**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Vyberte domény, ke které chcete zrušit nebo odstranit. 

### <a name="delete-hostname-bindings"></a>Odstranit vazby názvů hostitelů.

V levé nabídce domény, vyberte **vazby názvů hostitelů**. Vazby názvů hostitelů ze všech služeb Azure jsou zde uvedeny.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Doména aplikace služby nelze odstranit, dokud nejsou odstraněny všechny vazby názvů hostitelů.

Odstranit vazbu každý název hostitele tak, že vyberete **...**   >  **Odstranit**. Po odstranění se všechny vazby, vyberte **Uložit**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Zrušit nebo odstranění

V levé nabídce domény, vyberte **přehled**. 

Pokud nebyl uplynutí období zrušení na zakoupené doménu, vyberte **zrušit nákup**. Jinak se zobrazí **odstranit** tlačítko místo. Chcete-li odstranit doménu bez náhrady, vyberte **odstranit**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Pokud chcete potvrdit operaci, vyberte **Ano**.

Po dokončení operace doména je vydaná ze svého předplatného a k dispozici pro každý, kdo k nákupu znovu. 

## <a name="direct-default-url-to-a-custom-directory"></a>Směrování výchozí adresy URL do vlastního adresáře

Ve výchozím nastavení služba App Service směruje webové požadavky do kořenového adresáře kódu vaší aplikace. Pro přesměrování je na podadresáři, jako například `public`, najdete v části [přímé výchozí adresy URL do vlastní adresáře](app-service-web-tutorial-custom-domain.md#virtualdir).

## <a name="more-resources"></a>Další zdroje informací

[– Nejčastější dotazy: Doména aplikace služby (preview) a vlastní domény](https://blogs.msdn.microsoft.com/appserviceteam/2017/08/08/faq-app-service-domain-preview-and-custom-domains/)