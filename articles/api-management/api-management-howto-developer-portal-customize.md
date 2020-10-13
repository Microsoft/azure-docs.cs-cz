---
title: Přístup a přizpůsobení spravovaného portálu pro vývojáře – Azure API Management | Microsoft Docs
description: Naučte se používat spravovanou verzi portálu pro vývojáře v API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: 345b4950e25f48eb1a48c5e583908d0f771771ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86206243"
---
# <a name="access-and-customize-developer-portal"></a>Přístup a přizpůsobení portálu pro vývojáře

Portál pro vývojáře je automaticky generovaný plně přizpůsobitelný web s dokumentací vašich rozhraní API. Je tam, kde můžou příjemci rozhraní API zjistit vaše rozhraní API, zjistit, jak je používat, a požádat o přístup.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přístup ke spravované verzi portálu pro vývojáře
> * Přejít na jeho rozhraní pro správu
> * Přizpůsobení obsahu
> * Publikovat změny
> * Zobrazení publikovaného portálu

Další podrobnosti najdete na portálu pro vývojáře v článku [Přehled portálu pro vývojáře Azure API Management](api-management-howto-developer-portal.md).

![Portál pro vývojáře API Management – režim správy](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Požadavky

- Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)
- Import a publikování instance služby Azure API Management. Další informace najdete v tématu [Import a publikování](import-and-publish.md) .

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Přístup k portálu jako správce

Použijte následující postup, chcete-li získat přístup ke spravované verzi portálu.

1. V Azure Portal přejít na instanci služby API Management.
1. V horním navigačním panelu klikněte na tlačítko **portál pro vývojáře** . Otevře se nová karta prohlížeče s verzí pro správu portálu.

## <a name="understand-the-portals-administrative-interface"></a>Pochopení rozhraní pro správu portálu

### <a name="default-content"></a>Výchozí obsah 

