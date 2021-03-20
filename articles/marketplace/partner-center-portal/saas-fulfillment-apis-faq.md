---
title: Nejčastější dotazy k rozhraní API pro plnění SaaS – Microsoft Commercial Marketplace
description: Seznamte se s několika požadavky na integraci pro komerční tržiště Microsoftu, aby se zákazníci Azure mohli přihlásit k odběru SaaS nabídek.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 4c5d8b438764fa9aa3838b2225c63d412afc519b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88606813"
---
# <a name="common-questions-about-saas-fulfillment-apis"></a>Běžné otázky k rozhraní API pro plnění SaaS

Tento článek popisuje několik požadavků na integraci pro komerční tržiště Microsoftu, které umožní zákazníkům Azure přihlásit se k odběru SaaS nabídek.

## <a name="discovery-experience"></a>Prostředí zjišťování

Po publikování nabídky SaaS ji uživatelé Azure můžou zjistit v Azure Marketplace. Vaši zákazníci mohou filtrovat nabídky na základě typu produktu (SaaS) a zjišťovat služby SaaS, které vás zajímají.

## <a name="purchase-experience"></a>Možnosti nákupu

Jakmile se uživatel zajímá o konkrétní SaaS službu, uživatel se k němu může přihlásit z Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Co to znamená, aby se uživatel Azure přihlásil k odběru nabídky SaaS v Azure Marketplace?

To znamená, že uživatel může zobrazit podmínky použití a prohlášení o zásadách ochrany osobních údajů, které jsou přidružené ke službě SaaS, a vyjádřit souhlas s tím, jak platíte za fakturační podmínky nastavené vámi a vydavatelem nabídky SaaS na faktuře Microsoftu. Uživatelé můžou použít svůj existující platební profil v Azure k placení za spotřebu služby SaaS.

Tato možnost je výhodná pro několik způsobů. Zákazníci teď můžou vyhledat a přihlásit se k odběru na jednom místě pomocí Microsoft Cloud platformy jako důvěryhodného zdroje, aniž by museli VET každého softwaru ISV, který chce použít. Zákazníci můžou také použít svůj existující platební profil, aniž by museli explicitně platit jednotlivé nezávislé software společnosti.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>Účtuje se uživateli automaticky při přihlášení k odběru nabídky?

Při přihlášení k odběru nabídky SaaS se uživatel dohodl za spotřebu služby SaaS prostřednictvím platformy Microsoft. Poplatky se ale spustí jenom v případě, že se nabídka spotřebovává. Uživatel musí přejít na nabídku SaaS a potvrdit vytvoření účtu, aby bylo možné nabídku začít využívat. Pak budete informovat společnost Microsoft, aby zahájila fakturaci pro toto předplatné SaaS zákazníka.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Jak se dozvíte, když se uživatel přihlásí k odběru vaší nabídky SaaS?

Po přihlášení k odběru nabídky může uživatel Azure vyhledat a spravovat všechny své nabídky v Azure. Ve výchozím nastavení se stav nově přihlášené nabídky SaaS zobrazuje jako **zřizování a čeká na vyřízení**. V tomto stavu se uživateli Azure zobrazí výzva s akcí **Konfigurace účtu**, aby bylo možné přejít ke svému prostředí pro správu předplatného SaaS ve Azure Portal.

Když uživatel vybere **konfigurovat účet**, přesměruje se na web služby SaaS. Vydavatel nakonfiguroval adresu URL v době publikování nabídky. Tato stránka je označována jako úvodní stránka vydavatele. Uživatelé Azure se přihlásí k cílové stránce SaaS na základě svých stávajících přihlašovacích údajů služby AAD v Azure.

Když se uživatel Azure přesměruje na cílovou stránku, přidá se do adresy URL dotazu token. Tento token je krátkodobě a platný po dobu 24 hodin. Pak můžete zjistit přítomnost tohoto tokenu a zavolat rozhraní API Microsoftu a získat tak další kontext přidružený k tokenu.

![Tok zákaznického předplatného](media/saas-metering-service-integration-flow-a.png)

Další informace o kontraktu rozhraní API pro zpracování scénářů Transact-in v životním cyklu nabídky SaaS najdete v tématu [rozhraní API pro doplňování SaaS](pc-saas-fulfillment-api-v2.md).

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Jak poznáte nabídku SaaS, ke které se uživatel přihlašuje v Azure?

Odpověď na `Resolve` rozhraní API obsahuje informace o nabídce a plánu přidružené k předplatnému SaaS.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Jak může uživatel Azure změnit plán přidružený k tomuto předplatnému Azure?

* Uživatel Azure může změnit plán přidružený k předplatnému SaaS přímo v prostředí SaaS nebo prostřednictvím platformy Microsoft.

* Převody lze kdykoli provést v rámci fakturačního cyklu. Zobrazí se výzva, abyste potvrdili případné převody, které začnou platit po potvrzení.

* Předem placené tarify (**měsíční** nebo **roční**) jsou ohodnocené poměrně. Všechna nadlimitní využití vygenerovaná v době převodu se budou účtovat v další faktuře. Nové překročení limitů bude vygenerováno na základě nového plánu.

>[!Note]
>Pokud nechcete podporovat konkrétní cesty pro převod, můžete zablokovat Downgrady.

Následující sekvence zachycuje tok, když zákazník Azure změní plán v prostředí SaaS:

![Tok změn plánu zákazníků](media/saas-metering-service-integration-flow-b.png)

Následující sekvence zachycuje tok, když zákazník Azure změní plán v online obchodě Microsoftu:

![Tok změn plánu online obchodu zákazníka](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Jak může uživatel Azure zrušit odběr plánu přidruženého k předplatnému Azure?

Uživatel Azure může zrušit odběr zakoupené nabídky SaaS buď přímo v prostředí SaaS, nebo prostřednictvím platformy Microsoft. Jakmile se uživatel odhlásí, nebude se už účtovat z dalšího fakturačního cyklu.

Následující sekvence zachycuje tok, když se zákazník Azure odhlásí z nabídky SaaS v prostředí SaaS:

![Zákazník odhlašuje odběr v SaaS prostředí](media/saas-metering-service-integration-flow-d.png)

Následující sekvence zachycuje tok, když uživatel Azure zruší odběr online obchodu Microsoftu:

![Zákazník odhlašuje odběr online obchodu Microsoftu.](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Další kroky

[Rozhraní API služeb měření na marketplace](./marketplace-metering-service-apis.md)
