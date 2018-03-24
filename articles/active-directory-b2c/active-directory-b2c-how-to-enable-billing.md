---
title: Postup propojení předplatné Azure s Azure AD B2C | Microsoft Docs
description: Podrobný průvodce povolit fakturace pro klienta Azure AD B2C do předplatného Azure.
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 12/05/2017
ms.author: davidmu
ms.openlocfilehash: bb9324b01bb810ba15994612bac2ff20dc83ab82
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="linking-an-azure-subscription-to-an-azure-ad-b2c-tenant"></a>Propojování předplatné Azure klienta Azure AD B2C

> [!IMPORTANT]
> Nejnovější informace o využití fakturaci a cenách pro Azure AD B2C je na následující stránce: [cenová Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

K předplatnému Azure se účtují poplatky za používání pro Azure AD B2C. Při vytváření klienta služby Azure AD B2C, musí správce klienta explicitně propojení klienta Azure AD B2C k předplatnému Azure. Tento článek ukazuje, jak.

> [!NOTE]
> Předplatné propojené s klienta Azure AD B2C můžete použít pouze pro fakturaci využití Azure AD B2C. Odběr nelze použít k přidání další služby Azure nebo Office 365 licence *v rámci klienta Azure AD B2C*.

 Odkaz pro předplatné je dosaženo vytvořením Azure AD B2C "prostředek" v rámci cílové předplatné Azure. V rámci jednoho předplatného Azure, společně s dalším prostředkům služby Azure (pro virtuální počítače, například dat úložiště, LogicApps) můžete vytvořit mnoho Azure AD B2C "zdroje". Uvidíte všechny prostředky v rámci předplatného na klienta služby Azure AD, který je předplatné přidružené k webu.

Chcete-li pokračovat, je potřeba platné předplatné Azure.

## <a name="create-an-azure-ad-b2c-tenant"></a>Vytvoření tenanta Azure AD B2C

Je nutné nejprve [vytvoření klienta Azure AD B2C](active-directory-b2c-get-started.md) , které chcete propojit předplatné. Tento krok přeskočte, pokud jste již vytvořili klienta Azure AD B2C.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Otevřete portál Azure v klientovi Azure AD, který ukazuje vašeho předplatného Azure

Přejděte do klienta Azure AD, který ukazuje vašeho předplatného Azure. Otevřete [portál Azure](https://portal.azure.com)a přejděte na klienta Azure AD, který ukazuje předplatné Azure, které chcete použít.

![Přepnutí na klientovi Azure AD](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Najít Azure AD B2C v Azure Marketplace

Klikněte **vytvořit prostředek** tlačítko. Do pole **Hledat na Marketplace** zadejte `B2C`.

![Přidejte tlačítkem a text Azure AD B2C ve vyhledávání pole marketplace.](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

V seznamu výsledků vyberte **Azure AD B2C**.

![Azure AD B2C vybraného v seznamu výsledků](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Jsou uvedeny podrobnosti o Azure AD B2C. Pokud chcete začít konfigurovat nového tenanta Azure Active Directory B2C, klikněte na tlačítko **Vytvořit**.

Na obrazovce vytváření prostředků vyberte **odkaz existující Azure AD B2C klienta pro Moje předplatné**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Vytvořit prostředek služby Azure AD B2C v rámci předplatného Azure

V dialogovém okně vytváření prostředků vyberte z rozevíracího seznamu klienta Azure AD B2C. Zobrazí se všechny klienty, kteří jsou globální správce a ty, které již nejsou připojeny k odběru.

Název prostředku Azure AD B2C bude být předvoleno podle názvu domény klienta Azure AD B2C.

Pro předplatné vyberte aktivní předplatné Azure, které jsou oprávnění správce.

Vyberte skupinu prostředků a umístění skupiny prostředků. Výběr zde nemá žádný vliv na umístění klienta Azure AD B2C, výkon nebo stav fakturace.

![Vytvořte prostředek B2C](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Spravovat prostředky klienta Azure AD B2C

Po úspěšném vytvoření prostředek služby Azure AD B2C v rámci předplatného Azure, měli byste vidět nový prostředek typu "Klienta B2C" přidat spolu s vašich prostředků Azure.

Můžete použít tento prostředek na:

- Přejděte na předplatné si projděte fakturační informace.
- Přejděte ke klientovi Azure AD B2C
- Odeslání žádosti o podporu
- Přesunutí prostředku klienta Azure AD B2C, do jiného předplatného Azure nebo do jiné skupiny prostředků.

![Nastavení prostředků B2C](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Známé problémy

### <a name="csp-subscriptions"></a>Odběry zprostředkovatele kryptografických služeb

V současné době klienta Azure AD B2C **nelze** odkaz na předplatná CSP.

### <a name="self-imposed-restrictions"></a>Samoobslužné uložena omezení

Uživatel může vytvořit místní omezení pro vytváření prostředků Azure. Toto omezení by mohlo zabránit vytvoření prostředku Azure AD B2C. Pokud chcete zmírnit, prosím toto omezení zmírnit.

## <a name="next-steps"></a>Další postup

Po dokončení pro jednotlivé klienty Azure AD B2C těchto kroků se vašeho předplatného Azure se fakturuje v souladu s podrobností o Azure přímý nebo smlouvu Enterprise.

Využití a fakturačních údajů můžete zkontrolovat ve vašem vybraném předplatném Azure. Můžete rovněž zkontrolovat podrobné informace o použití ve dnech sestav pomocí [využití reporting rozhraní API](active-directory-b2c-reference-usage-reporting-api.md).
