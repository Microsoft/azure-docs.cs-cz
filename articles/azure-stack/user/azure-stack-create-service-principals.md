---
title: Vytvořit objekt služby pro Azure zásobníku | Microsoft Docs
description: Popisuje, jak vytvořit objekt služby, které je možné pomocí řízení přístupu na základě rolí ve službě Správce prostředků Azure, které pokud chcete spravovat přístup k prostředkům.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2018
ms.author: mabrigg
ms.openlocfilehash: de5712fd7b48a759b366f5b9808bbbefc6e305cd
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Poskytnutí aplikace přístup k prostředkům Azure zásobníku vytvořením objekty služby

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Můžete zajistit přístup aplikace k prostředkům Azure zásobníku tak, že vytvoříte službu objektu zabezpečení, která používá Azure Resource Manager. Objekt služby vám umožní konkrétní oprávnění delegáta pomocí [řízení přístupu na základě role](azure-stack-manage-permissions.md).

Jako osvědčený postup měli byste použít objekty služby pro vaše aplikace. Objekty služby, je vhodnější spuštění aplikace pomocí vlastních přihlašovacích údajů z následujících důvodů:

* Můžete přiřadit oprávnění do objektu, který se liší od vlastní oprávnění účtu služby. Objekt služby oprávnění jsou obvykle omezené na přesně co aplikaci je třeba provést.
* Nemáte ke změně pověření aplikace, pokud role nebo odpovědností změnit.
* Certifikát můžete použít k automatizaci ověřování při spuštění bezobslužného skriptu.

## <a name="example-scenario"></a>Příklad scénáře

Máte aplikace pro správu konfigurace, která potřebuje k inventarizaci prostředků Azure pomocí Azure Resource Manager. Můžete vytvořit objekt služby a přiřadit role Čtenář. Tato role poskytuje aplikace přístup jen pro čtení k prostředků Azure.

## <a name="getting-started"></a>Začínáme

Jako vodítko k použijte postup v tomto článku:

* Vytvořte objekt služby pro vaši aplikaci.
* Svou aplikaci zaregistrovat a vytvořte ověřovací klíč.
* Přiřazení aplikace k roli.

Způsob, jakým jste nakonfigurovali pro zásobník Azure Active Directory určuje, jak vytvořit objekt služby. Vyberte jednu z následujících možností:

