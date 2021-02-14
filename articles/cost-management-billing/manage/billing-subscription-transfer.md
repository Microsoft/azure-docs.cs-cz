---
title: Převod vlastnictví fakturace předplatného Azure
description: Popisuje, jak převést vlastnictví fakturace předplatného Azure na jiný účet.
keywords: převod předplatného azure, azure převod předplatného, přesun předplatného azure do jiného účtu, azure změna vlastníka předplatného, převod předplatného azure na jiný účet, azure převod fakturace
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 02/05/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 2fb1a8c3e583a4bdc88f2b61844e9bcb16dc7cdf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367193"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Převod vlastnictví fakturace předplatného Azure na jiný účet

Tento článek ukazuje kroky, které je potřeba provést k převodu vlastnictví fakturace předplatného Azure na jiný účet. Před převodem vlastnictví fakturace předplatného si přečtěte [informace o převodu vlastnictví fakturace předplatného Azure](../understand/subscription-transfer.md).

Pokud chcete vlastnictví fakturace zachovat, ale chcete změnit typ předplatného, přečtěte si téma [Přepnutí předplatného Azure na jinou nabídku](switch-azure-offer.md). Pokud chcete řídit, kdo má přístup k prostředkům v předplatném, přečtěte si téma věnované [předdefinovaným rolím v Azure](../../role-based-access-control/built-in-roles.md).

