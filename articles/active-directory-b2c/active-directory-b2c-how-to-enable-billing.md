---
title: Jak propojit předplatné Azure s Azure Active Directory B2C | Dokumentace Microsoftu
description: Podrobný průvodce pro povolení fakturace pro tenanta Azure AD B2C do předplatného Azure.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 12/07/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e39b7472904e7635340327d311eb7d4b9123f51e
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853183"
---
# <a name="linking-an-azure-subscription-to-an-azure-ad-b2c-tenant"></a>Propojení s předplatným Azure s tenantem Azure AD B2C

> [!IMPORTANT]
> Nejnovější informace o využití, fakturaci a cenách pro Azure AD B2C se na následující stránce: [Azure AD B2C Pricing](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

Účtují se poplatky za používání pro Azure AD B2C s předplatným Azure. Při vytvoření tenanta Azure AD B2C, musí správce klienta pro explicitní propojení tenanta Azure AD B2C s předplatným Azure. V tomto článku se dozvíte, jak.

> [!NOTE]
> Předplatné propojené s tenantem Azure AD B2C můžete použít pro fakturaci využití Azure AD B2C nebo další prostředky Azure, včetně dalších prostředků Azure AD B2C.  Nelze použít pro přidání další služby Azure na základě licence nebo licence Office 365 v rámci tenanta Azure AD B2C.

 Odkaz pro předplatné se dosahuje vytvořením Azure AD B2C "prostředek" v rámci cílového předplatného Azure. Azure AD B2C mnoho "resources" se dají vytvořit v rámci jednoho předplatného Azure společně s další prostředky Azure (pro příklad, virtuální počítače, úložiště dat, LogicApps). Zobrazí se všechny prostředky v rámci předplatného tak, že přejdete do tenanta služby Azure AD, který je předplatné přidruženo.

Platné předplatné Azure, je potřeba pokračovat.

## <a name="create-an-azure-ad-b2c-tenant"></a>Vytvoření tenanta Azure AD B2C

Je nutné nejprve [vytvoření tenanta Azure AD B2C](active-directory-b2c-get-started.md) , že chcete propojit předplatné. Tento krok přeskočte, pokud jste již vytvořili tenanta Azure AD B2C.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Otevřít portál Azure portal v tenantovi Azure AD, který zobrazuje vaše předplatné Azure

Přejděte do tenanta služby Azure AD, který zobrazuje vaše předplatné Azure. Otevřít [webu Azure portal](https://portal.azure.com)a přepněte se do tenanta služby Azure AD, který zobrazuje předplatné Azure, které chcete použít.

![Přepnutí na tenanta Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Azure AD B2C můžete vyhledat v Azure Marketplace

Klikněte na tlačítko **vytvořit prostředek** tlačítko. Do pole **Hledat na Marketplace** zadejte `B2C`.

![Zvýrazněné tlačítko Přidat a text Azure AD B2C do vyhledávacího pole marketplace](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

V seznamu výsledků vyberte **Azure AD B2C**.

![Azure AD B2C vybrali v seznamu výsledků](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Jsou uvedeny podrobnosti o Azure AD B2C. Pokud chcete začít konfigurovat nového tenanta Azure Active Directory B2C, klikněte na tlačítko **Vytvořit**.

V okně vytváření prostředku vyberte **Tenanta odkaz stávající službou Azure AD B2C s mým předplatným Azure**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Vytvoří prostředek služby Azure AD B2C v rámci předplatného Azure

V dialogové okno vytvoření prostředků vyberte z rozevíracího seznamu tenanta služby Azure AD B2C. Zobrazí se všechny tenanty, které jsou globální správce, kteří nejsou již propojený s předplatným.

Název prostředku Azure AD B2C se předem vybrali tak, aby odpovídaly název domény tenanta Azure AD B2C.

Pro předplatné vyberte aktivní předplatné Azure, která se na správce.

Vyberte skupinu prostředků a umístění skupiny prostředků. Zvolený nemá žádný vliv na umístění tenanta Azure AD B2C, výkon nebo stav fakturace.

![Vytvořit prostředek B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Správa prostředků tenanta Azure AD B2C

Po úspěšném vytvoření Azure AD B2C prostředků v rámci předplatného Azure, měli byste vidět nový prostředek typu "Tenanta B2C" Přidat vedle vašich prostředků Azure.

Můžete použít tento prostředek na:

- Přejděte na předplatné, které chcete zkontrolovat informace o fakturaci.
- Přejděte do svého tenanta Azure AD B2C
- Odeslání žádosti o podporu
- Přesunutí prostředku tenanta Azure AD B2C do jiného předplatného Azure nebo do jiné skupiny prostředků.

![Nastavení prostředek B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Známé problémy

### <a name="csp-subscriptions"></a>Předplatná CSP

V současné době tenanta služby Azure AD B2C **nelze** odkaz na předplatná CSP.

### <a name="self-imposed-restrictions"></a>Samoobslužné případným externím omezení

Uživatel může vytvořit místní omezení pro vytváření prostředků Azure. Toto omezení mohou zabránit vytvoření prostředků Azure AD B2C. Pokud chcete zmírnit, prosím toto omezení zmírnit.

## <a name="next-steps"></a>Další postup

Po dokončení pro jednotlivé tenanty Azure AD B2C tyto kroky se vaše předplatné Azure se účtuje v souladu s podrobnostmi o vašem Azure Direct nebo smlouvy Enterprise.

Využití a fakturace podrobnosti najdete ve vašem vybraném předplatném Azure. Můžete také zkontrolovat podrobné informace o použití – denně sestav pomocí [API pro vytváření sestav využití](active-directory-b2c-reference-usage-reporting-api.md).