* Vytvoření služby hlavní pro [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Vytvoření služby hlavní pro [Active Directory Federation Services (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

Kroky pro přiřazení objektu služby roli stejné pro Azure AD a služby AD FS. Po vytvoření objektu služby můžete [delegovat oprávnění](azure-stack-create-service-principals.md#assign-role-to-service-principal) podle jeho přiřazení k roli.

## <a name="create-a-service-principal-for-azure-ad"></a>Vytvořit objekt služby pro Azure AD

Pokud vaše zásobník Azure používá jako úložiště identit Azure AD, můžete vytvořit službu hlavní stejným postupem jako v Azure pomocí portálu Azure.

>[!NOTE]
Zkontrolujte, zda máte [požadovaná oprávnění Azure AD](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) před zahájením vytváření objektu služby.

### <a name="create-service-principal"></a>Vytvořit instanční objekt

Chcete-li vytvořit objekt služby pro aplikaci:

1. Přihlaste se k účtu Azure prostřednictvím [portál Azure](https://portal.azure.com).
2. Vyberte **Azure Active Directory** > **registrace aplikace** > **přidat**.
3. Zadejte název a URL aplikace. Vyberte buď **webovou aplikaci nebo API** nebo **nativní** pro typ aplikace, kterou chcete vytvořit. Po nastavení hodnoty, vyberte **vytvořit**.

### <a name="get-credentials"></a>Získat přihlašovací údaje

Při přihlášení prostřednictvím kódu programu, použijte Identifikátor vaší aplikace a ověřovací klíč. Pokud chcete získat tyto hodnoty:

1. Z **registrace aplikace** ve službě Active Directory, vyberte svou aplikaci.

2. Zkopírujte **ID aplikace** a uložte ho v kódu aplikace. Aplikace v [ukázkové aplikace](#sample-applications) použít **id klienta** k odkazování na **ID aplikace**.

     ![ID aplikace pro aplikaci](./media/azure-stack-create-service-principal/image12.png)
3. Pokud chcete generovat ověřovací klíč, vyberte **Klíče**.

4. Zadejte popis klíče a jeho dobu platnosti. Až budete hotovi, vyberte **Uložit**.

>[!IMPORTANT]
Po uložení klíče, klíče **hodnotu** se zobrazí. Poznamenejte si tuto hodnotu, protože nelze načíst klíč později. Hodnotu klíče uložte na místo, odkud ji aplikace může načíst.

![Hodnota klíče upozornění pro uložené klíč.](./media/azure-stack-create-service-principal/image15.png)

Posledním krokem je [přiřazení aplikace k roli](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Vytvoření instančního objektu služby AD FS

Pokud jste nasadili zásobník Azure pomocí služby AD FS jako úložiště identit, můžete použít prostředí PowerShell pro následující úkoly:

* Vytvoření instančního objektu.
* Přiřazení objektu služby roli.
* Přihlaste se pomocí identit instanční objekt.

### <a name="before-you-begin"></a>Než začnete

[Stáhněte požadované nástroje Azure zásobníku do místního počítače.](azure-stack-powershell-download.md)

### <a name="import-the-identity-powershell-module"></a>Naimportujte modul Powershellu Identity

Přejděte ke složce pro stažení nástroje Azure zásobníku a importujte modul Identity PowerShell pomocí následujícího příkazu:

```PowerShell
Import-Module .\Identity\AzureStack.Identity.psm1
```

Při importu modulu Identity, může získat tato chybová zpráva: "AzureStack.Connect.psm1 není digitálně podepsán. Skript nebude spustit v systému".

Chcete-li tento problém vyřešit, musíte nakonfigurovat zásady spouštění umožnit spouštění skriptu. Pokud chcete nastavit zásady spouštění, spusťte následující příkaz v relaci prostředí PowerShell zvýšenými oprávněními:

```PowerShell
Set-ExecutionPolicy Unrestricted
```

### <a name="create-the-service-principal"></a>Vytvoření instančního objektu

Můžete vytvořit objekt služby tak, že spustíte následující příkaz, a zkontrolujte, zda aktualizace **DisplayName** parametr:

```powershell
$servicePrincipal = New-AzSADGraphServicePrincipal `
 -DisplayName "<YourServicePrincipalName>" `
 -AdminCredential $(Get-Credential) `
 -AdfsMachineName "AZS-ADFS01" `
 -Verbose

```

### <a name="assign-a-role"></a>Přiřazení role

Po vytvoření objektu služby, je nutné [přiřadit roli](azure-stack-create-service-principals.md#assign-role-to-service-principal).

### <a name="sign-in-using-powershell"></a>Přihlaste se pomocí prostředí PowerShell

Můžete přihlásit k Azure zásobníku tak, že spustíte následující příkaz, a zkontrolujte, zda aktualizace **EnvironmentName** parametr s názvem vaší aplikace:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId `
 -TenantId $directoryTenantId
```

## <a name="assign-the-service-principal-to-a-role"></a>Přiřazení objektu služby roli

Pro přístup k prostředkům ve vašem předplatném, je nutné přiřadit aplikace k roli. Rozhodněte, jakou roli představuje správná oprávnění pro aplikaci. Další informace o dostupných rolí najdete v tématu [RBAC: integrovaným rolím](../../role-based-access-control/built-in-roles.md).

>[!NOTE]
Obor role můžete nastavit na úrovni předplatné, skupinu prostředků nebo prostředek. Na nižších úrovních oboru dědí oprávnění. Například aplikace pomocí role Čtenář pro skupinu prostředků znamená, že aplikace může číst všechny prostředky ve skupině prostředků.

Následující postup použijte jako vodítko pro přiřazení rolí k objektu služby.

1. Na portálu Azure zásobníku přejděte na úroveň oboru, které chcete přiřadit aplikaci. Například přiřazení role v oboru předplatné, vyberte **odběry**.

2. Vyberte předplatné přiřadit aplikaci. Předplatné v tomto příkladu je Visual Studio Enterprise.

     ![Vyberte předplatné Visual Studio Enterprise pro přiřazení](./media/azure-stack-create-service-principal/image16.png)

3. Vyberte **řízení přístupu (IAM)** pro předplatné.

     ![Vyberte řízení přístupu](./media/azure-stack-create-service-principal/image17.png)

4. Vyberte **Přidat**.

5. Vyberte roli, kterou chcete přiřadit k aplikaci.

6. Vyhledávání pro vaši aplikaci a vyberte jej.

7. Vyberte **OK** k dokončení přiřazení role. Zobrazí se aplikace v seznamu Uživatelé s přiřazenou rolí pro tento obor.

Teď, když jste vytvořili objekt služby a přiřazenou roli, můžete aplikaci přístup k prostředkům Azure zásobníku.

## <a name="next-steps"></a>Další postup

[Správa uživatelských oprávnění](azure-stack-manage-permissions.md)
