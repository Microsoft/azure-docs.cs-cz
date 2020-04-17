---
title: Migrace aktivního názvu DNS
description: Zjistěte, jak migrovat vlastní název domény DNS, který je už přiřazený k živému webu službě Azure App Service bez prostojů.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: 5c1760c746aca439e19ab5727e5be02f6dbad3cb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535685"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrace aktivního názvu DNS do služby Azure App Service

Tento článek ukazuje, jak migrovat aktivní název DNS do [služby Azure App Service](../app-service/overview.md) bez prostojů.

Když migrujete živý web a jeho název domény DNS do služby App Service, tento název DNS již zobrazuje živý provoz. Výtky v rozlišení DNS během migrace se můžete vyhnout tím, že aktivní název DNS svážete s aplikací App Service preventivně.

Pokud se neobáváte prostojů v překladu DNS, přečtěte si témat [mapovat existující vlastní název DNS na Službu Azure App Service](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Požadavky

Postup:

- [Ujistěte se, že vaše aplikace App Service není na úrovni FREE](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Preventivní vazba názvu domény

Pokud preventivně svážete vlastní doménu, před provedením jakýchkoli změn v záznamech DNS provádíte obě následující skutečnosti:

- Ověření vlastnictví domény
- Povolení názvu domény aplikace

Když konečně migrujete vlastní název DNS ze starého webu do aplikace App Service, nebude v rozlišení DNS žádné prostoje.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Vytvořit záznam ověření domény

Chcete-li ověřit vlastnictví domény, přidejte záznam TXT. TXT záznam mapy z _awverify.&lt; subdoména>_ na _ &lt;název aplikace>.azurewebsites.net_. 

Potřebný záznam TXT závisí na záznamu DNS, který chcete migrovat. Příklady naleznete v následující`@` tabulce (obvykle představuje kořenovou doménu):

| Příklad záznamu DNS | Hostitel TXT | Hodnota TXT |
| - | - | - |
| \@(kořen) | _awverify_ | _&lt;název aplikace>.azurewebsites.net_ |
| www (sub) | _awverify.www_ | _&lt;název aplikace>.azurewebsites.net_ |
| \*(zástupný znak) | _awverify.\*_ | _&lt;název aplikace>.azurewebsites.net_ |

Na stránce DNS záznamů si poznamenejte typ záznamu názvu DNS, který chcete migrovat. Služba App Service podporuje mapování ze záznamů CNAME a A.

> [!NOTE]
> Pro některé poskytovatele, jako `awverify.*` je CloudFlare, není platný záznam. Místo `*` toho použijte pouze.

> [!NOTE]
> Záznamy `*` se zástupnými symboly neověřují subdomény pomocí existujícího záznamu CNAME. Možná budete muset explicitně vytvořit záznam TXT pro každou subdoménu.


### <a name="enable-the-domain-for-your-app"></a>Povolení domény aplikace

Na [webu Azure Portal](https://portal.azure.com)v levém navigačním panelu aplikace vyberte **Vlastní domény**. 

![Nabídka Vlastní domény](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na stránce Vlastní domény vyberte **+** ikonu vedle **položky** **Přidat název hostitele**.

![Přidat název hostitele](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Zadejte plně kvalifikovaný název domény, pro který jste `www.contoso.com`přidali záznam TXT, například . Pro doménu se \*zástupnými symboly (například .contoso.com) můžete použít libovolný název DNS, který odpovídá doméně se zástupnými symboly. 

Vyberte **Ověřit**.

Aktivuje se tlačítko **Přidat název hostitele**. 

Ujistěte se, že **typ záznamu Hostname** je nastaven na typ záznamu DNS, který chcete migrovat.

Vyberte **Přidat název hostitele**.

![Přidání názvu DNS do aplikace](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Zobrazení nového názvu hostitele na stránce **Vlastní domény** aplikace může nějakou dobu trvat. Zkuste aktualizovat prohlížeč, aby se data aktualizovala.

![Přidaný záznam CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Váš vlastní název DNS je teď povolený v aplikaci Azure. 

## <a name="remap-the-active-dns-name"></a>Přemapování aktivního názvu DNS

Jediné, co zbývá udělat, je přemapování aktivního záznamu DNS tak, aby přecšlápne na službu App Service. Právě teď, to ještě ukazuje na vaše staré stránky.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Kopírování IP adresy aplikace (pouze záznam)

Pokud přemapujete záznam CNAME, tuto část přeskočte. 

Chcete-li přemapovat záznam A, potřebujete externí IP adresu aplikace App Service, která se zobrazí na stránce **Vlastní domény.**

Zavřete stránku **Přidat název hostitele** tak, že v pravém horním rohu **vyberete X.** 

Na stránce **Vlastní domény** zkopírujte IP adresu aplikace.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Aktualizace záznamu DNS

Zpět na stránce dns záznamů poskytovatele domény vyberte záznam DNS, který chcete přemapovat.

V `contoso.com` příkladu kořenové domény přemapujte záznam A nebo CNAME jako příklady v následující tabulce: 

| Příklad FQDN | Typ záznamu | Hostitel | Hodnota |
| - | - | - | - |
| contoso.com (kořen) | A | `@` | IP adresa z části [Zkopírování IP adresy aplikace](#info) |
| www\.contoso.com (sub) | CNAME | `www` | _&lt;název aplikace>.azurewebsites.net_ |
| \*.contoso.com (zástupný znak) | CNAME | _\*_ | _&lt;název aplikace>.azurewebsites.net_ |

Uložte nastavení.

Dotazy DNS by se měly začít řešit do aplikace App Service ihned po šíření DNS.

## <a name="active-domain-in-azure"></a>Aktivní doména v Azure

Aktivní vlastní doménu můžete migrovat v Azure, mezi předplatnými nebo v rámci stejného předplatného. Taková migrace bez prostojů však vyžaduje zdrojovou aplikaci a cílové aplikaci je v určitém okamžiku přiřazena stejná vlastní doména. Proto je třeba se ujistit, že dvě aplikace nejsou nasazeny do stejné jednotky nasazení (interně označované jako webový prostor). Název domény lze přiřadit pouze jedné aplikaci v každé jednotce nasazení.

Jednotku nasazení aplikace najdete tak, že se podíváte na `<deployment-unit>.ftp.azurewebsites.windows.net`název domény adresy URL FTP/S . Zkontrolujte a ujistěte se, že se jednotka nasazení liší mezi zdrojovou a cílovou aplikací. Jednotka nasazení aplikace je určena [plánem služby App Service,](overview-hosting-plans.md) ve které se nachází. Je vybrán náhodně Azure při vytváření plánu a nelze změnit. Azure pouze zajišťuje, že dva plány jsou ve stejné jednotce nasazení při [jejich vytvoření ve stejné skupině prostředků *a* ve stejné oblasti](app-service-plan-manage.md#create-an-app-service-plan), ale nemá žádnou logiku, aby se ujistil, že plány jsou v různých jednotkách nasazení. Jediným způsobem, jak vytvořit plán v jiné jednotce nasazení, je pokračovat ve vytváření plánu v nové skupině prostředků nebo oblasti, dokud nezískáte jinou jednotku nasazení.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak svázat vlastní certifikát TLS/SSL se službou App Service.

> [!div class="nextstepaction"]
> [Zabezpečení vlastního názvu DNS pomocí vazby TLS ve službě Azure App Service](configure-ssl-bindings.md)