Pokud k portálu přistupujete poprvé, výchozí obsah se automaticky zřídí na pozadí. Výchozí obsah byl navržený tak, aby předvedl možnosti portálu a minimalizoval množství přizpůsobení potřebných k přizpůsobení portálu. Další informace o tom, co je součástí obsahu portálu, najdete v článku [Přehled portálu pro vývojáře Azure API Management](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Vizuální Editor

Můžete přizpůsobit obsah portálu pomocí vizuálního editoru. Oddíly nabídky na levé straně umožňují vytvářet nebo upravovat stránky, multimédia, rozložení, nabídky, styly a nastavení webu. Položky nabídky v dolní části umožňují přepínání mezi zobrazeními (například mobilní nebo desktopové), zobrazení prvků portálu viditelných pro ověřené nebo anonymní uživatele nebo ukládání nebo vrácení akcí.

Kliknutím na modrou ikonu se znaménkem plus můžete přidat řádky na stránku. Widgety (například text, obrázky nebo seznam rozhraní API) lze přidat stisknutím šedé ikony se znaménkem plus. Můžete změnit uspořádání položek na stránce pomocí interakce přetažení myší. 

### <a name="layouts-and-pages"></a>Rozložení a stránky

![Stránky a rozložení](media/api-management-howto-developer-portal-customize/pages-layouts.png)

Rozložení definují způsob zobrazení stránek. Například ve výchozím obsahu jsou k dispozici dvě rozložení – jedna se vztahuje na domovskou stránku a druhá na všechny zbývající stránky.

Rozložení se použije na stránku podle odpovídající šablony URL k adrese URL stránky. Například rozložení s šablonou URL `/wiki/*` bude použito na všechny stránky s `/wiki/` segmentem v adrese URL: `/wiki/getting-started` , `/wiki/styles` atd.

Na obrázku výše je obsah patřící do rozložení označen modře, zatímco stránka je označena červeně. Oddíly nabídky jsou označeny v uvedeném pořadí.

### <a name="styling-guide"></a>Vodítko stylu

![Vodítko stylu](media/api-management-howto-developer-portal-customize/styling-guide.png)

Vodítko pro stylování je panel vytvořený s ohledem na použití návrháře. Umožňuje zobrazení a stylování všech vizuálních prvků na portálu. Styl je hierarchický – mnoho prvků dědí vlastnosti z jiných prvků. Například prvky Button používají barvy pro text a pozadí. Chcete-li změnit barvu tlačítka, je nutné změnit původní variaci barvy.

Pokud chcete upravit variantu, klikněte na ni a vyberte ikonu tužky, která se zobrazí v horní části. Jakmile provedete změny v místním okně, zavřete je.

### <a name="save-button"></a>Tlačítko Uložit

![Tlačítko Uložit](media/api-management-howto-developer-portal-customize/save-button.png)

Pokaždé, když provedete změnu na portálu, je nutné ji uložit ručně stisknutím tlačítka **Uložit** v nabídce v dolní části. Po uložení změn se změněný obsah automaticky nahraje do vaší služby API Management.

## <a name="customize-the-portals-content"></a>Přizpůsobení obsahu portálu

Než zpřístupníte portál návštěvníkům, měli byste přizpůsobit automaticky generovaný obsah. Mezi doporučené změny patří rozložení, styly a obsah domovské stránky.

> [!NOTE]
> Z důvodu integrace nelze následující stránky odebrat ani přesunout pod jinou adresu URL: `/404` , `/500` , `/captcha` , `/change-password` , `/config.json` , `/confirm/invitation` , `/confirm-v2/identities/basic/signup` , `/confirm-v2/password` , `/internal-status-0123456789abcdef` , `/publish` , `/signin` , `/signin-sso` , `/signup` .

### <a name="home-page"></a>Domovská stránka

Výchozí **Domovská** stránka je vyplněna fiktivním obsahem. Můžete buď odebrat všechny části s obsahem, nebo zachovat strukturu a upravit prvky jednu po jedné. Vygenerovaný text a obrázky nahraďte vlastními a ujistěte se, že odkazy odkazují na požadovaná umístění.

### <a name="layouts"></a>Rozložení

V navigačním panelu nahraďte automaticky vygenerované logo vlastním obrázkem.

### <a name="styling"></a>Použití stylů

Přestože nemusíte upravovat žádné styly, můžete zvážit úpravu určitých prvků. Například změňte primární barvu tak, aby odpovídala barvě vaší značky.

### <a name="customization-example"></a>Příklad přizpůsobení

V níže uvedeném videu jsme ukázali, jak upravit obsah portálu, přizpůsobit vzhled webu a publikovat změny.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>Publikování portálu

Pokud chcete, aby byl váš portál a jeho nejnovější změny dostupné pro návštěvníky, je nutné ji publikovat.

1. Ujistěte se, že jste změny uložili kliknutím na ikonu **Uložit** .
1. V nabídce v části **operace** klikněte na **Publikovat web** . Tato operace může trvat několik minut.  
    ![Publikovat portál](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> Portál se musí znovu publikovat po API Management změny konfigurace služby, jako je například přiřazení vlastní domény, aktualizace zprostředkovatelů identity, nastavení delegování, určení podmínek pro přihlášení a produkt a další.

## <a name="visit-the-published-portal"></a>Navštívit publikovaný portál

Po publikování portálu je možné k němu přistupovat na stejné adrese URL jako panel pro správu, například `https://contoso-api.developer.azure-api.net` . Zobrazí se v samostatné relaci prohlížeče (režim procházení anonymním/privátní) jako externí návštěvník.

## <a name="apply-the-cors-policy-on-apis"></a>Použití zásad CORS na rozhraních API

V rozhraních API musíte povolit CORS (sdílení prostředků mezi zdroji), aby návštěvníci vašeho portálu mohli testovat rozhraní API prostřednictvím integrované interaktivní konzoly. Další podrobnosti najdete v [tomto článku dokumentace](api-management-howto-developer-portal.md#cors) .

## <a name="next-steps"></a>Další kroky
- [Optimalizace a ukládání na cloudové útratě](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

Další informace o portálu pro vývojáře:

- [Přehled portálu pro vývojáře Azure API Management](api-management-howto-developer-portal.md)