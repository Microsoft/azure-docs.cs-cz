---
title: Přístup k spravovanému portálu pro vývojáře a jeho přizpůsobení – Správa rozhraní Azure API | Dokumenty společnosti Microsoft
description: Přečtěte si, jak používat spravovanou verzi portálu pro vývojáře ve správě rozhraní API.
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
ms.openlocfilehash: af7c995c11322a538dd9e27a905f1ddbc723e8ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244092"
---
# <a name="access-and-customize-developer-portal"></a>Přístup k portálu pro vývojáře a jejich přizpůsobení

Vývojářský portál je automaticky generovaný, plně přizpůsobitelný web s dokumentací vašich API. Je místo, kde mohou spotřebitelé rozhraní API zjistit vaše rozhraní API, naučit se je používat a požádat o přístup.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přístup ke spravované verzi portálu pro vývojáře
> * Navigace v jeho rozhraní pro správu
> * Přizpůsobení obsahu
> * Publikování změn
> * Zobrazit publikovaný portál

Další podrobnosti najdete na portálu pro vývojáře v [přehledu portálu pro správu rozhraní Azure API .](api-management-howto-developer-portal.md)

![Portál pro vývojáře pro správu rozhraní API – režim správy](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Požadavky

- Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)
- Importujte a publikujte instanci Azure API Management. Další informace naleznete v [tématu Import a publikování](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Přístup k portálu jako správce

Chcete-li získat přístup ke spravované verzi portálu, postupujte podle následujících kroků.

1. Přejděte na instanci služby Správa rozhraní API na webu Azure Portal.
1. Klikněte na tlačítko **Portál pro vývojáře** na horním navigačním panelu. Otevře se nová karta prohlížeče s verzí portálu pro správu.

## <a name="understand-the-portals-administrative-interface"></a>Principy rozhraní pro správu portálu

### <a name="default-content"></a>Výchozí obsah 

Pokud přistupujete k portálu poprvé, bude se na pozadí automaticky zřídit výchozí obsah. Výchozí obsah byl navržen tak, aby prezentoval možnosti portálu a minimalizoval množství úprav potřebných k přizpůsobení portálu. Další informace o tom, co je součástí obsahu portálu, najdete v [přehledu portálu pro vývojáře azure api managementu](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Vizuální editor

Obsah portálu můžete přizpůsobit pomocí vizuálního editoru. Oddíly nabídek vlevo umožňují vytvářet nebo upravovat stránky, média, rozložení, nabídky, styly nebo nastavení webu. Položky nabídky v dolní části umožňují přepínat mezi výřezy (například mobilní nebo stolní počítače), zobrazit prvky portálu viditelné pro ověřené nebo anonymní uživatele nebo uložit nebo vrátit akce.

Řádky můžete na stránku přidat kliknutím na modrou ikonu se znaménkem plus. Widgety (například text, obrázky nebo seznam rozhraní API) lze přidat stisknutím šedé ikony se znaménkem plus. Pomocí interakce přetažení myší můžete změnit uspořádání položek na stránce. 

### <a name="layouts-and-pages"></a>Rozložení a stránky

![Stránky a rozložení](media/api-management-howto-developer-portal-customize/pages-layouts.png)

Rozložení definují způsob zobrazení stránek. Například ve výchozím obsahu existují dvě rozložení – jedno se vztahuje na domovskou stránku a druhé na všechny zbývající stránky.

Rozložení se použije na stránku tak, že se její šablona URL přirovná k adrese URL stránky. Například rozložení se šablonou `/wiki/*` adresy URL bude použito `/wiki/` na každou `/wiki/getting-started`stránku se segmentem v adrese URL: , `/wiki/styles`, atd.

Na obrázku výše je obsah, který patří k rozložení, označen modře, zatímco stránka je označena červeně. Části nabídky jsou označeny.

### <a name="styling-guide"></a>Průvodce stylingem

![Průvodce stylingem](media/api-management-howto-developer-portal-customize/styling-guide.png)

Styling průvodce je panel vytvořený s designéry v mysli. Umožňuje dohlížet a styling všechny vizuální prvky ve vašem portálu. Styl je hierarchický - mnoho prvků dědí vlastnosti z jiných prvků. Například prvky tlačítek používají barvy pro text a pozadí. Chcete-li změnit barvu tlačítka, musíte změnit původní variantu barvy.

Chcete-li upravit variantu, klikněte na ni a vyberte ikonu tužky, která se zobrazí nad ní. Jakmile provedete změny v rozbalovacím okně, zavřete je.

### <a name="save-button"></a>Tlačítko Uložit

![Tlačítko Uložit](media/api-management-howto-developer-portal-customize/save-button.png)

Kdykoli provedete změnu na portálu, musíte ji uložit ručně stisknutím tlačítka **Uložit** v nabídce v dolní části. Když změny uložíte, upravený obsah se automaticky nahraje do služby api management.

## <a name="customize-the-portals-content"></a>Přizpůsobení obsahu portálu

Před zpřístupněním portálu návštěvníkům byste měli automaticky generovaný obsah přizpůsobit. Mezi doporučené změny patří rozložení, styly a obsah domovské stránky.

> [!NOTE]
> Z důvodu integrace nelze následující stránky odebrat nebo přesunout pod `/404` `/500`jinou adresou URL: `/signin`, `/signin-sso` `/signup`, `/captcha` `/change-password`, , `/config.json`, `/confirm/invitation` `/confirm-v2/identities/basic/signup`, `/confirm-v2/password`, `/internal-status-0123456789abcdef`, `/publish`, , , , . .

### <a name="home-page"></a>Domovská stránka

Výchozí **domovská** stránka je vyplněna fiktivním obsahem. Můžete buď odstranit celé oddíly s obsahem, nebo zachovat strukturu a upravit prvky jeden po druhém. Nahraďte generovaný text a obrázky vlastním a ujistěte se, že odkazy odkazují na požadovaná umístění.

### <a name="layouts"></a>Rozložení

Nahraďte automaticky generované logo na navigačním panelu vlastním obrázkem.

### <a name="styling"></a>Použití stylů

I když nemusíte upravovat žádné styly, můžete zvážit úpravu určitých prvků. Změňte například primární barvu tak, aby odpovídala barvě značky.

### <a name="customization-example"></a>Příklad přizpůsobení

Ve videu níže ukazujeme, jak upravit obsah portálu, přizpůsobit vzhled webu a publikovat změny.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>Publikování portálu

Chcete-li zpřístupnit portál a jeho nejnovější změny návštěvníkům, je třeba jej publikovat.

1. Kliknutím na ikonu **Uložit** se ujistěte, že jste změny uložili.
1. Klikněte na **publikovat web** v sekci **Operace** v nabídce. Tato operace může trvat několik minut.  
    ![Publikovat portál](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> Portál musí být znovu publikován po změnách konfigurace služby API Management, jako je přiřazení vlastní domény, aktualizace poskytovatelů identit, nastavení delegování, určení podmínek přihlášení a produktu a další.

## <a name="visit-the-published-portal"></a>Navštivte publikovaný portál

Po publikování portálu k němu můžete přistupovat na stejné adrese `https://contoso-api.developer.azure-api.net`URL jako například panel pro správu . Zobrazit v samostatné relaci prohlížeče (anonymní / soukromý režim prohlížení) jako externí návštěvník.

## <a name="apply-the-cors-policy-on-apis"></a>Použití zásad CORS u api

Je třeba povolit CORS (sdílení prostředků mezi zdroji) na vašich api, aby návštěvníci portálu otestovat api prostřednictvím integrované interaktivní konzoly. Další podrobnosti naleznete v [tomto článku s dokumentací.](api-management-howto-developer-portal.md#cors)

## <a name="next-steps"></a>Další kroky

Další informace o portálu pro vývojáře:

- [Přehled portálu pro vývojáře správy rozhraní Azure API](api-management-howto-developer-portal.md)
