---
title: Použití Azure VM identita spravované služby pro přihlášení
description: Podrobné pokyny a příklady pro použití přihlášení instančního objektu MSI virtuálního počítače Azure pro skript klienta a prostředků přístupu.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 4a811c5354a9ff2aaa48a300d9b2655f91fdab23
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901088"
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>Použití Azure VM Identity spravované služby (MSI) pro přihlášení 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Tento článek obsahuje příklady skriptů Powershellu a rozhraní příkazového řádku pro přihlášení pomocí instančního objektu MSI a doprovodné materiály o důležitých tématech, jako je zpracování chyb.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pokud máte v plánu pomocí prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure příklady v tomto článku, nezapomeňte nainstalovat nejnovější verzi [prostředí Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) nebo [příkazového řádku Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Všechny ukázkový skript v tomto článku předpokládá, že klient příkazového řádku běží na virtuálním počítači s povoleným MSI. Pomocí funkce "Připojení" virtuálního počítače na webu Azure Portal, se vzdáleně připojit k virtuálnímu počítači. Podrobnosti o povolení MSI virtuálního počítače najdete v tématu [nakonfigurovat virtuálním počítači Identity spravované služby (MSI) pomocí webu Azure portal](qs-configure-portal-windows-vm.md), nebo jeden z variant článků (pomocí Powershellu, rozhraní příkazového řádku, šablonu nebo pomocí sady Azure SDK). 
> - Aby se zabránilo chybám při přístupu k prostředkům, MSI Virtuálního počítače se musí předávat aspoň "Čtenář, získat přístup v příslušeného oboru (virtuální počítač nebo vyšší) umožňující operace Azure Resource Manageru ve virtuálním počítači. Zobrazit [přiřadit Identity spravované služby (MSI) přístup k prostředku na webu Azure portal](howto-assign-access-portal.md) podrobnosti.

## <a name="overview"></a>Přehled

Poskytuje MSI [instanční objekt](../develop/active-directory-dev-glossary.md#service-principal-object) , což je [vytvořené při povolení MSI](overview.md#how-does-it-work) na virtuálním počítači. Instanční objekt můžete udělen přístup k prostředkům Azure a použít jako identitu, skript nebo příkazového řádku klienti pro přihlašování a přístupu k prostředkům. Tradičně aby bylo možné přistupovat k zabezpečeným prostředkům v rámci své vlastní identity, skript klienta by potřeba:  

   - být registrován a vyjádření souhlasu s Azure AD jako důvěrné nebo webové klientské aplikace
   - Přihlaste se pod jeho instančnímu objektu služby, pomocí přihlašovacích údajů aplikace (které by mohly vložené do skriptu)

Pomocí MSI skript klienta už musí udělat, jak se můžete přihlásit pod instančního objektu MSI. 

## <a name="azure-cli"></a>Azure CLI

Tento skript ukazuje, jak:

1. Přihlaste se k Azure AD v rámci objektu služby MSI Virtuálního počítače  
2. Volání Azure Resource Manageru a získat ID instančního objektu služby Virtuálního počítače. Rozhraní příkazového řádku se stará o správu token pořízení a použití pro vás automaticky. Nezapomeňte nahradit název vašeho virtuálního počítače `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Tento skript ukazuje, jak:

1. Přihlaste se k Azure AD v rámci objektu služby MSI Virtuálního počítače  
2. Volání Azure Resource Manageru rutiny pro získání informací o virtuálním počítači. Prostředí PowerShell se stará o správu využití tokenu pro vás automaticky.  

   ```azurepowershell
   Add-AzureRmAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>ID prostředků služeb Azure

Zobrazit [, že podpora Azure AD ověřování služby Azure](services-support-msi.md#azure-services-that-support-azure-ad-authentication) seznam prostředků, které podporují služby Azure AD a prošel testováním s využitím MSI a jejich odpovídající ID prostředků.

## <a name="error-handling-guidance"></a>Pokyny pro zpracování chyb 

Odpovědi, jako je například následující může znamenat, že MSI Virtuálního počítače není nakonfigurovaná správně:

- Prostředí PowerShell: *Invoke-WebRequest: Nelze se připojit ke vzdálenému serveru*
- Rozhraní příkazového řádku: *MSI: nepovedlo se načíst token z "http://localhost:50342/oauth2/token" s chybou "HTTPConnectionPool (hostitele ="localhost", port = 50342)* 

Pokud se zobrazí jedna z těchto chyb, vraťte se k virtuálnímu počítači Azure ve [webu Azure portal](https://portal.azure.com) a:

- Přejděte **konfigurace** stránku a zkontrolujte "Se identita spravované služby" je nastavená na "Ano".
- Přejděte **rozšíření** stránky a ověřte rozšíření MSI úspěšné nasazení.

Pokud je buď nesprávný, budete muset znovu znovu provádět nasazení MSI pro váš prostředek ani řešení potíží s nasazení se nezdařilo. Zobrazit [nakonfigurovat virtuálním počítači Identity spravované služby (MSI) pomocí webu Azure portal](qs-configure-portal-windows-vm.md) Pokud potřebujete pomoc s konfigurací virtuálního počítače.

## <a name="related-content"></a>Související obsah

- Povolení MSI ve Virtuálním počítači Azure, najdete v článku [nakonfigurovat virtuálním počítači Identity spravované služby (MSI) pomocí prostředí PowerShell](qs-configure-powershell-windows-vm.md), nebo [nakonfigurovat virtuálním počítači Identity spravované služby (MSI) pomocí rozhraní příkazového řádku Azure](qs-configure-cli-windows-vm.md)

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.








