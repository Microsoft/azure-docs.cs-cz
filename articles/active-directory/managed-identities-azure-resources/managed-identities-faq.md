---
title: Spravované identity pro prostředky Azure – Nejčastější dotazy – Azure AD
description: Nejčastější dotazy týkající se spravovaných identit
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.openlocfilehash: 07b106630cffae75c5e4588d14de7ae938945614
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534118"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Spravované identity pro prostředky Azure – Nejčastější dotazy – Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

## <a name="administration"></a>Správa

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Jak můžete najít prostředky, které mají spravovanou identitu?

Seznam prostředků, které mají spravovanou identitu přiřazenou systémem, najdete pomocí následujícího příkazu rozhraní příkazového řádku Azure: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```


### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Jaká oprávnění Azure RBAC jsou vyžadována pro spravovanou identitu prostředku? 

- Spravovaná identita přiřazená systémem: ke zdroji potřebujete oprávnění k zápisu. Například v případě virtuálních počítačů potřebujete oprávnění Microsoft.Compute/virtualMachines/write. Tato akce je zahrnutá v předdefinovaných rolích specifických pro prostředky, jako je [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).
- Spravovaná identita přiřazená uživatelem: k prostředku potřebujete oprávnění k zápisu. Například v případě virtuálních počítačů potřebujete oprávnění Microsoft.Compute/virtualMachines/write. Kromě spravované identity se přiřazení role [operátora identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) .

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Návody zabránit vytváření spravovaných identit přiřazených uživatelem?

Uživatelům můžete zabránit v vytváření spravovaných identit přiřazených uživatelem pomocí [Azure Policy](../../governance/policy/overview.md)

1. Přejděte na [Azure Portal](https://portal.azure.com) a přejděte na **zásady**.
2. Zvolit **definice**
3. Vyberte **+ definice zásad** a zadejte potřebné informace.
4. V části pravidlo zásad vložte
    
    ```json
    {
      "mode": "All",
      "policyRule": {
        "if": {
          "field": "type",
          "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
        },
        "then": {
          "effect": "deny"
        }
      },
      "parameters": {}
    }
    
    ```

Až zásadu vytvoříte, přiřaďte ji do skupiny prostředků, kterou chcete použít.

1. Přejděte do skupiny prostředků.
2. Vyhledejte skupinu prostředků, kterou používáte pro testování.
3. V nabídce vlevo vyberte **zásady** .
4. Vybrat **přiřadit zásadu**
5. V části **základy** zadejte:
    1. **Rozsah** Skupina prostředků, kterou používáme pro testování
    1. **Definice zásad**: zásady, které jsme vytvořili dříve.
6. Ponechte všechna ostatní nastavení na výchozí hodnoty a klikněte na tlačítko **zkontrolovat + vytvořit** .

V tomto okamžiku se nezdaří pokus o vytvoření spravované identity přiřazené uživatelem ve skupině prostředků.

  ![Porušení zásad](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>Koncepty

### <a name="do-managed-identities-have-a-backing-app-object"></a>Mají spravované identity zálohovaný objekt aplikace?

No. Spravované identity a registrace Aplikace Azure AD nejsou v adresáři stejné.

Registrace aplikací mít dvě komponenty: objekt aplikace + objekt instančního objektu.
Spravované identity pro prostředky Azure mají jenom jednu z těchto součástí: instanční objekt služby.

Spravované identity nemají v adresáři objekt aplikace, což je to, co se běžně používá k udělení oprávnění aplikace pro MS Graph. Místo toho je třeba oprávnění MS Graph pro spravované identity udělit přímo k instančnímu objektu.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Jaké jsou přihlašovací údaje přidružené ke spravované identitě? Jak dlouho je platný a jak často se otáčí?

> [!NOTE]
> Způsob, jakým jsou spravované identity ověřovány, jsou podrobnosti interní implementace, které se mohou změnit bez předchozího upozornění.

Spravované identity používají ověřování založené na certifikátech. Platnost přihlašovacích údajů každé spravované identity vyprší po 90 dnech a je zahrnutá za 45 dnů.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Jakou identitu bude IMDS výchozí, pokud nezadáte identitu v žádosti?

- Pokud je povolená spravovaná identita přiřazená systémem a v požadavku není zadaná žádná identita, IMDS se ve výchozím nastavení spravovaná identita přiřazená systémem.
- Pokud není povolená spravovaná identita přiřazená systémem a existuje jenom jedna spravovaná identita přiřazená uživatelem, IMDS se ve výchozím nastavení Tato spravovaná identita přiřazená jednomu uživateli.
- Pokud není povolená spravovaná identita přiřazená systémem a existuje více spravovaných identit přiřazených uživateli, musíte v žádosti zadat spravovanou identitu.

## <a name="limitations"></a>Omezení

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>Může se stejná spravovaná identita používat napříč několika oblastmi?

V krátkém případě ano, můžete použít spravované identity přiřazené uživatelem ve více než jedné oblasti Azure. Delší odpověď je, že zatímco spravované identity přiřazené uživateli jsou vytvořené jako regionální prostředky, přidružený [objekt služby](../develop/app-objects-and-service-principals.md#service-principal-object) (SP) vytvořený v Azure AD je k dispozici globálně. Instanční objekt se dá použít v jakékoli oblasti Azure a jeho dostupnost závisí na dostupnosti služby Azure AD. Například pokud jste vytvořili spravovanou identitu přiřazenou uživatelem v South-Central oblasti a tato oblast nebude k dispozici, bude tento problém mít vliv jenom na aktivity [roviny řízení](../../azure-resource-manager/management/control-plane-and-data-plane.md) na samotné spravované identitě.  Nebudou ovlivněny aktivity prováděné všemi prostředky, které již byly nakonfigurovány pro použití spravovaných identit.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Pracují spravované identity prostředků Azure se službou Azure Cloud Services?

Ne, neexistují žádné plány na podporu spravovaných identit pro prostředky Azure v Azure Cloud Services.


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Jaká je hranice zabezpečení spravovaných identit pro prostředky Azure?

Hranice zabezpečení identity je prostředek, ke kterému je připojen. Například hranice zabezpečení pro virtuální počítač se spravovanými identitami pro prostředky Azure je virtuální počítač. Jakýkoli kód běžící na tomto virtuálním počítači může volat spravované identity pro koncový bod prostředků Azure a žádat tokeny. Podobně jako u jiných prostředků, které podporují spravované identity prostředků Azure, se jedná o podobné prostředí.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Budou spravované identity znovu automaticky vytvořeny při přesunu předplatného do jiného adresáře?

No. Pokud přesunete předplatné do jiného adresáře, budete ho muset ručně znovu vytvořit a znovu udělit přiřazení rolí Azure.
- Pro spravované identity přiřazené systémem: zakažte a znovu povolte. 
- U spravovaných identit přiřazených uživateli: Odstraňte, znovu vytvořte a připojte je znovu k potřebným prostředkům (například virtuálním počítačům).

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Můžu použít spravovanou identitu pro přístup k prostředkům v jiném adresáři nebo tenantovi?

No. Spravované identity v současné době nepodporují scénáře pro více adresářů. 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>Existují nějaké omezení přenosové rychlosti, která se vztahují na spravované identity?

Omezení spravovaných identit mají závislosti na omezeních služeb Azure, omezeních Azure Instance Metadata Service (IMDS) a omezení služby Azure Active Directory.

- **Omezení služby Azure** definují počet operací vytvoření, které se dají provádět na úrovních tenanta a předplatného. Spravované identity přiřazené uživateli mají také [omezení](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits) , jak mohou být pojmenovány.
- **IMDS** Obecně platí, že požadavky na IMDS jsou omezeny na pět požadavků za sekundu. Žádosti překračující tuto prahovou hodnotu budou odmítnuty s 429 odpověďmi. Žádosti do kategorie spravované identity jsou omezené na 20 požadavků za sekundu a 5 souběžných požadavků. Další informace najdete v článku o [Azure instance metadata Service (Windows)](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity) .
- **Služba Azure Active Directory** Každá spravovaná identita se počítá s limitem kvóty objektu v tenantovi Azure AD, jak je popsáno v tématu [limity a omezení služby Azure AD](../enterprise-users/directory-service-limits-restrictions.md).


### <a name="is-it-possible-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Je možné přesunout spravovanou identitu přiřazenou uživatelem do jiné skupiny prostředků nebo předplatného?

Přesunutí spravované identity přiřazené uživatelem do jiné skupiny prostředků se nepodporuje.

## <a name="next-steps"></a>Další kroky

- Informace [o tom, jak spravované identity fungují s virtuálními počítači](how-managed-identities-work-vm.md)
