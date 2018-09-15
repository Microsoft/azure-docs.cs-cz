---
title: Zákazník vyúčtování a vratka ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak načíst informace o využití prostředků ze služby Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: sethm
ms.reviewer: alfredop
ms.openlocfilehash: fcfd5d4e9e2f30d769818df29cf8a76bd9113d4f
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632401"
---
# <a name="usage-and-billing-in-azure-stack"></a>Využití a fakturace ve službě Azure Stack

Tento článek popisuje, jak uživatelům Azure stacku se vám účtovat využití prostředků. Dozvíte se, jak je fakturační údaje přistupovat k analýzám a poplatek za zpět.

Azure Stack shromažďuje a seskupuje data o využití za využité prostředky. Azure Stack pak předá tato data do Azure Commerce. Azure Commerce vám účtuje za využití služby Azure Stack stejným způsobem, jak by se vám účtovat za využití Azure.

Můžete také získat data o využití a export na vlastní fakturace nebo proplacení zálohování systému pomocí fakturační adaptér, nebo je exportovat do nástroje business intelligence, jako je Microsoft Power BI.


## <a name="usage-pipeline"></a>Použití kanálu

Každý poskytovatel prostředků ve službě Azure Stack odesílá data o využití za využití prostředků. Služba Usage pravidelně (po hodinách a každý den) agreguje data o využití a uloží je do databáze využití. Azure Stack operátory a uživatelé můžou k datům uložené využití prostřednictvím rozhraní API využití prostředků Azure Stack. 

Pokud máte [vaší instance služby Azure Stack a registrované v Azure](azure-stack-register.md), Azure Stack je nakonfigurovaná k odesílání dat o využití do Azure Commerce. Po nahrání dat do Azure můžete přistupovat prostřednictvím fakturačním portálu nebo pomocí rozhraní API využití prostředků Azure. Odkazovat [generování sestav dat využití](azure-stack-usage-reporting.md) článku se dozvíte o jaké využití více dat se oznamuje službě Azure.  

Následující obrázek ukazuje klíčové součásti v kanálu využití: 

![Použití kanálu](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Využití informací můžete najít a jak?

Azure Stack Resource poskytovatelů (jako jsou výpočty, úložiště a síť) generování dat o využití v hodinových intervalech pro každé předplatné. Data o využití obsahuje informace o prostředek, který používá jako je například název prostředku, předplatné použité a množství. Další informace o prostředcích ID měřiče, naleznete [nejčastější dotazy k používání rozhraní API](azure-stack-usage-related-faq.md) článku.

Po shromáždil data o využití je [hlášených Azure](azure-stack-usage-reporting.md) k vygenerování faktury, které lze zobrazit pomocí fakturačním portálu Azure. 

> [!NOTE]  
> Generování sestav dat využití není potřeba pro Azure Stack Development Kit a pro Azure Stack integrované systému, kteří licencí v rámci modelu kapacity. Další informace o licencování ve službě Azure Stack, najdete v článku [balení a ceny](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) informační list.

Fakturačním portálu Azure zobrazuje data o využití za fakturovatelnou prostředky. Kromě fakturovatelné prostředky služby Azure Stack zaznamená data o využití pro pestřejší škálu prostředků, které se zobrazí ve vašem prostředí Azure Stack prostřednictvím rozhraní REST API nebo Powershellu. Operátoři Azure stacku můžete získat data využití pro všechna předplatná uživatele. Jednotlivým uživatelům můžete získat pouze o jejich použití. 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Používání vytváření sestav pro víceklientskou poskytovatelů cloudových služeb

Víceklientská Cloud Service Provider (CSP), který má mnoho zákazníků pomocí služby Azure Stack může být vhodné pro zasílání zpráv o využití jednotlivých zákazníků samostatně, tak, aby zprostředkovatel může účtovat využití do různých předplatných Azure. 

Každý zákazník budete svou identitu reprezentována na jiného tenanta Azure Active Directory (Azure AD). Azure Stack podporuje přiřazení k jednomu předplatnému CSP ke každému tenantovi Azure AD. Základní registrace Azure Stack můžete přidat tenantů a jejich předplatného. Základní registrace se provádí pro všechny balíčky Azure. Pokud předplatné není zaregistrované pro tenanta, uživatel může dál používat Azure Stack a jejich používání se odešlou do předplatného, které použijete pro základní registrace. 


## <a name="next-steps"></a>Další postup

[Registrovat pomocí služby Azure Stack](azure-stack-registration.md)

[Azure Stack využití dat sestavy do Azure](azure-stack-usage-reporting.md)

[Rozhraní API využití prostředků poskytovatele](azure-stack-provider-resource-api.md)

[Rozhraní API využití prostředků tenanta](azure-stack-tenant-resource-usage-api.md)

[Nejčastější dotazy souvisí s využitím](azure-stack-usage-related-faq.md)