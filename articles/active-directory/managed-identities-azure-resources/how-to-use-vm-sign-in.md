---
title: Použití spravované identity pro prostředky Azure na Virtuálním počítači Azure pro přihlášení
description: Podrobné pokyny a příklady použití virtuálního počítače Azure spravované identity pro prostředky Azure instanční objekt pro klienta přihlašovacího skriptu a přístup k prostředkům.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: priyamo
ms.openlocfilehash: fd62bd74f0200ffca2926879e76744be0dfe68d0
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54900217"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Použití spravované identity pro prostředky Azure na Virtuálním počítači Azure pro přihlášení 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Tento článek obsahuje příklady skriptů Powershellu a rozhraní příkazového řádku pro přihlašování pomocí spravované identity pro instanční objekt služby prostředků Azure a doprovodné materiály k důležitá témata, jako je zpracování chyb.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pokud máte v plánu pomocí prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure příklady v tomto článku, nezapomeňte nainstalovat nejnovější verzi [prostředí Azure PowerShell](/powershell/azure/install-az-ps) nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Všechny ukázkový skript v tomto článku předpokládá, že klient příkazového řádku běží na virtuálním počítači pomocí spravované identity pro prostředky Azure, které jsou povolené. Pomocí funkce "Připojení" virtuálního počítače na webu Azure Portal, se vzdáleně připojit k virtuálnímu počítači. Podrobnosti o povolení spravovaných identit pro prostředky Azure na virtuálním počítači, naleznete v tématu [konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí webu Azure portal](qs-configure-portal-windows-vm.md), nebo jeden z variant článků (pomocí Powershellu, rozhraní příkazového řádku, šablonu nebo Azure SADA SDK). 
> - Aby se zabránilo chybám při přístupu k prostředkům, spravovanou identitu Virtuálního počítače se musí předávat aspoň "Čtenář, získat přístup v příslušeného oboru (virtuální počítač nebo vyšší) umožňující operace Azure Resource Manageru ve virtuálním počítači. Zobrazit [přiřazení spravovaných identit pro prostředky Azure přístup k prostředku na webu Azure portal](howto-assign-access-portal.md) podrobnosti.

## <a name="overview"></a>Přehled

Poskytuje spravované identity pro prostředky Azure [instanční objekt](../develop/developer-glossary.md#service-principal-object) , což je [vytvořené při povolování spravovaných identit pro prostředky Azure](overview.md#how-does-it-work) na virtuálním počítači. Instanční objekt můžete udělen přístup k prostředkům Azure a použít jako identitu, skript nebo příkazového řádku klienti pro přihlašování a přístupu k prostředkům. Tradičně aby bylo možné přistupovat k zabezpečeným prostředkům v rámci své vlastní identity, skript klienta by potřeba:  

   - být registrován a vyjádření souhlasu s Azure AD jako důvěrné nebo webové klientské aplikace
   - Přihlaste se pod jeho instančnímu objektu služby, pomocí přihlašovacích údajů aplikace (které by mohly vložené do skriptu)

Pomocí spravované identity pro prostředky Azure skript klienta už musí udělat, jak se můžete přihlásit pod spravovaným identitám pro instanční objekt prostředků Azure. 

## <a name="azure-cli"></a>Azure CLI

Tento skript ukazuje, jak:

1. Přihlaste se k Azure AD v rámci spravovanou identitu Virtuálního počítače pro instanční objekt prostředků Azure  
2. Volání Azure Resource Manageru a získat ID instančního objektu služby Virtuálního počítače. Rozhraní příkazového řádku se stará o správu token pořízení a použití pro vás automaticky. Nezapomeňte nahradit název vašeho virtuálního počítače `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Tento skript ukazuje, jak:

1. Přihlaste se k Azure AD v rámci spravovanou identitu Virtuálního počítače pro instanční objekt prostředků Azure  
2. Volání Azure Resource Manageru rutiny pro získání informací o virtuálním počítači. Prostředí PowerShell se stará o správu využití tokenu pro vás automaticky.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>ID prostředků služeb Azure

Zobrazit [, že podpora Azure AD ověřování služby Azure](services-support-msi.md#azure-services-that-support-azure-ad-authentication) seznam prostředků, které podporují služby Azure AD a prošel testováním s využitím spravované identity pro prostředky Azure a jejich odpovídající ID prostředků.

## <a name="error-handling-guidance"></a>Pokyny pro zpracování chyb 

Odpovědi, jako je například následující může znamenat, že spravovanou identitu Virtuálního počítače pro prostředky Azure nebyla nakonfigurována správně:

- PowerShell: *Invoke-WebRequest: Nelze se připojit ke vzdálenému serveru*
- CLI: *MSI: Nepovedlo se načíst token z "http://localhost:50342/oauth2/token" s chybou "HTTPConnectionPool (hostitele ="localhost", port = 50342)* 

Pokud se zobrazí jedna z těchto chyb, vraťte se k virtuálnímu počítači Azure ve [webu Azure portal](https://portal.azure.com) a:

- Přejděte na **Identity** stránce a zajištění **přiřazenou systémem** je nastavená na "Ano".
- Přejděte **rozšíření** stránce a ujistěte se spravovaným identitám pro rozšíření prostředků Azure **(plánovaná k převedení na zastaralého v lednu 2019)** úspěšné nasazení.

Pokud je buď nesprávný, budete muset znovu nasadit spravované identity pro prostředky Azure pro váš prostředek znovu, nebo řešení potíží s nasazení se nezdařilo. Zobrazit [konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí webu Azure portal](qs-configure-portal-windows-vm.md) Pokud potřebujete pomoc s konfigurací virtuálního počítače.

## <a name="next-steps"></a>Další postup

- Povolit spravovaným identitám pro prostředky Azure na Virtuálním počítači Azure, najdete v článku [konfigurace spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí Powershellu](qs-configure-powershell-windows-vm.md), nebo [konfigurace spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí Azure CLI](qs-configure-cli-windows-vm.md)






