---
title: Migrace aktivního názvu DNS – Azure App Service | Microsoft Docs
description: Naučte se migrovat vlastní název domény DNS, který je už přiřazený k živému webu, abyste Azure App Service bez výpadků.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/28/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 703a151f801f65b968ecf93eaa97640c22a71bd2
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073092"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrace aktivního názvu DNS na Azure App Service

V tomto článku se dozvíte, jak migrovat aktivní název DNS do [Azure App Service](../app-service/overview.md) bez výpadků.

Když migrujete živý web a název domény DNS na App Service, tento název DNS už obsluhuje živý provoz. Během migrace se můžete vyhnout výpadkům v překladu názvů DNS tím, že provedete nemožnost svázat aktivní název DNS s aplikací App Service.

Pokud se vám Neobáváte o výpadkech v překladu DNS, přečtěte si téma [Mapování existujícího vlastního názvu DNS na Azure App Service](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Požadavky

Dokončení tohoto postupu:

- Ujistěte se [, že vaše aplikace App Service není na úrovni Free](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Propojte název domény současně.

Když propojíte vlastní doménu bez jakýchkoli změn, provedete tyto změny v záznamech DNS:

- Ověřit vlastnictví domény
- Povolit název domény pro vaši aplikaci

Když nakonec migrujete svůj vlastní název DNS z původního webu do aplikace App Service, nebudete mít v překladu DNS žádný výpadek.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Vytvořit záznam pro ověření domény

Chcete-li ověřit vlastnictví domény, přidejte záznam TXT. Záznam TXT se mapuje z _awverify.&lt; > subdomény_ do  _&lt;AppName >. azurewebsites. NET_. 

Záznam TXT, který potřebujete, závisí na záznamu DNS, který chcete migrovat. Příklady najdete v následující tabulce (`@` obvykle představuje kořenovou doménu):

| Příklad záznamu DNS | TXT Host | Hodnota TXT |
| - | - | - |
| \@zobrazuje | _awverify_ | _&lt;appname>.azurewebsites.net_ |
| Webová (sub) | _awverify.www_ | _&lt;appname>.azurewebsites.net_ |
| \*použity | _awverify.\*_ | _&lt;appname>.azurewebsites.net_ |

Na stránce záznamy DNS si poznamenejte typ záznamu názvu DNS, který chcete migrovat. App Service podporuje mapování ze záznamů CNAME a a.

> [!NOTE]
> Pro určité poskytovatele, jako je například CloudFlare `awverify.*` , není platný záznam. Použijte `*` pouze místo toho.

> [!NOTE]
> Zástupné `*` záznamy neověřují poddomény s existujícím záznamem CNAME. Možná budete muset explicitně vytvořit záznam TXT pro každou subdoménu.


### <a name="enable-the-domain-for-your-app"></a>Povolení domény pro vaši aplikaci

V [Azure Portal](https://portal.azure.com)v levém navigačním panelu na stránce aplikace vyberte **vlastní domény**. 

![Nabídka Vlastní domény](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na stránce **vlastní domény** vyberte **+** ikonu vedle **Přidat název hostitele**.

![Přidat název hostitele](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Zadejte plně kvalifikovaný název domény, pro který jste přidali záznam TXT pro, například `www.contoso.com`. V případě domény se zástupnými znaky (například \*. contoso.com) můžete použít libovolný název DNS, který odpovídá zástupné doméně. 

Vyberte **Ověřit**.

Aktivuje se tlačítko **Přidat název hostitele**. 

Ujistěte se, že **typ záznamu názvu hostitele** je nastavený na typ záznamu DNS, který chcete migrovat.

Vyberte **Přidat název hostitele**.

![Přidání názvu DNS do aplikace](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Zobrazení nového názvu hostitele na stránce **Vlastní domény** aplikace může nějakou dobu trvat. Zkuste aktualizovat prohlížeč, aby se data aktualizovala.

![Přidaný záznam CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Ve vaší aplikaci Azure je teď povolený vlastní název DNS. 

## <a name="remap-the-active-dns-name"></a>Přemapovat aktivní název DNS

Jediná věc, kterou je potřeba udělat, je přemapování aktivního záznamu DNS tak, aby odkazovalo na App Service. Nyní stále odkazuje na starý Web.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Zkopírujte IP adresu aplikace (jenom záznam).

Pokud přemapujete záznam CNAME, přeskočte tuto část. 

K přemapování záznamu A potřebujete externí IP adresu aplikace App Service, která se zobrazí na stránce **vlastní domény** .

Kliknutím na **X** v pravém horním rohu zavřete stránku **Přidat název hostitele** . 

Na stránce **Vlastní domény** zkopírujte IP adresu aplikace.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Aktualizace záznamu DNS

Zpátky na stránce záznamy DNS vašeho poskytovatele domény vyberte záznam DNS, který chcete přemapovat.

V případě příkladu kořenové domény přemapujte záznam a nebo CNAME jako příklady v následující tabulce: `contoso.com` 

| Příklad plně kvalifikovaného názvu domény | Typ záznamu | Host | Value |
| - | - | - | - |
| contoso.com (kořen) | A | `@` | IP adresa z části [Zkopírování IP adresy aplikace](#info) |
| Webová\.contoso.com (sub) | CNAME | `www` | _&lt;appname>.azurewebsites.net_ |
| \*. contoso.com (zástupný znak) | CNAME | _\*_ | _&lt;appname>.azurewebsites.net_ |

Uložte nastavení.

Dotazy DNS by se měly hned po šíření DNS začít řešit do vaší aplikace App Service.

## <a name="next-steps"></a>Další postup

Naučte se navazovat vlastní certifikát SSL na App Service.

> [!div class="nextstepaction"]
> [Vytvoření vazby existujícího vlastního certifikátu SSL ke službě Azure App Service](app-service-web-tutorial-custom-ssl.md)
