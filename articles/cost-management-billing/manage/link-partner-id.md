---
title: Propojení účtu Azure s ID partnera
description: Sledujte komunikaci se zákazníky Azure tak, že propojíte ID partnera s uživatelským účtem, který používáte ke správě prostředků zákazníka.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 10/05/2020
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.openlocfilehash: 96b6467d0d529f5839c33182057f3aa3c39cb6e7
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132546"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Propojení ID partnera s účty Azure

Partneři Microsoftu poskytují služby, které zákazníkům pomáhají dosahovat podnikatelských a organizačních cílů pomocí produktů Microsoftu. Když při správě, konfiguraci a podpoře služeb Azure jednají uživatelé z partnerských společností jménem zákazníka, potřebují přístup k prostředí daného zákazníka. Při použití odkazu na správce partnera (Partner Admin Link, PAL) mohou partneři přidružit svoje ID partnerské sítě k přihlašovacím údajům, které se používají k doručování služeb.

PAL umožňuje Microsoftu identifikovat a rozpoznávat partnery, kteří posilují úspěchy zákazníků s Azure. Microsoft může vaší organizaci zajistit vliv a příjmy související s využitím Azure, a to na základě oprávnění účtu (role Azure) a rozsahu (předplatné, skupina prostředků, prostředek).

## <a name="get-access-from-your-customer"></a>Získání přístupu od zákazníka

Před propojením vašeho ID partnera vám zákazník musí udělit přístup k prostředkům Azure pomocí jedné z následujících možností:

- **Uživatel typu host**: Zákazník vás může přidat jako uživatele typu host a přiřadit nějaké role Azure. Další informace najdete v tématu [Přidání uživatelů typu host z jiného adresáře](../../active-directory/external-identities/what-is-b2b.md).

- **Účet v adresáři**: Zákazník vám může vytvořit uživatelský účet ve svém vlastním adresáři a přiřadit nějakou roli Azure.

- **Instanční objekt**: Zákazník může přidat aplikaci nebo skript z vaší organizace do svého adresáře a přiřadit nějakou roli Azure. Identita aplikace nebo skriptu se označuje jako instanční objekt.

