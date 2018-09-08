---
title: Vytvoření instančního objektu pro Azure Stack | Dokumentace Microsoftu
description: Popisuje, jak vytvořit nový instanční objekt, který lze použít s řízením přístupu na základě role v Azure Resource Manageru pro správu přístupu k prostředkům.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2018
ms.author: sethm
ms.openlocfilehash: 65fa9593b35af45ee9b8568bac5e4886909314e1
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092539"
---
# <a name="provide-applications-access-to-azure-stack"></a>Poskytnutí přístupu aplikací do Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Když aplikace potřebuje přístup k nasazení nebo konfiguraci prostředků prostřednictvím Azure Resource Manageru ve službě Azure Stack, vytvoření instančního objektu, což je přihlašovacích údajů pro vaši aplikaci. Potom můžete delegovat pouze potřebná oprávnění pro tento instanční objekt.  

Například můžete mít nástroj pro správu konfigurace, která používá Azure Resource Manageru k inventarizaci prostředků Azure. V tomto scénáři můžete vytvořit instanční objekt služby, přidělit tuto roli Čtenář takového objektu služby a omezit nástroje pro správu konfigurace pro přístup jen pro čtení. 

Instanční objekty jsou upřednostňovány vůči spuštění aplikace pod svými přihlašovacími údaji, protože:

* Můžete přiřadit oprávnění pro instanční objekt, který se liší od účtu oprávnění. Tato oprávnění jsou obvykle omezená přesně na to, co aplikace potřebuje dělat.
* Není potřeba změnit přihlašovací údaje aplikace, pokud se změní vaše odpovědnosti.
* Certifikát můžete použít k automatizaci ověřování při provádění bezobslužného skriptu.  

## <a name="getting-started"></a>Začínáme

