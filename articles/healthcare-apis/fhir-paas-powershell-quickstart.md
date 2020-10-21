---
title: 'Rychlý Start: nasazení rozhraní Azure API pro FHIR pomocí PowerShellu'
description: V tomto rychlém startu se dozvíte, jak nasadit Azure API pro FHIR pomocí PowerShellu.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.openlocfilehash: 2cb50f2ae98dab1e64e01498e0913d1932e455cd
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92339403"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-powershell"></a>Rychlý Start: nasazení rozhraní Azure API pro FHIR pomocí PowerShellu

V tomto rychlém startu se dozvíte, jak nasadit Azure API pro FHIR pomocí PowerShellu.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="register-the-azure-api-for-fhir-resource-provider"></a>Registrace poskytovatele prostředků Azure API pro FHIR

Pokud `Microsoft.HealthcareApis` poskytovatel prostředků ještě není pro vaše předplatné zaregistrovaný, můžete ho zaregistrovat pomocí:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

## <a name="create-azure-resource-group"></a>Vytvoření skupiny prostředků Azure

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroupName" -Location westus2
```

## <a name="deploy-azure-api-for-fhir"></a>Nasazení Azure API for FHIR

```azurepowershell-interactive
New-AzHealthcareApisService -Name nameoffhirservice -ResourceGroupName myResourceGroupName -Location westus2 -Kind fhir-R4
```

> [!NOTE]
> V závislosti na verzi `Az` modulu PowerShellu, který jste nainstalovali, může být zřízený Server FHIR nakonfigurovaný tak, aby používal [místní RBAC](configure-local-rbac.md) , a měl v seznamu povolených ID objektů pro službu FHIR aktuálně přihlášeného uživatele prostředí PowerShell. Po nasazení doporučujeme pro přiřazení rolí roviny dat [použít službu Azure RBAC](configure-azure-rbac.md) a možná budete muset po nasazení odstranit toto ID objektu uživatele, aby se povolil režim Azure RBAC.


## <a name="fetch-capability-statement"></a>Načíst příkaz schopnosti

Můžete ověřit, že je spuštěný účet rozhraní Azure API pro FHIR načtením příkazu FHIR schopnosti:

```azurepowershell-interactive
$metadata = Invoke-WebRequest -Uri "https://nameoffhirservice.azurehealthcareapis.com/metadata"
$metadata.RawContent
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte skupinu prostředků pomocí následujících kroků:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupName
```

## <a name="next-steps"></a>Další kroky

V této příručce pro rychlý Start jste nasadili rozhraní Azure API pro FHIR do svého předplatného. Pokud chcete nastavit další nastavení v rozhraní API Azure pro FHIR, přejděte k Průvodci dalšími nastaveními. Pokud jste připraveni začít používat rozhraní API Azure pro FHIR, přečtěte si další informace o tom, jak registrovat aplikace.

>[!div class="nextstepaction"]
>[Další nastavení v rozhraní API Azure pro FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Přehled registrace aplikací](fhir-app-registration.md)
