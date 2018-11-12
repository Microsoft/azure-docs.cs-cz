---
title: Migrace aktivního názvu DNS do Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak migrovat vlastní název domény DNS, který je už přiřazený k živého webu do Azure App Service bez jakýchkoli prostojů.
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
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: cephalin
ms.openlocfilehash: 632558760d0b4a00384763cc30de3b9241bb1ae6
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300162"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrace aktivního názvu DNS do Azure App Service

Tento článek popisuje, jak migrace aktivního názvu DNS na [služby Azure App Service](../app-service/app-service-web-overview.md) bez jakýchkoli prostojů.

Když migrujete živého webu a jeho název domény DNS do služby App Service, tento název DNS už obsluhuje živé přenosy. Pomocí preventivně vazby aktivního názvu DNS do aplikace služby App Service se můžete vyhnout výpadek při překladu DNS během migrace.

Pokud si nejste obavy o výpadku v překlad názvů DNS, přečtěte si téma [mapování existujícího vlastního názvu DNS na Azure Web Apps](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Požadavky

Dokončete tento postup:

- [Ujistěte se, že vaše aplikace app Service není v bezplatné úrovni](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Vytvoření vazby názvu domény preventivně

Když se navážete preventivně vlastní doménu, můžete provést z následujících možností před provedením jakýchkoli změn svoje záznamy DNS:

- Ověřit vlastnictví domény
- Povolit název domény pro vaši aplikaci

Při migraci nakonec vlastní název DNS ze staré lokality aplikace služby App Service, nebudou nedojde k žádnému výpadku v překlad DNS.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Vytvoření záznamu o ověření domény

Pokud chcete ověřit vlastnictví domény, přidejte záznam TXT. Záznam TXT mapuje z _awverify.&lt; subdoména >_ k  _&lt;název_aplikace >. azurewebsites.net_. 

Záznam TXT, které potřebujete, závisí na záznam DNS, které chcete migrovat. Příklady najdete v následující tabulce (`@` obvykle představuje kořenovou doménu):

| Příklad záznamu DNS | Hostitel TXT | Hodnota TXT |
| - | - | - |
| \@ (uživatel root) | _awverify_ | _&lt;název_aplikace >. azurewebsites.net_ |
| WWW (sub) | _awverify.www_ | _&lt;název_aplikace >. azurewebsites.net_ |
| \* (zástupný znak) | _awverify.\*_ | _&lt;název_aplikace >. azurewebsites.net_ |

Na stránce záznamy DNS Všimněte si typ záznamu názvu DNS, které chcete migrovat. App Service podporuje mapování CNAME záznamy a A.

> [!NOTE]
> U určitých poskytovatelů, jako je například CloudFlare, `awverify.*` není platný záznam. Použití `*` pouze místo.

> [!NOTE]
> Zástupný znak `*` záznamy neověří subdomény s existující CNAME záznamu. Musíte explicitně vytvořit záznam TXT pro každá z nich.


### <a name="enable-the-domain-for-your-app"></a>Povolit doménu pro vaši aplikaci

V [webu Azure portal](https://portal.azure.com), v levém navigačním panelu na stránce aplikace vyberte **vlastní domény**. 

![Nabídka Vlastní domény](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

V **vlastní domény** stránky, vyberte **+** ikonu vedle **přidat název hostitele**.

![Přidat název hostitele](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Zadejte plně kvalifikovaný název, že jste přidali záznam TXT, jako například `www.contoso.com`. Pro zástupné doméně (například \*. contoso.com), můžete použít libovolný název DNS, který odpovídá zástupné doméně. 

Vyberte **Ověřit**.

Aktivuje se tlačítko **Přidat název hostitele**. 

Ujistěte se, že **typ záznamu názvu hostitele** nastavená na typ záznamu DNS, které chcete migrovat.

Vyberte **Přidat název hostitele**.

![Přidání názvu DNS do aplikace](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Zobrazení nového názvu hostitele na stránce **Vlastní domény** aplikace může nějakou dobu trvat. Zkuste aktualizovat prohlížeč, aby se data aktualizovala.

![Přidaný záznam CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Vlastní název DNS je nyní povoleno v aplikaci Azure. 

## <a name="remap-the-active-dns-name"></a>Přemapování aktivního názvu DNS

Jen vlevo věc, kterou provedete je přemapování aktivní záznam DNS tak, aby odkazoval do služby App Service. Pravé teď stále odkazuje na starý Web.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Zkopírujte IP adresu aplikace (pouze záznam)

Pokud jsou přemapování záznamu CNAME, tuto část přeskočte. 

Přemapování záznamu A potřebujete aplikace služby App Service externí IP adresu, která je znázorněna v **vlastní domény** stránky.

Zavřít **přidat název hostitele** stránky tak, že vyberete **X** v pravém horním rohu. 

Na stránce **Vlastní domény** zkopírujte IP adresu aplikace.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Aktualizujte záznam DNS

Zpět v bude stránka záznamů DNS vašeho poskytovatele domény vyberte záznam DNS, který chcete přemapovat.

Pro `contoso.com` kořenové domény příklad, přemapujte záznam A nebo CNAME jako příklady v následující tabulce: 

| Příklad plně kvalifikovaný název domény | Typ záznamu | Hostitel | Hodnota |
| - | - | - | - |
| contoso.com (kořen) | A | `@` | IP adresa z části [Zkopírování IP adresy aplikace](#info) |
| www.contoso.com (sub) | CNAME | `www` | _&lt;název_aplikace >. azurewebsites.net_ |
| \*. contoso.com (zástupný znak) | CNAME | _\*_ | _&lt;název_aplikace >. azurewebsites.net_ |

Uložte nastavení.

Dotazy DNS by měl spustit okamžitě poté, co se stane, šíření DNS překládá na vaše aplikace app Service.

## <a name="next-steps"></a>Další postup

Zjistěte, jak vytvořit vazbu vlastního certifikátu SSL služby App Service.

> [!div class="nextstepaction"]
> [Vytvoření vazby existujícího vlastního certifikátu SSL k Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
