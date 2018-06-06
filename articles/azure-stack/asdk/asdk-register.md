---
title: Zaregistrovat ASDK Azure | Microsoft Docs
description: Popisuje postup registrace zásobník Azure pomocí Azure marketplace syndikace a vytváření sestav využití povolit.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: bc17045106b2656cdeb5f51037b3138aeac9ee0f
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757463"
---
# <a name="azure-stack-registration"></a>Registrace Azure zásobníku
Instalace služby Azure zásobníku Development Kit (ASDK) můžete zaregistrovat s Azure můžete stáhnout z Azure marketplace položky a nastavit obchodování při generování sestav dat zpět do společnosti Microsoft. Registrace je vyžadována pro podporu plnou funkčnost zásobník Azure, včetně syndikace marketplace. Registrace je doporučená, protože umožňuje otestovat důležité funkce Azure zásobníku jako syndikace marketplace a vytváření sestav využití. Po registraci Azure zásobníku využití oznamovány obchodu Spojených států v Azure. Zobrazí se v rámci předplatného, které jste použili pro registraci. Uživatelé ASDK však nejsou účtovat žádné využití, které patří.

Pokud vaše ASDK nezaregistrujete, může se zobrazit **je vyžadována aktivace** upozornění s výzvou k registraci vaší Azure zásobníku Development Kit. Toto chování je očekávané.

## <a name="prerequisites"></a>Požadavky
Před použitím těchto pokynů k registraci ASDK s Azure, ujistěte se, že máte nainstalovaný Azure PowerShell zásobníku a nástroje Azure zásobníku stáhnout, jak je popsáno v [konfiguraci po nasazení](asdk-post-deploy.md) článku.

Kromě toho mode jazyk prostředí PowerShell musí být nastavený na **FullLanguageMode** v počítači používá k registraci ASDK s Azure. Chcete-li ověřit, že aktuální jazyk režim je nastaven na úplné, otevřete okno prostředí PowerShell se zvýšenými oprávněními a spusťte následující příkazy prostředí PowerShell:

```powershell
$ExecutionContext.SessionState.LanguageMode
```

Zkontrolujte výstup vrací **FullLanguageMode**. Pokud je vrácen další jazyk režimu, registrace bude nutné spustit v jiném počítači nebo jazyk režimu bude nutné nastavit na **FullLanguageMode** než budete pokračovat.

## <a name="register-azure-stack-with-azure"></a>Zaregistrovat Azure zásobník Azure
Postupujte podle těchto kroků k registraci ASDK s Azure.

> [!NOTE]
> Všechny tyto kroky nutné spustit z počítače, který má přístup k privilegované koncový bod. Pro ASDK, která se development kit hostitelský počítač.

1. Otevřete konzolu prostředí PowerShell jako správce.  

2. Spusťte následující příkazy prostředí PowerShell při registraci vaší instalace ASDK s Azure. Musíte se přihlásit do vašeho předplatného Azure a místní instalaci ASDK. Pokud nemáte předplatné Azure, ale můžete [vytvořit bezplatný účet Azure zde](https://azure.microsoft.com/free/?b=17.06). Registrace Azure zásobníku způsobuje bez nákladů na vaše předplatné Azure.

  ```powershell
  # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
  Add-AzureRmAccount -EnvironmentName "AzureCloud"

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  #Register Azure Stack
  $AzureContext = Get-AzureRmContext
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01 `
      -BillingModel Development
  ```
3. Po dokončení skriptu se zobrazí tato zpráva: **prostředí je nyní zaregistrovaná a aktivována pomocí zadané parametry.**

    ![](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>Ověřte, zda že byla registrace úspěšná
Následujícím postupem ověřte, že registrace ASDK pomocí Azure bylo úspěšné.

1. Přihlaste se k [portálu pro správu Azure zásobníku](https://adminportal.local.azurestack.external).

2. Klikněte na tlačítko **Marketplace správu** > **přidat z Azure**.

    ![](media/asdk-register/2.PNG)

3. Pokud se zobrazí seznam položek, které jsou k dispozici prostřednictvím Azure, aktivací byla úspěšná.

    ![](media/asdk-register/3.PNG)

## <a name="next-steps"></a>Další postup
[Přidání položky zásobníku Azure marketplace.](asdk-marketplace-item.md)
