---
title: Propojení účtu Azure k ID partnera | Dokumentace Microsoftu
description: Sledovat spolupráce se zákazníky Azure díky propojení ID partnera s uživatelským účtem, který používáte ke správě prostředků zákazníka.
services: billing
author: dhirajgandhi
ms.author: cwatson
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 13748bf23ee70bfa77e15e7112b450637a231742
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51710711"
---
# <a name="link-partner-id-to-your-azure-accounts"></a>Propojení ID partnera k účtům Azure

Jako partner můžete sledovat vliv ve vašich zákazníků s azurecatem propojením vaše ID partnera s účty používané pro správu prostředků služby zákazníka.

Tato funkce je dostupná ve verzi public preview.

## <a name="get-access-from-your-customer"></a>Získání přístupu z vašich zákazníků

Předtím, než je propojit ID partnera, musí vaše zákazníky poskytují přístup k jejich prostředky Azure pomocí jedné z následujících možností:

- **Uživatel typu Host:** vašich zákazníků můžete vás přidal jako uživatele typu Host a přiřazení rolí RBAC. Další informace najdete v tématu [přidat uživatele typu Host z jiného adresáře](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Adresář účtu:** vašich zákazníků můžete vytvořit nového uživatele z vaší organizace v jejich adresáře a přiřadit libovolnou roli RBAC.

- **Instanční objekt:** vašich zákazníků můžete přidat aplikace nebo skriptu z vaší organizace v jejich directory a přiřadit libovolnou roli RBAC. Identita aplikace nebo skriptu, se označuje jako instanční objekt služby.

## <a name="link-partner-id"></a>Propojení ID partnera

Až budete mít přístup k prostředkům zákazníka, pomocí webu Azure portal, Powershellu nebo rozhraní příkazového řádku k propojení vaší společnosti Microsoft ID Partner Network (MPN ID) pro ID uživatele nebo instanční objekt. Budete muset propojit dané ID partnera v tenantovi každého zákazníka.

### <a name="use-azure-portal-to-link-new-partner-id"></a>Pomocí webu Azure portal k propojení nové ID partnera.

1. Přejděte na [odkaz s ID partnera](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) na webu Azure Portal.

2. Přihlaste se k portálu Azure.

3. Zadejte ID partnera Microsoftu. ID partnera [Network(MPN) partnera Microsoft](https://partner.microsoft.com/) ID vaší organizace.

  ![Snímek obrazovky zobrazující propojení ID partnera](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Propojit ID partnera pro odběratele jiného, pomocí přepínače adresářů. V adresáři přepínače zvolte adresáři.

  ![Snímek obrazovky zobrazující propojení ID partnera](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-new-partner-id"></a>Použití Powershellu k propojení nové ID partnera.

1. Nainstalujte [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner) modul prostředí PowerShell.

2. Přihlaste se do tenanta zákazníka pomocí uživatelského účtu nebo instanční objekt, pro další informace najdete v tématu [přihlášení pomocí prostředí Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
   ```


3. Propojte nové ID partnera. ID partnera [Network(MPN) partnera Microsoft](https://partner.microsoft.com/) ID vaší organizace.

    ```azurepowershell-interactive
    C:\> new-AzureRmManagementPartner -PartnerId 12345 
    ```

#### <a name="get-the-linked-partner-id"></a>Získejte ID propojené partnera
```azurepowershell-interactive
C:\> get-AzureRmManagementPartner 
```

#### <a name="update-the-linked-partner-id"></a>Aktualizace ID propojené partnera
```azurepowershell-interactive
C:\> Update-AzureRmManagementPartner -PartnerId 12345 
```
#### <a name="delete-the-linked-partner-id"></a>Odstranit ID propojené partnera
```azurepowershell-interactive
C:\> remove-AzureRmManagementPartner -PartnerId 12345 
```

### <a name="use-cli-to-link-new-partner-id"></a>Pomocí rozhraní příkazového řádku propojte nové ID partnera.
1.  Instalace rozšíření rozhraní příkazového řádku.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ``` 

2.  Přihlaste se do tenanta zákazníka s účtem uživatele nebo instanční objekt. Další informace najdete v tématu [přihlášení pomocí Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ``` 

3.  Propojte nové ID partnera. ID partnera [Network(MPN) partnera Microsoft](https://partner.microsoft.com/) ID vaší organizace.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Získejte ID propojené partnera
```azurecli-interactive
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>Aktualizace ID propojené partnera
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>Odstranit ID propojené partnera
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
``` 

## <a name="next-steps"></a>Další postup

Připojte se k diskusi v [komunitě partnerů Microsoft](https://aka.ms/PALdiscussion) přijímat aktualizace nebo poslat svůj názor.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Kdo může propojit ID partnera?**

Každý uživatel od partnerské organizace, který spravuje prostředky Azure zákazníka můžete propojit ID partnera účtu.

**Jakmile je propojený ID partnera. můžete ho změnit?**

Ano, ID partnera propojené lze změnit, přidat nebo odebrat.

**Co když má uživatel účet ve více tenantech zákazníka?**

Propojení ID partnera a účet se provádí pro každého zákazníka tenanta.  Budete muset propojit dané ID partnera v tenantovi každého zákazníka.

**Můžete dalších partnerů nebo zákazník upravit nebo odebrat propojení s ID partnera?**

Odkaz na úrovni účtu uživatele souvisí. Pouze můžete upravit nebo odebrat odkaz na ID partnera. Zákazníka a jiného partnera nelze změnit propojení ID partnera. 
