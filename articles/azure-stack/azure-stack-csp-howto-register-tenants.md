---
title: Přidat klienty pro využití a fakturace do protokolů Azure | Microsoft Docs
description: Kroky potřebné koncový uživatel přidat do Azure zásobníku spravuje poskytovatele cloudové služby.
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
ms.date: 03/08/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 27473ce4057fdb06ab9faf0f46dede62b4ee2246
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048835"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Přidat klienta pro využití a fakturace k Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy*

Tento článek popisuje kroky nutné přidat koncový uživatel se zásobníkem Azure spravovat pomocí poskytovatele cloudové služby (CSP). Pokud k novému klientovi používá prostředky, zásobník Azure zprávu využití příslušného předplatného poskytovatele CSP.

CSP často nabízejí služby pro více zákazníků (klientů) na jejich nasazení Azure zásobníku. Přidání klientů do Azure zásobníku registrace zajišťuje, že využití každého klienta bude být hlášené a účtován odpovídající předplatného poskytovatele CSP. Pokud není provést kroky v tomto článku, využití klienta je zodpovědné za předplatné použité při počáteční registraci Azure zásobníku. Před přidáním koncového zákazníka se zásobníkem Azure pro sledování využití a ke správě svého klienta, musíte nakonfigurovat Azure zásobníku jako zprostředkovatel kryptografických služeb. Kroky a zdroje najdete v tématu [spravovat využití a fakturace zásobník Azure jako poskytovatele cloudových služeb](azure-stack-add-manage-billing-as-a-csp.md).

Následující diagram znázorňuje kroky, které bude nutné k povolení nového zákazníka k používání Azure zásobníku a nastavení sledování zákazníka využití CSP. Přidáním koncového zákazníka, můžete také bude ke správě prostředků v Azure zásobníku. Máte dvě možnosti pro správu svých prostředků:

1. Můžete udržovat end zákazníka klienta a zadejte přihlašovací údaje pro místní předplatné Azure zásobníku pro koncového zákazníka.  
2. Nebo můžete pracovat místně svoje předplatné a přidat CSP jako hosta s oprávnění vlastníka koncového zákazníka.  

**Postup přidání koncového zákazníka**

![Nastavení poskytovatele cloudové služby pro sledování využití a ke správě účtu zákazníka end](media\azure-stack-csp-enable-billing-usage-tracking\process-csp-enable-billing.png)

## <a name="create-a-new-customer-in-partner-center"></a>Vytvoření nového zákazníka v partnerské Centrum

V partnerské Centrum vytvořte nové předplatné služby Azure pro zákazníka. Pokyny najdete v tématu [přidat nový zákazník](https://msdn.microsoft.com/partner-center/add-a-new-customer).


##  <a name="create-an-azure-subscription-for-the-end-customer"></a>Vytvořte předplatné Azure pro koncového zákazníka

Po vytvoření záznamu zákazníkovi v partnerské centrum, můžete je prodeje odběrů produktů v katalogu. Pokyny najdete v tématu [vytvořit, pozastavit nebo zrušit předplatná zákazníka](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

## <a name="create-a-guest-user-in-the-end-customer-directory"></a>Vytvoření uživatele guest na konci zákazníka adresáře

Pokud zákazník end bude spravovat svůj vlastní účet, vytvořte uživatele guest v jejich adresář a jejich odeslání informací. Koncový uživatel potom přidá Host a zvýšení oprávnění hosta k **vlastníka** k účtu Azure zásobníku CSP.
 
## <a name="update-the-registration-with-the-end-customer-subscription"></a>Aktualizace registrace pomocí předplatné zákazníka end

Aktualizace registrace s předplatným nové zákazníka. Azure sestavy využití zákazníka pomocí identity zákazníka z centrální partnera. Tento krok zajistí, že využití každého zákazníka je v části tohoto zákazníka jednotlivých předplatného poskytovatele CSP. Jednodušší sledování využití uživatele a fakturace.

> [!Note]  
> Chcete-li provést tento krok, musíte mít [zaregistrovat Azure zásobníku](azure-stack-register.md).

1. Otevřete prostředí Windows PowerShell s řádku se zvýšenými oprávněními a spusťte:  
    `Add-AzureRmAccount`
2. Zadejte přihlašovací údaje Azure.
3. V relaci prostředí PowerShell spusťte příkaz:

```powershell
    New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
```
### <a name="new-azurermresource-powershell-parameters"></a>Parametry prostředí PowerShell nové AzureRmResource
| Parametr | Popis |
| --- | --- | 
|registrationSubscriptionID | Předplatné Azure, která byla použita pro počáteční registraci zásobník Azure. |
| customerSubscriptionID | Předplatné Azure (ne Azure Stack) patřící do zákazníka k registraci. Musí být vytvořené v nabídku CSP; v praxi to znamená přes Partnerské centrum. Pokud zákazník má více než jednoho klienta Azure Active Directory, musí být vytvořeny toto předplatné v klientovi, který se použije k přihlášení do Azure zásobníku.
| Skupina prostředků | Skupina prostředků v Azure, ve kterém je uložený registrace. 
| registrationName | Název registrace do Azure zásobníku. Je objekt uložená v Azure. | 
| Vlastnosti | Určuje vlastnosti pro prostředek. Tento parametr použijte k určení hodnoty vlastností, které jsou specifické pro typ prostředku.


> [!Note]  
> Klienti musí být registrované s každou zásobník Azure používají. Pokud máte dvě nasazení Azure zásobníku a klienta bude používat obou z nich, budete muset aktualizovat počáteční registrace každého nasazení s předplatným klienta.

## <a name="onboard-tenant-to-azure-stack"></a>Zařadit klienta do Azure zásobníku

Nakonfigurujte zásobník Azure pro podporu uživatelů od víc klientů Azure AD pro službu v Azure zásobníku. Pokyny najdete v tématu [povolit víceklientský v zásobníku Azure](azure-stack-enable-multitenancy.md).


## <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Vytvořte místní prostředek v klientovi zákazníka end v Azure zásobníku

Až do zásobníku Azure přidáte nového zákazníka nebo klienta zákazníka end povolil účtu hosta s oprávněními vlastníka, ověřte, že můžete vytvořit prostředek v jejich klienta. Například můžete [vytvoření virtuálního počítače s Windows pomocí portálu Azure zásobníku](user\azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Další postup

 - Pokud se spouštějí v procesu registrace, projděte chybové zprávy, najdete v tématu [klienta registrace chybové zprávy](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
 - Další informace o tom, jak načíst informace o využití prostředků z zásobník Azure najdete v tématu [využití a cenách služby Azure zásobníku](/azure-stack-billing-and-chargeback.md).
 - Chcete-li zkontrolovat, jak koncového zákazníka může přidávat, jako zprostředkovatel kryptografických služeb, jako správce pro jejich zásobník Azure, klienta najdete v tématu [povolit poskytovatele cloudové služby ke správě svého předplatného Azure zásobníku](user\azure-stack-csp-enable-billing-usage-tracking.md).
