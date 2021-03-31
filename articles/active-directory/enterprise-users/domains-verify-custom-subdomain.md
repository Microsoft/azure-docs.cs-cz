---
title: Změna typu ověřování subdomény pomocí PowerShellu a grafu – Azure Active Directory | Microsoft Docs
description: Změna výchozího nastavení ověřování subdomény zděděné z nastavení kořenové domény v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 734e6824f13e62ad080500eff18c4892e1f76807
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95503676"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Změnit typ ověřování subdomény v Azure Active Directory

Po přidání kořenové domény do Azure Active Directory (Azure AD) budou všechny následující subdomény přidané do tohoto kořene v organizaci Azure AD automaticky dědit nastavení ověřování z kořenové domény. Pokud ale chcete spravovat nastavení ověřování domény nezávisle na nastaveních kořenové domény, můžete teď používat rozhraní Microsoft Graph API. Pokud máte například federovaný kořenovou doménu, například contoso.com, Tento článek vám může pomáhat ověřit subdoménu, jako je například child.contoso.com, jako spravovaná místo federovaného.

Pokud je nadřazená doména v portálu Azure AD federované a správce se pokusí ověřit spravovanou subdoménu na stránce **vlastní názvy domén** , dojde k chybě při přidání domény z důvodu, že jedna nebo více vlastností obsahuje neplatné hodnoty. Pokud se pokusíte přidat tuto subdoménu z centra pro správu Microsoft 365, zobrazí se podobná chyba. Další informace o této chybě najdete v tématu [Podřízená doména nedědí změny v nadřazené doméně v Office 365, Azure nebo Intune](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes).

## <a name="how-to-verify-a-custom-subdomain"></a>Ověření vlastní subdomény

Vzhledem k tomu, že subdomény dědí typ ověřování kořenové domény ve výchozím nastavení, musíte tuto subdoménu povýšit na kořenovou doménu v Azure AD pomocí Microsoft Graph, takže můžete nastavit typ ověřování na požadovaný typ.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>Přidání subdomény a zobrazení jejího typu ověřování

1. Pomocí PowerShellu přidejte novou subdoménu, která má výchozí typ ověřování kořenové domény. Centra pro správu Azure AD a Microsoft 365 tuto operaci ještě nepodporují.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. K získání domény použijte [Průzkumníka Azure AD Graph](https://graphexplorer.azurewebsites.net) . Protože doména není kořenovou doménou, zdědí typ ověření kořenová doména. Váš příkaz a výsledky můžou vypadat takto s použitím vlastního ID tenanta:

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>Použití rozhraní Azure AD Graph Exploreru API k vytvoření kořenové domény

Pro zvýšení úrovně subdomény použijte následující příkaz:

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>Změna typu ověřování subdomény

1. Pro změnu typu ověřování subdomény použijte následující příkaz:

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Ověření prostřednictvím GET v Průzkumníku Azure AD graphu, který je teď spravovaný typ ověřování subdomény:

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>Další kroky

- [Přidání vlastních názvů domén](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Správa názvů domén](domains-manage.md)
- [ForceDelete vlastní název domény s rozhraním API Microsoft Graph](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)