Pokud jste zákazníkem se smlouvou Enterprise (EA), může váš podnikový správce převádět vlastnictví fakturace vašich předplatných mezi účty. Další informace najdete v části [Změna vlastnictví účtu nebo předplatného Azure](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

Vlastnictví předplatného může převést pouze správce fakturace účtu.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Převod vlastnictví fakturace předplatného Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako správce fakturačního účtu obsahujícího předplatné, které chcete převést. Pokud si nejste jistí, jestli jste správce, nebo pokud potřebujete zjistit, kdo je správce, projděte si část [Určení správce fakturace účtu](../understand/subscription-transfer.md#whoisaa).
1. Vyhledejte **Cost Management a fakturace**.  
   ![Snímek obrazovky znázorňující hledání na webu Azure Portal](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)
1. V levém podokně vyberte **Předplatná**. V závislosti na vašem přístupu možná budete muset vybrat rozsah fakturace a pak **Předplatná** nebo **Předplatná Azure**.
1. U předplatného, které chcete převést, vyberte **Převést vlastnictví fakturace**.  
   ![Výběr předplatného pro převod](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)
1. Zadejte e-mailovou adresu uživatele, který je správcem fakturace účtu, který bude novým vlastníkem předplatného.
1. Pokud předplatné převádíte na účet v jiném tenantovi Azure AD, vyberte, jestli chcete předplatné do tenanta nového účtu přesunout. Další informace najdete v tématu [Převod předplatného na účet v jiném tenantovi Azure AD](#transfer-a-subscription-to-another-azure-ad-tenant-account).
    > [!IMPORTANT]
    > Pokud se rozhodnete přesunout předplatné do tenanta Azure AD nového účtu, všechna [přiřazení rolí Azure](../../role-based-access-control/role-assignments-portal.md) pro přístup k prostředkům v předplatném se trvale odstraní. Přístup ke správě prostředků v předplatném bude mít pouze uživatel v novém účtu, který přijme žádost o převod. Případně můžete zrušit zaškrtnutí políčka u možnosti **Tenant Azure AD předplatného**, abyste vlastnictví fakturace převedli bez přesunu předplatného do tenanta nového účtu. Pokud to uděláte, zachovají se stávající přiřazení rolí Azure pro přístup k prostředkům Azure.  
    ![Stránka odeslání žádosti o převod](./media/billing-subscription-transfer/billing-send-transfer-request.png)
1. Vyberte možnost **Poslat žádost o převod**.
1. Tento uživatel obdrží e-mail s pokyny ke kontrole vaší žádosti o převod.  
   ![E-mail pro převod předplatného odeslaný příjemci](./media/billing-subscription-transfer/billing-receiver-email.png)
1. Uživatel může žádost o převod schválit tím, že vybere odkaz v e-mailu a bude postupovat podle zobrazených pokynů. Uživatel potom zvolí způsob platby, který se bude pro platbu za předplatné používat. Pokud uživatel nemá účet Azure, musí si zaregistrovat nový účet.  
   ![První webová stránka převodu předplatného](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)
   ![Druhá webová stránka převodu předplatného](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)
   ![Třetí webová stránka převodu předplatného](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)
1. Výborně! Předplatné je teď převedené.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>Převod předplatného na jiný účet tenanta Azure AD

Tenant Azure Active Directory (AD) se vám vytvoří při registraci do Azure. Tenant představuje váš účet. Tenant se používá ke správě přístupu k předplatným a prostředkům.

Když vytvoříte nové předplatné, hostuje se v tenantovi Azure AD vašeho účtu. Pokud chcete přístup k vašemu předplatnému nebo jeho prostředkům poskytnout jiným uživatelům, musíte je pozvat, aby se připojili k vašemu tenantovi. Takto můžete řídit přístup k předplatným a prostředkům.

Když převádíte vlastnictví fakturace vašeho předplatného na účet v jiném tenantovi Azure AD, můžete předplatné do tenanta nového účtu přesunout. Pokud to uděláte, všichni uživatelé, skupiny nebo instanční objekty, které měly [přiřazení rolí Azure](../../role-based-access-control/role-assignments-portal.md) pro správu předplatných a jejich prostředků, tento přístup ztratí. Přístup ke správě prostředků bude mít pouze uživatel v novém účtu, který přijme žádost o převod. Pokud nový vlastník chce poskytnout přístup uživatelům, kteří ho ztratili, musí je do předplatného ručně přidat. Další informace najdete v tématu věnovaném [převodu předplatných Azure do jiného adresáře Azure AD](../../role-based-access-control/transfer-subscription.md).

## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Převod předplatných sady Visual Studio a programu Partner Network

Předplatná sady Visual Studio a programu Microsoft Partner Network mají přidružený měsíční opakující se kredit Azure. Při převodu těchto předplatných není kredit v cílovém fakturačním účtu dostupný. Předplatné používá kredit v cílovém fakturačním účtu. Robert například 9. září převede předplatné sady Visual Studio Enterprise do Janina účtu a Jana tento převod přijme. Jakmile se převod dokončí, začne předplatné používat kredit v Janině účtu. Kredit se resetuje vždy devátého v měsíci.

## <a name="next-steps-after-accepting-billing-ownership"></a>Další postup po přijetí vlastnictví fakturace

Pokud jste přijali vlastnictví fakturace předplatného Azure, doporučujeme vám projít si tyto další kroky:

1. Zkontrolujte a aktualizujte správce služby, spolusprávce a další přiřazení rolí Azure. Pokud se chcete dozvědět víc, přečtěte si téma [Přidání nebo změna správců předplatného Azure](add-change-subscription-administrator.md) a téma věnované [přidání nebo odebrání přiřazení rolí Azure s využitím webu Azure Portal](../../role-based-access-control/role-assignments-portal.md).
1. Aktualizujte přihlašovací údaje přidružené službám tohoto předplatného, mezi které patří:
   1. Certifikáty pro správu, které udělují uživateli oprávnění správce prostředků předplatného. Další informace najdete v tématu o [vytvoření a nahrání certifikátu pro správu Azure](../../cloud-services/cloud-services-certs-create.md).
   1. Přístupové klíče pro služby, jako je Storage. Další informace najdete v článku o [účtech Azure Storage](../../storage/common/storage-account-create.md).
   1. Oprávnění pro vzdálený přístup ke službám, jako je Azure Virtual Machines.
1. Pokud pracujete s partnerem, zvažte u předplatného aktualizaci ID partnera. ID partnera můžete aktualizovat na webu [Azure Portal](https://portal.azure.com). Další informace najdete v článku [Propojení ID partnera s účty Azure](link-partner-id.md).

## <a name="cancel-a-transfer-request"></a>Zrušení žádosti o převod

V každém okamžiku je aktivní vždycky jenom jedna žádost o převod. Platnost žádosti o převod je 15 dnů. Po uplynutí 15 dnů platnost žádosti o převod vyprší.

Zrušení žádosti o převod:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejděte na **předplatná** > vyberte předplatné, pro které jste odeslali žádost o přenos, a pak vyberte **přenést vlastnictví fakturace**.
1. V dolní části stránky vyberte **Zrušit žádost o převod**.

:::image type="content" source="./media/billing-subscription-transfer/transfer-billing-owership-cancel-request.png" alt-text="Příklad ukazující okno Převést vlastnictví fakturace s možností Zrušit žádost o převod" lightbox="./media/billing-subscription-transfer/transfer-billing-owership-cancel-request.png" :::

## <a name="troubleshooting"></a>Poradce při potížích

Pokud máte potíže s převodem předplatných, využijte následující informace o řešení potíží.

### <a name="original-azure-subscription-billing-owner-leaves-your-organization"></a>Vlastník fakturace původního předplatného Azure opouští vaši organizaci.

> [!Note]
> Tato část se týká konkrétně fakturačního účtu smlouvy o zákaznících Microsoftu. Ověřte, jestli máte přístup k [smlouvě o zákaznících Microsoftu](mca-request-billing-ownership.md#check-for-access).

Je možné, že původní vlastník fakturačního účtu, který vytvořil účet Azure a předplatné Azure, odejde z vaší organizace. Pokud k této situaci dojde, jejich identita uživatele už není v Azure Active Directory organizace. Předplatné Azure pak nemá vlastníka fakturace. Tato situace brání komukoli v provádění fakturačních operací s účtem, včetně zobrazení a platby za účty. Předplatné může přejít k dřívějšímu stavu. Předplatné by nakonec mohlo být zakázané kvůli neplatbě. Předplatné by nakonec mohlo být odstraněno a mělo by to mít vliv na všechny služby spuštěné v rámci předplatného.

Pokud předplatné už nemá platného účet fakturačního účtu, Azure pošle e-mail jiným vlastníkům fakturačního účtu, správcům služeb (pokud existuje), Co-Administrators (pokud existuje) a vlastníkům předplatného, kteří si tyto situace budou informovat, a poskytne jim odkaz na přijetí vlastnictví fakturace předplatného. Kterýkoli z uživatelů může vybrat odkaz pro příjem vlastnictví fakturace. Další informace o fakturačních rolích najdete v tématu [role fakturace](understand-mca-roles.md) a [klasické role a role Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md).

Tady je příklad toho, jak e-mail vypadá.

:::image type="content" source="./media/billing-subscription-transfer/orphaned-subscription-email.png" alt-text="Snímek obrazovky s ukázkovým e-mailem, který přijme vlastnictví fakturace" lightbox="./media/billing-subscription-transfer/orphaned-subscription-email.png" :::

Kromě toho Azure v Azure Portal okně podrobností předplatného zobrazuje hlavičku pro fakturaci, správce služeb, spolusprávce a vlastníky předplatného. Vyberte odkaz na informačním panelu pro příjem vlastnictví fakturace.

:::image type="content" source="./media/billing-subscription-transfer/orphaned-subscription-example.png" alt-text="Snímek obrazovky, který ukazuje příklad předplatného bez platného vlastníka fakturace" lightbox="./media/billing-subscription-transfer/orphaned-subscription-example.png" :::

### <a name="the-transfer-subscription-option-is-unavailable"></a>Možnost Převést předplatné není dostupná

<a name="no-button"></a> 

Pro váš fakturační účet není k dispozici samoobslužný převod předplatného. V současné době nepodporujeme převod vlastnictví fakturace předplatných v účtech smlouvy Enterprise (EA) na webu Azure Portal. Účty smluv se zákazníkem Microsoftu vytvořené během práce se zástupcem společnosti Microsoft také nepodporují převod vlastnictví fakturace.

###  <a name="not-all-subscription-types-can-transfer"></a>Ne všechny typy předplatného je možné převést

Převod vlastnictví fakturace nepodporují všechny typy předplatného. Seznam typů předplatného, které podporují převody, najdete v tématu [Podporované typy předplatného](../understand/subscription-transfer.md#supported-subscription-types).

###  <a name="access-denied-error-shown-when-trying-to-transfer-subscription-billing-ownership"></a>Zobrazení chyby odepření přístupu při pokusu o převod vlastnictví fakturace předplatného

Tato chyba se zobrazí, pokud se pokoušíte převést předplatné plánu Microsoft Azure a nemáte potřebná oprávnění. Pokud chcete převést předplatné plánu Microsoft Azure, musíte být vlastníkem nebo přispěvatelem v části faktury, ke které se předplatné účtuje. Další informace najdete v tématu [Správa předplatných pro části faktury](../manage/understand-mca-roles.md#manage-subscriptions-for-invoice-section).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- Zkontrolujte a aktualizujte správce služby, spolusprávce a další přiřazení rolí Azure. Pokud se chcete dozvědět víc, přečtěte si téma [Přidání nebo změna správců předplatného Azure](add-change-subscription-administrator.md) a téma věnované [přidání nebo odebrání přiřazení rolí Azure s využitím webu Azure Portal](../../role-based-access-control/role-assignments-portal.md).