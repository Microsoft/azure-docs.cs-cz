---
title: Kurz – přístup a přizpůsobení portálu pro vývojáře – Azure API Management | Microsoft Docs
description: V tomto kurzu se dozvíte, jak přizpůsobit portál pro API Management pro vývojáře, automaticky generovaný a plně přizpůsobitelný web s dokumentací vašich rozhraní API.
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 11/16/2020
ms.author: apimpm
ms.openlocfilehash: 90544fbafe7393630c3f3fbc694ae367eccb7f90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012969"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>Kurz: přístup a přizpůsobení portálu pro vývojáře

*Portál pro vývojáře* je automaticky generovaný plně přizpůsobitelný web s dokumentací vašich rozhraní API. Je tam, kde můžou příjemci rozhraní API zjistit vaše rozhraní API, zjistit, jak je používat, a požádat o přístup.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přístup ke spravované verzi portálu pro vývojáře
> * Přejít na jeho rozhraní pro správu
> * Přizpůsobení obsahu
> * Publikovat změny
> * Zobrazení publikovaného portálu

Další podrobnosti najdete na portálu pro vývojáře v článku [Přehled portálu pro vývojáře Azure API Management](api-management-howto-developer-portal.md).

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="Portál pro vývojáře API Management – režim správce" border="false":::

## <a name="prerequisites"></a>Předpoklady

- Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)
- Import a publikování instance služby Azure API Management. Další informace najdete v tématu [Import a publikování](import-and-publish.md) .

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Přístup k portálu jako správce

Použijte následující postup, chcete-li získat přístup ke spravované verzi portálu.

