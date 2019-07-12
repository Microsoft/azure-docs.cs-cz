---
title: Jak propojit předplatné Azure – Azure Active Directory B2C | Dokumentace Microsoftu
description: Podrobný průvodce pro povolení fakturace pro tenanta Azure AD B2C do předplatného Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 01/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5cc6c8b4947f8c5ebeb27be2dc82f323f84a6902
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835419"
---
# <a name="link-an-azure-subscription-to-an-azure-active-directory-b2c-tenant"></a>Propojit předplatné Azure s tenantem Azure Active Directory B2C

> [!IMPORTANT]
> Nejnovější informace o využití, fakturaci a cenách pro Azure Active Directory (Azure AD) B2C, najdete v části [ceny Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

Účtují se poplatky za používání pro Azure AD B2C s předplatným Azure. Při vytvoření tenanta Azure AD B2C, musí správce klienta pro explicitní propojení tenanta Azure AD B2C s předplatným Azure. V tomto článku se dozvíte, jak.

> [!NOTE]
> Předplatné propojené s tenantem Azure AD B2C můžete použít pro fakturaci využití Azure AD B2C nebo další prostředky Azure, včetně dalších prostředků Azure AD B2C.  Nelze použít pro přidání další služby Azure na základě licence nebo licence Office 365 v rámci tenanta Azure AD B2C.

Odkaz pro předplatné se dosahuje vytvořením Azure AD B2C "prostředek" v rámci cílového předplatného Azure. Azure AD B2C mnoho "resources" se dají vytvořit v rámci jednoho předplatného Azure společně s další prostředky Azure (pro příklad, virtuální počítače, úložiště dat, LogicApps). Zobrazí se všechny prostředky v rámci předplatného tak, že přejdete do tenanta služby Azure AD, který je předplatné přidruženo.

Předplatná Azure poskytovatele CSP (Cloud Solution) jsou podporované v Azure AD B2C. Funkce je k dispozici prostřednictvím rozhraní API nebo na webu Azure portal pro Azure AD B2C a pro všechny prostředky Azure. Správci předplatného poskytovatele CSP můžete propojit, přesunout a odstranit relace s Azure AD B2C stejným způsobem, který se má provést pro všechny prostředky Azure. Správa Azure AD B2C prostřednictvím řízení přístupu na základě rolí není ovlivněn přidružení tenanta Azure AD B2C s předplatným Azure CSP. Řízení přístupu na základě rolí se dosahuje prostřednictvím využití tenanta základní role, role není na základě předplatného.

Platné předplatné Azure, je potřeba pokračovat.

## <a name="create-an-azure-ad-b2c-tenant"></a>Vytvoření tenanta Azure AD B2C

Je nutné nejprve [vytvoření tenanta Azure AD B2C](active-directory-b2c-get-started.md) , že chcete propojit předplatné. Tento krok přeskočte, pokud jste již vytvořili tenanta Azure AD B2C.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Otevřít portál Azure portal v tenantovi Azure AD, který zobrazuje vaše předplatné Azure

Přejděte do tenanta služby Azure AD, který zobrazuje vaše předplatné Azure. Otevřít [webu Azure portal](https://portal.azure.com)a přepněte se do tenanta služby Azure AD, který zobrazuje předplatné Azure, které chcete použít.

![Přepnutí na tenanta Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Azure AD B2C můžete vyhledat v Azure Marketplace

Klikněte na tlačítko **vytvořit prostředek** tlačítko. V **Hledat na Marketplace** zadejte `Active Directory B2C`.

![Portálu snímek obrazovky s "Active Directory B2C: ve vyhledávání na webu Marketplace](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

V seznamu výsledků vyberte **Azure AD B2C**.

![Azure AD B2C vybrali v seznamu výsledků](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Jsou uvedeny podrobnosti o Azure AD B2C. Pokud chcete začít konfigurovat nového tenanta Azure Active Directory B2C, klikněte na tlačítko **Vytvořit**.

V okně vytváření prostředku vyberte **Tenanta odkaz stávající službou Azure AD B2C s mým předplatným Azure**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Vytvoří prostředek služby Azure AD B2C v rámci předplatného Azure

V dialogové okno vytvoření prostředků vyberte z rozevíracího seznamu tenanta služby Azure AD B2C. Zobrazí se všechny tenanty, které jsou globální správce, kteří nejsou již propojený s předplatným.

Název prostředku Azure AD B2C se předem vybrali tak, aby odpovídaly název domény tenanta Azure AD B2C.

Pro předplatné vyberte aktivní předplatné Azure, která se na správce.

Vyberte skupinu prostředků a umístění skupiny prostředků. Zvolený nemá žádný vliv na umístění tenanta Azure AD B2C, výkon nebo stav fakturace.

![Stránce vytváření prostředku Azure AD B2C na webu Azure portal](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Správa prostředků tenanta Azure AD B2C

Po úspěšném vytvoření Azure AD B2C prostředků v rámci předplatného Azure, měli byste vidět nový prostředek typu "Tenanta B2C" Přidat vedle vašich prostředků Azure.

Můžete použít tento prostředek na:

- Přejděte na předplatné, které chcete zkontrolovat informace o fakturaci.
- Přejděte do svého tenanta Azure AD B2C
- Odeslání žádosti o podporu
- Přesunutí prostředku tenanta Azure AD B2C do jiného předplatného Azure nebo do jiné skupiny prostředků.

![Stránka nastavení prostředek B2C na webu Azure Portal](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.PNG)

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Změna tenanta Azure AD B2C fakturace předplatného

Klienty Azure AD B2C můžete přesunout do jiného předplatného, pokud zdrojové a cílové předplatné existuje ve stejném tenantovi Azure Active Directory.

Zjistěte, jak přesunout prostředky Azure, například vašeho tenanta Azure AD B2C do jiného předplatného, najdete v článku [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md).

Než spustíte funkci přesunu, nezapomeňte si přečíst celý článek, abyste úplně pochopili omezení a požadavky pro tyto přesunout. Kromě pokynů pro přesun prostředků obsahuje důležité informace, jako jsou kontrolní seznam před přesunutí a jak ověřovat v provádění operace přesunutí.

## <a name="known-issues"></a>Známé problémy

### <a name="self-imposed-restrictions"></a>Samoobslužné případným externím omezení

Uživatel může vytvořit místní omezení pro vytváření prostředků Azure. Toto omezení mohou zabránit vytvoření prostředků Azure AD B2C. Pokud chcete zmírnit, prosím toto omezení zmírnit.

## <a name="next-steps"></a>Další postup

Po dokončení pro jednotlivé tenanty Azure AD B2C tyto kroky se vaše předplatné Azure se účtuje v souladu s podrobnostmi o vašem Azure Direct nebo smlouvy Enterprise.

Využití a fakturace podrobnosti najdete ve vašem vybraném předplatném Azure. Můžete také zkontrolovat podrobné informace o použití – denně sestav pomocí [API pro vytváření sestav využití](active-directory-b2c-reference-usage-reporting-api.md).