- **Azure Lighthouse:** Zákazník může delegovat předplatné (nebo skupinu prostředků), takže v něm uživatelé mohou pracovat z vašeho tenanta. Další informace najdete v tématu [Správa delegovaných prostředků Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

## <a name="link-to-a-partner-id"></a>Propojení s ID partnera

Když máte přístup k prostředkům zákazníka, použijte Azure Portal, PowerShell nebo Azure CLI k propojení vašeho ID programu Microsoft Partner Network (MPN ID) s vaším ID uživatele nebo instančním objektem. Propojte toto ID partnera v každém tenantovi zákazníka.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Použití webu Azure Portal k propojení s novým ID partnera

1. Na webu Azure Portal přejděte na [Propojit s ID partnera](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade).

2. Přihlaste se k portálu Azure.

3. Zadejte ID partnera Microsoftu. ID partnera je ID programu [Microsoft Partner Network](https://partner.microsoft.com/) pro vaši organizaci. Nezapomeňte použít **přidružené ID MPN**, které je uvedené ve vašem partnerském profilu.

   ![Snímek obrazovky se stránkou Propojit s ID partnera](./media/link-partner-id/link-partner-id01.png)

4. Pokud chcete propojit ID partnera pro jiného zákazníka, přepněte adresář. V části **Přepnout adresář** vyberte adresář.

   ![Snímek obrazovky se zobrazenou částí Přepnout adresář](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Použití PowerShellu k propojení s novým ID partnera

1. Nainstalujte modul PowerShellu [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/).

2. Přihlaste se k tenantovi zákazníka pomocí uživatelského účtu nebo instančního objektu. Další informace najdete v tématu [Přihlášení pomocí Azure PowerShellu](/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Vytvořte propojení s novým ID partnera. ID partnera je ID programu [Microsoft Partner Network](https://partner.microsoft.com/) pro vaši organizaci. Nezapomeňte použít **přidružené ID MPN**, které je uvedené ve vašem partnerském profilu.


    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Získání propojeného ID partnera
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Aktualizace propojeného ID partnera
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Odstranění propojeného ID partnera
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Použití Azure CLI k propojení s novým ID partnera
1. Nainstalujte rozšíření Azure CLI.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Přihlaste se k tenantovi zákazníka pomocí uživatelského účtu nebo instančního objektu. Další informace najdete v tématu [Přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Vytvořte propojení s novým ID partnera. ID partnera je ID programu [Microsoft Partner Network](https://partner.microsoft.com/) pro vaši organizaci.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Získání propojeného ID partnera
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Aktualizace propojeného ID partnera
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Odstranění propojeného ID partnera
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Další kroky

Připojte se k diskuzi v [komunitě partnerů Microsoftu](https://aka.ms/PALdiscussion), abyste mohli dostávat aktualizace nebo odesílat zpětnou vazbu.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Kdo může propojit ID partnera?**

Propojit ID partnera s účtem může každý uživatel z partnerské organizace, který spravuje prostředky Azure zákazníka.

**Je možné po propojení změnit ID partnera?**

Ano. Propojené ID partnera lze změnit, přidat nebo odebrat.

**Co když má uživatel účet ve více než jednom tenantovi zákazníka?**

Propojení mezi ID partnera a účtem se provádí u každého tenanta zákazníka. Propojte toto ID partnera v každém tenantovi zákazníka.

Pokud ale spravujete zákaznické prostředky přes Azure Lighthouse, měli byste vytvořit odkaz v tenantovi poskytovatele služeb, a to pomocí účtu, který má k těmto zákaznickým prostředkům přístup. Další informace najdete v tématu o [připojení vašeho ID partnera za účelem sledování vašeho dopadu na delegované prostředky](../../lighthouse/how-to/partner-earned-credit.md).

**Můžou propojení s ID partnera upravit nebo odebrat jiní partneři nebo zákazníci?**

Propojení je přiřazeno na úrovni uživatelského účtu. Propojení s ID partnera můžete upravit nebo odebrat jenom vy. Zákazník ani jiní partneři nemůžou propojení s ID partnera změnit.

**Které MPN ID mám použít, pokud jich má moje společnost více?**

Nezapomeňte použít **přidružené ID MPN**, které je uvedené ve vašem partnerském profilu.

**Kde najdu sestavy ovlivněných výnosů pro propojené ID partnera?**

Sestavy výkonu cloudových produktů jsou pro partnery dostupné v partnerském centru na [řídicím panelu s přehledem](https://partner.microsoft.com/membership/reports/myinsights). Jako typ přidružení partnera je nutné vybrat Odkaz na správce partnera.

**Proč se mi v sestavách nezobrazuje můj zákazník?**

Možné důvody nezobrazování zákazníka v sestavách:

1. Propojený uživatelský účet nemá v žádném předplatném nebo prostředku Azure daného zákazníka [řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md).

2. Předplatné Azure, ve kterém má daný uživatel [řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md), se vůbec nevyužívá.

**Funguje propojení ID partnera s Azure Stackem?**

Ano, ID partnera je možné propojit pro Azure Stack.

**Jak můžu propojit ID partnera, pokud v naší společnosti pro přístup k prostředkům zákazníků využíváme [Azure Lighthouse](../../lighthouse/overview.md)?**

Při onboardingu zákazníků do správy delegovaných prostředků Azure [publikováním nabídky spravovaných služeb na Azure Marketplace](../../lighthouse/how-to/publish-managed-services-offers.md) se automaticky přidruží vaše MPN ID.

Pokud k [onboardingu zákazníků využíváte nasazení šablon Azure Resource Manageru](../../lighthouse/how-to/onboard-customer.md), musíte přidružit vaše MPN ID nejméně k jednomu uživatelskému účtu, který má přístup ke všem vašim onboardovaným předplatným. Upozorňujeme, že tuto akci musíte provést v tenantovi poskytovatele služeb, nikoli v tenantech jednotlivých zákazníků. Pro zjednodušení doporučujeme vytvořit v tenantovi účet instančního objektu, přidružit ho k vašemu MPN ID a udělit mu přístup ke každému onboardovanému zákazníkovi s [předdefinovanou rolí Azure, u které jde uplatit kredit získaný partnerem](/partner-center/azure-roles-perms-pec). Další informace najdete v tématu o [připojení vašeho ID partnera za účelem sledování vašeho dopadu na delegované prostředky](../../lighthouse/how-to/partner-earned-credit.md).

**Jak mám zákazníkům vysvětlit PAL (Partner Admin Link)?**

PAL (Partner Admin Link) umožňuje Microsoftu identifikovat a rozpoznávat partnery, kteří zákazníkům pomáhají dosahovat vytčených obchodních cílů a zajišťovat hodnotu v cloudu. Zákazníci musí nejdřív partnerovi poskytnout přístup k prostředku Azure. Po udělení přístupu se k němu přidruží MPN ID (Microsoft Partner Network ID) partnera. Toto přidružení pomáhá Microsoftu pochopit ekosystém poskytovatelů IT služeb a zdokonalovat nástroje a programy potřebné k zajištění co nejlepší podpory pro běžné zákazníky.

**Jaká data shromažďuje PAL?**

Přidružení PAL k existujícím přihlašovacím údajům neposkytuje Microsoftu žádná nová zákaznická data. Microsoftu jednoduše poskytuje telemetrické údaje, kde je partner aktivně zapojený do prostředí Azure zákazníka. Microsoft může partnerské organizaci zajistit vliv a příjmy související s využitím Azure v prostředích zákazníků, a to na základě oprávnění účtu (role Azure) a rozsahu (skupina pro správu, předplatné, skupina prostředků, prostředek), které partnerovi poskytuje zákazník. 

**Má to vliv na zabezpečení prostředí Azure u zákazníka?**

Přidružení PAL jenom přidá MPN ID partnera k již zřízeným přístupovým oprávněním a nemění žádná oprávnění (role Azure) a partnerovi ani Microsoftu neposkytuje další údaje o službách Azure.