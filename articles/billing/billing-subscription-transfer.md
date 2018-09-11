---
title: Přenos vlastnictví předplatného Azure na jiný účet | Dokumentace Microsoftu
description: Popisuje, jak převést předplatné Azure na jiného uživatele a některé nejčastější dotazy (FAQ) o procesu
keywords: Převod předplatného přenos předplatného azure, azure, přesunout předplatné azure na jiný účet azure změnit vlastníka předplatného, přenos předplatného azure na jiný účet
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f58c156019048a4e6b08267bf28325857ec69b3a
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302199"
---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>Přenos vlastnictví předplatného Azure na jiný účet

Převod vašeho předplatného na jiného uživatele v centru účtů můžete změnit správce účtu a předá vlastnictví fakturace předplatného. Chcete-li změnit vašeho předplatného na jinou nabídku, [přepnout vašeho předplatného Azure na jinou nabídku](billing-how-to-switch-azure-offer.md).

> [!IMPORTANT]
> 
> Pokud přenesete předplatné na nový Azure AD tenanta, všechna přiřazení rolí v [řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md) se trvale odstraní z tenanta zdroje a nebudou migrována k cílovému tenantovi.

> [!div class="nextstepaction"]
> [Pomozte vylepšit dokumentaci k fakturaci Azure](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="transfer-ownership-of-an-azure-subscription"></a>Převod vlastnictví předplatného Azure

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Přihlaste se na [centra účtů Azure](https://account.windowsazure.com/Subscriptions) jako správce účtu. Chcete-li zjistit, kdo je správcem účtu předplatného, přečtěte si téma [– nejčastější dotazy](#faq).

1. Vyberte předplatné, které chcete převést.

1. Ověřte, zda je vaše předplatné nárok samoobslužné přenos kontrolou **nabízejí** a **ID nabídky** s [seznamu podporovaných nabídky](#supported).

   ![Ověřte ID nabídky předplatného v centru účtů](./media/billing-subscription-transfer/image0.png)
1. Klikněte na **Převod vlastnictví předplatného**.

   ![Kartu předplatná účtu Azure](./media/billing-subscription-transfer/image1.png)
1. Zadejte příjemce.

   > [!IMPORTANT]
   > 
   > Pokud přenesete předplatné na nový Azure AD tenanta, všechna přiřazení rolí v [řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md) se trvale odstraní z tenanta zdroje a nebudou migrována k cílovému tenantovi.

   ![Dialogové okno přenos předplatného](./media/billing-subscription-transfer/image2.PNG)

1. Příjemce automaticky dostane e-mail s odkazem pro akceptaci.

   ![E-mailu přenos předplatného příjemci](./media/billing-subscription-transfer/image3.png)
1. Příjemce klikne na odkaz a postupuje podle pokynů, včetně zadání platebních údajů.

   ![První předplatné přenos webové stránky](./media/billing-subscription-transfer/image4.png)

   ![Druhý předplatné přenos webové stránky](./media/billing-subscription-transfer/image5.png)
1. Výborně! Nyní převodu předplatného.

<a id="EA"></a>

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>Převod vlastnictví předplatného pro zákazníky, kteří Enterprise Agreement (EA)

Podnikový správce může převést vlastnictví předplatného v rámci registrace. Abyste mohli začít, najdete v článku [přenos vlastnictví účtu](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) přes portál EA.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Další kroky následující po přijetí vlastnictví předplatného

1. Teď jste správce účtu. Zkontrolovat a aktualizovat Správce služby, Spolusprávci a ostatní role RBAC. Další informace najdete v tématu [přidat nebo změnit správce předplatného Azure](billing-add-change-azure-subscription-administrator.md) a [správě přístupu pomocí RBAC a webu Azure portal](../role-based-access-control/role-assignments-portal.md).
1. Aktualizace přihlašovacích údajů spojené se službami tohoto předplatného, včetně:
   1. Certifikáty pro správu, které uživateli udělit oprávnění správce pro prostředky předplatného. Další informace najdete v tématu [vytvoření a nahrání certifikátu pro správu pro Azure](../cloud-services/cloud-services-certs-create.md)
   1. Přístupové klíče pro služby, jako jsou úložiště. Další informace najdete v tématu [účty Azure storage](../storage/common/storage-create-storage-account.md)
   1. Pověření vzdáleného přístupu pro služby, jako jsou Azure Virtual Machines. 
1. [Aktualizovat upozornění fakturace pro toto předplatné](billing-set-up-alerts.md) na [centra účtů Azure](https://account.windowsazure.com/Subscriptions). 
1. Pokud pracujete s partnerem, zvažte aktualizaci ID partnera v tomto předplatném. Můžete aktualizovat ID partnera v [webu Azure portal](https://portal.azure.com).

<a id="supported"></a>

## <a name="whats-supported"></a>Co je podporováno:

Převod předplatného samoobslužné je k dispozici pro nabídky nebo typy předplatného, které jsou uvedeny v následující tabulce. Momentálně nelze převést bezplatnou zkušební verzi nebo [Azure v Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) předplatných. Alternativní řešení, najdete v části [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md). Přenášet další předplatná, jako jsou [sponzorství](https://azure.microsoft.com/offers/ms-azr-0036p/) nebo plány podpory, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

| Název nabídky                                                                             | Číslo nabídky |
|----------------------------------------------------------------------------------------|--------------|
| [Smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*|MS-AZR-0017P        |
| [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)          | MS-AZR-0025P        |
| [Předplatné MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)                     | MS-AZR-0062P        |
| [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/)                      | MS-AZR-0003P        |
| [Vývoj/testování s průběžnými platbami](https://azure.microsoft.com/offers/ms-azr-0023p/)             | MS-AZR-0023P        |
| [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)           | MS-AZR-0063P        |
| [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P        |
| [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)         | MS-AZR-0059P        |
| [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)    | MS-AZR-0060P        |

\* [Na portálu EA](#EA)

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

### <a name="whoisaa"></a> Kdo je správcem účtu předplatného?

Správce účtu je osoba, která registrovanou službu nebo zakoupili předplatné Azure. Mají autorizaci pro přístup [centra pro účty](https://account.azure.com/Subscriptions) a provádět různé úlohy správy, jako je vytvářet předplatná, zrušit předplatná, fakturaci a předplatné změnit nebo změnit správce služeb. Pokud si nejste jisti, kdo je správcem účtu pro předplatné, postupujte následovně Pokud chcete zjistit.

1. Přejděte [stránce předplatná na webu Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Vyberte předplatné, které chcete zkontrolovat a pak hledejte v části **nastavení**.
1. Vyberte **vlastnosti**. Správce účtu předplatného se zobrazí v **správce účtu** pole.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Všechno, co přenosu? Včetně skupin prostředků, virtuální počítače, disků a další služby spuštěné?

Všechny prostředky, jako jsou virtuální počítače, disky a websites přenos do nového vlastníka. Však žádné [role správce](billing-add-change-azure-subscription-administrator.md) a [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md) nepřenášejí zásady, které jste nastavili v různých adresářích. Navíc [registrace aplikací](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) a dalších služeb specifickým pro tenanta se nepřevádějí společně.

### <a id="no-button"></a> Proč nevidíte tlačítko "Převod předplatného"?

Bohužel není k dispozici pro vaše nabídky nebo zemi přenos samoobslužné předplatné. Na převod vašeho předplatného [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Převod předplatného za následek způsobilo výpadek služeb?

Neexistuje žádný vliv na službu. Předplatné převedete předplatné v aktuálním účtu správce zruší a vytvoří odběr v rámci účtu příjemce. Nový odběr je přidružená k podpůrné služby Azure. ID předplatného zůstává stejná.

### <a name="how-do-i-use-this-process-to-change-the-directory-for-subscription"></a>Použití tohoto procesu můžete změnit adresář pro předplatné

Předplatné Azure se vytvoří v adresáři, který patří správce účtu. Chcete-li změnit adresář, přeneste toto předplatné k účtu uživatele v cílovém adresáři. Po dokončení kroků tak, aby přijímal přenosy tomuto uživateli předplatné automaticky přesunout do cílového adresáře.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-organization-do-they-continue-to-have-access-to-my-resources"></a>Pokud převzít vlastnictví fakturace předplatného z jiné organizace, nadále využívat Moje prostředky?

Pokud předplatné je přenesené na jiného tenanta, uživatelé přidružení k předchozí tenanta přijít o přístup k předplatnému. I v případě, že uživatel není správce služby nebo spolupracujícího správce už nepotřebujeme, mohou stále mít přístup k předplatnému přes jiné mechanismy zabezpečení, včetně:

* Certifikáty pro správu, které uživateli udělit oprávnění správce pro prostředky předplatného. Další informace najdete v tématu [vytvoření a nahrání certifikátu pro správu pro Azure](../cloud-services/cloud-services-certs-create.md).
* Přístupové klíče pro služby, jako jsou úložiště. Další informace najdete v článku o [účtech Azure Storage](../storage/common/storage-create-storage-account.md).
* Pověření vzdáleného přístupu pro služby, jako jsou Azure Virtual Machines.

Pokud příjemce je potřeba omezit přístup ke svým prostředkům, zvažte jejich aktualizuje všechny tajné kódy související se službou. Většinu prostředků lze aktualizovat pomocí následujících kroků:

  1. Přejděte na [Azure Portal](https://portal.azure.com).
  2. V nabídce centra vyberte **všechny prostředky**.
  3. Vyberte prostředek.
  4. V okně prostředků klikněte na tlačítko **nastavení**. Tady si můžete zobrazit a aktualizovat existující tajných kódů.

### <a name="if-i-transfer-the-subscription-in-the-middle-of-the-billing-cycle-does-the-recipient-pay-for-the-entire-billing-cycle"></a>Pokud je možné převést předplatné uprostřed fakturačního cyklu, příjemce platit pro celý fakturační cyklus?

Odesílatel je zodpovědná za platby za jakékoliv využití hlášené až do chvíle, dokončení přenosu. Příjemce je zodpovědná za využití hlášené z doby přenosu a vyšší. Může existovat několik využití, která proběhla před převodem ale byla nahlášena později. Využití je součástí příjemce faktury.

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>Má příjemce přístup k využití a historii fakturace?

  Jediná informace, které jsou k dispozici pro příjemce je množství poslední faktury nebo pokud předplatné bylo převedeno dříve, než první účet byl generován, aktuální zůstatek. Zbývající část využití a historii fakturace nepřenese s předplatným.

### <a name="can-the-offer-be-changed-during-a-transfer"></a>Je nabídka změnit během přenosu?

Nabídka musí zůstat stejná. Chcete-li změnit vaší nabídky, [přepnout vašeho předplatného Azure na jinou nabídku](billing-how-to-switch-azure-offer.md).

### <a name="can-i-transfer-a-subscription-to-a-user-account-in-another-country"></a>Je možné převést předplatné pro účet uživatele v jiné zemi?

Ne, není podporován převod předplatného uživatelský účet v jiné zemi. Příjemce uživatelský účet musí být ve stejné zemi.

### <a name="can-the-recipient-use-a-different-payment-method"></a>Příjemce, můžete použít jiný způsob platby?

Ano. Ale historie fakturace předplatného je rozdělit mezi dva účty.  

### <a name="is-the-payment-method-impacted-after-i-transferred-an-azure-subscription"></a>Má vliv způsob platby po přesunu předplatného Azure?

Přijmout převod předplatného, musí být uvedeny platební kartu ani podobně jako způsob platby za předplatné. Například pokud Bob přenese předplatné Jana a Jana přijímá přenos, Jana poskytují způsob platby za předplatné. Po dokončení převodu se mu fakturuje Jana předplatného není Bob.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Jak migrovat data a služby pro svoje předplatné Azure k novému předplatnému?

Pokud nelze převést vlastnictví předplatného, můžete ručně migrovat vaše prostředky. Zobrazit [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.