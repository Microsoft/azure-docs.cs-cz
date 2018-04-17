---
title: Vytvořit objekt služby pro Azure zásobníku | Microsoft Docs
description: Popisuje, jak vytvořit nový objekt služby, které je možné pomocí řízení přístupu na základě rolí ve službě Správce prostředků Azure, které pokud chcete spravovat přístup k prostředkům.
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
ms.date: 03/15/2018
ms.author: mabrigg
ms.openlocfilehash: 833efa7f24d01346525af3dd9ca37012ab0ba3c4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="provide-applications-access-to-azure-stack"></a>Uveďte přístup aplikace k Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Když aplikace potřebuje přístup k nasazení nebo konfiguraci prostředků prostřednictvím Správce Azure Resource Manager v zásobníku Azure, můžete vytvořit objekt, služby, což je přihlašovací údaje pro vaši aplikaci.  Potom můžete delegovat pouze potřebná oprávnění pro tohoto objektu služby.  

Například můžete mít nástroj pro správu konfigurace pomocí Azure Resource Manager, která inventáře prostředků Azure.  V tomto scénáři můžete vytvořit objekt služby, udělení tohoto objektu služby role Čtenář a omezit nástroj Konfigurace správy na oprávnění jen pro čtení. 

Objekty služby, je vhodnější spuštění aplikace vlastní oprávnění, protože:

* Můžete přiřadit oprávnění do objektu, který se liší od vlastní oprávnění účtu služby. Tato oprávnění jsou obvykle omezené na přesně co aplikaci je třeba provést.
* Nemáte ke změně pověření aplikace, pokud vaše odpovědnosti změnit.
* Certifikát můžete použít k automatizaci ověřování při provádění bezobslužného skriptu.  

## <a name="getting-started"></a>Začínáme