V závislosti na tom, jak nasadíte Azure Stack začnete tím, že vytváření instančního objektu. Tento dokument popisuje vytvoření instančního objektu pro obě [Azure Active Directory (Azure AD)](#create-service-principal-for-azure-ad) a [Active Directory Federation Services(AD FS)](#create-service-principal-for-ad-fs). Jakmile vytvoříte instanční objekt služby, se používají pro sadu kroků běžné služby AD FS a Azure Active Directory [delegovat oprávnění](#assign-role-to-service-principal) k roli.     

## <a name="create-service-principal-for-azure-ad"></a>Vytvoření instančního objektu pro službu Azure AD

Pokud jste nasadili Azure Stack jako úložiště identit pomocí Azure AD, můžete vytvořit instanční objekty stejně jako pro Azure. Tato část ukazuje, jak k provedení kroků na portálu. Zkontrolujte, jestli máte [požadovaná oprávnění Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) před zahájením.

### <a name="create-service-principal"></a>Vytvoření instančního objektu
V této části vytvoříte aplikaci (instanční objekt) ve službě Azure AD, která reprezentuje vaši aplikaci.

1. Přihlaste se ke svému účtu Azure prostřednictvím [webu Azure portal](https://portal.azure.com).
2. Vyberte **Azure Active Directory** > **registrace aplikací** > **registrace nové aplikace**   
3. Zadejte název a URL aplikace. Vyberte buď **webovou aplikaci nebo API** nebo **nativní** pro typ aplikace, kterou chcete vytvořit. Po nastavení hodnot, vyberte **vytvořit**.

Vytvoříte instanční objekt služby pro vaši aplikaci.

### <a name="get-credentials"></a>Získat přihlašovací údaje
Při programovém přihlášení pomocí ID je pro vaši aplikaci a webové aplikace a rozhraní API, ověřovací klíč. K získání těchto hodnot použijte následující postup:

1. Z **registrace aplikací** ve službě Active Directory, vyberte svou aplikaci.

2. Zkopírujte **ID aplikace** a uložte ho v kódu aplikace. Aplikací [ukázkové aplikace](#sample-applications) části odkazují na tuto hodnotu jako ID klienta.

     ![ID klienta](./media/azure-stack-create-service-principal/image12.png)
3. Chcete-li generovat ověřovací klíč pro webovou aplikaci / rozhraní API, vyberte **nastavení** > **klíče**. 

4. Zadejte popis klíče a jeho dobu platnosti. Až budete hotovi, vyberte **Uložit**.

Jakmile klíč uložíte, zobrazí se jeho hodnota. Zkopírujte tuto hodnotu do poznámkového bloku nebo jiného dočasného umístění, protože klíč nelze načíst později. Hodnotu klíče uveďte s ID aplikace, aby přihlásit jako aplikace. Hodnota klíče Store na místě, kde aplikace může načíst ji.

![Uložený klíč](./media/azure-stack-create-service-principal/image15.png)

Jakmile budete hotovi, přejděte k [přiřazení role aplikace](#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Vytvoření instančního objektu služby AD FS
Pokud jste nasadili Azure Stack se službou AD FS, slouží k vytvoření instančního objektu, přiřazení role pro přístup a přihlásit z Powershellu pomocí tohoto identity prostředí PowerShell.

Je skript spuštěn na virtuálním počítači ERCS z privileged koncového bodu.

Požadavky:
- Certifikát je povinný.

#### <a name="parameters"></a>Parametry

Tyto informace se vyžaduje jako vstup pro automatizaci parametry:


|Parametr|Popis|Příklad:|
|---------|---------|---------|
|Název|Název pro účet hlavní název služby|Moje aplikace|
|Vlastnost ClientCertificates|Pole objektů certifikátu|X509 certifikátu|
|ClientRedirectUris<br>(Volitelné)|Identifikátor URI přesměrování aplikace|-|

#### <a name="example"></a>Příklad:

1. Otevřete relaci Windows Powershellu se zvýšenými oprávněními a spusťte následující příkazy:

   > [!NOTE]
   > Tento příklad vytvoří certifikát podepsaný svým držitelem. Při spuštění těchto příkazů v produkčním nasazení použijte [Get-Certificate](/powershell/module/pkiclient/get-certificate) načíst objekt certifikátu pro certifikát, který chcete použít.

   ```PowerShell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

    # This produces a self signed cert for testing purposes. It is prefered to use a managed certificate for this.
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange

    $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name '<yourappname>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }
    $session|remove-pssession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "azurestackuser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```

2. Po dokončení automatizace, zobrazí požadované podrobnosti, které chcete použít hlavní název služby. 

   Příklad:

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="assign-a-role"></a>Přiřazení role
Jakmile se vytvoří nový instanční objekt služby, je nutné [přiřadit k roli](#assign-role-to-service-principal).

### <a name="sign-in-through-powershell"></a>Přihlaste se pomocí Powershellu
Jakmile přiřadíte roli, můžete přihlásit ke službě Azure Stack pomocí instančního objektu pomocí následujícího příkazu:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ClientId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Přiřadit roli instančnímu objektu služby
Pro přístup k prostředkům ve vašem předplatném, musíte přiřadit aplikace k roli. Rozhodněte, jakou roli představuje správná oprávnění pro aplikaci. Další informace o dostupných rolí, najdete v článku [RBAC: vestavěné role](../role-based-access-control/built-in-roles.md).

Nastavit obor na úrovni předplatného, skupinu prostředků nebo prostředek. Oprávnění se dědí do oboru na nižších úrovních. Například přidáním aplikace k roli Čtenář pro skupinu prostředků znamená, že můžete přečíst, skupinu prostředků a všechny prostředky, které obsahuje.

1. Na portálu Azure Stack přejděte na úroveň oboru, který chcete přiřadit aplikaci. Například vyberte přiřazení role v oboru předplatného, **předplatná**. Místo toho můžete třeba vybrat skupinu prostředků nebo prostředek.

2. Vyberte konkrétní předplatné (skupinu prostředků nebo prostředek), přiřazení aplikace.

     ![Vyberte předplatné pro přiřazení](./media/azure-stack-create-service-principal/image16.png)

3. Vyberte **řízení přístupu (IAM)**.

     ![Vyberte přístup](./media/azure-stack-create-service-principal/image17.png)

4. Vyberte **Přidat**.

5. Vyberte roli, kterou chcete přiřadit k aplikaci.

6. Vyhledávání pro vaši aplikaci a vyberte ji.

7. Vyberte **OK** k dokončení přiřazení role. Zobrazí se vaše aplikace v seznamu Uživatelé přiřazení k roli pro tento obor.

Teď, když máte vytvořený instanční objekt služby a přiřazenou roli, můžete začít používat to v rámci vaší aplikace pro přístup k prostředkům Azure Stack.  

## <a name="next-steps"></a>Další postup

[Přidání uživatelů pro AD FS](azure-stack-add-users-adfs.md)
[Správa uživatelských oprávnění](azure-stack-manage-permissions.md)
