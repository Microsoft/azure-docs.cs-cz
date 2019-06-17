---
title: Propojení účtu Azure s ID partnera | Dokumentace Microsoftu
description: Sledovat spolupráce se zákazníky Azure díky propojení ID partnera s uživatelským účtem, který používáte ke správě prostředků zákazníka.
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 03/12/2019
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 6bf61e2afd96e3923938ac4f815d34ae08f7c618
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64694225"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Propojit ID partnera účtů Azure

Jako partner můžete sledovat vliv napříč vaše zákazníky. Propojte své ID partnera k účtům, které se používají ke správě prostředků zákazníka.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Získání přístupu z vašich zákazníků

Předtím, než je propojit ID partnera, musí vaše zákazníky poskytují přístup k jejich prostředky Azure pomocí jedné z následujících možností:

- **Uživatel typu Host**: Zákazník může vás přidal jako uživatele typu Host a přiřadit přístup na základě rolí role (RBAC) ovládacího prvku. Další informace najdete v tématu [přidat uživatele typu Host z jiného adresáře](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Adresář účtu**: Vašich zákazníků můžete vytvořit uživatelský účet pro vás v jejich vlastní adresáře a přiřadit libovolnou roli RBAC.

- **Instanční objekt služby**: Vašich zákazníků můžete přidat aplikace nebo skriptu z vaší organizace v jejich directory a přiřadit libovolnou roli RBAC. Identita aplikace nebo skriptu, se označuje jako instanční objekt služby.

## <a name="link-to-a-partner-id"></a>Propojit ID partnera.

Až budete mít přístup k prostředkům zákazníka, použijte na webu Azure portal, Powershellu nebo rozhraní příkazového řádku Azure k propojení vaší společnosti Microsoft ID Partner Network (MPN ID) vaše ID uživatele nebo instanční objekt. Propojte ID partnera v tenantovi každého zákazníka.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Odkaz na nové ID partnera pomocí webu Azure portal

1. Přejděte na [odkaz s ID partnera](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) na webu Azure Portal.

2. Přihlaste se k portálu Azure.

3. Zadejte ID partnera Microsoftu. ID partnera [programu Microsoft Partner Network](https://partner.microsoft.com/) ID pro vaši organizaci.

   ![Snímek obrazovky, který se zobrazí odkaz s ID partnera](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Propojit ID partnera pro odběratele jiného, přepněte adresáře. V části **přepnout adresář**, vyberte svůj adresář.

   ![Snímek obrazovky zobrazující přepnout adresář](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Použití Powershellu k odkaz na nové ID partnera.

1. Nainstalujte [AzureRM.ManagementPartner](https://www.powershellgallery.com/packages/AzureRM.ManagementPartner) modul prostředí PowerShell.

2. Přihlaste se do tenanta zákazníka s účtem uživatele nebo instanční objekt služby. Další informace najdete v tématu [Přihlaste se pomocí prostředí PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Odkaz na nové ID partnera. ID partnera [programu Microsoft Partner Network](https://partner.microsoft.com/) ID pro vaši organizaci.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Získejte ID propojené partnera
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Aktualizace ID propojené partnera
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Odstranit ID propojené partnera
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Použití rozhraní příkazového řádku Azure pro odkaz na nové ID partnera.
1. Nainstalujte rozšíření Azure CLI.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Přihlaste se do tenanta zákazníka s účtem uživatele nebo instanční objekt služby. Další informace najdete v tématu [Přihlaste se pomocí Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Odkaz na nové ID partnera. ID partnera [programu Microsoft Partner Network](https://partner.microsoft.com/) ID pro vaši organizaci.

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

Každý uživatel od partnerské organizace, který spravuje prostředky Azure na základě můžete propojit ID partnera účtu.

**Může být po je propojený změnit ID partnera?**

Ano. ID partnera propojené lze změnit, přidat nebo odebrat.

**Co když má uživatel účet ve více než jednoho tenanta zákazníka?**

Propojení ID partnera a účet se provádí pro každého zákazníka tenanta. Propojte ID partnera v tenantovi každého zákazníka.

**Můžete dalších partnerů nebo zákazníky za upravit nebo odebrat propojení s ID partnera?**

Odkaz na úrovni účtu uživatele souvisí. Pouze můžete upravit nebo odebrat odkaz na ID partnera. Zákazníka a dalšími partnery, nelze změnit propojení ID partnera.


**U kterého ID MPN by měl používat když Moje společnost má víc?**

Můžete použít libovolné platné ID MPN s výjimkou virtuální organization(v-org) ID MPN. Většina partneři zvolit MPN ID pro zeměpisné oblasti, kde se zákazník nachází nebo služby jsou distribuována.

**Kde najdu vliv na výnosy vytváření sestav pro ID propojené partnera?**

Můžete najít na generování sestav k nim výnosy [Moje přehledný řídicí panel](https://partner.microsoft.com/membership/reports/myinsights). Musíte vybrat partnerské propojení správce jako typ přidružení partnera.

**Proč nelze zobrazit Moje zákazníka v sestavách?**

Nelze zobrazit zákazníka v sestavách z následujících důvodů

1. Propojené uživatelský účet nemá [přístupu podle Role](https://docs.microsoft.com/azure/role-based-access-control/overview) na kterýkoli zákazník předplatné Azure nebo prostředků.

2. Předplatné Azure, kde má uživatel [přístupu podle Role](https://docs.microsoft.com/azure/role-based-access-control/overview) přístup nemá žádné využití.

**Odkaz partnera, se kterým ID funguje s Azure Stack?**

Ano, je možné propojit ID partnera pro službu Azure Stack.
