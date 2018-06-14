---
title: Cluster HPC Pack s Azure Active Directory | Microsoft Docs
description: Zjistěte, jak integrovat Microsoft HPC Pack 2016 clusteru v Azure s Azure Active Directory
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/16/2017
ms.author: danlep
ms.openlocfilehash: bb0e878c4e987d111a535603cede25c639087ca7
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2017
ms.locfileid: "25452568"
---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>Spravovat cluster služby HPC Pack v Azure pomocí Azure Active Directory
[Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) podporuje integraci se službou [Azure Active Directory](../../active-directory/index.md) (Azure AD) pro správce, kteří nasazení clusteru HPC Pack v Azure.



Postupujte podle kroků v tomto článku pro následující úlohy vysoké úrovně: 
* Cluster HPC Pack ručně integrate klientovi Azure AD
* Spravovat a plánování úloh v clusteru HPC Pack v Azure 

Integrace s Azure AD řešení clusteru prostředí HPC Pack postupuje standardní integrovat dalšími aplikacemi a službami. Tento článek předpokládá, že jste se seznámili s základní uživatel správy ve službě Azure AD. Další informace a pozadí, najdete v článku [dokumentaci služby Azure Active Directory](../../active-directory/index.md) a v následující části.

## <a name="benefits-of-integration"></a>Výhody integrace


Azure Active Directory (Azure AD) je víceklientské cloudové adresáře a identity management služba, která poskytuje jednotné přihlašování (SSO) přístup k cloudové řešení.

Integrace clusteru HPC Pack s Azure AD můžete dosáhnout sledovat tyto cíle:

