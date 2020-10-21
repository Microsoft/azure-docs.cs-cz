---
title: Informace o převodu vlastnictví fakturace předplatného Azure
description: Tento článek vysvětluje vše, co potřebujete vědět před převodem vlastnictví fakturace předplatného Azure do jiného účtu.
keywords: převod předplatného azure, azure převod předplatného, přesun předplatného azure do jiného účtu, azure změna vlastníka předplatného, převod předplatného azure na jiný účet, azure převod fakturace
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: f4dd6d67d60603ed6cad7056cff4bb07dcb1c2e5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92149377"
---
# <a name="about-transferring-billing-ownership-for-an-azure-subscription"></a>Informace o převodu vlastnictví fakturace předplatného Azure

Tento článek vám pomůže porozumět všemu, co byste měli vědět před převodem vlastnictví fakturace předplatného Azure na jiný účet. 

Vlastnictví fakturace vašeho předplatného Azure můžete chtít převést, když opouštíte organizaci, nebo pokud chcete, aby se vaše předplatné účtovalo k jinému účtu. Převod vlastnictví fakturace na jiný účet poskytuje správcům v novém účtu oprávnění pro fakturační úkoly. Mohou změnit způsob platby, zobrazit si poplatky a předplatné zrušit.

Pokud chcete vlastnictví fakturace zachovat, ale chcete změnit typ vašeho předplatného, přečtěte si téma [Přepnutí předplatného Azure na jinou nabídku](../manage/switch-azure-offer.md). Pokud chcete řídit, kdo má přístup k prostředkům v předplatném, přečtěte si téma věnované [předdefinovaným rolím v Azure](../../role-based-access-control/built-in-roles.md).

Pokud jste zákazníkem se smlouvou Enterprise (EA), můžou vaši podnikoví správci převádět vlastnictví fakturace vašich předplatných mezi účty.

Vlastnictví předplatného může převést pouze správce fakturace účtu.

## <a name="determine-account-billing-administrator"></a>Určení správce fakturace účtu

<a name="whoisaa"></a>

