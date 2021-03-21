---
title: Koupit vlastní název domény
description: Naučte se koupit doménu App Service a použít ji jako vlastní doménu pro Azure App Service vaší aplikace.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: cdcf22a42375949cc4d6be0b4f3062cee26219d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704850"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Nákup vlastního názvu domény pro Azure App Service

App Service domény jsou vlastní domény spravované přímo v Azure. Usnadňují správu vlastních domén pro [Azure App Service](overview.md). V tomto kurzu se dozvíte, jak koupit doménu App Service a přiřadit k Azure App Service názvy DNS.

Informace o virtuálním počítači nebo Azure Storage Azure najdete v tématu [přiřazení App Service domény k virtuálnímu počítači Azure nebo Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). Cloud Services najdete v tématu [Konfigurace vlastního názvu domény pro cloudovou službu Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu potřebujete:

* [Vytvořit plán služby App Service](./index.yml) nebo použít aplikaci, kterou jste vytvořili pro účely jiného kurzu. Aplikace by měla být ve veřejné oblasti Azure. V současné době nejsou podporovány vnitrostátní cloudy Azure.
* [Odeberte limit útraty ve vašem předplatném](../cost-management-billing/manage/spending-limit.md#remove). Nemůžete nakupovat App Service domén s kredity bezplatného předplatného.

## <a name="buy-an-app-service-domain"></a>Koupit doménu App Service

Informace o cenách App Servicech doménách najdete na [stránce s cenami App Service](https://azure.microsoft.com/pricing/details/app-service/windows/) a posuňte se dolů na App Service doménu.

1. Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.

1. Na panelu hledání vyhledejte a vyberte **App Service domény**.

    ![Navigace na portálu pro Azure App Service domény](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. V zobrazení **domény App Service** klikněte na **Přidat**.

    ![Klikněte na Přidat v App Service doménách.](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. Vyberte **kliknutím a vyzkoušejte si novější verzi App Service domény vytvořit prostředí**.

    ![Vytvoření App Service domény s novým prostředím](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>Karta základy

1. Na kartě **základy** nakonfigurujte nastavení pomocí následující tabulky:  

   | Nastavení  | Popis |
   | -------- | ----------- |
   | **Předplatné** | Předplatné, které se má použít k nákupu domény. |
   | **Skupina prostředků** | Skupina prostředků, ve které se má doména umístit Například skupina prostředků, ve které je vaše aplikace. |
   | **Doména** | Zadejte doménu, kterou chcete. Například **contoso.com**. Pokud požadovaná doména není k dispozici, můžete vybrat ze seznamu návrhů dostupných domén nebo vyzkoušet jinou doménu. |

    > [!NOTE]
    > Následující [domény nejvyšší úrovně](https://wikipedia.org/wiki/Top-level_domain) jsou podporovány App Service doménami: _com_, _net_, _co.UK_, _org_, _nl_, _in_,,  _org.UK_ a _co.in_.
    >
    >
    
2. Po dokončení klikněte na **Další: kontaktní informace**.

### <a name="contact-information-tab"></a>Karta kontaktní informace

1. Dodejte své informace podle požadavků [ICANN](https://go.microsoft.com/fwlink/?linkid=2116641) k registraci domény. 

    Je důležité vyplnit všechna povinná pole co nejvíc přesností. Nesprávná data pro kontaktní údaje můžou mít za následek neúspěšné zakoupení domény.

1. Po dokončení klikněte na **Další: Upřesnit**.

### <a name="advanced-tab"></a>Karta Upřesnit

1. Na kartě **Upřesnit** nakonfigurujte volitelná nastavení:  

   | Nastavení  | Popis |
   | -------- | ----------- |
   | **Automatické obnovení** | Ve výchozím nastavení povoleno. Vaše doména App Service je zaregistrovaná na vás při přírůstcích po jednom roce. Automatické obnovení zajišťuje vypršení platnosti vaší registrace domény a uchování vlastnictví domény. Vaše předplatné Azure se automaticky účtuje za roční registrační poplatek za doménu v době obnovení. Pokud se chcete odhlásit, vyberte **Zakázat**. Pokud je automatické obnovení zakázané, můžete [ho obnovit ručně](#renew-the-domain). |
   | **Privacy protection** | Ve výchozím nastavení povoleno. Ochrana osobních údajů skrývá kontaktní informace k registraci vaší domény z databáze WHOIS. Ochrana osobních údajů je už zahrnutá v rámci ročního registračního poplatku za doménu. Pokud se chcete odhlásit, vyberte **Zakázat**. |

2. Po dokončení klikněte na tlačítko **Další: značky**.

### <a name="finish"></a>Dokončit

1. Na kartě **značky** nastavte požadované značky pro doménu App Service. Označování se nevyžaduje pro použití App Service domén, ale je [funkce v Azure, která vám pomůže se správou prostředků](../azure-resource-manager/management/tag-resources.md).

1. Klikněte na **Další: zkontrolovat + vytvořit**.

1. Na kartě **Revize + vytvořit** Zkontrolujte pořadí domény. Jakmile budete hotovi, klikněte na **Vytvořit**.

    > [!NOTE]
    > App Service domény používají GoDaddy k registraci domény a Azure DNS k hostování domén. Kromě ročního registračního poplatku v doméně platí poplatky za využití Azure DNS. Informace najdete v tématu [Azure DNS ceny](https://azure.microsoft.com/pricing/details/dns/).
    >
    >

1. Po dokončení registrace domény se zobrazí tlačítko **Přejít na prostředek** . Vyberte ho, abyste viděli jeho stránku správy.

    ![Doména App Service vytvořena. Přejít k prostředku](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

Nyní jste připraveni přiřadit aplikaci App Service k této vlastní doméně.

## <a name="prepare-the-app"></a>Příprava aplikace

Pokud chcete namapovat vlastní název DNS na webovou aplikaci, [plán App Service](https://azure.microsoft.com/pricing/details/app-service/) webové aplikace musí být placená úroveň (Shared, Basic, Standard, Premium nebo spotřeba pro Azure Functions). V tomto kroku se ujistíte, že je aplikace App Service na podporované cenové úrovni.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Přechod do aplikace na webu Azure Portal

1. V horním panelu vyhledávání vyhledejte a vyberte **App Services**.

    ![Hledat App Services](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Vyberte název aplikace.

    ![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Zobrazí se stránka pro správu aplikace App Service.  

### <a name="check-the-pricing-tier"></a>Kontrola cenové úrovně

1. V levém navigačním panelu na stránce aplikace se posuňte do části **Nastavení** a vyberte **Vertikálně navýšit kapacitu (plán služby App Service)**.

    ![Nabídka Vertikálně navýšit kapacitu](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Aktuální úroveň aplikace je zvýrazněná modrým ohraničením. Zkontrolujte, že aplikace není na úrovni **F1**. Vlastní DNS se na úrovni **F1** nepodporuje. 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="Snímek obrazovky vlevo v navigační nabídce stránky aplikace s vybraným stupněm navýšení (App Servicem plánem)":::

1. Pokud App Service plán není ve vrstvě **F1** , zavřete stránku **horizontálního navýšení kapacity** a přejděte k [nákupu domény](#buy-an-app-service-domain).

### <a name="scale-up-the-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

1. Vyberte některou z placených úrovní (**D1**, **B1**, **B2**, **B3** nebo kteroukoli úroveň v kategorii **Produkční**). Další možnosti se zobrazí po kliknutí na odkaz **Zobrazit další možnosti**.

1. Klikněte na **Použít**.

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="Snímek obrazovky s cenovými úrovněmi vlastní domény v produkční kategorii pomocí karty výroba, plán B1 a zvýrazněné tlačítko použít":::

    Až se zobrazí následující oznámení, operace škálování je dokončená.

    ![Potvrzení operace škálování](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>Mapování App Service domény na aplikaci

Název hostitele ve vaší App Service doméně je možné snadno namapovat na aplikaci App Service, pokud je ve stejném předplatném. Namapujete App Service doménu nebo některou z jejích subdomén přímo v aplikaci a Azure vytvoří potřebné záznamy DNS za vás.

> [!NOTE]
> Pokud je doména a aplikace v různých předplatných, namapujete App Service doménu na aplikaci stejným způsobem jako [mapování externě koupené domény](app-service-web-tutorial-custom-domain.md). V takovém případě je Azure DNS externí poskytovatel domény a potřebujete [ručně přidat požadované záznamy DNS](#manage-custom-dns-records).
>

### <a name="map-the-domain"></a>Mapování domény

1. V levém navigačním panelu na stránce aplikace se posuňte do části **Nastavení** a vyberte **vlastní domény**.

    ![Snímek obrazovky, který zobrazuje nabídku vlastní domény.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Vyberte **Přidat vlastní doménu**.

    ![Snímek obrazovky, který zobrazuje položku pro přidání názvu hostitele](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Zadejte doménu App Service (například **contoso.com**) nebo subdoménu (například **www.contoso.com**) a klikněte na **ověřit**.

    > [!NOTE]
    > Pokud jste provedli překlep v App Service název domény, zobrazí se v dolní části stránky chyba ověření, která vám sdělí, že některé záznamy DNS chybí. Tyto záznamy nemusíte přidávat ručně pro doménu App Service. Stačí zadat název domény správně a znovu kliknout na **ověřit** .
    >
    > ![Snímek obrazovky, který zobrazuje chybu ověřování.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. Přijměte **typ záznamu hostname** a klikněte na **Přidat vlastní doménu**.

    ![Snímek obrazovky, který zobrazuje tlačítko Přidat vlastní doménu](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. Může trvat nějakou dobu, než se nová vlastní doména projeví na stránce **vlastní domény** aplikace. Aktualizujte prohlížeč, aby se data aktualizovala.

    ![Snímek obrazovky, který ukazuje přidání záznamu CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > **Nezabezpečený** popisek pro vaši vlastní doménu znamená, že ještě není vázaný na certifikát TLS/SSL. Jakýkoli požadavek HTTPS z prohlížeče do vaší vlastní domény obdrží chybu nebo varování v závislosti na prohlížeči. Pokud chcete přidat vazbu TLS, přečtěte si téma [zabezpečení vlastního názvu DNS s vazbou TLS/SSL v Azure App Service](configure-ssl-bindings.md).
    
### <a name="test-the-custom-domain"></a>Testování vlastní domény

Pokud chcete vlastní doménu otestovat, přejděte do ní v prohlížeči.

## <a name="renew-the-domain"></a>Obnovení domény

App Service doména, kterou jste zakoupili, je platná po dobu jednoho roku od zakoupení. Ve výchozím nastavení je doména nakonfigurovaná tak, aby se automaticky obnovila tak, že si zaznamenáme způsob platby na další rok. Název domény můžete ručně obnovit.

Pokud chcete vypnout automatické obnovování nebo pokud chcete, aby se doména obnovila ručně, postupujte podle kroků uvedených tady.

1. Na panelu hledání vyhledejte a vyberte **App Service domény**.

    ![Navigace na portálu pro Azure App Service domény](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. V části **App Service domény** vyberte doménu, kterou chcete nakonfigurovat.

1. V levém navigačním panelu domény vyberte **obnovení domény**. Pokud chcete zastavit obnovení domény automaticky, vyberte **vypnuto**. Nastavení se projeví okamžitě.

    ![Snímek obrazovky, který ukazuje možnost automaticky obnovit vaši doménu.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > Při navigaci pryč ze stránky se kliknutím na **OK** zobrazí chybová zpráva o tom, že Neuložené úpravy se zahodí.
    >

Chcete-li ručně obnovit doménu, vyberte možnost **obnovit doménu**. Toto tlačítko je ale aktivní až [90 dní před vypršením platnosti domény](#when-domain-expires).

Pokud je obnovení domény úspěšné, obdržíte e-mailové oznámení během 24 hodin.

## <a name="when-domain-expires"></a>Po vypršení platnosti domény

Azure se týká vypršení platnosti nebo App Service domén s vypršenou platností následujícím způsobem:

* Pokud je automatické obnovení zakázané: 90 dní před vypršením platnosti domény, pošle se vám e-mail s oznámením o prodloužení a na portálu se aktivuje tlačítko **prodloužit doménu** .
* Pokud je povoleno automatické obnovení: dne po datu vypršení platnosti domény, Azure se pokusí vyfakturovat za obnovení názvu domény.
* Pokud při automatickém obnovování dojde k chybě (například vypršela platnost karty na soubor) nebo pokud je automatické obnovení zakázané a pokud povolíte vypršení platnosti domény, Azure vás upozorní na vypršení platnosti domény a parky, které mají název vaší domény. Doménu můžete [obnovit ručně](#renew-the-domain) .
* V 4.12. dnech dne po vypršení platnosti vám Azure pošle další e-maily s oznámením. Doménu můžete [obnovit ručně](#renew-the-domain) . 5. den po vypršení platnosti se překlad DNS zastaví pro doménu, jejíž platnost vypršela.
* 19 den po vypršení platnosti zůstane doména pozastavená, ale bude se řídit poplatkem za uplatnění. Můžete zavolat zákaznickou podporu a prodloužit název domény, a to v souladu s platnými poplatky za obnovení a uplatnění.
* Od 25 dnů po vypršení platnosti Azure zaznamená vaši doménu do aukce za aukci pomocí služby obor názvů domény. Můžete zavolat zákaznickou podporu a prodloužit název domény, a to v souladu s platnými poplatky za obnovení a uplatnění.
* Po uplynutí 30 dnů po vypršení platnosti už nebudete moci uplatnit svoji doménu.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Správa vlastních záznamů DNS

V Azure se záznamy DNS pro App Service doméně spravují pomocí [Azure DNS](https://azure.microsoft.com/services/dns/). Můžete přidávat, odebírat a aktualizovat záznamy DNS stejně jako u externě koupené domény. Správa vlastních záznamů DNS:

1. Na panelu hledání vyhledejte a vyberte **App Service domény**.

    ![Navigace na portálu pro Azure App Service domény](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. V části **App Service domény** vyberte doménu, kterou chcete nakonfigurovat.

1. Na stránce **Přehled** vyberte **Spravovat záznamy DNS**.

    ![Snímek obrazovky, který ukazuje, kde získat přístup k záznamům DNS.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Informace o tom, jak upravit záznamy DNS, najdete v tématu [správa zóny DNS v Azure Portal](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Zrušit nákup (odstranit doménu)

Po zakoupení App Service domény budete mít za pět dní, abyste zrušili nákup pro plnou refundaci. Po pěti dnech můžete doménu App Service odstranit, ale nemůžete ji vrátit.

1. Na panelu hledání vyhledejte a vyberte **App Service domény**.

    ![Navigace na portálu pro Azure App Service domény](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. V části **App Service domény** vyberte doménu, kterou chcete nakonfigurovat.

1. V levém navigačním panelu domény vyberte možnost **vazby názvů hostitelů**. Tady jsou uvedené vazby názvů hostitelů ze všech služeb Azure.

    ![Snímek obrazovky zobrazující stránku vazeb názvu hostitele](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

1. Odstraňte všechny vazby hostitele výběrem **...**  >  **Odstraňte**. Po odstranění všech vazeb vyberte **Save (Uložit**).

    <!-- ![Screenshot that shows where to delete the hostname bindings.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png) -->

1. V levém navigačním panelu domény vyberte **Přehled**. 

1. Pokud neuplynula doba zrušení u koupené domény, vyberte **zrušit nákup**. V opačném případě se místo toho zobrazí tlačítko pro **odstranění** . Chcete-li odstranit doménu bez náhrady, vyberte možnost **Odstranit**.

    ![Snímek obrazovky, který ukazuje, kde odstranit nebo zrušit zakoupenou doménu.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. Potvrďte operaci výběrem možnosti **Ano**.

    Po dokončení operace se doména uvolní z vašeho předplatného a bude k dispozici pro kohokoli zakoupení. 

## <a name="direct-default-url-to-a-custom-directory"></a>Směrování výchozí adresy URL do vlastního adresáře

Ve výchozím nastavení služba App Service směruje webové požadavky do kořenového adresáře kódu vaší aplikace. Pokud je chcete směrovat do podadresáře, například `public` , viz [přesměrování na vlastní adresář](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory).

## <a name="next-steps"></a>Další kroky

Naučte se navazovat vlastní certifikát TLS/SSL na App Service.

> [!div class="nextstepaction"]
> [Zabezpečení vlastního názvu DNS s vazbou TLS v Azure App Service](configure-ssl-bindings.md)
