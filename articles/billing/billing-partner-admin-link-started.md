---
title: Propojení účtu Azure při spolupráci ID | Microsoft Docs
description: Sledovat oznámeních podporujících zapojení uživatelů s Azure zákazníky pomocí propojení ID partnera k uživatelskému účtu, který používáte ke správě prostředků zákazníka.
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: f729a0228c6b0c2f514ab7170299b0271ed28ad2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="link-partner-id-to-your-azure-accounts"></a>ID partnera odkaz na vaše účty Azure 
Jako partner můžete sledovat vaše dopad napříč závazky vašeho zákazníka pomocí propojení vaše ID partnera účtů používaných pro správu prostředků zákazníka.

Tato funkce je dostupná ve verzi public preview. 

## <a name="get-access-from-your-customer"></a>Získání přístupu z vašich zákazníků 
Předtím, než se propojíte vaše ID partnera, zákazník musí umožňují přístup k jejich prostředků Azure pomocí jedné z následujících možností:

- **Uživatele Guest:** zákazníkovi, můžete je přidat jako uživatel guest a přiřadit žádné role RBAC. Další informace najdete v tématu [přidat uživatele typu Host z jiného adresáře](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Účet Directory:** můžete vytvořit nového uživatele z vaší organizace v jejich adresář a přiřadit všechny role RBAC vašich zákazníků. 

- **Instanční objekt:** zákazníkovi můžete přidat aplikace nebo skriptu z vaší organizace v jejich adresář a přiřadit všechny role RBAC. Identita aplikace nebo skriptu, se označuje jako instanční objekt.

## <a name="link-partner-id"></a>ID partnera odkaz
Až budete mít přístup k prostředkům zákazníka, pomocí prostředí PowerShell nebo rozhraní příkazového řádku propojení ID sítě partnera vaší společnosti Microsoft (MPN ID) s ID uživatele nebo instanční objekt. Budete muset propojit ID partnera pro každého klienta zákazníka. 

### <a name="use-powershell-to-link-new-partner-id"></a>Pomocí prostředí PowerShell propojení nové ID partnera.

1. Nainstalujte [AzurePartnerRP](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner/0.1.0-preview) modul prostředí PowerShell.

2. Přihlaste se k zákazníka klienta pomocí uživatelského účtu nebo instančního objektu, další informace najdete v tématu [přihlášení pomocí prostředí Powershell](https://docs.microsoft.com/en-us/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Login-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Propojení nové ID partnera. Partner ID je [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) ID vaší organizace.

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>Získání ID propojené partnera.
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>Aktualizovat ID propojené partnera.
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>Odstranit ID propojené partnera.
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>Použití rozhraní příkazového řádku pro odkaz nové ID partnera.
1.  Nainstalujte rozšíření rozhraní příkazového řádku.

    ```azure-cli
    C:\ az extension add --name managementpartner
    ``` 

2.  Přihlaste se k zákazníka klientovi s uživatelským účtem nebo instanční objekt. Další informace najdete v tématu [Přihlaste se pomocí Azure CLI 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azure-cli
    C:\ az login --tenant <tenant>
    ``` 


3.  Propojení nové ID partnera. Partner ID je [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) ID vaší organizace.

     ```azure-cli
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Získání ID propojené partnera.
```azure-cli
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>Aktualizovat ID propojené partnera.
```azure-cli
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>Odstranit ID propojené partnera.
```azure-cli
C:\ az managementpartner delete --partner-id 12345
``` 


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Kdo může propojit ID partnera?**

Každý uživatel od partnerské organizace, který spravuje zákazníka prostředků můžete propojit ID partnera k účtu.
  

**Jakmile je propojený ID partnera lze ho změnit?**

Ano, ID propojené partnera lze změnit, přidat nebo odebrat.

**Co dělat, když má uživatel účet v několika klientech zákazníka?**

Propojení mezi ID partnera a účet se děje u každého zákazníka klienta.  Budete muset propojit ID partnera v každém klientovi zákazníka.

**Můžete další partnera nebo zákazníkovi upravit nebo odebrat odkaz na ID partnera?**

Odkaz na úrovni účtu souvisí. Pouze můžete upravit nebo odebrat odkaz na ID partnera. Zákazník a druhého partnera nelze změnit na odkaz na ID partnera. 


