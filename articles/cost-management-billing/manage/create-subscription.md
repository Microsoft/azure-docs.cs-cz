---
title: Vytvoření dalšího předplatného Azure
description: Naučte se, jak na webu Azure Portal přidat nové předplatné Azure. Projděte si informace o formulářích fakturačního účtu a další dostupné zdroje informací.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 006c995217ced91690798ae68f04e3b6c3148228
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131951"
---
# <a name="create-an-additional-azure-subscription"></a>Vytvoření dalšího předplatného Azure

Na webu Azure Portal můžete vytvořit další předplatné pro svůj fakturační účet pro [smlouvu Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [smlouvu se zákazníkem Microsoftu](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) nebo [smlouvu s partnerem Microsoftu](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement). Můžete chtít další předplatné, abyste se vyhnuli omezením předplatného, vytvořili samostatná prostředí pro účely zabezpečení nebo izolovali data z důvodů dodržování předpisů.

Pokud máte fakturační účet programu Microsoft Online Service (MOSP), můžete vytvářet další předplatná na [registračním portálu Azure](https://account.azure.com/signup?offer=ms-azr-0003p).

Další informace o fakturačních účtech a určení typu fakturačního účtu najdete v tématu [Zobrazení fakturačních účtů na webu Azure Portal](view-all-accounts.md).

## <a name="permission-required-to-create-azure-subscriptions"></a>Oprávnění požadovaná k vytváření předplatných Azure

K vytváření předplatných potřebujete následující oprávnění:

|Fakturační účet  |Oprávnění  |
|---------|---------|
|Smlouva Enterprise (EA) |  Role Vlastník účtu pro registraci smlouvy Enterprise Další informace najdete v tématu [Principy rolí pro správu smlouvy Azure Enterprise v Azure](understand-ea-roles.md).    |
|Smlouva se zákazníkem Microsoftu (MCA) |  Role vlastníka nebo přispěvatele oddílu faktury, fakturačního profilu nebo fakturačního účtu Případně role Tvůrce předplatného Azure u oddílu faktury  Další informace najdete v tématu [Role a úlohy související s fakturací předplatného](understand-mca-roles.md#subscription-billing-roles-and-tasks).    |
|Smlouva s partnerem Microsoftu |   Role Globální správce nebo Agent správy v partnerské organizaci CSP Další informace najdete v tématu [Partnerské centrum – Přiřazování uživatelských rolí a oprávnění](/partner-center/permissions-overview).  Pro vytvoření předplatného Azure se musí uživatel přihlásit k tenantu partnera.   |

## <a name="create-a-subscription-in-the-azure-portal"></a>Vytvoření předplatného na webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Předplatná**.

   ![Snímek obrazovky, který zobrazuje hledání předplatného na portálu](./media/create-subscription/billing-search-subscription-portal.png)

1. Vyberte **Přidat**.

   ![Snímek obrazovky, který zobrazuje tlačítko Přidat v zobrazení Předplatná](./media/create-subscription/subscription-add.png)

1. Pokud máte přístup k více fakturačním účtům, vyberte fakturační účet, pro který chcete předplatné vytvořit.

1. Vyplňte formulář a klikněte na **Vytvořit**. V následujících tabulkách jsou uvedená pole formuláře pro jednotlivé typy fakturačních účtů.

**Smlouva Enterprise**

|Pole  |Definice  |
|---------|---------|
|Název     | Zobrazovaný název umožňující snadnou identifikaci předplatného na webu Azure Portal  |
|Nabídka     | Pokud chcete toto předplatné používat pro účely vývoje a testování úloh, vyberte nabídku EA pro vývoj a testování, jinak použijte nabídku Microsoft Azure Enterprise. Aby registrační účet mohl vytvářet předplatná EA pro vývoj a testování, je potřeba povolit nabídku Vývoj/testování.|

**Smlouva se zákazníkem Microsoftu**

|Pole  |Definice  |
|---------|---------|
|Fakturační profil     | Na vybraný fakturační profil se budou účtovat poplatky za vaše předplatné. Pokud máte přístup pouze k jednomu fakturačnímu profilu, možnost výběru bude neaktivní.     |
|Oddíl faktury     | V tomto oddílu faktury pro fakturační profil se zobrazí poplatky za vaše předplatné. Pokud máte přístup pouze k jednomu oddílu faktury, možnost výběru bude neaktivní.  |
|Plánování     | Pokud chcete toto předplatné používat pro účely vývoje a testování úloh, vyberte Plán Azure pro vývoj a testování, jinak použijte Plán Microsoft Azure. Pokud je pro fakturační profil povolený pouze jeden plán, možnost výběru bude neaktivní.  |
|Název     | Zobrazovaný název umožňující snadnou identifikaci předplatného na webu Azure Portal  |

**Smlouva s partnerem Microsoftu**

|Pole  |Definice  |
|---------|---------|
|Zákazník    | Předplatné se vytvoří pro vybraného zákazníka. Pokud máte pouze jednoho zákazníka, možnost výběru bude neaktivní.  |
|Prodejce    | Prodejce, který bude poskytovat služby zákazníkovi. Toto pole je volitelné a vztahuje se pouze na nepřímé prodejce ve dvouúrovňovém modelu CSP. |
|Název     | Zobrazovaný název umožňující snadnou identifikaci předplatného na webu Azure Portal  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>Programové vytvoření dalšího předplatného Azure

Můžete také vytvořit další předplatná programově. Další informace najdete v tématu [Programové vytváření předplatných Azure](programmatically-create-subscription.md).

## <a name="next-steps"></a>Další kroky

- [Přidávání a změna správců předplatného Azure](add-change-subscription-administrator.md)
- [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Vytvoření skupin pro správu pro organizaci a správu prostředků](../../governance/management-groups/create-management-group-portal.md)
- [Zrušení předplatného pro Azure](cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).