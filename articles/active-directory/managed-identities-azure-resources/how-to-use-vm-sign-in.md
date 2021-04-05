---
title: Použití spravovaných identit na virtuálním počítači Azure pro přihlášení – Azure ADV
description: Podrobné pokyny a příklady použití identit spravovaných virtuálním počítačem Azure pro instanční objekt služby Azure pro přihlašování a přístup k prostředkům klienta.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 61e83bd27c9434c4222e0161e3b643b183d1aa84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99090956"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Použití spravovaných identit pro prostředky Azure na virtuálním počítači Azure pro přihlášení 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Tento článek poskytuje příklady skriptu PowerShellu a rozhraní příkazového řádku pro přihlášení pomocí spravovaných identit pro instanční objekty prostředků Azure a pokyny k důležitým tématům, jako je zpracování chyb.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pokud máte v úmyslu použít Azure PowerShell nebo příklady Azure CLI v tomto článku, nezapomeňte nainstalovat nejnovější verzi [Azure PowerShell](/powershell/azure/install-az-ps) nebo rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Všechny ukázkové skripty v tomto článku předpokládají, že klient příkazového řádku je spuštěný na virtuálním počítači se spravovanými identitami pro prostředky Azure. Pomocí funkce připojit k VIRTUÁLNÍmu počítači ve Azure Portal se můžete vzdáleně připojit k vašemu VIRTUÁLNÍmu počítači. Podrobnosti o povolení spravovaných identit pro prostředky Azure na virtuálním počítači najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí Azure Portal](qs-configure-portal-windows-vm.md)nebo některého z článků variant (pomocí PowerShellu, rozhraní příkazového řádku, šablony nebo sady Azure SDK). 
> - Aby se zabránilo chybám při přístupu k prostředkům, musí mít spravovaná identita virtuálního počítače alespoň přístup čtenářů v příslušném oboru (virtuální počítač nebo vyšší), aby bylo možné Azure Resource Manager operace na virtuálním počítači. Podrobnosti najdete v tématu [přiřazení spravovaných identit pro prostředky Azure přístup k prostředku pomocí Azure Portal](howto-assign-access-portal.md) .

## <a name="overview"></a>Přehled

Spravované identity pro prostředky Azure poskytují [instanční objekt služby](../develop/developer-glossary.md#service-principal-object) , který se [vytvoří po povolení spravovaných identit pro prostředky Azure](overview.md) na virtuálním počítači. Instančnímu objektu se může udělit přístup k prostředkům Azure a používá se jako identita klienty skriptu a příkazového řádku pro přihlášení a přístup k prostředkům. Aby bylo možné přistupovat k zabezpečeným prostředkům v rámci vlastní identity, je třeba, aby klient skriptu:  

   - musí být zaregistrované a odsouhlasené s Azure AD jako důvěrná/Webová klientská aplikace.
   - Přihlaste se pod svým instančním objektem a použijte přihlašovací údaje aplikace (které jsou ve skriptu nejspíš vložené).

Se spravovanými identitami pro prostředky Azure již není nutné, aby klient s vaším skriptem, jak se může přihlásit pod spravovanými identitami pro objekt služby Azure Resources. 

## <a name="azure-cli"></a>Azure CLI

Následující skript ukazuje, jak:

1. Přihlaste se ke službě Azure AD v části spravovaná identita virtuálního počítače pro objekt služby Azure sources.  
2. Zavolejte Azure Resource Manager a Získejte ID objektu služby virtuálního počítače. CLI se postará o správu získání a používání tokenu automaticky. Nezapomeňte nahradit název virtuálního počítače pro `<VM-NAME>` .  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Následující skript ukazuje, jak:

1. Přihlaste se ke službě Azure AD v části spravovaná identita virtuálního počítače pro objekt služby Azure sources.  
2. Pokud chcete získat informace o virtuálním počítači, zavolejte rutinu Azure Resource Manager. PowerShell se postará o automatické řízení použití tokenu.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>ID prostředků pro služby Azure

V tématu [služby Azure, které podporují ověřování Azure AD](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) , najdete seznam prostředků, které podporují Azure AD a byly testovány se spravovanými identitami pro prostředky Azure a jejich příslušnými ID prostředků.

## <a name="error-handling-guidance"></a>Pokyny pro zpracování chyb 

Odpovědi, jako například následující, mohou znamenat, že spravovaná identita virtuálního počítače pro prostředky Azure nebyla správně nakonfigurována:

- PowerShell: *Invoke-WebRequest: nejde se připojit ke vzdálenému serveru.*
- CLI: *MSI: Nepodařilo se načíst token z `http://localhost:50342/oauth2/token` s chybou ' HTTPConnectionPool (host = ' localhost ', port = 50342)* 

Pokud se zobrazí jedna z těchto chyb, vraťte se k virtuálnímu počítači Azure ve [Azure Portal](https://portal.azure.com) a přejděte na stránku **Identita** a ujistěte se, že **přiřazený systém** je nastavený na Ano.

## <a name="next-steps"></a>Další kroky

- Pokud chcete povolit spravované identity pro prostředky Azure na virtuálním počítači Azure, přečtěte si téma [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí PowerShellu](qs-configure-powershell-windows-vm.md)nebo [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí Azure CLI](qs-configure-cli-windows-vm.md) .