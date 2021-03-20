---
title: Migrace aktivního názvu DNS
description: Naučte se migrovat vlastní název domény DNS, který je už přiřazený k živému webu, abyste Azure App Service bez výpadků.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: e1b50675bef0f883ff617b3098a742d3491b3c13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89484290"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrace aktivního názvu DNS na Azure App Service

V tomto článku se dozvíte, jak migrovat aktivní název DNS do [Azure App Service](../app-service/overview.md) bez výpadků.

Když migrujete živý web a název domény DNS na App Service, tento název DNS už obsluhuje živý provoz. Během migrace se můžete vyhnout výpadkům v překladu názvů DNS tím, že provedete nemožnost svázat aktivní název DNS s aplikací App Service.

Pokud se vám Neobáváte o výpadkech v překladu DNS, přečtěte si téma [Mapování existujícího vlastního názvu DNS na Azure App Service](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Předpoklady

Dokončení tohoto postupu:

- Ujistěte se [, že vaše aplikace App Service není na úrovni Free](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Propojte název domény současně.

Když propojíte vlastní doménu, provedete obě následující kroky a teprve potom provedete všechny změny v existujících záznamech DNS:

- Ověření vlastnictví domény
- Povolit název domény pro vaši aplikaci

Když nakonec migrujete svůj vlastní název DNS z původního webu do aplikace App Service, nebudete mít v překladu DNS žádný výpadek.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="get-domain-verification-id"></a>Získat ID ověření domény

ID ověření domény pro vaši aplikaci získáte podle kroků uvedených v části [získání ID ověření domény](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id).

### <a name="create-domain-verification-record"></a>Vytvořit záznam pro ověření domény

Chcete-li ověřit vlastnictví domény, přidejte záznam TXT pro ověření domény. Název hostitele pro záznam TXT závisí na typu typu záznamu DNS, který chcete namapovat. Podívejte se na následující tabulku ( `@` obvykle představuje kořenovou doménu):

| Příklad záznamu DNS | Hostitel TXT | Hodnota TXT |
| - | - | - |
| \@ zobrazuje | _asuid_ | [ID ověření domény pro vaši aplikaci](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| Webová (sub) | _asuid. www_ | [ID ověření domény pro vaši aplikaci](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| \* použity | _asuid_ | [ID ověření domény pro vaši aplikaci](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |

Na stránce záznamy DNS si poznamenejte typ záznamu názvu DNS, který chcete migrovat. App Service podporuje mapování ze záznamů CNAME a a.

> [!NOTE]
> Zástupné `*` záznamy neověřují poddomény s existujícím záznamem CNAME. Možná budete muset explicitně vytvořit záznam TXT pro každou subdoménu.

### <a name="enable-the-domain-for-your-app"></a>Povolení domény pro vaši aplikaci

1. V [Azure Portal](https://portal.azure.com)v levém navigačním panelu na stránce aplikace vyberte **vlastní domény**. 

    ![Nabídka Vlastní domény](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Na stránce **vlastní domény** vyberte **Přidat vlastní doménu**.

    ![Přidat název hostitele](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Zadejte plně kvalifikovaný název domény, který chcete migrovat, který odpovídá vytvořenému záznamu TXT, například `contoso.com` , `www.contoso.com` nebo `*.contoso.com` . Vyberte **Ověřit**.

    Aktivuje se tlačítko **Přidat vlastní doménu** . 

1. Ujistěte se, že **typ záznamu názvu hostitele** je nastavený na typ záznamu DNS, který chcete migrovat. Vyberte **Přidat název hostitele**.

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

Na stránce **Vlastní domény** zkopírujte IP adresu aplikace.

![Přechod do aplikace Azure na portálu](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Aktualizace záznamu DNS

Zpátky na stránce záznamy DNS vašeho poskytovatele domény vyberte záznam DNS, který chcete přemapovat.

V případě `contoso.com` příkladu kořenové domény přemapujte záznam a nebo CNAME jako příklady v následující tabulce: 

| Příklad plně kvalifikovaného názvu domény | Typ záznamu | Hostitel | Hodnota |
| - | - | - | - |
| contoso.com (kořen) | A | `@` | IP adresa z části [Zkopírování IP adresy aplikace](#info) |
| Webová \. contoso.com (sub) | CNAME | `www` | _&lt;AppName>. azurewebsites.net_ |
| \*. contoso.com (zástupný znak) | CNAME | _\*_ | _&lt;AppName>. azurewebsites.net_ |

Uložte nastavení.

Dotazy DNS by se měly hned po šíření DNS začít řešit do vaší aplikace App Service.

## <a name="migrate-domain-from-another-app"></a>Migrovat doménu z jiné aplikace

Můžete migrovat aktivní vlastní doménu v Azure, mezi předplatnými nebo v rámci stejného předplatného. Taková migrace ale bez výpadků ale vyžaduje, aby se zdrojová aplikace a cílová aplikace v určitou dobu přiřadily k stejné vlastní doméně. Proto je nutné zajistit, aby tyto dvě aplikace nebyly nasazeny na stejné jednotce nasazení (interně označované jako webový prostor). Název domény se dá přiřadit jenom k jedné aplikaci v každé jednotce nasazení.

Jednotku nasazení pro vaši aplikaci najdete tak, že si vyhledáte název domény adresy URL FTP/S `<deployment-unit>.ftp.azurewebsites.windows.net` . Zkontrolujte a zajistěte, aby se jednotka nasazení v cílové aplikaci lišila od zdrojové aplikace. Jednotka nasazení aplikace je určena [plánem App Service](overview-hosting-plans.md) . Při vytváření plánu ho náhodně vybrala Azure a nedá se změnit. Azure jenom v případě, že je [vytvoříte ve stejné skupině prostředků *a* ve stejné oblasti](app-service-plan-manage.md#create-an-app-service-plan), jsou ve stejné jednotce nasazení pouze dva plány, ale nemá žádnou logiku, která by zajistila, že se plány nacházejí v různých jednotkách nasazení. Jediným způsobem, jak vytvořit plán v jiné jednotce nasazení, je zachovat plán v nové skupině nebo oblasti prostředků, dokud nezískáte jinou jednotku nasazení.

## <a name="next-steps"></a>Další kroky

Naučte se navazovat vlastní certifikát TLS/SSL na App Service.

> [!div class="nextstepaction"]
> [Zabezpečení vlastního názvu DNS s vazbou TLS v Azure App Service](configure-ssl-bindings.md)