Správce fakturace je osoba, která má oprávnění ke správě fakturace účtu. Má oprávnění přistupovat k fakturaci na webu [Azure Portal](https://portal.azure.com) a provádět různé fakturační úkoly, jako je vytváření předplatných, zobrazení a platby faktur nebo aktualizace způsobů platby.

Pokud chcete zjistit, pro které účty jste správcem fakturace, přejděte na [stránku Cost Management + Billing na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview). V levém podokně vyberte **Všechny rozsahy fakturace**. Na stránce s předplatnými se zobrazí všechna předplatná, pro která jste správcem fakturace.

Pokud si nejste jisti, kdo je správcem účtu předplatného, přejděte na [stránku Předplatná na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Vyberte předplatné, které chcete zkontrolovat, a podívejte se do části **Nastavení**. Vyberte **Vlastnosti** a správce účtu předplatného se zobrazí v poli **Správce účtu**.


## <a name="supported-subscription-types"></a>Podporované typy předplatného

Převod předplatného na webu Azure Portal je k dispozici pro níže uvedené typy předplatného. V současné době se převod nepodporuje pro [bezplatné zkušební verze](https://azure.microsoft.com/offers/ms-azr-0044p/) nebo předplatná [systému Azure v rámci licenčního programu Open (AIO)](https://azure.microsoft.com/offers/ms-azr-0111p/). Alternativní řešení najdete v článku [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Pokud chcete převést další předplatná, jako jsou plány podpory, [obraťte se na podporu Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)<sup>1</sup>
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Předplatitelé sady Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Průběžné platby dle aktuálního využití pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Plán Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)<sup>2</sup>

<sup>1</sup> S využitím portálu EA Portal.

<sup>2</sup> Podporuje se jen u účtů vytvořených během registrace na webu Azure.

## <a name="resources-transferred-with-subscriptions"></a>Prostředky převedené společně s předplatnými

Všechny prostředky, jako jsou virtuální počítače, disky a weby, se převedou na nový účet. Pokud ale převádíte předplatné do účtu v jiném tenantovi Azure AD, žádné [role správce](../manage/add-change-subscription-administrator.md) ani [přiřazení rolí Azure](../../role-based-access-control/role-assignments-portal.md) předplatného se nepřevedou. Dále se společně s předplatným nepřevádí [registrace aplikací](../../active-directory/develop/quickstart-register-app.md) a jiné služby specifické pro tenanta.

## <a name="transfer-account-ownership-to-another-countryregion"></a>Převod vlastnictví účtu do jiné země nebo oblasti

Pomocí webu Azure Portal bohužel nemůžete převádět předplatná mezi zeměmi ani oblastmi. Můžete je však převést tím, že vytvoříte žádost o podporu. Pokud chcete vytvořit žádost o podporu, [kontaktujte podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="transfer-a-subscription-from-one-account-to-another"></a>Převod předplatného z jednoho účtu do jiného

Pokud jste správcem dvou účtu, můžete mezi nimi převést předplatné. Vaše účty se koncepčně považují za účty dvou různých uživatelů, proto mezi nimi můžete převádět předplatná.
Pokud chcete zobrazit kroky, které je potřeba provést k převodu předplatného, projděte si téma [Převod vlastnictví fakturace předplatného Azure](../manage/billing-subscription-transfer.md).

## <a name="transferring-a-subscription-shouldnt-create-downtime"></a>Při převodu předplatného by nemělo dojít k výpadku

Pokud převádíte předplatné na účet ve stejném tenantovi Azure AD, nemá to žádný vliv na prostředky spuštěné v tomto předplatném. Informace o kontextu uložené v PowerShellu se ale neaktualizují, takže je možná budete muset vymazat nebo změnit nastavení. Pokud převádíte předplatné na účet v jiném tenantovi a rozhodnete se předplatné do tohoto tenanta přesunout, všichni uživatelé, skupiny a instanční objekty, které měly [přiřazení rolí Azure](../../role-based-access-control/role-assignments-portal.md) pro přístup k prostředkům v předplatném, tento přístup ztratí. Následkem toho může dojít k výpadku služeb.

## <a name="new-account-usage-and-billing-history"></a>Historie využití a fakturace nového účtu

Jediné informace, které mají uživatelé pro nový účet k dispozici, jsou informace o nákladech na předplatné za poslední měsíc. Zbytek historie využití a fakturace se společně s předplatným nepřevede.

## <a name="manually-migrate-data-and-services"></a>Ruční migrace dat a služeb

Při převodu předplatného se převedou i jeho prostředky. Pokud nemůžete převést vlastnictví předplatného, můžete jeho prostředky migrovat ručně. Další informace najdete v tématu, které se zabývá [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="remaining-subscription-credits"></a>Zbývající kredity předplatného 

Pokud máte předplatné sady Visual Studio nebo programu Microsoft Partner Network, získáváte měsíční kredit. Váš kredit se nepřenese společně předplatným do nového účtu. Uživatel, který přijímá žádost o převod, musí mít licenci sady Visual Studio, aby žádost o převod mohl přijmout. Předplatné používá kredit sady Visual Studio, který je k dispozici v účtu uživatele. Další informace najdete v tématu věnovaném [převodu předplatných sady Visual Studio a programu Partner Network](../manage/billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions).

## <a name="users-keep-access-to-transferred-resources"></a>Uživatelé si zachovají přístup k převedeným prostředkům

Mějte na paměti, že uživatelé s přístupem k prostředkům v předplatném si zachovají přístup i po převodu vlastnictví. Může ale dojít k odebrání [rolí správce](../manage/add-change-subscription-administrator.md) a [přiřazení rolí Azure](../../role-based-access-control/role-assignments-portal.md). Ke ztrátě přístupu dojde, když je váš účet v jiném tenantovi Azure AD, než je tenant předplatného, a uživatel, který odeslal požadavek na převod, přesune předplatné do tenanta vašeho účtu. 

Uživatele, kteří mají přiřazení rolí Azure pro přístup k prostředkům v předplatném, můžete zobrazit na webu Azure Portal. Přejděte na [stránku Předplatná na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Vyberte předplatné, které chcete zkontrolovat, a v levém podokně vyberte **Řízení přístupu (IAM)** . V horní části stránky vyberte **Přiřazení rolí**. Na stránce přiřazení rolí se zobrazí seznam všech uživatelů, kteří mají k předplatnému přístup.

I když během převodu dojde k odebrání [přiřazení rolí Azure](../../role-based-access-control/role-assignments-portal.md), uživatelé v účtu původního vlastníka mohou mít k předplatnému stále přístup prostřednictvím jiných mechanismů zabezpečení, mezi které patří:

* Certifikáty pro správu, které udělují uživateli oprávnění správce prostředků předplatného. Další informace najdete v tématu o [vytvoření a nahrání certifikátu pro správu Azure](../../cloud-services/cloud-services-certs-create.md).
* Přístupové klíče pro služby, jako je Storage. Další informace najdete v článku o [účtech Azure Storage](../../storage/common/storage-account-create.md).
* Oprávnění pro vzdálený přístup ke službám, jako je Azure Virtual Machines.

Pokud příjemce potřebuje omezit přístup k prostředkům, měl by zvážit aktualizaci tajných klíčů přidružených ke službě. Většinu prostředků je možné aktualizovat. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a v nabídce centra vyberte **Všechny prostředky**. Vyberte prostředek. Na stránce prostředku vyberte **Nastavení**. Tady můžete zobrazit a aktualizovat existující tajné klíče.

## <a name="you-pay-for-usage-when-you-receive-ownership"></a>Po získání vlastnictví platíte za využití

Váš účet zodpovídá za platbu za jakékoliv využití, které je hlášené od okamžiku převodu. Může dojít k využití, které se uskutečnilo před převodem, ale bylo nahlášeno až poté. Takové využití je součástí faktury vašeho účtu.

## <a name="use-a-different-payment-method"></a>Použití jiného způsobu platby

Při přijímání žádosti o převod můžete vybrat stávající způsob platby, který je propojený s vaším účtem, nebo přidat nový způsob platby.

## <a name="transfer-enterprise-agreement-subscription-ownership"></a>Převod vlastnictví předplatného se smlouvou Enteprise

Podnikový správce může aktualizovat vlastnictví jakéhokoli účtu i po odchodu původního vlastníka účtu z organizace. Další informace o převodu účtů se smlouvou Azure Enterprise najdete v tématu [Převody smluv Azure Enterprise](../manage/ea-transfers.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Převod vlastnictví fakturace předplatného Azure](../manage/billing-subscription-transfer.md)