V závislosti na tom, jak jste nasadili zásobník Azure je nejprve vytvořit službu objektu zabezpečení.  Tento dokument vás provede vytvořením objekt služby pro obě [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) a [Active Directory Federation Services(AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).  Po vytvoření objektu služby, sadu kroků běžné služby AD FS a Azure Active Directory se používá ke [delegovat oprávnění](azure-stack-create-service-principals.md#assign-role-to-service-principal) k roli.     

## <a name="create-service-principal-for-azure-ad"></a>Vytvoření instančního objektu pro Azure AD

Pokud jste nasadili zásobník Azure pomocí služby Azure AD jako úložiště identit, můžete vytvořit objekty služby stejně, jako je tomu u Azure.  V této části se dozvíte, jak provést kroky prostřednictvím portálu.  Zkontrolujte, zda máte [požadovaná oprávnění Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) před zahájením.

### <a name="create-service-principal"></a>Vytvoření instančního objektu
V této části vytvoříte aplikaci (instanční objekt) ve službě Azure AD, která reprezentuje vaši aplikaci.

1. Přihlaste se k účtu Azure prostřednictvím [portál Azure](https://portal.azure.com).
2. Vyberte **Azure Active Directory** > **registrace aplikace** > **přidat**   
3. Zadejte název a URL aplikace. Vyberte buď **webovou aplikaci nebo API** nebo **nativní** pro typ aplikace, kterou chcete vytvořit. Po nastavení hodnoty, vyberte **vytvořit**.

Vytvořili jste objekt služby pro vaši aplikaci.

### <a name="get-credentials"></a>Získání přihlašovacích údajů
Při prostřednictvím kódu programu přihlášení, abyste používali ID pro vaši aplikaci a pro webovou aplikaci nebo rozhraní API, ověřovací klíč. K získání těchto hodnot použijte následující postup:

1. Z **registrace aplikace** ve službě Active Directory, vyberte svou aplikaci.

2. Zkopírujte **ID aplikace** a uložte ho v kódu aplikace. Aplikace v [ukázkové aplikace](#sample-applications) části odkazují na tuto hodnotu jako ID klienta.

     ![ID klienta](./media/azure-stack-create-service-principal/image12.png)
3. Generovat ověřovací klíč pro webovou aplikaci nebo rozhraní API, vyberte **nastavení** > **klíče**. 

4. Zadejte popis klíče a jeho dobu platnosti. Až budete hotovi, vyberte **Uložit**.

Jakmile klíč uložíte, zobrazí se jeho hodnota. Zkopírujte si ji, protože později už klíč nepůjde načíst. Hodnota klíče byl poskytnout ID aplikace pro přihlášení jako aplikace. Hodnotu klíče uložte na místo, odkud ji aplikace může načíst.

![Uložený klíč](./media/azure-stack-create-service-principal/image15.png)


Po dokončení pokračujte [přiřazení aplikace k roli](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Vytvoření instančního objektu služby AD FS
Pokud jste nasadili zásobník Azure se službou AD FS, můžete použít PowerShell vytvoření instančního objektu, přiřazení role pro přístup a přihlašování z prostředí PowerShell pomocí danou identitu.

Z koncového bodu privilegované spuštění skriptu na virtuálním počítači ERCS.


Požadavky:
- Certifikovaných je povinný.

**Parametry**

Tyto informace o požadované jako vstup pro automatizaci parametry:


|Parametr|Popis|Příklad:|
|---------|---------|---------|
|Název|Název SPN účtu|Moje aplikace|
|ClientCertificates|Pole objektů certifikátu|X509 certifikátu|
|ClientRedirectUris<br>(Volitelné)|Aplikace identifikátor URI pro přesměrování|         |

**Příklad**

1. Otevřete relaci prostředí Windows PowerShell zvýšenými oprávněními a spusťte následující příkazy:

   > [!NOTE]
   > Tento příklad vytvoří certifikát podepsaný svým držitelem. Při spuštění těchto příkazů v produkčním nasazení, použijte Get-certifikát načíst objekt certifikátu pro certifikát, který chcete použít.

   ```
   $creds = Get-Credential

   $session = New-PSSession -ComputerName <IP Address of ECRS> -ConfigurationName PrivilegedEndpoint -Credential $creds

   $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=testspn2" -KeySpec KeyExchange

   Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name 'MyApp' -ClientCertificates $using:cert}

   $session|remove-pssession

   ```

2. Po dokončení automatizace zobrazí požadované podrobnosti používat hlavní název služby. 

   Příklad:

   ```
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```
### <a name="assign-a-role"></a>Přiřazení role
Po vytvoření objektu služby musíte [přiřadit k roli](azure-stack-create-service-principals.md#assign-role-to-service-principal)

### <a name="sign-in-through-powershell"></a>Přihlaste se pomocí prostředí PowerShell
Jakmile jste přiřazenou roli, můžete přihlásit do protokolů Azure pomocí objektu služby pomocí následujícího příkazu:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Přiřazení role instančního objektu
Pro přístup k prostředkům ve vašem předplatném, je nutné přiřadit aplikace k roli. Rozhodněte, jakou roli představuje správná oprávnění pro aplikaci. Další informace o dostupných rolí najdete v tématu [RBAC: integrovaným rolím](../role-based-access-control/built-in-roles.md).

Rozsah můžete nastavit na úrovni předplatné, skupinu prostředků nebo prostředek. Na nižších úrovních oboru dědí oprávnění. Například přidání aplikace do role Čtenář pro skupinu prostředků znamená, že ho může číst skupině prostředků a všechny prostředky, které obsahuje.

1. Na portálu Azure zásobníku přejděte na úroveň oboru, který chcete přiřadit aplikaci. Například přiřazení role v oboru předplatné, vyberte **odběry**. Místo toho můžete třeba vybrat skupinu prostředků nebo prostředek.

2. Vyberte určitý odběr (skupinu prostředků nebo prostředek) aplikaci přiřadit.

     ![Vyberte předplatné pro přiřazení](./media/azure-stack-create-service-principal/image16.png)

3. Vyberte **přístup k ovládacímu prvku (IAM)**.

     ![Vybrat přístupu](./media/azure-stack-create-service-principal/image17.png)

4. Vyberte **Přidat**.

5. Vyberte roli, kterou chcete přiřadit k aplikaci.

6. Vyhledávání pro vaši aplikaci a vyberte jej.

7. Vyberte **OK** k dokončení přiřazení role. Zobrazí aplikace v seznamu Uživatelé s přiřazenou rolí pro tento obor.

Teď, když jste vytvořili objekt služby a přiřazenou roli, můžete začít používat to v rámci vaší aplikace pro přístup k prostředkům Azure zásobníku.  

## <a name="next-steps"></a>Další postup

[Přidat uživatele pro AD FS](azure-stack-add-users-adfs.md)
[Správa oprávnění uživatele](azure-stack-manage-permissions.md)