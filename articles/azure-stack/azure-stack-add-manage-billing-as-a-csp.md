---
title: Spravovat využití a fakturace zásobník Azure jako poskytovatele cloudových služeb | Microsoft Docs
description: Procházení prostřednictvím registrace Azure zásobníku jako poskytovatel cloudu a přidání zákazníků.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 21a52af4943004789b0a9bdbe4695ab1a603c046
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796695"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Spravovat využití a fakturace zásobník Azure jako poskytovatele cloudových služeb 

*Platí pro: Azure zásobníku integrované systémy*

Tento článek vás provede registrací zásobník Azure jako zprostředkovatele cloudu (CSP) a přidání zákazníků.

Jako zprostředkovatele kryptografických služeb by mohly mít velký počet různých zákazníků, pomocí vaší zásobník Azure. Každý zákazník má předplatného poskytovatele CSP v Azure a budete muset nasměrovat využití z vaší zásobník Azure pro každého uživatele předplatného.

Následující diagram znázorňuje kroky, které budete muset vybrat účtu sdílených služeb a zaregistrovat účet azure s účtem. Pokud byla provedena, můžete zaváděním koncovým zákazníkům.

**Postup přidání využití sledování jako zprostředkovatel kryptografických služeb**

![Proces pro povolení využití a správu jako poskytovatele cloudových služeb.](media\azure-stack-add-manage-billing-as-a-csp\process-add-useage-as-a-csp.png)

## <a name="create-a-csp-or-cspss-subscription"></a>Vytvoření zprostředkovatele kryptografických služeb nebo CSPSS odběru

### <a name="cloud-service-provider-subscription-types"></a>Typy předplatného poskytovatele služeb v cloudu

Musíte vybrat typ účtu sdílených služeb, který používáte pro Azure zásobníku. Typy odběry, které lze použít pro registraci víceklientské zásobník Azure jsou:

 - Poskytovatel cloudové služby 
 - Předplatné partnera sdílené služby 

#### <a name="csp-shared-services"></a>Zprostředkovatel kryptografických služeb sdílené služby

Cloudová předplatná služby zprostředkovatele sdílených služeb (CSPSS) jsou upřednostňované volbou pro registraci při přímé CSP nebo distributora CSP funguje Azure zásobníku.

Předplatné CSPSS propojeno se službou sdílené služby klienta. Při registraci zásobník Azure, budete muset zadat přihlašovací údaje pro účet, který je vlastníkem předplatného. Účet, který použijete k registraci Azure zásobníku se může lišit od účtu správce, který používáte pro nasazení; Proveďte dvě *není* musí patřit do stejné domény. Jinými slovy může nasadit pomocí klienta, který již používáte. Může například pomocí ContosoCSP.onmicrosoft.com a potom zaregistrovat pomocí různých klienta, například IURContosoCSP.onmicrosoft.com. Musíte si pamatovat, abyste se přihlásili pomocí ContosoCSP.onmicrosoft.com při den proveďte Azure zásobníku správy. Při přihlašování do Azure pomocí IURContosoCSP.onmicrosoft.com, když potřebujete provést registraci operace.

Můžete řídit následujícími popis CSPSS odběry a pokyny o tom, jak vytvořit odběr [přidat Azure partnera sdílené služby](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Odběry zprostředkovatele kryptografických služeb

Cloudová předplatná zprostředkovatele služeb (CSP) jsou upřednostňované volbou pro registraci při CSP prodejce nebo koncového zákazníka funguje Azure zásobníku.

## <a name="register-azure-stack"></a>Zaregistrovat Azure zásobníku

Zaregistrovat se zásobníkem Azure, najdete v tématu [zaregistrovat zásobník Azure s předplatným Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Přidání koncového zákazníka

Konfigurace Azure zásobníku, takže když nový klient používá prostředky jejich využití ohlásí do svého předplatného poskytovatele cloudové služby (CSP) najdete v tématu [přidat klienta pro využití a fakturace pro Azure zásobník](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Účtují správné odběrů

Azure zásobníku používá funkci registrace. Registrace je objekt, uložené v Azure, což dokumenty, které předplatná Azure používat k účtují pro danou zásobník Azure. Tento oddíl popisuje význam registrace.

Použití registrace zásobník Azure můžete:
 - Předání dat o využití zásobník Azure do obchodu Azure a účtovat pošle předplatné Azure.
 - Sestavy využití každého zákazníka jiné předplatné s víceklientské nasazení Azure zásobníku. Víceklientská umožňuje zásobník Azure pro podporu různých organizace ve stejné instanci Azure zásobníku.

Pro každý zásobník Azure je jedno předplatné výchozí a mnoho odběrů klienta podle potřeby. Výchozí předplatné je předplatné Azure, který bude účtován, pokud neexistuje odběr konkrétního klienta. Musí být první k registraci. Využití víceklientské reporting fungovat musí být předplatné zprostředkovatele kryptografických služeb nebo CSPSS předplatné.

Potom registrace se aktualizuje předplatné pro každého klienta, který bude používat Azure zásobníku. Odběry klienta musí být typu zprostředkovatele kryptografických služeb a musí souhrnné na partnera, který vlastní výchozí předplatné. Jinými slovy nelze zaregistrovat zákazníkům jiného uživatele.

Pokud Azure zásobníku předává informace o využití do globální Azure, služby ve službě Azure zajímají registrace a využití každého klienta se mapuje na příslušnou tenanta předplatného. Pokud klient nebyl registrován, toto využití přejde na výchozí předplatné pro instanci Azure zásobníku, z něhož pochází.

Vzhledem k tomu, že klienta odběry jsou odběry zprostředkovatele kryptografických služeb, jejich faktury je odeslaná partnerskému zprostředkovatele kryptografických služeb a informace o použití nejsou viditelné pro koncového zákazníka.



## <a name="next-steps"></a>Další postup

 - Další informace o programu CSP, najdete v části [programu poskytovatele cloudových řešení](https://partnercenter.microsoft.com/en-us/partner/programs).
 - Další informace o tom, jak načíst informace o využití prostředků z zásobník Azure najdete v tématu [využití a cenách služby Azure zásobníku](azure-stack-billing-and-chargeback.md).