* Odeberte z clusteru HPC Pack tradiční řadič domény služby Active Directory. To může pomoct snížit náklady na údržbu clusteru, pokud to není nezbytné pro své firmy a zrychlit procesu nasazení.
* Využívejte následující výhody, které jsou uvedeny ve službě Azure AD:
    *   Jednotné přihlašování 
    *   Pomocí místní AD identity pro cluster HPC Pack v Azure 

    ![Prostředí Azure Active Directory](./media/hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>Požadavky
* **Cluster HPC Pack 2016 nasazené ve virtuálních počítačích Azure** – pokyny najdete v tématu [nasazení clusteru HPC Pack 2016 v Azure](hpcpack-2016-cluster.md). Potřebujete název DNS hlavního uzlu a přihlašovací údaje Správce clusteru k dokončení kroků v tomto článku.

  > [!NOTE]
  > Integrace se službou Azure Active Directory není podporováno ve verzích sady HPC Pack před HPC Pack 2016.



* **Klientský počítač** -potřebují klientský počítač Windows nebo Windows Server ke spuštění nástroje HPC Pack klienta. Pokud chcete používat k odesílání úloh HPC Pack webový portál nebo REST API, můžete použít libovolného klientského počítače podle svého výběru.

* **HPC Pack klienta nástroje** -nainstalujte nástroje HPC Pack klienta v klientském počítači, pomocí dostupné volné instalační balíček z webu Microsoft Download Center.


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>Krok 1: Zaregistrujte server clusteru HPC s klientovi Azure AD
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet umožňuje přístup k více než jednoho klienta Azure AD, klikněte na váš účet v pravém horním rohu. Nastavte požadované klienta portálu relace. Musíte mít oprávnění k přístupu k prostředkům v adresáři. 
3. Klikněte na tlačítko **Azure Active Directory** v levém navigačním podokně služby, klikněte na tlačítko **uživatelů a skupin**a ověřte, zda uživatelských účtů již vytvořené nebo nakonfigurovaná.
4. V **Azure Active Directory**, klikněte na tlačítko **registrace aplikace** > **nové registrace aplikace**. Zadejte následující informace:
    * **Název** -HPCPackClusterServer
    * **Typ aplikace** – vyberte **webovou aplikaci nebo rozhraní API**
    * **Adresa URL přihlašování**– základní adresu URL pro vzorku, který je ve výchozím nastavení`https://hpcserver`
    * Klikněte na možnost **Vytvořit**.
5. Po přidání aplikace, vyberte ho **registrace aplikace** seznamu. Pak klikněte na tlačítko **nastavení** > **vlastnosti**. Zadejte následující informace:
    * Vyberte **Ano** pro **nevyužívá dělené tabulky více**.
    * Změna **identifikátor ID URI aplikace** k `https://<Directory_name>/<application_name>`. Nahraďte `<Directory_name`> s úplným názvem klientovi Azure AD, například `hpclocal.onmicrosoft.com`a nahraďte `<application_name>` s názvem, který jste vybrali dříve.
6. Klikněte na **Uložit**. Po dokončení ukládání, na stránce aplikace klikněte na tlačítko **Manifest**. Upravte manifest tím, že se `appRoles` nastavení a přidání následující role aplikace a pak klikněte na tlačítko **Uložit**:

  ```json
  "appRoles": [
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "displayName": "HpcAdminMirror",
     "id": "61e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "description": "HpcAdminMirror",
     "value": "HpcAdminMirror"
     },
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "description": "HpcUsers",
     "displayName": "HpcUsers",
     "id": "91e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "value": "HpcUsers"
     }
  ],
  ```
7. V **Azure Active Directory**, klikněte na tlačítko **podnikové aplikace, které** > **všechny aplikace**. Vyberte **HPCPackClusterServer** ze seznamu.
8. Klikněte na tlačítko **vlastnosti**a změňte **přiřazení uživatelských požadované** k **Ano**. Klikněte na **Uložit**.
9. Klikněte na tlačítko **uživatelů a skupin** > **přidat uživatele**. Vyberte uživatele, vyberte roli a potom klikněte **přiřadit**. Přiřadíte jeden z dostupných rolí (HpcUsers nebo HpcAdminMirror) pro uživatele. Opakujte tento krok s dalším uživatelům v adresáři. Základní informace o uživatelích, clusteru, najdete v části [Správa uživatelů clusteru](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx).


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>Krok 2: Registrace klienta clusteru HPC s klientovi Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet umožňuje přístup k více než jednoho klienta Azure AD, klikněte na váš účet v pravém horním rohu. Nastavte požadované klienta portálu relace. Musíte mít oprávnění k přístupu k prostředkům v adresáři. 
3. V **Azure Active Directory**, klikněte na tlačítko **registrace aplikace** > **nové registrace aplikace**. Zadejte následující informace:

    * **Název** -HPCPackClusterClient    
    * **Typ aplikace** – vyberte **nativní**
    * **Identifikátor URI pro přesměrování** - `http://hpcclient`
    * Klikněte na **Vytvořit**

4. Po přidání aplikace, vyberte ho **registrace aplikace** seznamu. Kopírování **ID aplikace** hodnotu a uložte ho. Budete potřebovat později při konfiguraci aplikace.

5. Klikněte na tlačítko **nastavení** > **požadovaná oprávnění** > **přidat** > **vybrat rozhraní API**. Hledání a vyberte aplikaci HpcPackClusterServer (vytvořený v kroku 1).

6. V **povolit přístup** vyberte **přístup HpcClusterServer**. Potom klikněte na **Done** (Hotovo).


## <a name="step-3-configure-the-hpc-cluster"></a>Krok 3: Konfigurace clusteru HPC

1. Připojení k hlavnímu uzlu HPC Pack 2016 v Azure.

2. Spusťte prostředí HPC PowerShell.

3. Spusťte následující příkaz:

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcPackClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    kde

    * `AADTenant`Určuje název klienta Azure AD, jako například`hpclocal.onmicrosoft.com`
    * `AADClientAppId`Určuje ID aplikace pro aplikaci vytvořili v kroku 2.

4. Proveďte jednu z následujících, v závislosti na konfiguraci hlavního uzlu:

    * V clusteru HPC Pack jeden hlavní uzel restartujte službu HpcScheduler.

    * Na clusteru HPC Pack s více hlavních uzlech spusťte z hlavního uzlu se restartovat službu HpcSchedulerStateful následující příkazy prostředí PowerShell:

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName "fabric:/HpcApplication/SchedulerStatefulService"

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>Krok 4: Správa a odesílání úloh z klienta

Pokud chcete nainstalovat klienta nástroje HPC Pack ve vašem počítači, stáhněte instalační soubory HPC Pack 2016 (úplná instalace) z webu Microsoft Download Center. Abyste před zahájením instalace, zvolte možnost instalační program **HPC Pack klienta nástroje**.

Příprava klientského počítače, nainstalujte certifikát použitý během [nastavení clusteru HPC](hpcpack-2016-cluster.md) v klientském počítači. Použijte standardní postupy správy certifikátu Windows veřejný certifikát, který chcete nainstalovat **certifikáty – aktuální uživatel** > **důvěryhodné kořenové certifikační autority** uložit. 

Nyní můžete spustit příkazy HPC Pack nebo pomocí Správce úloh HPC Pack grafickým uživatelským rozhraním odesílat a spravovat úlohy clusteru pomocí účtu Azure AD. Možnosti pro odeslání úlohy najdete v tématu [clusteru HPC odeslání úlohy HPC Pack v Azure](hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster).

> [!NOTE]
> Při pokusu o připojení ke clusteru HPC Pack v Azure poprvé, zobrazí se místní windows. Zadejte přihlašovací údaje Azure AD k přihlášení. Token se pak uloží do mezipaměti. Novější připojení do clusteru v Azure používat token v mezipaměti, pokud není zaškrtnuté změny ověřování nebo do mezipaměti.
>
  
Například po dokončení předchozích kroků se můžete dotazovat pro úlohy z lokálního klienta následujícím způsobem:

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>Užitečné rutiny pro odeslání úlohy s integrací služby Azure AD 

### <a name="manage-the-local-token-cache"></a>Spravovat místní mezipaměti tokenu

HPC Pack 2016 poskytuje následující rutiny prostředí HPC PowerShell ke správě místního mezipamětí tokenů. Tyto rutiny jsou užitečné pro odesílání úloh interaktivně. Podívejte se na následující příklad:

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>Nastavte přihlašovací údaje pro odesílání úloh pomocí účtu Azure AD 

V některých případech můžete chtít spustit úlohu uživatele clusteru prostředí HPC (pro připojené k doméně clusteru HPC, spusťte jako jednu doménu uživatele; pro domény nepřipojená cluster prostředí HPC, spustit jako jeden místní uživatel z hlavního uzlu).

1. Pomocí následujících příkazů nastavit přihlašovací údaje:

    ```powershell
    $localUser = "<username>"

    $localUserPassword="<password>"

    $secpasswd = ConvertTo-SecureString $localUserPassword -AsPlainText -Force

    $mycreds = New-Object System.Management.Automation.PSCredential ($localUser, $secpasswd)

    Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name>
    ```

2. Potom takto odeslání úlohy. Úloh se spouští v části $localUser výpočetní uzly.

    ```powershell
    $emptycreds = New-Object System.Management.Automation.PSCredential ($localUser, (new-object System.Security.SecureString))
    ...
    $job = New-HpcJob –Scheduler https://<Azure load balancer DNS name>

    Add-HpcTask -Job $job -CommandLine "ping localhost" -Scheduler https://<Azure load balancer DNS name>

    Submit-HpcJob -Job $job -Scheduler https://<Azure load balancer DNS name> -Credential $emptycreds
    ```
    
   Pokud `–Credential` není zadaný s `Submit-HpcJob`, úlohy nebo úkolu spouští pod místním uživatelským namapované jako účet Azure AD. (Clusteru HPC vytvoří místní uživatele se stejným názvem jako účet služby Azure AD, který chcete spustit úlohu.)
    
3. Nastavit rozšířená data pro účet Azure AD. To je užitečné, pokud se používá úlohu MPI na Linuxových uzlů pomocí účtu Azure AD.

   * Nastavit rozšířená data pro samotné účet Azure AD

      ```powershell
      Set-HpcJobCredential -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data> -AadUser
      ```
      
   * Nastavte Rozšířená data a spusťte jako uživatel clusteru HPC
   
      ```powershell
      Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data>
      ```

