---
title: Migrovat název DNS active do služby Azure App Service | Microsoft Docs
description: Zjistěte, jak migrovat vlastní název domény DNS, který je již přiřazen k lokalitě v za provozu do služby Azure App Service bez odstávky.
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
ms.openlocfilehash: cd04be2046a23901471cb7bd0da9e0ed2d514d0d
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2018
ms.locfileid: "27566487"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrovat název DNS active do služby Azure App Service

Tento článek ukazuje, jak migrovat active názvu DNS, aby [Azure App Service](../app-service/app-service-web-overview.md) bez odstávky.

Když provádíte migraci za provozu lokality a jeho název domény DNS do služby App Service, tento název DNS je již obsluhující provoz za provozu. Výpadek při překlad DNS během migrace vyhnete tak, že ho preventivně vazby active název DNS do aplikace služby App Service.

Pokud si nejste obavy o výpadek při překlad názvů DNS, najdete v části [mapovat existující vlastní název DNS pro službu Azure Web Apps](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li provést tento postup:

- [Ujistěte se, že aplikaci aplikační služby není v úrovni FREE](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Název domény ho preventivně vazby

Pokud jste ho preventivně vytvořit vazbu vlastní doménu, musíte provést obě tyto před provedením jakýchkoli změn se záznamy DNS:

- Ověřit vlastnictví domény
- Povolit název domény pro vaši aplikaci.

Při migraci nakonec váš vlastní název DNS ze staré lokality do aplikace služby App Service, budou v překlad DNS existovat nedojde k žádnému výpadku.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Vytvořit záznam ověření domény

Pokud chcete ověřit vlastnictví domény, přidejte záznam TXT. Záznam TXT mapuje z _awverify.&lt; subdoména >_ k  _&lt;appname >. azurewebsites.net_. 

Záznam TXT, které potřebujete, závisí na záznam DNS, kterou chcete migrovat. Příklady, najdete v následující tabulce (`@` obvykle představuje kořenové domény):

| Příklad záznamu DNS | TXT hostitele | Hodnota TXT |
| - | - | - |
| @ (uživatel root) | _awverify_ | _&lt;AppName >. azurewebsites.net_ |
| WWW (pod) | _awverify.www_ | _&lt;AppName >. azurewebsites.net_ |
| \*(zástupný znak) | _awverify.\*_ | _&lt;AppName >. azurewebsites.net_ |

Na stránce záznamy DNS Všimněte si, typ záznamu DNS název, který chcete migrovat. App Service podporuje mapování z CNAME a záznamy.

### <a name="enable-the-domain-for-your-app"></a>Povolit domény pro vaši aplikaci.

V [portál Azure](https://portal.azure.com), v levém navigačním panelu stránku aplikace, vyberte **vlastní domény**. 

![Nabídky vlastní doménu.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

V **vlastní domény** vyberte  **+**  ikonu vedle **přidat název hostitele**.

![Přidat název hostitele](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Zadejte název plně kvalifikované domény přidat záznam TXT, jako například `www.contoso.com`. Pro doménu zástupných znaků (například \*. contoso.com), můžete použít libovolný název DNS, který odpovídá zástupné domény. 

Vyberte **ověření**.

**Přidat název hostitele** se aktivuje tlačítko. 

Ujistěte se, že **typ záznamu Hostname** nastavená na typ záznamu DNS, kterou chcete migrovat.

Vyberte **přidat název hostitele**.

![Přidání názvu DNS do aplikace](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Může trvat nějakou dobu, než nový název hostitele v aplikaci projeví **vlastní domény** stránky. Aktualizujte prohlížeč aktualizovat data.

![Přidat záznam CNAME](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Váš vlastní název DNS je teď povolené v aplikaci Azure. 

## <a name="remap-the-active-dns-name"></a>Přemapování active název DNS

Pouze vlevo věc udělat je přemapování vaše active záznam DNS tak, aby odkazoval na službu App Service. Pravé teď stále odkazuje na vaše staré lokality.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Zkopírujte adresu IP aplikace (jenom záznam)

Pokud jsou přemapování záznam CNAME, tuto část přeskočte. 

Přemapování záznam A, musíte aplikace App Service externí IP adresu, která se zobrazí v **vlastní domény** stránky.

Zavřít **přidat název hostitele** stránky tak, že vyberete **X** v pravém horním rohu. 

V **vlastní domény** stránky, zkopírujte aplikace IP adresu.

![Portál navigace do aplikace Azure](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Aktualizace záznamu DNS

Zpět na stránce záznamy DNS vaší domény poskytovatele, vyberte záznam DNS, který chcete přemapovat.

Pro `contoso.com` kořen domény příklad, přemapovat záznam CNAME nebo A jako příklady v následující tabulce: 

| Příklad plně kvalifikovaný název domény | Typ záznamu | Hostitel | Hodnota |
| - | - | - | - |
| contoso.com (uživatel root) | A | `@` | IP adresu z [zkopírujte aplikace IP adresu](#info) |
| www.contoso.com (pod) | CNAME | `www` | _&lt;AppName >. azurewebsites.net_ |
| \*. contoso.com (zástupný znak) | CNAME | _\*_ | _&lt;AppName >. azurewebsites.net_ |

Uložte nastavení.

Dotazy DNS by se měl spustit řešení do aplikace služby App Service hned se stane šíření záznamů DNS.

## <a name="next-steps"></a>Další postup

Naučte se vytvořit vazbu vlastní certifikát SSL služby App Service.

> [!div class="nextstepaction"]
> [Vytvoření vazby stávající vlastní certifikát SSL pro službu Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
