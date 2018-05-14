---
title: Přenos vlastnictví předplatného Azure na jiný účet | Microsoft Docs
description: Popisuje, jak převést předplatné Azure do jiného uživatele a některé nejčastější dotazy (FAQ) o procesu
keywords: přenos předplatného přenos předplatného azure, azure, přesunout do jiné vlastník předplatného změnu účtu azure předplatné azure, přenos předplatného azure na jiný účet
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
ms.date: 05/11/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 110e2f611ba8bfc42fe17de6aa4487683db4a414
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>Přenos vlastnictví předplatného služby Azure na jiný účet

Přenos předplatného na jiného uživatele v Centru pro účet a změňte účet správce ruční přes vlastnictví fakturace předplatného. Chcete-li změnit předplatné na jinou nabídku, [vašeho předplatného Azure přepnout na jinou nabídku](billing-how-to-switch-azure-offer.md).

> [!IMPORTANT]
> 
> Pokud předáte předplatné nové Azure AD klienta, všechna přiřazení rolí v [řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md) jsou trvale odstraněny z klienta, zdroje a nebyly migrovány do cílového klienta.

## <a name="transfer-ownership-of-an-azure-subscription"></a>Přenos vlastnictví předplatného Azure

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. Přihlaste se na [centra účtů Azure](https://account.windowsazure.com/Subscriptions) jako účet správce. Chcete-li zjistit, kdo je správce účtu předplatného, přečtěte si téma [nejčastější dotazy](#faq).

1. Vyberte předplatné pro přenos.

1. Ověřte, zda je vaše předplatné vhodné pro přenos samoobslužný postup kontrolou **nabízejí** a **nabízejí ID** s [seznamu podporovaných nabízí](#supported).

   ![Ověřte ID nabídky předplatného v centru účtů](./media/billing-subscription-transfer/image0.png)
1. Klikněte na **Převod vlastnictví předplatného**.

   ![Karta odběry účet Azure](./media/billing-subscription-transfer/image1.png)
1. Zadejte příjemce.

   > [!IMPORTANT]
   > 
   > Pokud předáte předplatné nové Azure AD klienta, všechna přiřazení rolí v [řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md) jsou trvale odstraněny z klienta, zdroje a nebyly migrovány do cílového klienta.

   ![Dialogové okno přenos předplatného](./media/billing-subscription-transfer/image2.PNG)

1. Příjemce automaticky dostane e-mail s odkazem pro akceptaci.

   ![Předplatné přenosu e-mailu k příjemce](./media/billing-subscription-transfer/image3.png)
1. Příjemce klikne na odkaz a postupuje podle pokynů, včetně zadání platebních údajů.

   ![První předplatné přenos webové stránky](./media/billing-subscription-transfer/image4.png)

   ![Druhý předplatné přenos webové stránky](./media/billing-subscription-transfer/image5.png)
1. Výborně! Předplatné je nyní přenést.

<a id="EA"></a>

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>Přenos vlastnictví předplatného pro zákazníky Enterprise Agreement (EA)

Správce podnikové sítě může převést vlastnictví předplatných v rámci zápisu. Abyste mohli začít, najdete v části [přenos vlastnictví účet](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) EA portálu.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Další kroky následující po přijetí vlastnictví předplatného

1. Nyní jste účet správce. Zkontrolovat a aktualizovat Správce služby, Spolusprávci a jiné role RBAC. Další informace najdete v tématu [přidání nebo změna role Správce služby Azure, které spravují předplatné nebo služby](billing-add-change-azure-subscription-administrator.md).
1. Aktualizujte přihlašovací údaje související s toto předplatné služby, včetně:
   1. Certifikáty pro správu, které udělit oprávnění správce pro prostředky předplatného. Další informace najdete v tématu [vytvoření a nahrání certifikátu správy pro Azure.](../cloud-services/cloud-services-certs-create.md)
   1. Přístupové klávesy pro služby jako úložiště. Další informace najdete v tématu [účty Azure storage](../storage/common/storage-create-storage-account.md)
   1. Pověření vzdáleného přístupu pro služby, jako virtuální počítače Azure. 
1. [Aktualizovat fakturace výstrahy pro toto předplatné](billing-set-up-alerts.md) na [centra účtů Azure](https://account.windowsazure.com/Subscriptions). 
1. Pokud pracujete s partnerem, zvažte aktualizaci ID partnera u tohoto předplatného. Můžete aktualizovat ID partnera v [portál Azure](https://portal.azure.com).

<a id="supported"></a>

## <a name="whats-supported"></a>Co je podporováno:

Přenos předplatného samoobslužný postup je k dispozici pro nabídky nebo typy předplatného, které jsou uvedené v následující tabulce. Aktuálně nemůže přenést bezplatnou zkušební verzi nebo [Azure v otevřené (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/) odběry. Alternativní řešení, najdete v části [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md). Přenášet další odběry, jako například [sponzorství](https://azure.microsoft.com/offers/ms-azr-0036p/) nebo podporovat plány, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

| Název nabídky                                                                             | Číslo nabídky |
|----------------------------------------------------------------------------------------|--------------|
| [Smlouva Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*|MS-AZR-0017P        |
| [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)          | MS-AZR-0025P        |
| [MSDN platformy](https://azure.microsoft.com/offers/ms-azr-0062p/)                     | MS-AZR-0062P        |
| [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/)                      | MS-AZR-0003P        |
| [Průběžné platby vývoje/testování](https://azure.microsoft.com/offers/ms-azr-0023p/)             | MS-AZR-0023P        |
| [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)           | MS-AZR-0063P        |
| [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P        |
| [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)         | MS-AZR-0059P        |
| [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)    | MS-AZR-0060P        |

\* [Prostřednictvím portálu EA](#EA)

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

### <a name="whoisaa"></a> Kdo je správce účtu předplatného?

Správce účtu je osoba, která registraci aplikace nebo kód zakoupili předplatné Azure. Mají autorizaci pro přístup [centra účtů](https://account.azure.com/Subscriptions) a provádět různé úlohy správy, jako jsou vytvářet odběry, zrušit předplatné, změnit fakturace pro předplatné nebo změnit správce služeb. Pokud si nejste jistí, který je účet správce pro předplatné, použijte následující kroky a zjistěte.

1. Přejděte [odběry stránky na portálu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Vyberte předplatné, které chcete kontrolovat, a pak hledejte v části **nastavení**.
1. Vyberte **vlastnosti**. Správce účtu předplatného se zobrazí v **správce účtu** pole.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>Všechno přenosu? Včetně skupin prostředků, virtuálních počítačů, disků a další spuštěné služby?

Všechny prostředky, například virtuálních počítačů, disků a weby přenos do nového vlastníka. Však žádné [role správce](billing-add-change-azure-subscription-administrator.md) a [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/role-assignments-portal.md) zásady, které jste nastavili nepřenášejí mezi různé adresáře. Navíc [registrace aplikace](../active-directory//develop/active-directory-integrating-applications.md) a jinými službami konkrétního klienta není přenos společně.

### <a id="no-button"></a> Proč se nezobrazí tlačítko "Přenesení předplatného"?

Přenos předplatného samoobslužný postup bohužel není k dispozici pro nabídky nebo zemi. Přenos vašeho předplatného, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>Přenos předplatného důsledkem výpadek služeb?

Neexistuje žádný vliv na službu. Přenos předplatného zruší předplatného v rámci aktuálního účtu správce a vytvoří odběr pod účtem příjemce. Nový odběr je přidružena k základní služby Azure. ID předplatného se nezmění.

### <a name="how-do-i-use-this-process-to-change-the-directory-for-subscription"></a>Jak používat tento proces a změnit adresář pro předplatné?

Předplatné Azure je vytvořen v adresáři, který je součástí správce účtu. Chcete-li změnit adresář, přeneste předplatné do účet uživatele v cílovém adresáři. Po dokončení tohoto uživatele kroky tak, aby přijímal přenos předplatného je automaticky přesunuta do cílový adresář.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-organization-do-they-continue-to-have-access-to-my-resources"></a>Pokud převzít vlastnictví fakturace předplatného z jiné organizace, se nadále přístup k mé prostředkům?

Pokud předplatné se přenese do jiného klienta, uživatelé, přidružený k předchozí klientovi ztratit přístup k předplatnému. I když uživatel není správce služby nebo spolusprávce už, mohou mít stále přístup k předplatnému pomocí jiné mechanismy zabezpečení, včetně:

* Certifikáty pro správu, které udělit oprávnění správce pro prostředky předplatného. Další informace najdete v tématu [vytvoření a nahrání certifikátu pro správu pro Azure](../cloud-services/cloud-services-certs-create.md).
* Přístupové klávesy pro služby jako úložiště. Další informace najdete v tématu [účty Azure storage](../storage/common/storage-create-storage-account.md).
* Pověření vzdáleného přístupu pro služby, jako virtuální počítače Azure.

Pokud příjemce potřebuje k omezení přístupu ke svým prostředkům, se zvažte aktualizaci všech tajných klíčů spojené s touto službou. Většina prostředků můžete aktualizovat pomocí následujících kroků:

  1. Přejděte na [portál Azure](https://portal.azure.com).
  2. V nabídce centra vyberte **všechny prostředky**.
  3. Vyberte prostředek.
  4. V okně prostředků klikněte na **nastavení**. Zde si můžete zobrazit a aktualizovat existující tajných klíčů.

### <a name="if-i-transfer-the-subscription-in-the-middle-of-the-billing-cycle-does-the-recipient-pay-for-the-entire-billing-cycle"></a>Pokud přenést předplatné uprostřed fakturačního cyklu, příjemce platím pro celý fakturaci cyklu?

Odesílatel je zodpovědná za platbu žádné využití, které byla nahlášena až do chvíle, že dokončení přenosu. Příjemce je zodpovědná za využití nahlásila čas přenosu a vyšší. Může mít některé využití, která byla provedena před přenosu ale ohlásil později. Využití je součástí příjemce faktury.

### <a name="does-the-recipient-have-access-to-usage-and-billing-history"></a>Má příjemce přístup k využití a fakturace historie?

  Veškeré informace, které jsou k dispozici k příjemce je množství poslední faktury nebo pokud předplatné bylo přeneseno před první faktury generuje, aktuální stav. Využití a historie fakturace zbytek k přenosu k předplatnému.

### <a name="can-the-offer-be-changed-during-a-transfer"></a>Lze změnit nabídku během přenosu?

Nabídka musí zůstat stejné. Chcete-li změnit nabídku, [vašeho předplatného Azure přepnout na jinou nabídku](billing-how-to-switch-azure-offer.md).

### <a name="can-i-transfer-a-subscription-to-a-user-account-in-another-country"></a>Můžou přenášet předplatné na uživatelský účet v jiné zemi?

Ne, není podporován přenosu předplatné na uživatelský účet v jiné zemi. Příjemce uživatelský účet musí být ve stejné zemi.

### <a name="can-the-recipient-use-a-different-payment-method"></a>Příjemce, můžete použít jiný způsob platby?

Ano. Ale historie fakturace předplatného je rozdělit mezi dva účty.  

### <a name="is-the-payment-method-impacted-after-i-transferred-an-azure-subscription"></a>Je způsob platby dopad po přesunu předplatné Azure?

Přijmout přenos předplatného, musí se zadat platební karty nebo podobné způsob platby platit pro předplatné. Například pokud Bob přenese předplatné Jana a Jana přijímá přenos, Jana musí poskytnout způsob platby a zaplatit předplatné. Po dokončení přenosu Jana se fakturuje pro předplatné není Bob.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>Jak migrovat data a služby pro Moje předplatné Azure do nového předplatného?

Pokud nelze převést vlastnictví předplatného, můžete migrovat ručně vašich prostředků. V tématu [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md).

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Obraťte se na podporu.

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) získat rychle vyřešit problém.