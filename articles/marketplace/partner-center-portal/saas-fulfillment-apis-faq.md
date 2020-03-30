---
title: Api pro plnění SaaS – nejčastější dotazy | Azure Marketplace
description: Zjišťování a nákupy zákazníků nabídky SaaS na Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6d3a84341d5221950da20f39456461dafc5d2e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275692"
---
# <a name="saas-fulfillment-apis---faq"></a>Rozhraní API pro naplnění SaaS – nejčastější dotazy

Jsou uvedeny požadavky na integraci s Azure Marketplace, které zákazníkům Azure umožňují přihlásit se k odběru nabídky SaaS.

## <a name="discovery-experience"></a>Zkušenosti s zjišťováním

Po publikování nabídky můžou uživatelé Azure zjistit nabídku SaaS na Azure Marketplace. Vaši zákazníci budou moci filtrovat nabídky na základě typu produktu (SaaS) a objevovat služby SaaS, které je zajímají.

## <a name="purchase-experience"></a>Zkušenosti s nákupem

Jakmile má uživatel zájem o konkrétní službu SaaS, může si ji předplatit z Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>Co znamená, že si uživatel Azure může na Azure Marketplace předplatit nabídku SaaS?

To znamená, že uživatel si může zobrazit podmínky použití a prohlášení o zásadách ochrany osobních údajů spojené se službou SaaS a souhlasit s tím, že za ně zaplatí podle fakturačních podmínek, které jste nastavili vy, vydavatel nabídky SaaS, na faktuře společnosti Microsoft. Uživatelé můžou použít svůj stávající platební profil v Azure k platbě za spotřebu služby SaaS.

To je prospěšné z mnoha důvodů. Zákazníci nyní mohou objevovat a odebírat předplatné na jednom místě pomocí platformy Microsoft Cloud Platform jako důvěryhodného zdroje, aniž by museli prověřovat každý software pro výrobce softwaru, který hodlá používat. Zákazníci mohou také používat svůj stávající platební profil, aniž by museli explicitně platit každý software nezávislých nezávislých nezávislých nezávislých výrobců softwaru.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>Účtuje se uživateli automaticky, když je nabídka přihlášena k odběru?

Při přihlášení k odběru nabídky SaaS, uživatel souhlasil s platbou za spotřebu služby SaaS prostřednictvím platformy Microsoft. Poplatky však začnou pouze v případě, že je nabídka spotřebována. Uživatel musí přejít na vaši nabídku SaaS a potvrdit vytvoření účtu, aby mohl nabídku začít spotřebovávat. Poté budete společnost Microsoft informovat, aby začala účtovat pro toto zákaznické předplatné SaaS.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Jak jste upozorněni, když se uživatel přihlásí k odběru vaší nabídky SaaS?

Po přihlášení k odběru nabídky může uživatel Azure zjistit a spravovat všechny své nabídky v Azure. Ve výchozím nastavení je stav nově upsané nabídky SaaS **"Zřizování, plnění čeká na vyřízení"**. V tomto stavu se uživateli Azure zobrazí výzva s akcí **"Konfigurace účtu"**, aby mohl přejít na své prostředí správy předplatného SaaS na webu Azure Portal.

Když uživatel klikne na **"Konfigurovat účet"**, bude přesměrován na web služby SaaS. Adresa URL, na kterou jsou navigovány, je poskytována vydavatelem v době publikování nabídky. Tato stránka se označuje jako vstupní stránka vydavatele. Uživatelé Azure by se měli moct přihlásit na vstupní stránku SaaS na základě svých stávajících přihlašovacích údajů AAD v Azure.

Když je uživatel Azure přesměrován na vstupní stránku, přidá se token na adresu URL dotazu. Tento token je krátkodobý a platný po dobu 24 hodin. Potom můžete zjistit přítomnost tohoto tokenu a volání rozhraní API společnosti Microsoft získat další kontext spojené s tokenem.

![Tok předplatného odběratele](media/saas-metering-service-integration-flow-a.png)

Další informace o kontraktu rozhraní API pro zpracování scénářů pro zpracování transakt v životním cyklu nabídky SaaS najdete v dokumentu [rozhraní API pro plnění](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) SaaS.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Jak znáte nabídku SaaS, ke které se uživatel přihlásí v Azure?

Odpověď na `Resolve` rozhraní API zahrnuje informace o nabídce a plánu spojené s předplatným SaaS.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Jak může uživatel Azure změnit plán přidružený k tomuto předplatnému Azure?

* Uživatel Azure můžete změnit plán přidružený k předplatnému SaaS přímo v prostředí SaaS nebo prostřednictvím platformy Microsoft.

* Konverze lze provádět kdykoli v fakturačním cyklu. Musíte potvrdit jakoukoli konverzi, která nabude účinnosti, jakmile bude uznána.

* Předplacené tarify **(měsíční** nebo **roční)** jsou poměrné. Jakékoli překročení vyzařované až do doby převodu bude účtováno v další faktuře. Nové nadbytky budou emitovány na základě nového plánu.

>[!Note]
>Pokud nechcete podporovat konkrétní konverzní cesty, můžete je zablokovat.

Následující sekvence zachycuje tok, když zákazník Azure změní plán v prostředí SaaS:

![Tok změn plánu zákazníka](media/saas-metering-service-integration-flow-b.png)

Níže uvedená sekvence zachycuje tok, když zákazník Azure změní plán v obchodě Microsoftu

![Tok změny plánu prodejen zákazníků](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Jak se uživatel Azure může odhlásit z plánu přidruženého k předplatnému Azure?

Uživatel Azure se může odhlásit z zakoupené nabídky SaaS buď přímo v prostředí SaaS, nebo prostřednictvím platformy Microsoft. Jakmile se uživatel odhlásí, nebude mu nadále účtovánpoplatek z dalšího fakturačního cyklu.

Následující sekvence zachycuje tok, když se zákazník Azure odhlásí z nabídky SaaS v prostředí SaaS:

![Odhlášení ze zákazníka v prostředí SaaS](media/saas-metering-service-integration-flow-d.png)

Níže uvedená sekvence zachycuje tok, když se uživatel Azure odhlásí v obchodě Microsoftu:

![Odhlášení ze zákazníka v obchodě společnosti Microsoft](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Další kroky

- Další informace najdete [v tématu Marketplace metering service API.](./marketplace-metering-service-apis.md)
