---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: 2e6697b07f305a78ef4cc3f462c76804160a9d12
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571994"
---
Po rozšíření záznamů pro název domény, byste měli moct ověřit, že lze použít vlastní název domény pro přístup k vaší webové aplikace ve službě Azure App Service pomocí svého prohlížeče.

> [!NOTE]
> Může trvat nějakou dobu vaše CNAME pro rozšíří v rámci systému DNS. Je třeba použít službu <a href="http://www.digwebinterface.com/"> http://www.digwebinterface.com/ </a> k ověření, že záznam CNAME je k dispozici.
> 
> 

Pokud jste ještě nepřidali vaší webové aplikace jako koncový bod služby Traffic Manager, musíte to provést před název řešení bude fungovat jako trasy název vlastní domény na Traffic Manager. Traffic Manager následně směruje do vaší webové aplikace. Pomocí informací v [přidat nebo odstranit koncové body](../articles/traffic-manager/traffic-manager-endpoints.md) přidat do webové aplikace jako koncový bod ve vašem profilu Traffic Manageru.

> [!NOTE]
> Pokud vaše webová aplikace není uveden, při přidání koncového bodu, ověřte, že je nakonfigurovaný pro **standardní** režimu plán služby App Service. Je nutné použít **standardní** režimu pro vaši webovou aplikaci, aby bylo možné pracovat s Traffic Managerem.
> 
> 

1. V prohlížeči se otevře [webu Azure Portal](https://portal.azure.com).
2. V **Web Apps** kartu, klikněte na název vaší webové aplikace, vyberte **nastavení**a pak vyberte **vlastní domény**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. V **vlastní domény** okna, klikněte na tlačítko **přidat název hostitele**.
4. Použití **Hostname** textových polí zadejte název domény Traffic Manageru pro přidružení k této webové aplikace.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Klikněte na tlačítko **ověřit** uložíte název domény.
6. Po kliknutí na tlačítko **ověřit** Azure bude aktivovat pracovní postup ověření domény. To bude kontrolovat vlastnictví domény i název hostitele dostupnost a sestavy úspěch nebo podrobné chybové s doporučený k vyřešení chyby.    
7. Po úspěšném ověření **přidat název hostitele** tlačítko je aktivní a bude možné přiřadit název hostitele. Teď přejděte na název vlastní domény v prohlížeči. Teď byste měli vidět vaše aplikace spuštěná pomocí vlastního názvu domény. 
   
   Po dokončení konfigurace vlastního názvu domény nebude uvedené **názvy domén** část vaší webové aplikace.

V tomto okamžiku byste měli moct zadat název domény Traffic Manageru v prohlížeči a podívejte se, že je úspěšně přejdete do webové aplikace.

