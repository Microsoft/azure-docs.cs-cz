---
title: Zabezpečení trezoru klíčů Azure – Azure Key Vault | Dokumentace Microsoftu
description: Spravujte přístupová oprávnění pro Azure Key Vault, klíče a tajné kódy. Popisuje model ověřování a autorizace pro Key Vault a postupy zabezpečení trezoru klíčů.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: ambapat
ms.openlocfilehash: ff831d117edc708303be1d9665b2c97bc248f6c2
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999757"
---
# <a name="secure-your-key-vault"></a>Zabezpečení trezoru klíčů

Služba Azure Key Vault je Cloudová služba, která chrání šifrovací klíče a tajné kódy (například certifikáty, připojovací řetězce a hesla). Protože tato data jsou citlivá a pro důležité obchodní informace, třeba zabezpečený přístup k vašim trezorům klíčů, umožňuje jenom autorizovaným aplikacím a uživatelům. Tento článek obsahuje přehled modelu access Key Vault. Vysvětluje ověření a autorizaci a popisuje, jak zabezpečit přístup.

## <a name="overview"></a>Přehled

Přístup k trezoru klíčů je řízen prostřednictvím dvou oddělených rozhraní: rovina správy a rovina dat. 
**Rovina správy** se zabývá Správa trezoru, například – vytvoření trezoru, aktualizuje se trezor, odstranění trezoru služby. 
**Rovina dat** obchody s tajnými kódy v trezoru, ve kterém je vytvářet, aktualizovat, odstranit a čtení tajného klíče v trezoru. Pro obě roviny řádné ověření a autorizace jsou požadovány, než může volající (uživatel nebo aplikace) získat přístup k trezoru klíčů. Ověření určí identitu volajícího a autorizace následně určí operace, které daný volající povoleno provádět.

K ověření využívají rovina správy i rovina dat službu Azure Active Directory. K autorizaci ale rovina správy používá řízení přístupu podle role (RBAC), zatímco rovina dat používá zásady přístupu trezoru klíčů.

Stručný přehled tímto článkem pokrytých témat:

