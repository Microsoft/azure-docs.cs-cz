---
title: Jak propojit předplatné Azure – Azure Active Directory B2C | Microsoft Docs
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
ms.openlocfilehash: 892f47b6acf22c62ce2290e2ede9d0bcd21eefc8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065899"
---
# <a name="link-an-azure-subscription-to-an-azure-active-directory-b2c-tenant"></a>Propojení předplatného Azure s klientem Azure Active Directory B2C

> [!IMPORTANT]
> Nejnovější informace o fakturaci využití a cenách pro Azure Active Directory B2C (Azure AD B2C) najdete v tématu [Azure AD B2C ceny](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

Účtují se poplatky za používání pro Azure AD B2C s předplatným Azure. Při vytvoření tenanta Azure AD B2C, musí správce klienta pro explicitní propojení tenanta Azure AD B2C s předplatným Azure. V tomto článku se dozvíte, jak.

> [!NOTE]
> Předplatné propojené s tenantem Azure AD B2C můžete použít pro fakturaci využití Azure AD B2C nebo další prostředky Azure, včetně dalších prostředků Azure AD B2C.  Nelze použít pro přidání další služby Azure na základě licence nebo licence Office 365 v rámci tenanta Azure AD B2C.

Odkaz pro předplatné se dosahuje vytvořením Azure AD B2C "prostředek" v rámci cílového předplatného Azure. Azure AD B2C mnoho "resources" se dají vytvořit v rámci jednoho předplatného Azure společně s další prostředky Azure (pro příklad, virtuální počítače, úložiště dat, LogicApps). Zobrazí se všechny prostředky v rámci předplatného tak, že přejdete do tenanta služby Azure AD, který je předplatné přidruženo.

Předplatná poskytovatele Cloud Solution Provider (CSP) jsou podporovaná v Azure AD B2C. Tato funkce je dostupná pomocí rozhraní API nebo Azure Portal pro Azure AD B2C a pro všechny prostředky Azure. Správci předplatného CSP můžou relace propojit, přesunout a odstranit pomocí Azure AD B2C stejným způsobem, jakým je to hotové pro všechny prostředky Azure. Správa Azure AD B2C pomocí řízení přístupu na základě role není ovlivněna přidružením mezi klientem Azure AD B2C a předplatným CSP Azure. Řízení přístupu založené na rolích se dosahuje pomocí rolí pro tenanta, nikoli rolí založených na předplatném.

Platné předplatné Azure, je potřeba pokračovat.

## <a name="create-an-azure-ad-b2c-tenant"></a>Vytvoření tenanta Azure AD B2C

Je nutné nejprve [vytvoření tenanta Azure AD B2C](active-directory-b2c-get-started.md) , že chcete propojit předplatné. Tento krok přeskočte, pokud jste již vytvořili tenanta Azure AD B2C.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Otevřít portál Azure portal v tenantovi Azure AD, který zobrazuje vaše předplatné Azure

Přejděte do tenanta služby Azure AD, který zobrazuje vaše předplatné Azure. Otevřít [webu Azure portal](https://portal.azure.com)a přepněte se do tenanta služby Azure AD, který zobrazuje předplatné Azure, které chcete použít.

![Přepnutí na tenanta Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Azure AD B2C můžete vyhledat v Azure Marketplace

Klikněte na tlačítko **vytvořit prostředek** tlačítko. Do pole **Hledat na Marketplace** zadejte `Active Directory B2C`.

![Snímek obrazovky portálu s Active Directory B2C v hledání na Marketplace](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

V seznamu výsledků vyberte **Azure AD B2C**.

![Azure AD B2C vybrali v seznamu výsledků](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Jsou uvedeny podrobnosti o Azure AD B2C. Pokud chcete začít konfigurovat nového tenanta Azure Active Directory B2C, klikněte na tlačítko **Vytvořit**.

V okně vytváření prostředku vyberte **Tenanta odkaz stávající službou Azure AD B2C s mým předplatným Azure**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Vytvoří prostředek služby Azure AD B2C v rámci předplatného Azure

V dialogové okno vytvoření prostředků vyberte z rozevíracího seznamu tenanta služby Azure AD B2C. Zobrazí se všechny tenanty, které jsou globální správce, kteří nejsou již propojený s předplatným.

Název prostředku Azure AD B2C se předem vybrali tak, aby odpovídaly název domény tenanta Azure AD B2C.

Pro předplatné vyberte aktivní předplatné Azure, která se na správce.

Vyberte skupinu prostředků a umístění skupiny prostředků. Zvolený nemá žádný vliv na umístění tenanta Azure AD B2C, výkon nebo stav fakturace.

![Stránka pro vytvoření prostředku Azure AD B2C v Azure Portal](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Správa prostředků tenanta Azure AD B2C

Po úspěšném vytvoření Azure AD B2C prostředků v rámci předplatného Azure, měli byste vidět nový prostředek typu "Tenanta B2C" Přidat vedle vašich prostředků Azure.

Můžete použít tento prostředek na:

- Přejděte na předplatné, které chcete zkontrolovat informace o fakturaci.
- Přejděte do svého tenanta Azure AD B2C
- Odeslání žádosti o podporu
- Přesunutí prostředku tenanta Azure AD B2C do jiného předplatného Azure nebo do jiné skupiny prostředků.

![Stránka nastavení prostředků B2C v Azure Portal](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.PNG)

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Změna předplatného fakturace klienta Azure AD B2C

Pokud zdrojové a cílové odběry existují v rámci stejného Azure Active Directory tenanta, můžete Azure AD B2C klienty přesunout do jiného předplatného.

Informace o tom, jak přesunout prostředky Azure jako tenanta Azure AD B2C do jiného předplatného, najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md).

Než začnete s přesunem, nezapomeňte si přečíst celý článek, abyste plně pochopili omezení a požadavky pro takové přesunutí. Kromě pokynů pro přesunutí prostředků obsahuje důležité informace, jako je například kontrolní seznam před přesunem a ověření operace přesunutí.

## <a name="known-issues"></a>Známé problémy

### <a name="self-imposed-restrictions"></a>Samoobslužné případným externím omezení

Uživatel může vytvořit místní omezení pro vytváření prostředků Azure. Toto omezení mohou zabránit vytvoření prostředků Azure AD B2C. Pokud chcete zmírnit, prosím toto omezení zmírnit.

## <a name="next-steps"></a>Další postup

Po dokončení pro jednotlivé tenanty Azure AD B2C tyto kroky se vaše předplatné Azure se účtuje v souladu s podrobnostmi o vašem Azure Direct nebo smlouvy Enterprise.

Využití a fakturace podrobnosti najdete ve vašem vybraném předplatném Azure. Můžete také zkontrolovat podrobné informace o použití – denně sestav pomocí [API pro vytváření sestav využití](active-directory-b2c-reference-usage-reporting-api.md).
