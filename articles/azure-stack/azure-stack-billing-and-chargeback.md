---
title: Zákazník fakturace a vrácení peněz v Azure zásobníku | Microsoft Docs
description: Zjistěte, jak načíst informace o využití prostředků z Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: eca335797f48b7c44351655f17c8b6499f3d5999
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
ms.locfileid: "29877479"
---
# <a name="usage-and-billing-in-azure-stack"></a>Využití a cenách služby Azure zásobníku

Tento článek popisuje, jak jsou uživatelé zásobník Azure účtuje využití prostředků. Dozvíte, jak fakturační informace přistupuje k analýze a zpětným poplatků.

Azure zásobníku shromažďuje data o využití pro všechny prostředky používané skupiny a předává tato data do obchodu Azure. Azure obchodování bills je pro použití Azure zásobníku stejným způsobem, jako by se vám účtovat za použití Azure.

Můžete také získat data o využití a export jeho vlastní fakturace nebo poplatků zálohování systému pomocí adaptérem fakturace nebo exportovat je do nástroje business intelligence, jako je Microsoft Power BI a použít jej pro analýzu.


## <a name="usage-pipeline"></a>Použití kanálu

Každý poskytovatel prostředků v Azure zásobníku vysílá data o využití za využití prostředků. Služba Usage pravidelně (každou hodinu a každý den) agreguje data o využití a ukládá je do databáze využití. Azure operátory zásobníku a uživatelé měli přístup k datům uložené využití prostřednictvím rozhraní API s využití prostředků Azure zásobníku. 

Pokud máte [vaší instanci Azure zásobníku a registrované v Azure](azure-stack-register.md), zásobník Azure je nakonfigurován pro odesílání dat o využití do obchodu Azure. Po odeslání dat do Azure můžete přistupovat prostřednictvím portálu fakturace nebo pomocí rozhraní API využití prostředků Azure. Odkazovat [generování sestav dat využití](azure-stack-usage-reporting.md) tématu, dozvíte se více o jaké využití dat údajně do Azure.  

Následující obrázek znázorňuje klíčové komponenty v kanálu využití: 

![Použití kanálu](media\azure-stack-billing-and-chargeback\usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Jaké informace o využití můžete najít a jak?

Azure zprostředkovatelé zásobníku prostředků, jako jsou výpočty, úložiště a sítě, generování dat o využití v hodinových intervalech pro každé předplatné. Data o využití obsahuje informace o prostředku použít jako název prostředku, předplatné použité a množství použít. Další informace o ID prostředky měřidla, naleznete [nejčastější dotazy týkající se používání rozhraní API](azure-stack-usage-related-faq.md) článku.

Po shromážděných dat o využití, je [oznamovány Azure](azure-stack-usage-reporting.md) ke generování faktury, které lze zobrazit pomocí portálu Azure fakturace. 


> [!NOTE]
> Generování sestav dat využití není nutné pro Azure zásobníku Development Kit a zásobník Azure integrované systému uživatele, kteří licencují v rámci modelu kapacity. Další informace o licencování v Azure zásobníku, najdete v článku [balení a ceny](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) dat listu.

Portál Azure fakturace zobrazuje data o využití pro fakturovatelné prostředky. Kromě fakturovatelné prostředků Azure zásobníku zaznamená data o využití pro širší sadě síťových prostředků, které dostanete ve vašem prostředí zásobníku Azure prostřednictvím rozhraní API REST nebo PowerShell. Operátory Azure zásobníku můžete získat data o využití pro všechna předplatná uživatele. Jednotlivým uživatelům můžete pouze získat podrobnosti o jejich využití. 

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Používání vytváření sestav pro víceklientské poskytovatele cloudových služeb

Víceklientská poskytovatele cloudové služby (CSP), který má mnoho zákazníků pomocí Azure zásobníku chtít sestav využití každého zákazníka samostatně, tak, aby zprostředkovatel můžete účtují využití do různých předplatných Azure. 

Každý zákazník budete svou identitu reprezentována různých klienta Azure Active Directory (Azure AD). Azure zásobníku podporuje přiřazení jedno předplatné CSP každému klientovi Azure AD. Základní registrace zásobník Azure můžete přidat klientů a jejich odběry. Základní registrace se provádí pro všechny balíčky Azure. Pokud předplatné není registrované pro klienta, uživatel může přesto použít Azure zásobníku a jejich využití odešlou do předplatné použité pro základní registrace. 


## <a name="next-steps"></a>Další postup

[Zaregistrovat Azure zásobníku](azure-stack-registration.md)

[Generování sestav Azure zásobníku využití dat do Azure](azure-stack-usage-reporting.md)

[Využití prostředků poskytovatele rozhraní API](azure-stack-provider-resource-api.md)

[Využití prostředků rozhraní API klienta](azure-stack-tenant-resource-usage-api.md)

[Nejčastější dotazy souvisí s využitím](azure-stack-usage-related-faq.md)