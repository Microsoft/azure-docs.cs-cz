---
title: "Jak používat Azure virtuálního počítače spravované služby Identity pro přihlášení"
description: "Podrobné pokyny a příklady pro použití, přihlaste se objektu služby MSI virtuálních počítačů Azure pro skript klienta a prostředku přístup."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 735b673227ac128bbc01d3b2072f4b14d84b0e66
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>Jak používat Azure virtuálního počítače spravované služby Identity (MSI) pro přihlášení 

[!INCLUDE [preview-notice](../../includes/active-directory-msi-preview-notice.md)]  
Tento článek obsahuje příklady skriptů prostředí PowerShell a rozhraní příkazového řádku pro přihlašování pomocí služby MSI instančního objektu a pokyny na důležité oblastech, jako je zpracování chyb.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pokud budete chtít použít v prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure příkladech v tomto článku, je nutné nainstalovat nejnovější verzi [prostředí Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) nebo [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Všechny ukázkový skript v tomto článku předpokládá, že klient příkazového řádku běží na virtuálním počítači povolená MSI. Použijte funkci "Připojit" virtuální počítač na portálu Azure se vzdáleně připojit k virtuálnímu počítači. Informace o povolení MSI na virtuálním počítači, v [konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí portálu Azure](msi-qs-configure-portal-windows-vm.md), nebo jeden z typu variant článků (pomocí prostředí PowerShell, rozhraní příkazového řádku, šablonu nebo Azure SDK). 
> - Aby se zabránilo chybám při přístupu k prostředkům, MSI Virtuálního počítače musí mít alespoň "Čtečky" získat přístup v oboru příslušné (virtuálního počítače nebo vyšší) umožňuje Azure Resource Manager operace na virtuálním počítači. V tématu [přiřadit identita spravované služby (MSI) přístup k prostředku na portálu Azure](msi-howto-assign-access-portal.md) podrobnosti.

## <a name="overview"></a>Přehled

Poskytuje MSI [instanční objekt](develop/active-directory-dev-glossary.md#service-principal-object), což je [při povolení MSI vytvářeny](msi-overview.md#how-does-it-work) ve virtuálním počítači. Objekt služby můžete poskytnut přístup k prostředkům Azure a použít jako identita skriptu nebo příkazového-řádku klienti pro přihlašování a přístupu k prostředkům. Tradičně aby bylo možné získat přístup k zabezpečeným prostředkům v rámci své vlastní identity, klient skriptu by bylo potřeba:  

   - zaregistrovat a dá souhlas s Azure AD jako důvěrné nebo webové klientské aplikace
   - Přihlaste se pod jeho instanční objekt, pomocí přihlašovacích údajů aplikace (které by mohly vložená ve skriptu)

Pomocí Instalační služby MSI vašeho skriptu klienta už nepotřebuje udělat, jak můžete přihlásit v rámci objektu služby MSI. 

## <a name="azure-cli"></a>Azure CLI

Následující skript ukazuje, jak:

1. Přihlaste se k Azure AD v rámci objektu služby MSI Virtuálního počítače  
2. Volání správce prostředků Azure a získat ID Virtuálního počítače službu objektu zabezpečení. Rozhraní příkazového řádku se stará o správu tokenu získávání nebo použití pro vás automaticky. Nezapomeňte nahradit název virtuálního počítače pro `<VM-NAME>`.  

   ```azurecli
   az login --msi
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Následující skript ukazuje, jak:

1. Získejte přístupový token MSI pro virtuální počítač.  
2. Použití tokenu přístupu pro přihlášení ke službě Azure AD, v části odpovídající objektu služby MSI.   
3. Volání rutiny Azure Resource Manager získat informace o virtuálním počítači. Prostředí PowerShell se stará o správu využití tokenu pro vás automaticky.  

   ```azurepowershell
   # Get an access token for the MSI
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                                 -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
   $content =$response.Content | ConvertFrom-Json
   $access_token = $content.access_token
   echo "The MSI access token is $access_token"

   # Use the access token to sign in under the MSI service principal. -AccountID can be any string to identify the session.
   Login-AzureRmAccount -AccessToken $access_token -AccountId "MSI@50342"

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>ID prostředků pro služby Azure

V tématu [služeb Azure, podpora Azure AD ověření](msi-overview.md#azure-services-that-support-azure-ad-authentication) seznam prostředků, které podporují Azure AD a byly testovány s MSI a jejich odpovídající ID prostředku.

## <a name="error-handling-guidance"></a>Pokyny pro zpracování chyb 

Odpovědi, například následující může znamenat, že MSI Virtuálního počítače nebyla nakonfigurována správně:

- Prostředí PowerShell: *Invoke-WebRequest: Nelze se připojit ke vzdálenému serveru*
- Rozhraní příkazového řádku: *MSI: Nepodařilo se načíst token zabezpečení ze (http://localhost:50342/oauth2/token) s chybou "HTTPConnectionPool (hostitele = localhost, port = 50342)* 

Pokud se zobrazí jednu z těchto chyb, vraťte se do virtuálního počítače Azure v [portál Azure](https://portal.azure.com) a:

- Přejděte na **konfigurace** stránky a "Identita spravované služby" je nastavena na "Ano".
- Přejděte na **rozšíření** stránky a zkontrolujte příponou MSI úspěšně nasazena.

Pokud je buď nesprávný, musíte znovu nasaďte MSI v prostředku znovu, nebo vyřešit potíže s selhání nasazení. V tématu [konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí portálu Azure](msi-qs-configure-portal-windows-vm.md) Pokud potřebujete pomoc s konfigurací virtuálních počítačů.

## <a name="related-content"></a>Související obsah

- Povolit MSI ve virtuálním počítači Azure, najdete v části [konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí prostředí PowerShell](msi-qs-configure-powershell-windows-vm.md), nebo [konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí rozhraní příkazového řádku Azure](msi-qs-configure-cli-windows-vm.md)

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.








