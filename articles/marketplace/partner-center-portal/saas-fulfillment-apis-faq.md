---
title: Rozhraní API pro plnění SaaS – Nejčastější dotazy | Azure Marketplace
description: Zjišťování a nákup prostředí pro zákazníky SaaS nabídky ve Azure Marketplace.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 2f8aa41604ac176181252fb7b30cdeb2f039d10f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822879"
---
# <a name="saas-fulfillment-apis---faq"></a>Rozhraní API pro naplnění SaaS – nejčastější dotazy

V seznamu jsou uvedené požadavky na integraci s Azure Marketplace, které umožní zákazníkům Azure přihlásit se k odběru nabídky SaaS.

## <a name="discovery-experience"></a>Prostředí zjišťování

Po publikování nabídky můžou uživatelé Azure zjistit nabídku SaaS v Azure Marketplace. Vaši zákazníci budou moct filtrovat nabídky na základě typu produktu (SaaS) a zjišťovat služby SaaS, které vás zajímají.

## <a name="purchase-experience"></a>Možnosti nákupu

Jakmile se uživatel zajímá o konkrétní SaaS službu, uživatel se k němu může přihlásit z Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Co to znamená, aby se uživatel Azure přihlásil k odběru nabídky SaaS v Azure Marketplace?

To znamená, že uživatel může zobrazit podmínky použití a prohlášení o zásadách ochrany osobních údajů, které jsou přidružené ke službě SaaS, a vyjádřit souhlas s tím, jak platíte za fakturační podmínky nastavené vámi a vydavatelem nabídky SaaS na faktuře Microsoftu. Uživatelé můžou použít svůj existující platební profil v Azure k placení za spotřebu služby SaaS.

To je užitečné z mnoha důvodů. Zákazníci teď můžou vyhledat a přihlásit se k odběru na jednom místě pomocí Microsoft Cloud platformy jako důvěryhodného zdroje, aniž by museli VET každého softwaru ISV, který chce použít. Zákazníci můžou také použít svůj existující platební profil, aniž by museli explicitně platit jednotlivé nezávislé software společnosti.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>Účtuje se uživateli automaticky při přihlášení k odběru nabídky?

Při přihlášení k odběru nabídky SaaS se uživatel dohodl za spotřebu služby SaaS prostřednictvím platformy Microsoft. Poplatky se ale spustí jenom v případě, že se nabídka spotřebovává. Uživatel musí přejít na nabídku SaaS a potvrdit vytvoření účtu, aby bylo možné nabídku začít využívat. Pak budete informovat společnost Microsoft, aby zahájila fakturaci pro toto předplatné SaaS zákazníka.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Jak se dozvíte, když se uživatel přihlásí k odběru vaší nabídky SaaS?

Po přihlášení k odběru nabídky může uživatel Azure vyhledat a spravovat všechny své nabídky v Azure. Ve výchozím nastavení je stav nově předplaceného SaaS nabídky **"zřizování", čeká na vyřízení**. V tomto stavu se uživateli Azure zobrazí výzva s akcí **konfigurovat účet**, aby bylo možné přejít ke svému prostředí správy předplatného SaaS ve Azure Portal.

Když uživatel klikne na **"konfigurovat účet"** , budou přesměrováni na web služby SaaS. Adresa URL, na kterou se přechází, je poskytnuta vydavatelem v době publikování nabídky. Tato stránka je označována jako úvodní stránka vydavatele. Uživatelé Azure by měli být schopni se přihlásit k cílové stránce SaaS na základě svých stávajících přihlašovacích údajů služby AAD v Azure.

Když se uživatel Azure přesměruje na cílovou stránku, přidá se do adresy URL dotazu token. Tento token je krátký a platný po dobu 24 hodin. Pak můžete zjistit přítomnost tohoto tokenu a zavolat rozhraní API Microsoftu a získat tak další kontext přidružený k tokenu.

![Tok zákaznického předplatného](media/saas-metering-service-integration-flow-a.png)

V dokumentu [rozhraní API pro plnění SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) najdete další informace o kontraktu rozhraní API pro zpracování scénářů Transact-in v životním cyklu nabídky SaaS.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Jak poznáte nabídku SaaS, ke které se uživatel přihlašuje v Azure?

Odpověď na rozhraní `Resolve` API obsahuje informace o nabídce a plánu přidružené k předplatnému SaaS.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Jak může uživatel Azure změnit plán přidružený k tomuto předplatnému Azure?

* Uživatel Azure může změnit plán přidružený k předplatnému SaaS přímo v prostředí SaaS nebo prostřednictvím platformy Microsoft.

* Převody lze kdykoli provést v rámci fakturačního cyklu. Je nutné potvrdit jakýkoli převod, který bude platit až po potvrzení.

* Předem placené tarify (**měsíční** nebo **roční**) jsou ohodnocené poměrně. Všechna nadlimitní využití vygenerovaná v době převodu se budou účtovat v další faktuře. Nové překročení limitů bude vygenerováno na základě nového plánu.

>[!Note]
>Pokud nechcete podporovat konkrétní cesty pro převod, můžete zablokovat Downgrady.

Následující sekvence zachycuje tok, když zákazník Azure změní plán v prostředí SaaS:

![Tok změn plánu zákazníků](media/saas-metering-service-integration-flow-b.png)

Následující sekvence zachycuje tok, když zákazník Azure změní plán v prezentace Microsoftu.

![Tok změn plánu Customer prezentace](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Jak může uživatel Azure zrušit odběr plánu přidruženého k předplatnému Azure?

Uživatel Azure může zrušit odběr zakoupené nabídky SaaS buď přímo v prostředí SaaS, nebo prostřednictvím platformy Microsoft. Jakmile se uživatel odhlásí, nebude se už účtovat z dalšího fakturačního cyklu.

Následující sekvence zachycuje tok, když se zákazník Azure odhlásí z nabídky SaaS v prostředí SaaS:

![Zákazník odhlašuje odběr v SaaS prostředí](media/saas-metering-service-integration-flow-d.png)

Následující sekvence zachycuje tok, když uživatel Azure zruší odběr služby prezentace od Microsoftu:

![Zákazník odhlašuje odběr v prezentace Microsoftu.](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu [rozhraní API služby měření softwaru Marketplace](./marketplace-metering-service-apis.md) .