[Ověření s použitím Azure Active Directory](#authentication-using-azure-active-directory): Tato část vysvětluje, jak se volající ověřuje ve službě Azure Active Directory za účelem přístupu k trezoru klíčů prostřednictvím roviny správy i roviny dat. 

Obě roviny pro ověřování pomocí Azure Active Directory (Azure AD). K autorizaci ale rovina správy používá řízení přístupu na základě rolí (RBAC), zatímco rovina dat používá zásady přístupu trezoru klíčů.

## <a name="authenticate-by-using-azure-active-directory"></a>Ověřování pomocí Azure Active Directory

Když vytvoříte trezor klíčů v předplatném Azure, automaticky se přidruží s tenantem Azure AD předplatného. Všichni volající musí být v tomto tenantovi registrováni a musí ověřit přístup k trezoru klíčů. Tento požadavek platí pro obě roviny a data přístup k rovině správy. V obou případech se aplikace přístupné služby Key Vault dvěma způsoby:

* **přístup uživatele a aplikace**: Použít u aplikací, které přístup k trezoru klíčů jménem přihlášeného uživatele. Prostředí Azure PowerShell a na webu Azure portal jsou příkladem tento typ přístupu. Existují dva způsoby, jak udělit přístup uživatelům:

  - Přístup k trezoru klíčů z libovolné aplikace.
  - Access Key Vault jenom při použití konkrétní aplikace (označováno jako složená identita).

* **přístup jen pro aplikace**: Použít s aplikací, které běží jako proces démon, služeb nebo úloh na pozadí. Přístup k trezoru klíčů je udělen identitě aplikace.

V obou typů aplikací, aplikace nejprve ověří ve službě Azure AD pomocí některé z [podporovaných metod ověřování](../active-directory/develop/authentication-scenarios.md)a získá token. Metodu ověřování používanou závisí na typu aplikace. Aplikace pak tento token použije a odešle žádost o rozhraní REST API služby Key Vault. Požadavky rovině správy jsou směrovány přes koncový bod Azure Resource Manageru. Při přístupu k rovině dat, aplikace komunikuje přímo na koncový bod služby Key Vault. Další informace najdete v tématu [celý tok ověřování](../active-directory/develop/v1-protocols-oauth-code.md). 

Název prostředku, pro který aplikace požádá o token závisí na které roviny aplikace přistupuje. Název prostředku je koncový bod roviny správy nebo bodem roviny dat, v závislosti na prostředí Azure. Další podrobnosti najdete v tabulce dále v tomto článku.

Používání jediného mechanismu ověřování pro obě roviny má několik výhod:

* Organizace mohou centrálně řídit přístup ke všem trezorům klíčů v organizaci.
* Pokud uživatel opustí, nezíská okamžitě se ztratí přístup ke všem trezorům klíčů v organizaci.
* Organizace mohou ověření přizpůsobit prostřednictvím možností ve službě Azure AD (například povolit vícefaktorové ověřování pro zvýšení zabezpečení).

## <a name="the-management-plane-and-the-data-plane"></a>Rovina správy a rovina dat

Ke správě samotného trezoru klíčů pomocí roviny správy. To zahrnuje operace, jako je Správa atributů a nastavování zásad přístupu roviny dat. Rovina dat můžete přidat, odstranit, upravit a pomocí klíčů, tajných kódů a certifikátů uložených v Key Vault.

Přístup k rozhraní roviny roviny a data správy prostřednictvím různých koncových bodů, které jsou uvedeny v následující tabulce. Druhý sloupec v tabulce popisuje názvy DNS pro tyto koncové body v různých prostředích Azure. Třetí sloupec popisuje operace, které můžete z každé roviny přístupu provádět. Každá rovina přístupu má také vlastní mechanismus řízení přístupu. Řízení přístupu roviny správy byla nastavena pomocí Azure Resource Manageru řízení přístupu na základě role (RBAC). Řízení přístupu roviny dat byla nastavena pomocí zásad přístupu trezoru klíčů.

| Rovina přístupu | Koncové body přístupu | Operace | Mechanismus řízení přístupu |
| --- | --- | --- | --- |
| Rovina správy |**Globální:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 |Vytvoření, čtení, aktualizace nebo odstranění trezoru klíčů <br> Nastavení zásad přístupu pro Key Vault<br>Nastavit značky pro Key Vault |RBAC pro Azure Resource Manageru |
| Rovina dat |**Globální:**<br> &lt;název_trezoru&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;název_trezoru&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;název_trezoru&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;název_trezoru&gt;.vault.microsoftazure.de:443 |Pro klíče: Dešifrování, Encrypt, UnwrapKey, WrapKey, ověřte, podepsat, získání, seznam, aktualizovat, vytvořit, importovat, odstranit, zálohování, obnovení<br><br> Pro tajné klíče: Získání, seznam, nastavení, odstranit |Zásady přístupu trezoru klíčů |

Řízení přístupu roviny správy pro rovinu a data pracovat nezávisle. Například pokud chcete aplikaci udělit přístup k používání klíčů v trezoru klíčů, jenom musíte udělit přístup k rovině dat. Udělíte přístup pomocí zásady přístupu trezoru klíčů. Uživatel, který potřebuje číst vlastnosti služby Key Vault a značky, ale ne přístup k datům (klíče, tajné kódy nebo certifikáty), a naopak, potřebuje pouze přístup k rovině správy. Udělte přístup přiřazením oprávnění ke čtení pro uživatele pomocí RBAC.

## <a name="management-plane-access-control"></a>Řízení přístupu roviny správy

Rovina správy se skládá z operací, které mají vliv samotná služba key vault, jako například:

- Vytvoření nebo odstranění trezoru klíčů.
- Načítá se seznam trezorů v předplatném.
- Načítání vlastností služby Key Vault (například SKU a značky).
- Zásady přístupu trezoru klíčů nastavení, které ovládací prvek uživatelů a aplikací přístup ke klíčům a tajným klíčům.

Řízení přístupu roviny správy používá RBAC.  

### <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)

Každé předplatné Azure má instanci služby Azure AD. Udělit přístup k uživatelům, skupinám a aplikacím z tohoto adresáře ke správě prostředků v předplatném Azure, které používají model nasazení Azure Resource Manageru. Tento typ řízení přístupu se označuje jako RBAC. Ke správě tohoto přístupu můžete používat [Azure Portal](https://portal.azure.com/), [nástroje Azure CLI](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) nebo [rozhraní Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Vytvoření trezoru klíčů ve skupině prostředků a řízení přístupu k rovině správy pomocí služby Azure AD. Můžete například udělit uživatele nebo skupinu schopnost spravovat trezory klíčů ve skupině prostředků.

Můžete udělit přístup uživatelům, skupinám a aplikacím v konkrétním oboru přiřazením odpovídajících rolí RBAC. Například pro udělení přístupu uživatelům ke správě trezorů klíčů, přiřadíte předdefinovanou roli Přispěvatel Key Vault s tímto uživatelem, v konkrétním oboru. Rozsah v tomto případě by předplatné, skupinu prostředků nebo určitý trezor klíčů. Role přiřazená na úrovni předplatného se vztahuje na všechny skupiny prostředků a prostředky v tomto předplatném. Role přiřazená na úrovni skupiny prostředků se vztahuje na všechny prostředky v příslušné skupině prostředků. Role přiřazená pro určitý prostředek se vztahuje na tento prostředek. Existuje několik předdefinovaných rolí (viz [RBAC: Předdefinované role](../role-based-access-control/built-in-roles.md)). Pokud předdefinované role nevejde vašim potřebám, můžete definovat vlastní role.

> [!IMPORTANT]
> Všimněte si, že pokud má uživatel Přispěvatel udělit oprávnění k rovině správy trezoru klíčů, může sám přístup k rovině dat pomocí nastavení zásad přístupu trezoru klíčů. Proto by měl důsledně řídit, kdo má přístup přispěvatele k vašim trezorům klíčů. Ujistěte se, že jen autorizované osoby můžete přístup k a spravovat trezory klíčů, klíče, tajné kódy a certifikáty.
>

## <a name="data-plane-access-control"></a>Řízení přístupu roviny dat

Operace roviny dat služby Key Vault se vztahují na uložených objektů, jako jsou klíče, tajné kódy a certifikáty. Zahrnout klíčové operace vytvoření, import, aktualizovat, výpis, zálohování a obnovení klíče. Kryptografické operace použijte symbol, ověření, šifrování, dešifrování, zabalení, rozbalení, nastavit značky a nastavit dalších atributů pro klíče. Podobně patří operace s tajnými kódy get, set, seznam, odstranit.

Udělit přístup k rovině dat tím, že nastavíte zásady přístupu pro trezor klíčů. Uživatele, skupiny nebo aplikace musí mít oprávnění přispěvatele pro rovinu správy trezoru klíčů, aby mohla nastavovat zásady přístupu pro tento trezor klíčů. Uživatele, skupinu nebo aplikaci přístup k provádění konkrétních operací pro klíče nebo tajné kódy ve službě key vault můžete udělit. Key Vault podporuje až 1 024 položky zásad přístupu pro trezor klíčů. Udělit několika uživatelům přístup k rovině dat, vytvoření skupiny zabezpečení služby Azure AD a přidání uživatelů do této skupiny.

### <a name="key-vault-access-policies"></a>Zásady přístupu trezoru klíčů

Zásady přístupu trezoru klíčů udělují odděleně oprávnění pro klíče, tajné kódy a certifikáty. Například můžete umožnit uživateli přístup pouze ke klíčům a žádné oprávnění k tajným klíčům. Oprávnění pro přístup k klíče, tajné kódy a certifikáty jsou na úrovni trezoru. Zásady přístupu trezoru klíčů nepodporuje granulární oprávnění na úrovni objektů, například konkrétní klíč, tajný klíč nebo certifikát. Můžete použít [webu Azure portal](https://portal.azure.com/), [nástroje rozhraní příkazového řádku Azure](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), nebo [rozhraní REST API správy Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx) k nastavení zásad přístupu pro trezor klíčů.

> [!IMPORTANT]
> Zásady přístupu trezoru klíčů použít na úrovni trezoru. Když má například uživatel oprávnění vytvářet a odstraňovat klíče, může tyto operace provádět na všech klíčích v daném trezoru klíčů.

Kromě použití zásady přístupu, můžete taky omezit přístup k rovině dat pomocí [koncové body služeb virtuální sítě pro Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Nakonfigurujete [brány firewall a pravidla virtuální sítě](key-vault-network-security.md) pro další vrstvu zabezpečení.

## <a name="example"></a>Příklad:

Řekněme, že vyvíjíte aplikaci, která používá certifikát pro protokol SSL, Azure Storage k ukládání dat a klíč RSA 2048 bitů pro podpisové operace. Řekněme, že tato aplikace běží ve virtuálním počítači Azure (nebo škálovací sady virtuálních počítačů). Můžete použít trezor klíčů k uložení všech tajných kódů aplikace a uložení zaváděcího certifikátu používaného aplikací k ověření ve službě Azure AD.

Zde je uveden seznam typy klíče a tajné kódy uložené:

* **Certifikát SSL**: Používá se pro protokol SSL.
* **Klíč úložiště**: Použít k získání přístupu k účtu úložiště.
* **2048bitový klíč RSA**: Použít pro podpisové operace.
* **Zaváděcího certifikátu**: Používá k ověřování pomocí Azure AD. Po udělení přístupu můžete načíst klíč úložiště a použije klíč RSA k podpisu.

Teď Pojďme na lidi, kteří jsou ve správě, nasazení a auditovat tuto aplikaci. V tomto příkladu použijeme tři role.

* **Bezpečnostní tým**: Obvykle pracovníci IT z kanceláře CSO (Chief Security Officer) nebo ekvivalentní. Tento tým je zodpovědná za řádné a zabezpečené ukládání tajných klíčů. Mezi příklady patří certifikáty SSL, klíčů RSA pro podpisové operace, připojovacích řetězců a klíče účtu úložiště.
* **Vývojářům a operátorům**: Lidé, kteří aplikace a pak ho nasadíme v Azure. Obvykle nejsou součástí bezpečnostního týmu, a proto by neměl mít přístup k citlivým datům, jako jsou certifikáty SSL a klíče RSA. Jimi nasazovaná aplikace by měl mít přístup k těmto objektům.
* **Auditoři**: Obvykle jinou sadu lidí, izolovaná od vývojářů i obecných pracovníků IT. Jejich odpovědností je Zkontrolujte použití a správa certifikátů, klíčů a tajných kódů, aby se zajistila shoda se standardy zabezpečení. 

Existuje jeden další role, která je mimo obor této aplikace, ale relevantní tu zmínit. jde. Tato role je předplatné (nebo skupinu prostředků) správce. Správce předplatného nastavuje počáteční přístupová oprávnění pro bezpečnostní tým. Správce předplatného uděluje přístup k bezpečnostnímu týmu pomocí skupiny prostředků obsahující prostředky, které tato aplikace vyžaduje.

Teď se podívejme, jaké akce každá role v kontextu aplikace provádí.

* **Bezpečnostní tým**
  * Vytvoří trezorům klíčů.
  * Zapnutí protokolování v Key Vault.
  * Přidá klíči nebo tajnými kódy.
  * Vytvoří zálohy klíčů pro zotavení po havárii.
  * Nastaví zásad přístupu trezoru klíčů pro udělení oprávnění pro uživatele nebo aplikace, abyste prováděli konkrétní operace.
  * Pravidelně postupně klíči nebo tajnými kódy.
* **Vývojáři/operátoři**
  * Získání odkazů na spuštění a certifikáty SSL (kryptografické otisky), klíč úložiště (URI tajného klíče) a podpisový klíč (URI) od bezpečnostního týmu.
  * Vyvíjejte a nasazujte aplikace pro přístup ke klíčům a tajným klíčům prostřednictvím kódu programu.
* **Auditoři**
  * Kontrola protokolů použití potvrďte správné použití klíče/tajné klíče a dodržování standardů zabezpečení dat

Nyní podívejme, jaká přístupová oprávnění jsou vyžadována pro každou roli a aplikace na jejich přiřazené úkoly. 

| Role uživatele | Oprávnění k rovině správy | Oprávnění k rovině dat |
| --- | --- | --- |
| Bezpečnostní tým |Přispěvatel Key Vaultu |Klíče: zálohovat (backup), vytvořit (create), odstranit (delete), získat (get), importovat (import), vypsat (list), obnovit (restore) <br> Tajné klíče: vše |
| Vývojářům a operátorům |Key Vault nasazení oprávnění, aby virtuální počítače, které nasazují, mohl načítat tajné klíče z trezoru klíčů. |Žádný |
| Auditoři |Žádný |Klíče: vypsat (list)<br>Tajné klíče: vypsat (list) |
| Aplikace |Žádný |Klíče: podepsat (sign)<br>Tajné klíče: získat (get) |

> [!NOTE]
> Auditoři potřebují oprávnění seznamu klíčů a tajných klíčů, takže se můžete kontrolu i těch atributů klíčů a tajných kódů, které nejsou zaznamenávány do protokolů. Tyto atributy zahrnout značky, aktivace a data vypršení platnosti.
> 
> 

Kromě oprávnění služby Key Vault, všechny tři role potřebovat přístup k dalším prostředkům. Například aby bylo možné nasadit virtuální počítače (nebo funkci Web Apps služby Azure App Service), vývojářů a operátorů potřebovat přístup přispěvatele k těmto typům prostředků. Auditoři potřebují přístup pro čtení k účtu úložiště, kde se ukládají protokoly trezoru klíčů.

Protože hlavním cílem tohoto článku je zabezpečení přístupu k trezoru klíčů, jsme pouze objasnění konceptů vztahující se k tomuto tématu. Podrobnosti ohledně nasazení certifikátů a přístup ke klíčům a tajným klíčům prostřednictvím kódu programu najdete jinde. Příklad:

- Nasazením certifikátů uložených v Key Vault a virtuální počítače se věnuje [nasazení certifikátů do virtuálních počítačů z trezoru klíčů spravovaných zákazníkem](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (příspěvek na blogu).
- [Stáhnout ukázky klienta služby Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) ilustruje použití zaváděcího certifikátu pro ověření do služby Azure AD pro přístup k trezoru klíčů.

Většinu přístupových oprávnění lze udělit pomocí webu Azure portal. Chcete-li udělovat různě odstupňovaná oprávnění, můžete potřebovat pomocí Azure Powershellu nebo rozhraní příkazového řádku k dosažení požadovaného výsledku. 

Následující fragmenty kódu PowerShellu předpokládají:

* Skupiny zabezpečení, které reprezentují uvedené tři role (Contoso Security Team, Contoso App Devops a Contoso App Auditors) vytvořil správce Azure AD. Správce již také přidal uživatele do skupin, do kterých patří.
* **ContosoAppRG** je skupina prostředků, ve které jsou všechny tyto prostředky. **contosologstorage** je úložiště, kam se ukládají protokoly. 
* Trezor klíčů **ContosoKeyVault**a účet úložiště používaný pro protokoly Key Vault **contosologstorage**, musí být ve stejném umístění Azure.

Nejprve správce předplatného přiřadí `key vault Contributor` a `User Access Administrator` bezpečnostnímu týmu role. Pracovníci v těchto rolích povolit bezpečnostnímu týmu spravovat přístup k dalším prostředkům a spravovat trezory klíčů ve skupině prostředků ContosoAppRG.

```PowerShell
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Tento skript je ukázkou, jak může bezpečnostní tým vytvořit trezor klíčů a nastavit oprávnění přístupu a protokolování. Podrobnosti o oprávnění zásad přístupu trezoru klíčů najdete v tématu [o službě Azure Key Vault klíče, tajné kódy a certifikáty](about-keys-secrets-and-certificates.md).

```PowerShell
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Definovanou vlastní roli je možné přiřadit k předplatnému, jenom Pokud `ContosoAppRG` se vytvoří skupina prostředků. Pokud budou stejné vlastní role se použije pro jiné projekty v jiných předplatných, může mít svého oboru přidat další předplatná.

Přiřazení vlastní role pro oprávnění "deploy/action" pro vývojáře a operátory je vymezeny na skupinu prostředků. To umožňuje pouze virtuální počítače vytvořené ve skupině prostředků `ContosoAppRG` mít přístup k tajným kódům (certifikát SSL a zaváděcí certifikát). Virtuální počítače vytvořené v jiné skupině prostředků vývojáři a člen týmu operátory nebudete mít přístup k těmto tajným kódům i v případě, že mají URI tajných.

Tento příklad ukazuje jednoduchý scénář. Reálné scénáře v praxi může být složitější, a můžete upravit oprávnění k trezoru klíčů na základě vašich potřeb. V našem příkladu předpokládáme, že bezpečnostní tým poskytuje klíče a tajného kódu odkazy (identifikátory URI a kryptografické otisky), které vývojáři a operátoři potřebují k odkazování ve svých aplikacích. Vývojáři a operátoři nevyžadují žádné přístup k rovině dat. V tomto příkladu se zaměřuje na zabezpečení trezoru klíčů. By měl poskytují obdobnou zabezpečit [vaše virtuální počítače](https://azure.microsoft.com/services/virtual-machines/security/), [účty úložiště](../storage/common/storage-security-guide.md)a dalších prostředků Azure.

> [!NOTE]
> Tento příklad ukazuje, jak bude přístup k trezoru klíčů uzamčen v produkčním prostředí. Vývojáři by měl mít své vlastní předplatné nebo skupinu prostředků ve kterých budou mít úplná oprávnění ke správě svých trezorů, virtuálních počítačů a účet úložiště vývoji aplikace.

Důrazně doporučujeme další zabezpečený přístup k vašemu trezoru klíčů pomocí [konfigurace virtuálních sítí a bran firewall služby Key Vault](key-vault-network-security.md).

## <a name="resources"></a>Zdroje a prostředky

* [Řízení přístupu na základě role Azure Active Directory](../role-based-access-control/role-assignments-portal.md)
  
* [RBAC: Vestavěné role](../role-based-access-control/built-in-roles.md)
  
* [Principy nasazení podle modelu Resource Manager a klasického nasazení](../azure-resource-manager/resource-manager-deployment-model.md)
  
* [Správa řízení přístupu na základě role pomocí Azure Powershellu](../role-based-access-control/role-assignments-powershell.md)
  
* [Správa řízení přístupu na základě role pomocí rozhraní REST API](../role-based-access-control/role-assignments-rest.md)
  
* [Řízení přístupu na základě rolí pro Microsoft Azure z Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Tato 2015 popisuje video konference Microsoft Ignite správy přístupu a možností vytváření sestav v Azure. Také popisuje osvědčené postupy pro zabezpečení přístupu k předplatným Azure pomocí Azure AD.
* [Autorizace přístupu k webovým aplikacím pomocí OAuth 2.0 a Azure AD](../active-directory/develop/v1-protocols-oauth-code.md)
  
* [Rozhraní API REST pro správu služby Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Tento dokument slouží jako reference k rozhraní REST API pro správu trezoru klíčů prostřednictvím kódu programu, včetně nastavení zásad přístupu trezoru klíčů.
* [Rozhraní REST API služby Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
* [Řízení přístupu ke klíčům](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Řízení přístupu k tajným klíčům](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* [Nastavte](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy) a [odebrat](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Remove-AzureRmKeyVaultAccessPolicy) zásad přístupu trezoru klíčů pomocí Powershellu
  
## <a name="next-steps"></a>Další postup

[Konfigurace bran firewall a virtuálních sítí pro Key Vault](key-vault-network-security.md)

Úvodní kurz pro správce najdete v tématu [Začínáme s Azure Key Vault](key-vault-get-started.md).

Další informace o protokolování využití služby Key Vault najdete v tématu [Protokolování v Azure Key Vault](key-vault-logging.md).

Další informace o používání klíčů a tajných klíčů se službou Azure Key Vault najdete v tématu [informace o klíčích a tajných kódů](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Pokud máte dotazy týkající se služby Key Vault, navštivte [fóra](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

