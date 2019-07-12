---
title: Převést vlastnictví fakturace předplatného Azure na jiný účet | Dokumentace Microsoftu
description: Popisuje, jak převést vlastnictví fakturace předplatného Azure na jiný účet a některé nejčastější dotazy (FAQ) o procesu
keywords: Převod předplatného přenos předplatného azure, azure, přesunout předplatné azure na jiný účet azure změnit vlastníka předplatného, přenos předplatného azure do jiné převod účtu, azure billing
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
ms.openlocfilehash: 7d41e32065e3de37eb8f01ab1b836040e7f57b12
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657877"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Převést vlastnictví fakturace předplatného Azure na jiný účet

Můžete chtít převést vlastnictví fakturace předplatného Azure, pokud jsou byste museli opustit organizaci, nebo chcete, aby vaše předplatné účtovat na jiný účet. Převést vlastnictví fakturace na jiný účet poskytuje správcům v rámci nového účtu oprávnění k provádění fakturační úkoly, jako změnit způsob platby, zobrazení nákladů a zrušit předplatné.

Pokud chcete zachovat vlastnictví fakturace ale změnit typ vašeho předplatného, přečtěte si téma [přepnout vašeho předplatného Azure na jinou nabídku](billing-how-to-switch-azure-offer.md). Pokud chcete řídit, kdo může spravovat prostředky v rámci předplatného, přečtěte si téma [předdefinované role pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Pokud jste zákazníkem Agreement(EA) organizace, může správce organizace převést vlastnictví fakturace z vašich předplatných mezi účty. Další informace najdete v tématu [převést vlastnictví fakturace předplatného Enterprise Agreement (EA)](#transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions).

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Převést vlastnictví fakturace předplatného Azure

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako správce fakturační účet, který má předplatné, které chcete převést. Pokud jste správce, najdete v tématu [– nejčastější dotazy](#faq).

1. Hledat na **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující Azure search na portálu](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. Vyberte **předplatná** v levém podokně. V závislosti na přístup, budete muset vybrat fakturační obor a pak vyberte **předplatná** nebo **předplatná Azure**.

1. Vyberte **převést vlastnictví fakturace** pro předplatné, které chcete převést. 

   ![Vyberte předplatné pro přenos](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. Zadejte e-mailová adresa uživatele, který je správcem fakturace účtu, který bude nový vlastník předplatného a pak vyberte **odeslat žádost o převod**.

    > [!IMPORTANT]
    >
    > Pokud se převést vlastnictví fakturace předplatného na účet uživatele v jiné služby Azure AD tenanta, všechny [řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md) přiřazení ke správě prostředků v předplatném se trvale odeberou. Nový vlastník budou mít přístup ke správě prostředků v předplatném. Další informace najdete v tématu [přenos předplatného na uživatele v jiném tenantovi Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md).
  
    ![Odeslat zprávu na pager přenosu](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. Uživatel dostane e-mail s pokyny ke kontrole vašeho požadavku na převedení.

   ![E-mailu přenos předplatného příjemci](./media/billing-subscription-transfer/billing-receiver-email.png)

1. Schválit žádost o převod, uživatel vybere odkaz v e-mailu a postupuje podle pokynů. Uživatel bude muset vyberte způsob platby, který se použije k úhradě předplatného. Kromě toho pokud uživatel nemá účet Azure, by mají si zaregistrovat nový účet. 

   ![První předplatné přenos webové stránky](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![Druhý předplatné přenos webové stránky](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![Druhý předplatné přenos webové stránky](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. Výborně! Nyní převodu předplatného.

## <a name="transferring-subscription-to-an-account-in-another-azure-ad-tenant"></a>Přenos předplatného do účtu v jiném tenantovi Azure AD

Tenanta služby Azure Active Directory (AD) se vytvoří pro vás při registraci k Azure. Klient představuje váš účet. Správa přístupu ke svým předplatným a prostředkům pomocí tenanta.

Když vytvoříte nové předplatné, je hostován v tenantovi Azure AD vašeho účtu. Pokud chcete poskytnout ostatním přístup k předplatnému nebo prostředkům, musíte pozvat do tenanta. Díky tomu můžete řídit přístup ke svým předplatným a prostředkům.

Když převést vlastnictví fakturace předplatného do účtu v jiném tenantovi Azure AD, předplatné se přesune do nového účtu tenanta. Všechny uživatele, skupiny nebo instanční objekty, kteří měli [na základě rolí (RBAC) přístup](../role-based-access-control/overview.md) ke správě prostředků v předplatném přijít o přístup. Pouze uživatel na nový účet, který přijímá vašeho požadavku na převedení bude mít přístup ke správě prostředků. Pokud chcete poskytnout přístup uživatelům, kteří původně měli přístup, musel nového vlastníka [ručně přidat tyto uživatele k předplatnému](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).


## <a name="transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions"></a>Přenos sady Visual Studio, Microsoft Partner Network (MPN) a průběžné platby. vývojové a testovací předplatné

Předplatná sady Visual Studio a Microsoft Partner Network máte opakované kreditu Azure měsíčně k nim má přiřazené. Při přenosu těchto předplatných, váš kredit není k dispozici v cílovém fakturačního účtu. Předplatné používá tento kredit v cílovém fakturačního účtu. Například pokud Bob přenese předplatné Visual Studio Enterprise pro účet na 9. září a Jana Jana přijme přenos. Po dokončení přenosu předplatného spustí pomocí platební Jana účtu. Kreditu obnoví každý měsíc na 9. 


<a id="EA"></a>

## <a name="transfer-billing-ownership-of-enterprise-agreement-ea-subscriptions"></a>Převést vlastnictví fakturace předplatného Enterprise Agreement (EA)

Podnikový správce může převést vlastnictví předplatného mezi účty v rámci registrace. Další informace najdete v tématu [přenos vlastnictví účtu](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) přes portál EA.

<a id="CSP"></a>

## <a name="next-steps-after-accepting-billing-ownership"></a>Další kroky následující po přijetí vlastnictví fakturace

Pokud jste přijali vlastnictví fakturace předplatného Azure, doporučujeme, abyste že si přečetli tyto další kroky:

1. Zkontrolovat a aktualizovat Správce služby, Spolusprávci a ostatní role RBAC. Další informace najdete v tématu [přidat nebo změnit správce předplatného Azure](billing-add-change-azure-subscription-administrator.md) a [správě přístupu pomocí RBAC a webu Azure portal](../role-based-access-control/role-assignments-portal.md).
1. Aktualizace přihlašovacích údajů spojené se službami tohoto předplatného, včetně:
   1. Certifikáty pro správu, které uživateli udělit oprávnění správce pro prostředky předplatného. Další informace najdete v tématu [vytvoření a nahrání certifikátu pro správu pro Azure](../cloud-services/cloud-services-certs-create.md)
   1. Přístupové klíče pro služby, jako jsou úložiště. Další informace najdete v tématu [účty Azure storage](../storage/common/storage-create-storage-account.md)
   1. Pověření vzdáleného přístupu pro služby, jako jsou Azure Virtual Machines.
1. Pokud pracujete s partnerem, zvažte aktualizaci ID partnera v tomto předplatném. Můžete aktualizovat ID partnera v [webu Azure portal](https://portal.azure.com). Další informace najdete v tématu [propojit účtů Azure s ID partnera.](billing-partner-admin-link-started.md)

<a id="supported"></a>

## <a name="supported-subscription-types"></a>Typy podporované předplatného

Převod předplatného na webu Azure Portal je k dispozici níže uvedené typy předplatného. Aktuálně není podporován převod pro [bezplatnou zkušební verzi](https://azure.microsoft.com/offers/ms-azr-0044p/) nebo [Azure v Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) předplatných. Alternativní řešení, najdete v části [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md). Přenášet další předplatná, jako jsou [sponzorství](https://azure.microsoft.com/offers/ms-azr-0036p/) nebo plány podpory, [obraťte na podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Předplatitelé sady Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) 
- [Předplatné MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Vývoj/testování s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [Na portálu EA](#EA).

\*\* Podporováno pouze pro účty, které jsou vytvořeny během přihlášení si na webu Azure. 

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>Nejčastější dotazy (FAQ) pro odesílatele

Tyto nejčastější dotazy k použití pro uživatele, kteří se převést vlastnictví fakturace předplatného Azure na jiný účet.

### <a name="whoisaa"></a> Kdo je správcem fakturačního účtu?

Správce fakturace je osoba, která má oprávnění ke správě fakturace pro účet. Mají autorizaci pro přístup k fakturaci na [webu Azure portal](https://portal.azure.com) a provádění různých úloh fakturace, jako je vytvoření předplatných, zobrazení a platit podle faktury nebo aktualizace platebních metod.

K identifikaci předplatná, u kterých jste správcem fakturace, postupujte následovně:

1. Přejděte [Správa nákladů a fakturace stránky na webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview).
1. Vyberte **předplatná** v levém podokně. V závislosti na přístup, budete muset vybrat fakturační obor a pak vyberte **předplatná** nebo **předplatných Azure**
1. Na stránce předplatná zobrazí všechna předplatná, u kterých jste správcem fakturace.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Všechno, co přenosu? Včetně skupin prostředků, virtuální počítače, disků a další služby spuštěné?

Všechny prostředky, jako jsou virtuální počítače, disky a websites přenosy do nového účtu. Ale pokud se převod předplatného do účtu v jiné služby Azure AD tenanta, některé [role správce](billing-add-change-azure-subscription-administrator.md) a [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md) přiřazení předplatného [nepodporují přenos](#transferring-subscription-to-an-account-in-another-azure-ad-tenant). Navíc [registrace aplikací](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) a dalších služeb specifickým pro tenanta se nepřevádějí spolu se předplatné.

### <a name="can-i-transfer-ownership-to-an-account-in-another-country"></a>Může převést vlastnictví na účet v jiné zemi?
Bohužel křížové země převody nelze provést na webu Azure Portal. Pro přenos předplatného v zemích, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>Jsem správce na dva účty. Je možné převést předplatné z jednoho účty do jiného?
Ano, můžete převod předplatného mezi účty. Vaše účty považují koncepčně účty ve dvou různých uživatelů, proto použijete výše uvedené kroky pro přenos předplatného mezi účty.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Převod předplatného za následek způsobilo výpadek služeb?

Pokud převod předplatného na uživatele ve stejném tenantovi Azure AD, neexistuje žádný vliv na prostředky spuštěné v rámci předplatného.  Ale pokud převedete předplatné pro uživatele v jiném tenantovi, všechny uživatele, skupiny nebo instanční objekty, kteří měli [na základě rolí (RBAC) přístup](../role-based-access-control/overview.md) ke správě prostředků v předplatném přijít o přístup. 

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>Má příjemce přístup k využití a historii fakturace?

Jediná informace, které jsou k dispozici pro příjemce je toto poslední měsíc nákladů pro vaše předplatné. Zbývající část využití a historii fakturace nepřenese s předplatným

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Jak migrovat data a služby pro svoje předplatné Azure k novému předplatnému?

Pokud nelze převést vlastnictví předplatného, můžete ručně migrovat vaše prostředky. Zobrazit [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md).

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Pokud je možné převést předplatné sady Visual Studio nebo programu Microsoft Partner Network, svůj kredit převést do dalšího období předplatného na nový účet?

Ne, váš kredit není k dispozici v rámci nového účtu. Uživatel, který přijme žádost o převod musí mít licenci sady Visual Studio tak, aby přijímal žádosti o přenos. Odběr využívá kredit sady Visual Studio, která je k dispozici v účtu uživatele. Další informace najdete v tématu [předplatných sady Visual Studio přenosu, Microsoft Partner Network (MPN) a průběžné platby pro vývoj/testování](#transferring-visual-studio-microsoft-partner-network-mpn-and-pay-as-you-go-devtest-subscriptions)


## <a name="frequently-asked-questions-faq-for-recipients"></a>Nejčastější dotazy (FAQ) pro příjemce

Tyto nejčastější dotazy k použití pro uživatele, kteří jsou převzetí vlastnictví fakturace předplatného služby Azure z jiného účtu.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>Pokud převzít vlastnictví fakturace předplatného z jiného účtu dělat v tom, že účet i nadále mít přístup k prostředkům Moje uživatele?

Pokud předplatné je přenesené na účet ve stejném tenantovi Azure AD, všechny uživatele, skupiny nebo instanční objekty, kteří měli [na základě rolí (RBAC) přístup](../role-based-access-control/overview.md) ke správě prostředků v předplatném zachovat jejich přístup. Chcete-li zobrazit, kteří mají přístup RBAC k předplatnému, postupujte následovně:

1. Přejděte [stránku předplatné na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Vyberte předplatné, které chcete zkontrolovat a pak vyberte **řízení přístupu (IAM)** v levém podokně.
1. Vyberte **přiřazení rolí** z horní části stránky. Na stránce přiřazení role uvedeny všechny uživatele, kteří mají přístup RBAC v rámci předplatného.

Pokud předplatné je přenesené na účet v jiném tenantovi Azure AD, všechny uživatele, skupiny nebo instanční objekty, kteří měli [na základě rolí (RBAC) přístup](../role-based-access-control/overview.md) ke správě prostředků v předplatném přijít o přístup. Nicméně i v případě, že už nemají přístup RBAC, může stále mají přístup k předplatnému přes některé mechanismy zabezpečení, včetně:

* Certifikáty pro správu, které uživateli udělit oprávnění správce pro prostředky předplatného. Další informace najdete v tématu [vytvoření a nahrání certifikátu pro správu pro Azure](../cloud-services/cloud-services-certs-create.md).
* Přístupové klíče pro služby, jako jsou úložiště. Další informace najdete v článku o [účtech Azure Storage](../storage/common/storage-create-storage-account.md).
* Pověření vzdáleného přístupu pro služby, jako jsou Azure Virtual Machines.

Pokud příjemce je potřeba omezit, přístup ke svým prostředkům, zvažte jejich aktualizuje všechny tajné kódy související se službou. Většinu prostředků lze aktualizovat pomocí následujících kroků:

  1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
  2. V nabídce centra vyberte **všechny prostředky**.
  3. Vyberte prostředek.
  4. Na stránce prostředků klikněte na **nastavení**. Tady si můžete zobrazit a aktualizovat existující tajných kódů.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>Pokud převzít vlastnictví fakturace předplatného uprostřed fakturačního cyklu musím platit pro celý fakturační cyklus?

Váš účet je zodpovědná za platby za jakékoliv využití, jež bude nahlášena z doby přenosu a vyšší. Může existovat několik využití, která proběhla před převodem ale byla nahlášena později. Využití je součástí vašeho účtu vyúčtování.

### <a name="can-i-use-a-different-payment-method"></a>Můžete použít jiný způsob platby?

Ano. Při přijímání žádost o převod, můžete vybrat existující způsob platby, který je propojený s vaším účtem nebo přidat nový způsob platby.

## <a name="troubleshooting"></a>Řešení potíží

### <a id="no-button"></a> Proč nevidíte tlačítko "Převod předplatného"?

Bohužel není k dispozici pro váš fakturační účet samoobslužné převedení předplatného. V současné době nepodporujeme, přenos, vlastnictví fakturace účtu Enterprise Agreement (EA) na webu Azure Portal. Kromě toho účty Microsoft smlouvy zákazníka, které jsou vytvořeny při práci prostřednictvím Microsoft sales nepodporují převést vlastnictví fakturace předplatného. 

### <a id="no-button"></a> Proč se mi nezobrazuje můj odběr zadejte přenos podporu? 

Bohužel není podporováno všemi typy předplatných fakturační přenos vlastnictví. Chcete-li zobrazit seznam typů předplatného, které podporují přenosy, naleznete v tématu [podporované typy předplatného](#supported-subscription-types)

### <a id="no-button"></a> Proč dochází při pokusu převést vlastnictví fakturace předplatného chybu odepření přístupu? 

Pokud se snažíte převod předplatného Microsoft Azure plánování a nemáte potřebná oprávnění, zobrazí se tato chyba. Na převod předplatného plánu Microsoft Azure, musíte být vlastníkem nebo přispěvatelem části faktury, ke kterému se účtuje předplatné. Další informace najdete v tématu [spravovat předplatná pro části faktury](billing-understand-mca-roles.md#manage-subscriptions-for-invoice-section).


## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

- Zkontrolovat a aktualizovat Správce služby, Spolusprávci a ostatní role RBAC. Další informace najdete v tématu [přidat nebo změnit správce předplatného Azure](billing-add-change-azure-subscription-administrator.md) a [správě přístupu pomocí RBAC a webu Azure portal](../role-based-access-control/role-assignments-portal.md).
