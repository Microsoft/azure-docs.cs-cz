---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ddeb46a2c7bc7f24f55c22f446926529cee7b598
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059635"
---
Po rozšíření záznamů pro název domény byste měli být schopni použít prohlížeč k ověření, že se vlastní název domény dá použít pro přístup k webové aplikaci v Azure App Service.

> [!NOTE]
> Může trvat nějakou dobu, než se váš CNAME rozšíří přes systém DNS. Můžete použít službu <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> , jako je, k ověření, že je záznam CNAME k dispozici.
> 
> 

Pokud jste svou webovou aplikaci ještě nepřidali jako koncový bod Traffic Manager, musíte to provést, než bude překlad názvů fungovat, protože vlastní název domény směruje na Traffic Manager. Traffic Manager potom trasy do vaší webové aplikace. Použijte informace v části [Přidání nebo odstranění koncových bodů](../articles/traffic-manager/traffic-manager-endpoints.md) k přidání webové aplikace jako koncového bodu v profilu Traffic Manager.

> [!NOTE]
> Pokud vaše webová aplikace není při přidávání koncového bodu uvedená, ověřte, že je nakonfigurovaná pro **standardní** režim App Serviceho plánu. Chcete-li pracovat s Traffic Manager, je nutné použít **standardní** režim pro vaši webovou aplikaci.
> 
> 

1. V prohlížeči otevřete [Azure Portal](https://portal.azure.com).
2. Na kartě **Web Apps** klikněte na název vaší webové aplikace, vyberte **Nastavení**a pak vyberte **vlastní domény** .
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. V okně **vlastní domény** klikněte na **Přidat název hostitele**.
4. K zadání vlastního názvu domény, který se má přidružit k této webové aplikaci, použijte textová pole **název hostitele** .
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Kliknutím na **ověřit** uložte konfiguraci názvu domény.
6. Po kliknutí na **ověřit** , že Azure začne pracovat s pracovním postupem pro ověření domény. Provedete to tak, že provedete kontrolu vlastnictví domény i dostupnost názvu hostitele a podrobnou chybu s podrobnými pokyny, jak chybu opravit.    
7. Po úspěšném ověření se tlačítko **Přidat název hostitele** stane aktivním a budete moci přiřadit název hostitele. Teď v prohlížeči přejděte na vlastní název domény. Teď by se měla zobrazit vaše aplikace s použitím vlastního názvu domény. 
   
   Po dokončení konfigurace se vlastní název domény zobrazí v části **názvy domén** ve vaší webové aplikaci.

V tuto chvíli byste měli být schopni zadat název domény Traffic Manager v prohlížeči a podívat se, jestli se k vaší webové aplikaci úspěšně dostanete.