1. V [Azure Portal](https://portal.azure.com)přejděte k instanci API Management.
1. V horním navigačním panelu vyberte tlačítko **portál pro vývojáře** . Otevře se nová karta prohlížeče s verzí pro správu portálu.

## <a name="understand-the-portals-administrative-interface"></a>Pochopení rozhraní pro správu portálu

### <a name="default-content"></a>Výchozí obsah 

Pokud k portálu přistupujete poprvé, výchozí obsah se automaticky zřídí na pozadí. Výchozí obsah byl navržený tak, aby předvedl možnosti portálu a minimalizoval vlastní nastavení potřebné k přizpůsobení portálu. Další informace o tom, co je součástí obsahu portálu, najdete v článku [Přehled portálu pro vývojáře Azure API Management](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Vizuální Editor

Můžete přizpůsobit obsah portálu pomocí vizuálního editoru. 
* Oddíly nabídky na levé straně umožňují vytvářet nebo upravovat stránky, multimédia, rozložení, nabídky, styly a nastavení webu. 
* Položky nabídky v dolní části umožňují přepínání mezi zobrazeními (například mobilní nebo desktopové), zobrazení prvků portálu viditelných pro ověřené nebo anonymní uživatele nebo ukládání nebo vrácení akcí.
* Kliknutím na modrou ikonu se znaménkem plus přidejte řádky na stránku. 
* Widgety (například text, obrázky nebo seznam rozhraní API) lze přidat stisknutím šedé ikony se znaménkem plus.
* Změní uspořádání položek na stránce pomocí interakce přetažení myší. 

### <a name="layouts-and-pages"></a>Rozložení a stránky

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="Stránky a rozložení" border="false":::

Rozložení definují způsob zobrazení stránek. Ve výchozím obsahu například existují dvě rozložení: jedna platí pro domovskou stránku a druhá na všechny zbývající stránky.

Rozložení se použije na stránku podle odpovídající šablony URL k adrese URL stránky. Například rozložení s šablonou URL `/wiki/*` bude použito na všechny stránky s `/wiki/` segmentem v adrese URL: `/wiki/getting-started` , `/wiki/styles` atd.

V předchozím obrázku je obsah patřící do rozložení označen modře, zatímco stránka je označena červeně. Oddíly nabídky jsou označeny v uvedeném pořadí.

### <a name="styling-guide"></a>Vodítko stylu

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="Vodítko stylu" border="false":::

Vodítko pro stylování je panel vytvořený s ohledem na použití návrháře. Umožňuje zobrazení a stylování všech vizuálních prvků na portálu. Styl je hierarchický – mnoho prvků dědí vlastnosti z jiných prvků. Například prvky Button používají barvy pro text a pozadí. Chcete-li změnit barvu tlačítka, je nutné změnit původní variaci barvy.

Chcete-li upravit variantu, vyberte ji a vyberte ikonu tužky, která se zobrazí nad ní. Po provedení změn v místním okně ji zavřete.

### <a name="save-button"></a>Tlačítko Uložit

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="Tlačítko Uložit" border="false":::

Pokaždé, když provedete změnu na portálu, budete ji muset uložit ručně, a to tak, že v nabídce dole kliknete na tlačítko **Uložit** , nebo stisknete [CTRL] + [S]. Po uložení změn se změněný obsah automaticky nahraje do vaší služby API Management.

## <a name="customize-the-portals-content"></a>Přizpůsobení obsahu portálu

Než zpřístupníte portál návštěvníkům, měli byste přizpůsobit automaticky generovaný obsah. Mezi doporučené změny patří rozložení, styly a obsah domovské stránky.

> [!NOTE]
> Z důvodu integrace nelze následující stránky odebrat ani přesunout pod jinou adresu URL: `/404` , `/500` , `/captcha` , `/change-password` , `/config.json` , `/confirm/invitation` , `/confirm-v2/identities/basic/signup` , `/confirm-v2/password` , `/internal-status-0123456789abcdef` , `/publish` , `/signin` , `/signin-sso` , `/signup` .

### <a name="home-page"></a>Domovská stránka

Výchozí **Domovská** stránka je vyplněna zástupným obsahem. Můžete buď odebrat celé oddíly obsahující tento obsah, nebo zachovat strukturu a upravit prvky jednu po jedné. Vygenerovaný text a obrázky nahraďte vlastními a ujistěte se, že odkazy odkazují na požadovaná umístění.

### <a name="layouts"></a>Rozložení

V navigačním panelu nahraďte automaticky vygenerované logo vlastním obrázkem.

### <a name="styling"></a>Použití stylů

Přestože nemusíte upravovat žádné styly, můžete zvážit úpravu určitých prvků. Například změňte primární barvu tak, aby odpovídala barvě vaší značky.

### <a name="customization-example"></a>Příklad přizpůsobení

V následujícím videu ukážeme, jak upravit obsah portálu, přizpůsobit vzhled webu a publikovat změny.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> Publikování portálu

Pokud chcete, aby byl váš portál a jeho nejnovější změny dostupné pro návštěvníky, je nutné ji *publikovat* . Portál můžete publikovat v rámci rozhraní pro správu portálu nebo z Azure Portal.

### <a name="publish-from-the-administrative-interface"></a>Publikování z rozhraní pro správu

1. Ujistěte se, že jste změny uložili, a to tak, že vyberete ikonu **Uložit** .
1. V části **operace** v nabídce vyberte **Publikovat web** . Tato operace může trvat několik minut.  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="Publikovat portál" border="false":::

### <a name="publish-from-the-azure-portal"></a>Publikování z Azure Portal

1. V [Azure Portal](https://portal.azure.com)přejděte k instanci API Management.
1. V nabídce vlevo v části **portál pro vývojáře** vyberte **Přehled portálu**.
1. V okně **Přehled portálu** vyberte **publikovat**.

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Publikování portálu z Azure Portal":::

> [!NOTE]
> Po API Management změn konfigurace služby je potřeba portál znovu publikovat. Portál můžete například znovu publikovat po přiřazení vlastní domény, aktualizaci zprostředkovatelů identity, nastavení delegování nebo určení podmínek přihlášení a produktu.


## <a name="visit-the-published-portal"></a>Navštívit publikovaný portál

Po publikování portálu je možné k němu přistupovat na stejné adrese URL jako panel pro správu, například `https://contoso-api.developer.azure-api.net` . Zobrazí se v samostatné relaci prohlížeče (pomocí anonymním nebo privátního režimu procházení) jako externí návštěvník.

## <a name="apply-the-cors-policy-on-apis"></a>Použití zásad CORS na rozhraních API

Pokud chcete návštěvníkům portálu umožnit otestovat rozhraní API prostřednictvím integrované interaktivní konzoly, povolte CORS (sdílení prostředků mezi zdroji) na vašich rozhraních API. Podrobnosti najdete v tématu [Přehled portálu pro vývojáře Azure API Management](api-management-howto-developer-portal.md#cors).

## <a name="next-steps"></a>Další kroky

Další informace o portálu pro vývojáře:

- [Přehled vývojářského portálu pro službu Azure API Management](api-management-howto-developer-portal.md)
- [Migrujte na nový portál pro vývojáře](developer-portal-deprecated-migration.md) z zastaralého staršího portálu.