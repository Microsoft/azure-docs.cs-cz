---
title: Propojit účet Azure s ID partnera | Microsoft Docs
description: Pomocí odkazů na uživatelský účet, který používáte ke správě prostředků zákazníka, Sledujte zapojení se zákazníky Azure.
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
ms.openlocfilehash: 0448ffbccddc913bd6359f5f6bbf42988239afb4
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706409"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Propojení ID partnera s účty Azure

Partneři Microsoftu poskytují služby, které zákazníkům pomůžou dosáhnout podnikatelských a organizačních cílů pomocí produktů Microsoftu. Když jednají jménem zákazníka, který spravuje, konfiguruje a podporuje služby Azure, budou mít partneři přístup k prostředí zákazníka. Pomocí odkazu správce partnerů můžou partneři přidružit své ID partnerské sítě k přihlašovacím údajům použitým pro doručování služeb.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Získání přístupu od zákazníka

Před propojením ID partnera vám zákazník musí udělit přístup k prostředkům Azure pomocí jedné z následujících možností:

- **Uživatel typu Host**: Váš zákazník se může přidat jako uživatel typu Host a přiřadit libovolné role řízení přístupu na základě role (RBAC). Další informace najdete v tématu [Přidání uživatelů typu host z jiného adresáře](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Účet adresáře**: Zákazník může vytvořit uživatelský účet ve vlastním adresáři a přiřadit roli RBAC.

- **Instanční objekt**: Zákazník může do svého adresáře přidat aplikaci nebo skript z vaší organizace a přiřadit roli RBAC. Identita aplikace nebo skriptu se označuje jako instanční objekt.

## <a name="link-to-a-partner-id"></a>Propojit s ID partnera

Když máte přístup k prostředkům zákazníka, pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure můžete propojit ID Microsoft Partner Network (MPN ID) s vaším ID uživatele nebo instančním objektem. Propojte ID partnera v každém tenantovi zákazníka.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Připojení k novému ID partnera pomocí Azure Portal

1. Přejít na [odkaz na ID partnera](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) v Azure Portal.

2. Přihlaste se k portálu Azure.

3. Zadejte ID partnera Microsoftu. ID partnera je [Microsoft Partner Network](https://partner.microsoft.com/) ID vaší organizace.

   ![Snímek obrazovky, který zobrazuje odkaz na ID partnera](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Pokud chcete propojit ID partnera pro jiného zákazníka, přepněte adresář. V části **Přepnout adresář**vyberte svůj adresář.

   ![Snímek obrazovky se zobrazeným adresářem Switch](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Použití PowerShellu k propojení s novým ID partnera

1. Nainstalujte modul PowerShell [AZ. ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) .

2. Přihlaste se k tenantovi zákazníka pomocí uživatelského účtu nebo instančního objektu. Další informace najdete v tématu věnovaném [přihlášení pomocí PowerShellu](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Odkaz na nové ID partnera ID partnera je [Microsoft Partner Network](https://partner.microsoft.com/) ID vaší organizace.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Získat ID propojeného partnera
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Aktualizovat ID propojeného partnera
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Odstranit ID propojeného partnera
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Použití rozhraní příkazového řádku Azure pro odkazování na nové ID partnera
1. Nainstalujte rozšíření Azure CLI.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Přihlaste se k tenantovi zákazníka pomocí uživatelského účtu nebo instančního objektu. Další informace najdete v tématu [přihlášení pomocí Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Odkaz na nové ID partnera ID partnera je [Microsoft Partner Network](https://partner.microsoft.com/) ID vaší organizace.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Získat ID propojeného partnera
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Aktualizovat ID propojeného partnera
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Odstranit ID propojeného partnera
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Další postup

Připojte se k diskuzi v [komunitě partnerů Microsoftu](https://aka.ms/PALdiscussion) , abyste mohli dostávat aktualizace nebo odesílat zpětnou vazbu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Kdo může propojit ID partnera?**

Každý uživatel z partnerské organizace, který spravuje prostředky Azure, může propojit ID partnera s účtem.

**Může se po propojení změnit ID partnera?**

Ano. Propojené ID partnera lze změnit, přidat nebo odebrat.

**Co když má uživatel účet ve více než jednom tenantovi zákazníka?**

Propojení mezi ID partnera a účtem se provádí pro každého tenanta zákazníka. Propojte ID partnera v každém tenantovi zákazníka.

**Můžou jiní partneři nebo zákazníci upravit nebo odebrat propojení s ID partnera?**

Odkaz je přidružen na úrovni uživatelského účtu. Odkaz na ID partnera můžete upravit nebo odebrat jenom vy. Zákazník a další partneři nemůžou změnit odkaz na ID partnera.


**Které ID MPN mám použít, pokud má moje společnost víc?**

Účty umístění partnera a přidružená ID MPN by se měly používat pro propojování ID partnera.  Další informace o [partnerských účtech](https://docs.microsoft.com/partner-center/account-structure)

**Kde najdu nahlášení ovlivněných výnosů pro ID propojeného partnera?**

Vytváření sestav o výkonu cloudových produktů je dostupné pro partnery v partnerském centru na [řídicím panelu moje přehledy](https://partner.microsoft.com/membership/reports/myinsights). Jako typ přidružení partnera musíte vybrat odkaz partner admin.

**Proč se mi v sestavách nezobrazuje můj zákazník?**

Z následujících důvodů nelze zobrazit zákazníka v sestavách.

1. Propojený uživatelský účet nemá [přístup na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview) u předplatného nebo prostředku Azure pro zákazníky.

2. Předplatné Azure, ve kterém má uživatel přístup [na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview) , nemá žádné použití.

**Funguje s Azure Stackem Link partner?**

Ano, můžete propojit ID partnera pro Azure Stack.
