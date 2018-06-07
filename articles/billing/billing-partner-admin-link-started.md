---
title: Propojení účtu Azure při spolupráci ID | Microsoft Docs
description: Sledovat oznámeních podporujících zapojení uživatelů s Azure zákazníky pomocí propojení ID partnera k uživatelskému účtu, který používáte ke správě prostředků zákazníka.
services: billing
author: dhirajgandhi
ms.author: dhgandhi
ms.date: 03/12/2018
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a48298668e2297cb95f2a2f16eac6387ff509781
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608708"
---
# <a name="link-partner-id-to-your-azure-accounts"></a>ID partnera odkaz na vaše účty Azure

Jako partner můžete sledovat vaše dopad napříč závazky vašeho zákazníka pomocí propojení vaše ID partnera účtů používaných pro správu prostředků zákazníka.

Tato funkce je dostupná ve verzi public preview.

## <a name="get-access-from-your-customer"></a>Získání přístupu z vašich zákazníků

Předtím, než se propojíte vaše ID partnera, zákazník musí umožňují přístup k jejich prostředků Azure pomocí jedné z následujících možností:

- **Uživatele Guest:** zákazníkovi, můžete je přidat jako uživatel guest a přiřadit žádné role RBAC. Další informace najdete v tématu [přidat uživatele typu Host z jiného adresáře](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Účet Directory:** můžete vytvořit nového uživatele z vaší organizace v jejich adresář a přiřadit všechny role RBAC vašich zákazníků.

- **Instanční objekt:** zákazníkovi můžete přidat aplikace nebo skriptu z vaší organizace v jejich adresář a přiřadit všechny role RBAC. Identita aplikace nebo skriptu, se označuje jako instanční objekt.

## <a name="link-partner-id"></a>Propojení ID partnera

Až budete mít přístup k prostředkům zákazníka, použijte portál Azure, PowerShell nebo rozhraní příkazového řádku propojení ID sítě partnera vaší společnosti Microsoft (MPN ID) na vaše ID uživatele nebo instanční objekt. Budete muset propojit ID partnera v každém klientovi zákazníka.

### <a name="use-azure-portal-to-link-new-partner-id"></a>Použít portál Azure k propojení nové ID partnera.

1. Přejděte na [odkaz na ID partnera](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) na portálu Azure.

2. Přihlaste se k portálu Azure.

3. Zadejte ID partnera společnosti Microsoft. Partner ID je [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) ID vaší organizace.

  ![Snímek obrazovky, který zobrazuje odkaz ID partnera.](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Odkaz ID partnera pro jiné zákazníka, použijte directory přepínači. V adresáři přepínače zvolte adresáře.

  ![Snímek obrazovky, který zobrazuje odkaz ID partnera.](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-new-partner-id"></a>Pomocí prostředí PowerShell propojení nové ID partnera.

1. Nainstalujte [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner) modul prostředí PowerShell.

2. Přihlaste se k zákazníka klienta pomocí uživatelského účtu nebo instančního objektu, další informace najdete v tématu [přihlášení pomocí prostředí Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-5.2.0).
 
   ```azurepowershell-interactive
    C:\> Connect-AzureRmAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
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

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ``` 

2.  Přihlaste se k zákazníka klientovi s uživatelským účtem nebo instanční objekt. Další informace najdete v tématu [Přihlaste se pomocí Azure CLI 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ``` 

3.  Propojení nové ID partnera. Partner ID je [Microsoft Partner Network(MPN)](https://partner.microsoft.com/) ID vaší organizace.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Získání ID propojené partnera.
```azurecli-interactive
C:\ az managementpartner show
``` 

#### <a name="update-the-linked-partner-id"></a>Aktualizovat ID propojené partnera.
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
``` 

#### <a name="delete-the-linked-partner-id"></a>Odstranit ID propojené partnera.
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
``` 

## <a name="next-steps"></a>Další postup

Připojit k diskuse [Microsoft Partner Community](https://aka.ms/PALdiscussion) k přijetí aktualizací nebo odeslání zpětné vazby.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Kdo může propojit ID partnera?**

Každý uživatel od partnerské organizace, který spravuje zákazníka prostředků můžete propojit ID partnera k účtu.

**Jakmile je propojený ID partnera lze ho změnit?**

Ano, ID propojené partnera lze změnit, přidat nebo odebrat.

**Co dělat, když má uživatel účet v několika klientech zákazníka?**

Propojení mezi ID partnera a účet se děje u každého zákazníka klienta.  Budete muset propojit ID partnera v každém klientovi zákazníka.

**Můžete další partnera nebo zákazníkovi upravit nebo odebrat odkaz na ID partnera?**

Odkaz na úrovni účtu souvisí. Pouze můžete upravit nebo odebrat odkaz na ID partnera. Zákazník a druhého partnera nelze změnit na odkaz na ID partnera. 
