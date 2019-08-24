---
title: Přeneste vlastnictví fakturace předplatného Azure na jiný účet | Microsoft Docs
description: Popisuje, jak přenést vlastnictví fakturace předplatného Azure na jiný účet a nejčastější dotazy k procesu.
keywords: Přeneste si předplatné Azure, předplatné Azure transferu, přesuňte předplatné Azure na jiný účet, vlastníka Azure Change Subscription, přeneste předplatné Azure na jiný účet, fakturační přenos Azure
author: bandersmsft
manager: amberb
tags: billing,top-support-issue
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e4496e2b5d2b21fd878ef68665b8e5b06fa6cc5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012551"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Přenos vlastnictví fakturace předplatného Azure na jiný účet

Pokud odejdete do vaší organizace, nebo chcete, aby se vaše předplatné účtovalo na jiný účet, můžete chtít přenést vlastnictví fakturace vašeho předplatného Azure. Převod vlastnictví fakturace na jiný účet poskytuje správcům v novém účtu oprávnění k provádění fakturačních úkolů, jako je třeba Změna způsobu platby, zobrazit poplatky a zrušit předplatné.

Pokud chcete zachovat vlastnictví fakturace, ale změnit typ vašeho předplatného, přečtěte si téma [Přepnutí předplatného Azure na jinou nabídku](billing-how-to-switch-azure-offer.md). Pokud chcete řídit, kdo může spravovat prostředky v předplatném, přečtěte si téma [předdefinované role pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Pokud jste zákazníkem smlouva Enterprise (EA), můžou správci vaší organizace přenášet vlastnictví fakturace vašich předplatných mezi účty. Další informace najdete v tématu [přenos vlastnictví fakturace předplatných smlouva Enterprise (EA)](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Přenos vlastnictví fakturace předplatného Azure

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce fakturačního účtu s předplatným, které chcete přenést. Chcete-li zjistit, zda jste správce, přečtěte si [Nejčastější dotazy](#faq).

1. Hledat na **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. V levém podokně vyberte odběry. V závislosti na vašem přístupu možná budete muset vybrat obor fakturace a pak vybrat předplatná nebo **předplatné Azure**.

1. U předplatného, které chcete přenést, vyberte **přenést vlastnictví fakturace** . 

   ![Vyberte předplatné, které chcete přenést.](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Zadejte e-mailovou adresu uživatele, který je správcem fakturace účtu, který bude novým vlastníkem tohoto předplatného.

1. Pokud přenášíte předplatné na účet v jiném tenantovi služby Azure AD, vyberte, jestli chcete přesunout předplatné do tenanta nového účtu. Další informace najdete v tématu [přenos předplatného do účtu v jiném tenantovi služby Azure AD](#transferring-subscription-to-an-account-in-another-azure-ad-tenant) .

    > [!IMPORTANT]
    >
    > Pokud se rozhodnete přesunout předplatné do tenanta Azure AD nového účtu, trvale se odeberou všechna přiřazení [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/overview.md) pro správu prostředků v předplatném. Přístup ke správě prostředků v předplatném bude mít jenom uživatel v novém účtu, který požadavek na přenos přijme. Další informace najdete v tématu [přenos předplatného uživateli v jiném tenantovi služby Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md). Případně můžete zrušit políčko u předplatného tenanta Azure AD, abyste přenesli vlastnictví fakturace bez přesunu předplatného na tenanta nového účtu. Pokud to uděláte, zachovají se stávající oprávnění RBAC pro správu prostředků Azure.
  
    ![Stránka pro odeslání přenosu](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Vyberte **Odeslat požadavek na přenos**.

1. Uživatel dostane e-mail s pokyny ke kontrole žádosti o přenos.

   ![E-mail pro přenos předplatných odeslaný příjemci](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Pokud chcete žádost o přenos schválit, uživatel vybere odkaz v e-mailu a postupuje podle pokynů. Uživatel bude muset zvolit způsob platby, který se použije k platbě za předplatné. Pokud navíc uživatel nemá účet Azure, musel by se zaregistrovat k novému účtu. 

   ![Webová stránka převodu prvního předplatného](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Druhá webová stránka převodu předplatného](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Druhá webová stránka převodu předplatného](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Výborně! Předplatné se teď přenáší.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Převod předplatného na účet v jiném tenantovi služby Azure AD

Tenant Azure Active Directory (AD) se vytvoří za vás při registraci do Azure. Tenant představuje váš účet. Tenant se používá ke správě přístupu k předplatným a prostředkům.

Když vytváříte nové předplatné, je hostované v tenantovi Azure AD vašeho účtu. Pokud chcete poskytnout ostatním uživatelům přístup k vašemu předplatnému nebo jeho prostředkům, musíte je pozvat, aby se připojili k vašemu tenantovi. To vám pomůže řídit přístup k předplatným a prostředkům.

Když přenášíte vlastnictví fakturace předplatného na účet v jiném tenantovi služby Azure AD, můžete přesunout předplatné do tenanta nového účtu. Pokud to uděláte, všichni uživatelé, skupiny nebo instanční objekty, které mají [přístup na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md) , ke správě předplatných a jejich prostředky ztratí přístup. Přístup ke správě prostředků bude mít jenom uživatel v novém účtu, který přijme vaši žádost o přenos. Aby bylo možné poskytnout přístup uživatelům, kteří mají původně přístup, bude muset nový vlastník [Přidat tyto uživatele do předplatného ručně](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Přenos předplatných pro vývoj a testování v programu Visual Studio, MPN a průběžné platby

K předplatným sady Visual Studio a Microsoft Partner Network se přiřadí měsíční kredit Azure, který je k nim přidružený. Při přenosu těchto předplatných není váš kredit k dispozici v cílovém fakturačním účtu. Předplatné používá kredit v cílovém fakturačním účtu. Například pokud Bob přenáší Visual Studio Enterprise předplatné na účet Jana v 9 září a Jana akceptuje přenos. Po dokončení přenosu začne předplatné používat kredit v účtu Jana. Kredit se resetuje každý měsíc 9. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Přenos vlastnictví fakturace předplatných smlouva Enterprise (EA)

Podnikový správce může přenášet vlastnictví předplatných mezi účty v rámci registrace. Další informace najdete v tématu [přenos vlastnictví účtu](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) na portálu EA.

## <a name="next-steps-after-accepting-billing-ownership"></a>Další kroky po přijetí vlastnictví fakturace

Pokud jste přijali vlastnictví fakturace předplatného Azure, doporučujeme vám projít si tyto další kroky:

1. Zkontrolujte a aktualizujte Správce služby, spolusprávce a další role RBAC. Další informace najdete v tématech [Přidání nebo změna správců předplatného Azure](billing-add-change-azure-subscription-administrator.md) a [Správa přístupu pomocí RBAC a Azure Portal](../role-based-access-control/role-assignments-portal.md).
1. Aktualizujte přihlašovací údaje přidružené k službám tohoto předplatného, včetně:
   1. Certifikáty pro správu, které udělují oprávnění správce uživatelům k prostředkům předplatného. Další informace najdete v tématu [Vytvoření a nahrání certifikátu pro správu pro Azure](../cloud-services/cloud-services-certs-create.md) .
   1. Přístupové klíče pro služby, jako je úložiště Další informace najdete v tématu [informace o účtech Azure Storage](../storage/common/storage-create-storage-account.md) .
   1. Přihlašovací údaje vzdáleného přístupu pro služby, jako je Azure Virtual Machines.
1. Pokud pracujete s partnerem, zvažte aktualizaci ID partnera v tomto předplatném. ID partnera můžete aktualizovat v [Azure Portal](https://portal.azure.com). Další informace najdete v tématu [propojení ID partnera s účty Azure](billing-partner-admin-link-started.md) .

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Podporované typy předplatného

Přenos předplatného v Azure Portal je k dispozici pro níže uvedené typy předplatného. V současné době se přenos nepodporuje pro odběry [bezplatné zkušební verze](https://azure.microsoft.com/offers/ms-azr-0044p/) nebo [systém Azure v rámci licenčního programu Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) . Alternativní řešení najdete v článku [Přesunutí prostředků do nové skupiny prostředků nebo](../azure-resource-manager/resource-group-move-resources.md)předplatného. Pokud chcete přenést další předplatná [](https://azure.microsoft.com/offers/ms-azr-0036p/) , jako jsou sponzorství nebo plány podpory, obraťte se na [podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Předplatitelé Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Průběžné platby dle aktuálního využití pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\*[Prostřednictvím portálu EA](#EA).

\*\*Podporuje se jenom pro účty, které se vytvořily během registrace na webu Azure. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Nejčastější dotazy týkající se odesílatelů

Tyto nejčastější dotazy se vztahují na uživatele, kteří převádějí vlastnictví fakturace předplatného Azure na jiný účet.

### <a name="whoisaa"></a>Kdo je účet správcem fakturace účtu?

Správce fakturace je osoba, která má oprávnění ke správě fakturace pro účet. Mají oprávnění získat přístup k fakturaci na [Azure Portal](https://portal.azure.com) a provádět různé fakturační úkoly, jako je vytváření předplatných, zobrazení a platby faktury nebo aktualizace způsobů platby.

K identifikaci účtů, u kterých jste správcem fakturace, použijte následující postup:

1. Přejděte na [stránku cost management + fakturace v Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. V levém podokně vyberte **všechny fakturační rozsahy** . 
1. Na stránce Předplatná najdete seznam všech předplatných, pro které jste správcem fakturace.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Převádí všechno? Včetně skupin prostředků, virtuálních počítačů, disků a dalších spuštěných služeb?

Všechny vaše prostředky, jako jsou virtuální počítače, disky a weby, se přenesou do nového účtu. Pokud však přenášíte předplatné na účet v jiném tenantovi služby Azure AD, žádné [role správce](billing-add-change-azure-subscription-administrator.md) a přiřazení [Access Control na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md) v předplatném se nepřenášejí. [](#transferring-subscription-to-an-account-in-another-azure-ad-tenant) [Registrace aplikací](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) a jiné služby specifické pro tenanta se také nepřenosují společně s předplatným.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Můžu přenášet vlastnictví na účet v jiné zemi?
Transfery mezi země se v Azure Portal bohužel nedají provést. Pokud chcete přenést předplatné mezi země, [obraťte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)se na podporu.

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Jsem správce na dvou účtech. Můžu přenášet předplatné z jednoho z mých účtů na jiný?
Ano, můžete přenést předplatné mezi vašimi účty. Vaše účty jsou koncepčně považované za účty dvou různých uživatelů, abyste mohli použít výše uvedené kroky k přenosu předplatných mezi vašimi účty.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Způsobí přenos předplatného nějaké výpadky služeb?

Pokud přenesete předplatné na účet ve stejném tenantovi Azure AD, nebude to mít žádný vliv na prostředky spuštěné v rámci předplatného. Pokud však přenesete předplatné na účet v jiném tenantovi a rozhodnete se přesunout předplatné do tenanta, všichni uživatelé, skupiny a instanční objekty, které mají [přístup na základě role (RBAC)](../role-based-access-control/overview.md) pro správu prostředků v předplatném, ztratí přístup . To může vést k výpadkům služby.

### <a name="do-users-in-new-account-have-access-to-usage-and-billing-history"></a>Mají uživatelé v novém účtu přístup k historii využití a fakturace?

Jediné informace, které jsou k dispozici uživatelům v novém účtu, jsou náklady za poslední měsíc vašeho předplatného. Zbytek historie využití a fakturace se nepřevádí do předplatného.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Návody migrovat data a služby pro předplatné Azure do nového předplatného?

Pokud nemůžete převést vlastnictví předplatného, můžete prostředky migrovat ručně. Viz [Přesunutí prostředků do nové skupiny prostředků nebo](../azure-resource-manager/resource-group-move-resources.md)předplatného.

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Když Přenáším předplatné sady Visual Studio nebo Microsoft Partner Network, přenese můj kredit do nového účtu předplatné?

Ne, váš kredit není v novém účtu k dispozici. Uživatel, který přijme požadavek na přenos, musí mít licenci sady Visual Studio, aby mohl přijmout žádost o přenos. Předplatné používá kredit sady Visual Studio, který je k dispozici v uživatelském účtu. Další informace najdete v tématech [přenos sady Visual Studio, Microsoft Partner Network (MPN) a průběžné platby podle svých předplatných pro vývoj a testování](#transferring-visual-studio-mpn-and-pay-as-you-go-devtest-subscriptions).


## <a name="frequently-asked-questions-faq-for-recipients"></a>Nejčastější dotazy týkající se příjemců

Tyto nejčastější dotazy se vztahují na uživatele, kteří přijímají vlastnictví fakturace předplatného Azure z jiného účtu.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Pokud převezmem vlastnictví fakturace předplatného z jiného účtu, budou mít uživatelé tohoto účtu i nadále přístup k mým prostředkům?

Ano. Pokud je ale váš účet v tenantovi Azure AD, který se liší od tenanta předplatného a uživatel, který odeslal požadavek na přenos, přesune předplatné do tenanta vašeho účtu, všechny [role správců](billing-add-change-azure-subscription-administrator.md) a [Access Control na základě rolí (RBAC). ](../role-based-access-control/role-assignments-portal.md)přiřazení se odeberou. Chcete-li zobrazit uživatele, kteří mají přístup na [základě rolí (RBAC)](../role-based-access-control/overview.md) pro správu prostředků v rámci předplatného, použijte následující postup:

1. Navštivte [stránku předplatného v Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Vyberte předplatné, které chcete kontrolovat, a pak v levém podokně vyberte **řízení přístupu (IAM)** .
1. V horní části stránky vyberte **přiřazení rolí** . Na stránce přiřazení rolí jsou uvedeni všichni uživatelé, kteří mají k předplatnému přístup RBAC.

I když během přenosu dojde k odebrání přiřazení [Access Control na základě role (RBAC)](../role-based-access-control/role-assignments-portal.md) , mohou uživatelé v původním účtu vlastníka stále mít přístup k předplatnému prostřednictvím některých mechanismů zabezpečení, včetně těchto:

* Certifikáty pro správu, které udělují oprávnění správce uživatelům k prostředkům předplatného. Další informace najdete v tématu [Vytvoření a nahrání certifikátu pro správu pro Azure](../cloud-services/cloud-services-certs-create.md).
* Přístupové klíče pro služby, jako je úložiště Další informace najdete v článku o [účtech Azure Storage](../storage/common/storage-create-storage-account.md).
* Přihlašovací údaje vzdáleného přístupu pro služby, jako je Azure Virtual Machines.

Pokud příjemce potřebuje omezit přístup ke svým prostředkům, měl by zvážit aktualizaci tajných kódů přidružených k této službě. Většinu prostředků je možné aktualizovat pomocí následujících kroků:

  1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
  2. V nabídce centra vyberte **všechny prostředky**.
  3. Vyberte prostředek.
  4. Na stránce prostředek klikněte na **Nastavení**. Tady můžete zobrazit a aktualizovat existující tajné kódy.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Pokud převezmem vlastnictví fakturace předplatného uprostřed fakturačního cyklu, musím platit za celý fakturační cyklus?

Váš účet zodpovídá za platbu za jakékoliv použití, které je nahlášené od okamžiku přenosu. Může dojít k některému využití, které bylo provedeno před přenosem, ale následně bylo oznámeno. Využití je součástí faktury vašeho účtu.

### <a name="can-i-use-a-different-payment-method"></a>Můžu použít jiný způsob platby?

Ano. Při přijímání žádosti o přenos můžete vybrat stávající způsob platby, který je propojený s vaším účtem, nebo přidat nový způsob platby.

## <a name="troubleshooting"></a>Řešení potíží

### <a id="no-button"></a>Proč se mi nezobrazuje tlačítko "předplatné přenosu"?

Pro fakturační účet není k dispozici migrace samoobslužného předplatného. V současné době nepodporujeme převod vlastnictví předplatných v rámci účtů smlouva Enterprise (EA) v Azure Portal. Účty zákaznických slev společnosti Microsoft, které jsou vytvořeny při práci s zástupcem společnosti Microsoft, navíc nepodporují přenos vlastnictví fakturace. 

### <a id="no-button"></a>Proč můj typ předplatného nepodporuje přenos? 

Ne všechny typy předplatných podporují přenos vlastnictví fakturace. Seznam typů předplatného, které podporují přenosy, najdete v tématu [podporované typy](#supported-subscription-types) předplatného.

### <a id="no-button"></a>Proč se mi zobrazuje chyba odepření přístupu při pokusu o přenos vlastnictví předplatného? 

Tato chyba se zobrazí, pokud se pokoušíte přenést předplatné plánu Microsoft Azure a nemáte potřebná oprávnění. Pokud chcete přenést předplatné plánu Microsoft Azure, musíte být vlastníkem nebo přispěvatelem na faktuře, ke které se předplatné účtuje. Další informace najdete v [části Správa předplatných pro fakturu](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

- Zkontrolujte a aktualizujte Správce služby, spolusprávce a další role RBAC. Další informace najdete v tématech [Přidání nebo změna správců předplatného Azure](billing-add-change-azure-subscription-administrator.md) a [Správa přístupu pomocí RBAC a Azure Portal](../role-based-access-control/role-assignments-portal.md).
