---
title: Použití spravovaných identit na virtuálním počítači Azure pro přihlášení – Azure AD
description: Krok za krokem pokyny a příklady pro použití identit spravované virtuálním počítačem Azure pro instanční objekt prostředků Azure pro přihlášení klienta skriptu a přístup k prostředkům.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34f4dc749c0254b5aa4e9ff018d2a869832de3f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547391"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Jak používat spravované identity pro prostředky Azure na virtuálním počítači Azure pro přihlášení 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Tento článek obsahuje příklady skriptů prostředí PowerShell a rozhraní příkazového příkazu pro přihlášení pomocí spravovaných identit pro instanční objekt prostředků Azure a pokyny k důležitým tématům, jako je zpracování chyb.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pokud máte v plánu použít příklady Azure PowerShellnebo Azure CLI v tomto článku, nezapomeňte nainstalovat nejnovější verzi [Azure PowerShellnebo](/powershell/azure/install-az-ps) [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Všechny ukázkový skript v tomto článku předpokládá, že klient příkazového řádku běží na virtuálním počítači s povolenými spravovanými identitami pro prostředky Azure. Pomocí funkce Připojení virtuálního počítače na webu Azure Portal můžete vzdáleně připojit k virtuálnímu počítači. Podrobnosti o povolení spravovaných identit pro prostředky Azure na virtuálním počítači najdete [v tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí portálu Azure Portal](qs-configure-portal-windows-vm.md)nebo jednoho z článků variant (pomocí PowerShellu, CLI, šablony nebo Sady Azure SDK). 
> - Chcete-li zabránit chybám během přístupu k prostředkům, musí mít spravovaná identita virtuálního počítače alespoň přístup "Reader" v příslušném oboru (virtuální počítač nebo vyšší), aby bylo možné povolit operace Azure Resource Manager na virtuálním počítači. Podrobnosti najdete [v tématu Přiřazení spravovaných identit pro prostředky Azure k prostředku pomocí portálu Azure.](howto-assign-access-portal.md)

## <a name="overview"></a>Přehled

Spravované identity pro prostředky Azure poskytuje [objekt instančního objektu](../develop/developer-glossary.md#service-principal-object) , který [se vytvoří po povolení spravovaných identit pro prostředky Azure](overview.md#how-does-the-managed-identities-for-azure-resources-work) na virtuálním počítači. Instanční objekt může být udělen přístup k prostředkům Azure a použít jako identitu skript/příkazový řádek klienty pro přihlášení a přístup k prostředkům. Klient skriptu by tradičně musel:  

   - být registrována a souhlasně se službou Azure AD jako důvěrná/webová klientská aplikace
   - Přihlaste se pod instančníobjekt pomocí přihlašovacích údajů aplikace (které jsou pravděpodobně vložené do skriptu)

Se spravovanými identitami pro prostředky Azure už klient skriptu nemusí dělat ani jedno, protože se může přihlásit pod spravovanými identitami pro instanční objekt prostředků Azure. 

## <a name="azure-cli"></a>Azure CLI

Následující skript ukazuje, jak:

1. Přihlášení ke službě Azure AD v rámci spravované identity virtuálního počítače pro hlavní server služeb prostředků Azure  
2. Zavolejte na Správce prostředků Azure a získejte ID instančního objektu virtuálního počítače. CLI se postará o automatické správě získávání/použití tokenů. Nezapomeňte nahradit název virtuálního `<VM-NAME>`počítače pro .  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Následující skript ukazuje, jak:

1. Přihlášení ke službě Azure AD v rámci spravované identity virtuálního počítače pro hlavní server služeb prostředků Azure  
2. Zavolejte rutinu Správce prostředků Azure a získejte informace o virtuálním počítači. PowerShell se postará o automatické správě využití tokenů.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>ID prostředků pro služby Azure

Podívejte se na [služby Azure, které podporují ověřování Azure AD](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) pro seznam prostředků, které podporují Azure AD a byly testovány se spravovanými identitami pro prostředky Azure a jejich příslušná ID prostředků.

## <a name="error-handling-guidance"></a>Pokyny pro zpracování chyb 

Odpovědi, jako je například následující může znamenat, že spravované identity virtuálního počítače pro prostředky Azure nebyla správně nakonfigurovaná:

- Prostředí PowerShell: *Invoke-WebRequest : Nelze se připojit ke vzdálenému serveru*
- ROZHRANÍ SE KZAUk: *MSI: `http://localhost:50342/oauth2/token` Nepodařilo se načíst token s chybou 'HTTPConnectionPool(host='localhost', port=50342)* 

Pokud se vám zobrazí jedna z těchto chyb, vraťte se k virtuálnímu počítači Azure na [webu Azure portal](https://portal.azure.com) a:

- Přejděte na stránku **Identita** a ujistěte se, že **přiřazený systém** je nastaven na "Ano".
- Přejděte na stránku **Rozšíření** a ujistěte se, že spravované identity pro rozšíření prostředků Azure **(plánované pro vyřazení v lednu 2019)** byly úspěšně nasazeny.

Pokud je některý z nich nesprávný, bude pravděpodobně nutné znovu nasadit spravované identity pro prostředky Azure na prostředek znovu nebo řešení potíží selhání nasazení. Pokud potřebujete pomoc s konfigurací virtuálního počítače, přečtěte si [název Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí portálu Azure Portal.](qs-configure-portal-windows-vm.md)

## <a name="next-steps"></a>Další kroky

- Pokud chcete povolit spravované identity pro prostředky Azure na virtuálním počítači Azure, přečtěte [Configure managed identities for Azure resources on an Azure VM using Azure CLI](qs-configure-cli-windows-vm.md) si, že najdete [v tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí Azure VM](qs-configure-powershell-windows-vm.md)






