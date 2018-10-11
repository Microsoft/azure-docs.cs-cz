---
title: Zabezpečení trezoru klíčů Azure | Dokumentace Microsoftu
description: Spravujte přístupová oprávnění pro key vault pro správu služby Azure Key Vault, klíče a tajné kódy. Model ověřování a autorizace pro trezor klíčů a postupy zabezpečení trezoru klíčů.
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
ms.date: 10/09/2018
ms.author: ambapat
ms.openlocfilehash: 58307b25c03202fea63be25136c5a9c5a2d32473
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079034"
---
# <a name="secure-your-key-vault"></a>Zabezpečení trezoru klíčů
Služba Azure Key Vault je Cloudová služba, která chrání šifrovací klíče a tajné kódy (například certifikáty, připojovací řetězce, hesla). Protože tato data jsou citlivá a pro důležité obchodní informace, musí být zabezpečené přístup k vašim trezorům klíčů, umožňuje pouze oprávnění aplikace a uživatelé získat přístup. 

Tento článek obsahuje přehled modelu přístupu trezoru klíčů. Vysvětluje ověření a autorizaci a popisuje, jak zabezpečit přístup k trezoru klíčů.

## <a name="overview"></a>Přehled
Přístup k trezoru klíčů je řízen prostřednictvím dvou oddělených rozhraní: rovina správy a rovina dat. Pro obě roviny řádné ověření a autorizace jsou požadovány, než může volající (uživatel nebo aplikace) získat přístup k trezoru klíčů. Ověření určí identitu volajícího a autorizace následně určí operace, které daný volající povoleno provádět.

K ověření využívají rovina správy i rovina dat službu Azure Active Directory. K autorizaci ale rovina správy používá řízení přístupu podle role (RBAC), zatímco rovina dat používá zásady přístupu trezoru klíčů.

Stručný přehled tímto článkem pokrytých témat:

[Ověření s použitím Azure Active Directory](#authentication-using-azure-active-directory): Tato část vysvětluje, jak se volající ověřuje ve službě Azure Active Directory za účelem přístupu k trezoru klíčů prostřednictvím roviny správy i roviny dat. 

[Rovina správy a rovina dat](#management-plane-and-data-plane): Rovina správy a rovina dat jsou dvě roviny přístupu, které se používají pro přístup k vašemu trezoru klíčů. Každá rovina podporuje určité operace. Tato část popisuje koncové body přístupu, podporované operace a metody řízení přístupu používané těmito rovinami. 

[Řízení přístupu roviny správy](#management-plane-access-control): V této části se podíváme na povolení přístupu k operacím roviny správy s použitím řízení přístupu podle role.

[Řízení přístupu roviny dat](#data-plane-access-control): Tato část popisuje, jak použít zásady přístupu trezoru klíčů k řízení přístupu k rovině dat.

[Příklad](#example) – tento příklad popisuje, jak nastavit řízení přístupu k trezoru klíčů na umožnili třem různým týmům (bezpečnostní tým vývojářů a operátorů a auditoři) provádět konkrétní úkoly za účelem vývoje, správy a monitorování aplikace v Azure.

## <a name="authentication-using-azure-active-directory"></a>Ověření pomocí služby Azure Active Directory
Když vytvoříte trezor klíčů v předplatném Azure, automaticky se přidruží k tenantovi Azure Active Directory předplatného. Všichni volající (uživatelé a aplikace) musí být v tomto tenantovi registrováni a musí ověřit přístup k trezoru klíčů. Tento požadavek platí pro obě roviny a data přístup k rovině správy. V obou případech může aplikace přistupovat k trezoru klíčů dvěma způsoby:

* **přístup uživatele a aplikace** – používá se s aplikacemi, které přistupují k trezoru klíčů jménem přihlášeného uživatele. Prostředí Azure PowerShell a webu Azure portal jsou příkladem tento typ přístupu. Existují dva způsoby, jak udělit přístup uživatelům: 
- Udělit přístup uživatelům, aby mohli získat přístup trezoru klíčů z libovolné aplikace.
- Udělte uživatelům přístup k trezoru klíčů jen při použití konkrétní aplikace (označováno jako složená identita).

* **přístup jen pro aplikace** – používá se u aplikací, které spustit jako služby démonů, nebo úlohy na pozadí. Přístup k trezoru klíčů je udělen identitě aplikace.

U obou typů aplikací s aplikace nejprve ověří ve službě Azure Active Directory s použitím libovolné [podporované metody ověření](../active-directory/develop/authentication-scenarios.md) a získá token. Použitá metoda ověření závisí na typu aplikace. Aplikace pak tento token použije a odešle požadavek REST API na trezor klíčů. Požadavky rovině správy jsou směrovány přes koncový bod Azure Resource Manageru. Při přístupu k rovině dat aplikace komunikují přímo s koncovým bodem trezoru klíčů. Další podrobnosti najdete v [úplném diagramu procesu ověření](../active-directory/develop/v1-protocols-oauth-code.md). 

Název prostředku, pro který aplikace žádá token, je různý podle toho, jestli aplikace přistupuje k rovině správy nebo rovině dat. Název prostředku je tak buď koncový bod roviny správy, nebo koncový bod roviny dat, jak je popsáno v tabulce níže, v závislosti na prostředí Azure.

Používání jediného mechanismu ověřování pro rovinu správy i rovinu dat má svoje výhody:

* Organizace mohou centrálně řídit přístup ke všem trezorům klíčů v organizaci.
* Když uživatel organizaci opustí, ztratí okamžitě přístup ke všem trezorům klíčů v organizaci.
* Organizace si mohou ověření přizpůsobit prostřednictvím možností v Azure Active Directory (mohou například povolit vícefaktorové ověřování pro vyšší bezpečnost).

## <a name="management-plane-and-data-plane"></a>Rovina správy a rovina dat
Azure Key Vault je služba Azure k dispozici prostřednictvím modelu nasazení Azure Resource Manageru. Když vytvoříte trezor klíčů, získáte virtuální kontejner pro ukládání citlivých objektů, jako jsou klíče, tajné kódy a certifikáty. Konkrétní operace provádějí v trezoru klíčů pomocí roviny rozhraní roviny a data správy. Rovina správy se používá ke správě samotná služba key vault. To zahrnuje operace, jako je Správa atributů a nastavování zásad přístupu roviny dat. Rozhraní roviny dat umožňuje přidat, odstranit, upravit a použití klíčů, tajných kódů a certifikátů uložených v trezoru klíčů.

Rozhraní roviny roviny a data správy se přistupuje prostřednictvím různých koncových bodů uvedených níže. Druhý sloupec popisuje názvy DNS pro tyto koncové body v různých prostředích Azure. Třetí sloupec popisuje operace, které můžete z každé roviny přístupu provádět. Každá rovina přístupu má také vlastní mechanismus řízení přístupu. Řízení přístupu roviny správy se nastavuje pomocí služby Řízení přístupu (RBAC). Řízení přístupu roviny dat se nastavuje pomocí zásady přístupu trezoru klíčů.

| Rovina přístupu | Koncové body přístupu | Operace | Mechanismus řízení přístupu |
| --- | --- | --- | --- |
| Rovina správy |**Globální:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 |Vytvořit (create), číst (read), aktualizovat (update), odstranit (delete) trezor klíčů <br> Nastavit zásady přístupu pro trezor klíčů<br>Nastavit značky pro trezor klíčů |Řízení přístupu podle role (RBAC) služby Azure Resource Manager |
| Rovina dat |**Globální:**<br> &lt;název_trezoru&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;název_trezoru&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;název_trezoru&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;název_trezoru&gt;.vault.microsoftazure.de:443 |Pro klíče: dešifrovat (decrypt), zašifrovat (encrypt), rozbalit (UnwrapKey), zabalit (WrapKey), ověřit (verify), podepsat (sign), získat (get), vypsat (list), aktualizovat (update), vytvořit (create), importovat (import), odstranit (delete), zálohovat (backup), obnovit (restore)<br><br> Pro tajné klíče: získat (get), vypsat (list), nastavit (set), odstranit (delete) |Zásady přístupu trezoru klíčů |

Řízení přístupu roviny správy pro rovinu a data pracovat nezávisle. Například pokud chcete aplikaci udělit přístup k používání klíčů v trezoru klíčů, jenom musíte udělit přístup k rovině dat. Pomocí zásady přístupu trezoru klíčů je udělen přístup. Uživatel, který potřebuje číst vlastnosti trezoru a značky, ale ne přístup k datům (klíče, tajné kódy nebo certifikáty), a naopak, potřebuje pouze přístup k rovině řízení. Přístup uděluje přiřazením "přístup pro čtení' pro uživatele pomocí RBAC.

## <a name="management-plane-access-control"></a>Řízení přístupu roviny správy
Rovina správy se skládá z operací, které mají vliv samotná služba key vault, jako například:

- Vytvoření nebo odstranění trezoru klíčů.
- Načítá se seznam trezorů v předplatném.
- Načítání vlastností služby key vault (například SKU, značky).
- Nastavení zásady přístupu trezoru klíčů, které přistupují k řízení uživatelů a aplikací na klíče a tajné kódy.

Řízení přístupu roviny správy používá RBAC. Zobrazit úplný seznam operací trezoru klíčů, které je možné provádět prostřednictvím roviny správy, v tabulce v předchozí části. 

### <a name="role-based-access-control-rbac"></a>Řízení přístupu podle role (RBAC)
Každé předplatné Azure zahrnuje službu (adresář) Azure Active Directory. Uživatelům, skupinám a aplikacím z tohoto adresáře lze udělit přístup ke správě prostředků v tomto předplatném Azure, které používají model nasazení Azure Resource Manager. Tento typ řízení přístupu je označován řízení přístupu podle role (RBAC). Ke správě tohoto přístupu můžete používat [Azure Portal](https://portal.azure.com/), [nástroje Azure CLI](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) nebo [rozhraní Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Vytvoření trezoru klíčů v prostředku skupiny a řízení přístupu k rovině správy pomocí služby Azure Active Directory. Můžete například udělit uživatele nebo skupinu schopnost spravovat trezory klíčů ve skupině prostředků.

Můžete udělit přístup uživatelům, skupinám a aplikacím v konkrétním oboru přiřazením odpovídajících rolí RBAC. Například pro udělení přístupu uživatelům ke správě trezorů klíčů, přiřadíte předdefinovanou roli "Přispěvatel trezoru klíč" tomuto uživateli v konkrétním oboru. Rozsah v tomto případě by předplatné, skupinu prostředků nebo určitý trezor klíčů. Role přiřazená na úrovni předplatného se bude vztahovat na všechny skupiny prostředků a prostředky v tomto předplatném. Role přiřazená na úrovni skupiny prostředků se bude vztahovat na všechny prostředky v této skupině. Role přiřazená pro určitý prostředek se vztahuje na tento prostředek. Existuje několik předdefinovaných rolí (viz [RBAC: vestavěné role](../role-based-access-control/built-in-roles.md)). Pokud není předdefinovanou roli podle vašich potřeb můžete definovat vlastní role.

> [!IMPORTANT]
> Všimněte si, že pokud uživatel má oprávnění role Přispěvatel (RBAC) k rovině správy trezoru klíčů, může sám sobě udělit i přístup k rovině dat – může totiž nastavovat zásady přístupu trezoru klíčů, které řídí přístup k rovině dat. Doporučujeme proto důsledně kontrolovat, kdo má k vašim trezorům klíčů přístup jako Přispěvatel, aby bylo zajištěno, že jen autorizované osoby budou mít přístup a budou moci spravovat vaše trezory klíčů, klíče, tajné klíče a certifikáty.
> 
> 

## <a name="data-plane-access-control"></a>Řízení přístupu roviny dat
Operace roviny dat trezoru klíčů se vztahují na uložených objektů, jako jsou klíče, tajné kódy a certifikáty. Zahrnout klíčové operace vytvoření, import, aktualizovat, výpis, zálohování a obnovení klíče. Kryptografické operace použijte symbol, ověření, šifrování, dešifrování, zabalení, rozbalení, nastavení značek a dalších atributů pro klíče. Podobně patří operace s tajnými kódy get, set, seznam, odstranit.

Přístup k rovině dat je udělován nastavením zásad přístupu pro trezor klíčů. Uživatel, skupina nebo aplikace musí mít oprávnění role Přispěvatel (RBAC) pro rovinu správy trezoru klíčů, aby mohla nastavovat zásady pro tento trezor klíčů. Uživateli, skupině nebo aplikaci lze udělit přístup k provádění konkrétních operací pro klíče nebo tajné klíče v trezoru klíčů. Trezor klíčů podporuje až 1 024 položek zásad přístupu na jeden trezor klíčů. Vytvořte skupinu zabezpečení Azure Active Directory a do této skupiny přidejte uživatele, kterým chcete udělit přístup k rovině dat trezoru klíčů.

### <a name="key-vault-access-policies"></a>Zásady přístupu trezoru klíčů
Zásady přístupu trezoru klíčů udělují odděleně oprávnění pro klíče, tajné kódy a certifikáty. Například můžete umožnit uživateli přístup pouze ke klíčům a žádné oprávnění k tajným klíčům. Oprávnění pro přístup ke klíči nebo tajnými kódy nebo certifikáty jsou na úrovni trezoru. Zásady přístupu trezoru klíčů nepodporuje oprávnění na úrovni detailní objektu, jako je například určitý klíč/tajný klíč/certifikát. K nastavení zásad přístupu pro trezor klíčů můžete použít [Azure Portal](https://portal.azure.com/), [nástroje Azure CLI](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) nebo [rozhraní REST API správy trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Zásady přístupu trezoru klíčů se aplikuj a úrovni trezoru. Když má například uživatel oprávnění vytvářet a odstraňovat klíče, může tyto operace provádět na všech klíčích v daném trezoru klíčů.

Kromě zásad přístupu můžete přístup k rovině dat omezit [koncovými body služby Virtual Network pro Azure Key Vault](key-vault-overview-vnet-service-endpoints.md) tím, že nakonfigurujete [brány firewall a pravidla virtuální sítě](key-vault-network-security.md) a tím přidáte další vrstvu zabezpečení.

## <a name="example"></a>Příklad:
Řekněme, že vyvíjíte aplikaci, která používá certifikát pro protokol SSL, Azure storage k ukládání dat a klíč RSA 2048 bitů pro podpisové operace. Řekněme, že tato aplikace běží ve virtuálním počítači Azure (nebo Škálovací sady virtuálních počítačů). Můžete použít trezor klíčů k uložení všech tajných kódů aplikace a uložení zaváděcího certifikátu používaného aplikací k ověření pomocí Azure Active Directory.

Zde je uveden seznam typy klíčů a tajných klíčů uložených v trezoru klíčů:

* **Certifikát SSL** – používá se pro protokol SSL
* **Klíč úložiště** – slouží k získání přístupu k účtu úložiště
* **2048bitový klíč RSA** – používá se pro podpisové operace
* **Zaváděcího certifikátu** – slouží k ověřování pomocí Azure Active Directory. Po udělení přístupu můžete načíst klíč úložiště a použije klíč RSA k podpisu.

Teď Pojďme na lidi, kteří jsou ve správě, nasazení a auditovat tuto aplikaci. V tomto příkladu použijeme tři role.

* **Bezpečnostní tým** – obvykle pracovníky z kanceláře CSO (Chief Security Officer) nebo ekvivalentní. Tento tým je zodpovědná za řádné a zabezpečené ukládání tajných klíčů. Například certifikátů SSL, klíčů RSA pro podpisové operace, připojovacích řetězců a klíče účtu úložiště.
* **Vývojářům a operátorům** -lidé, kteří aplikace a pak ho nasadíme v Azure. Obvykle nejsou součástí bezpečnostního týmu, a proto by neměl mít přístup k citlivým datům, jako jsou certifikáty SSL a klíče RSA. Jimi nasazovaná aplikace by měl mít přístup k těmto objektům.
* **Auditoři** – obvykle odlišná skupina lidí, izolovaná od vývojářů i obecných pracovníků IT. Jejich odpovědností je Zkontrolujte použití a správa certifikátů, klíčů a tajných kódů, aby se zajistila shoda se standardy zabezpečení. 

Existuje jeden další role, která je mimo obor této aplikace, ale relevantní tu zmínit. jde. Tato role je předplatné (nebo skupinu prostředků) správce. Správce předplatného nastavuje počáteční přístupová oprávnění pro bezpečnostní tým. Správce předplatného uděluje přístup k bezpečnostnímu týmu pomocí skupiny prostředků obsahující prostředky, které tato aplikace vyžaduje.

Teď se podívejme, jaké akce každá role v kontextu aplikace provádí.

* **Bezpečnostní tým**
  * Vytváření trezorů klíčů
  * Zapnutí protokolování trezoru klíčů
  * Přidání klíčů / tajných klíčů
  * Vytvoření zálohy klíčů pro zotavení po havárii
  * Nastavení zásad přístupu trezoru klíčů, aby se uživatelům a aplikacím udělila oprávnění provádět konkrétní operace
  * Pravidelná obměna klíčů / tajných klíčů
* **Vývojáři/operátoři**
  * Získání odkazů na spuštění a SSL certifikátů (kryptografické otisky), klíč úložiště (URI tajného klíče) a podpisový klíč (URI klíče) od bezpečnostního týmu
  * Vývoj a nasazení aplikace, která přistupuje ke klíčům a tajným klíčům prostřednictvím programového kódu
* **Auditoři**
  * Kontrola protokolů použití, aby bylo možné potvrdit, že se klíče / tajné klíče používají řádně a jsou plněny standardy zabezpečení dat

Nyní podívejme, jaká přístupová oprávnění jsou vyžadována pro každou roli a aplikace na jejich přiřazené úkoly. 

| Role uživatele | Oprávnění k rovině správy | Oprávnění k rovině dat |
| --- | --- | --- |
| Bezpečnostní tým |Přispěvatel trezoru klíčů |Klíče: zálohovat (backup), vytvořit (create), odstranit (delete), získat (get), importovat (import), vypsat (list), obnovit (restore) <br> Tajné klíče: vše |
| Vývojáři/operátoři |Oprávnění k nasazení trezoru klíčů, aby virtuální počítač, který nasazují, mohl načítat tajné klíče z trezoru klíčů |Žádný |
| Auditoři |Žádný |Klíče: vypsat (list)<br>Tajné klíče: vypsat (list) |
| Aplikace |Žádný |Klíče: podepsat (sign)<br>Tajné klíče: získat (get) |

> [!NOTE]
> Auditoři potřebují oprávnění vypsat seznam pro klíče a tajné klíče, aby mohli provádět kontrolu i těch atributů klíčů a tajných klíčů, které nejsou zaznamenávány do protokolů, jako jsou například značky, aktivace a data vypršení platnosti.
> 
> 

Kromě oprávnění služby key vault, všechny tři role potřebovat přístup k dalším prostředkům. Například vývojáři a operátoři potřebují nasazovat virtuální počítače (nebo webové aplikace a podobně) a budou tedy potřebovat přístup úrovně Přispěvatel i k těmto typům prostředků. Auditoři potřebují "čtení" přístup k účtu úložiště ve kterém se ukládají protokoly trezoru klíčů.

Protože hlavním cílem tohoto článku je zabezpečení přístupu k trezoru klíčů, jsme pouze objasnění konceptů vztahující se k tomuto tématu. Podrobnosti ohledně nasazení certifikátů, přístup ke klíčům a tajným klíčům prostřednictvím kódu programu a další, jsou zahrnuté jinde. Příklad:

- Nasazením certifikátů uložených v trezoru klíčů, aby virtuální počítače se věnuje [blogový příspěvek: Nasazení certifikátů do virtuálních počítačů z trezoru klíčů spravovaných zákazníkem](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)
- [Stáhnout ukázky klienta služby Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) ilustruje použití zaváděcího certifikátu pro ověření do služby Azure AD pro přístup k trezoru klíčů.

Většinu přístupových oprávnění lze udělit prostřednictvím webu Azure portal. Chcete-li udělovat různě odstupňovaná oprávnění, budete muset pomocí Azure Powershellu nebo rozhraní příkazového řádku k dosažení požadovaného výsledku. 

Následující fragmenty kódu PowerShellu předpokládají:

* Správce Azure Active Directory vytvořil skupiny zabezpečení, které reprezentují uvedené tři role (Contoso Security Team, Contoso App Devops, Contoso App Auditors). Správce již také přidal uživatele do skupin, do kterých patří.
* **ContosoAppRG** je skupina prostředků, ve které jsou všechny tyto prostředky. **contosologstorage** je úložiště, kam se ukládají protokoly. 
* Trezor klíčů **ContosoKeyVault** a účet úložiště používaný pro protokoly trezoru klíčů **contosologstorage** musí být ve stejném umístění Azure

Nejprve správce předplatného přiřadí bezpečnostnímu týmu role „key vault Contributor“ a „User Access Administrator“. Pracovníci v těchto rolích povolit bezpečnostnímu týmu spravovat přístup k dalším prostředkům a spravovat trezory klíčů ve skupině prostředků ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Tento skript je ukázkou, jak může bezpečnostní tým vytvořit trezor klíčů a nastavit oprávnění přístupu a protokolování. Zobrazit [o službě Azure Key Vault klíče, tajné kódy a certifikáty](about-keys-secrets-and-certificates.md) podrobnosti o službě Key Vault přístup k oprávnění zásad.

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
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

Definovanou vlastní roli je možné přiřadit jenom v předplatném, ve kterém je vytvořená skupina prostředků ContosoAppRG. Pokud budou stejné vlastní role se použije pro jiné projekty v jiných předplatných, můžou mít svého oboru přidat další předplatná.

Přiřazení vlastní role pro oprávnění vývojářům a operátorům "deploy/action" je vymezeny na skupinu prostředků. To umožňuje pouze virtuální počítače vytvořené ve skupině prostředků "ContosoAppRG' Chcete-li mít přístup k tajným kódům (certifikát SSL a zaváděcí certifikát). Virtuální počítače vytvořené v jiné skupině prostředků podle členů týmu vývojářů a operátorů nebudete mít přístup k těmto tajným kódům i v případě, že mají skrytou identifikátorů URI.

Tento příklad ukazuje jednoduchý scénář. Reálné scénáře v praxi může být složitější, a může být potřeba upravit oprávnění k trezoru klíčů na základě vašich potřeb. V našem příkladu předpokládáme, že bezpečnostní tým poskytne klíč a tajný odkazy (identifikátory URI a kryptografické otisky), které vývojářů a operátorů potřebuje k odkazování ve svých aplikacích. Vývojářům a operátorům nevyžadují žádné přístup k rovině dat. V tomto příkladu se zaměřuje na zabezpečení trezoru klíčů. Obdobnou by měla být věnována zabezpečení [vaše virtuální počítače](https://azure.microsoft.com/services/virtual-machines/security/), [účty úložiště](../storage/common/storage-security-guide.md)a dalších prostředků Azure.

> [!NOTE]
> Poznámka: Tento příklad ukazuje, jak bude přístup k trezoru klíčů uzamčen v produkčním prostředí. Vývojáři by měli mít vedle toho vlastní předplatné nebo skupinu prostředků, ve kterých budou mít oprávnění k plné správě svých trezorů, virtuálních počítačů a účtů úložiště při vývoji aplikace.

Důrazně doporučujeme pro zabezpečený přístup k trezoru klíčů dále podle [konfigurace virtuálních sítí a bran firewall služby Key Vault](key-vault-network-security.md).

## <a name="resources"></a>Zdroje a prostředky
* [Řízení přístupu na základě role v Azure Active Directory](../role-based-access-control/role-assignments-portal.md)
  
  Tento článek popisuje řízení přístupu podle role v Azure Active Directory a vysvětluje, jak funguje.
* [RBAC: vestavěné role](../role-based-access-control/built-in-roles.md)
  
  Tento článek podrobně popisuje všechny vestavěné role dostupné v RBAC.
* [Principy nasazení podle modelu Resource Manager a klasického nasazení](../azure-resource-manager/resource-manager-deployment-model.md)
  
  Tento článek popisuje model nasazení Resource Manager ve srovnání s klasickým modelem nasazení a vysvětluje, jaké výhody přináší Resource Manager a skupiny prostředků.
* [Správa řízení přístupu na základě role pomocí Azure PowerShellu](../role-based-access-control/role-assignments-powershell.md)
  
  Tento článek vysvětluje, jak spravovat řízení přístupu podle role pomocí prostředí Azure PowerShell.
* [Správa řízení přístupu na základě role pomocí REST API](../role-based-access-control/role-assignments-rest.md)
  
  Tento článek popisuje, jak používat rozhraní REST API ke správě RBAC.
* [Řízení přístupu na základě role pro Microsoft Azure z Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Tato 2015 popisuje video konference Microsoft Ignite správy přístupu a možností vytváření sestav v Azure. Také popisuje osvědčené postupy pro zabezpečení přístupu k předplatným Azure pomocí Azure Active Directory.
* [Autorizace přístupu k webovým aplikacím s použitím OAuth 2.0 a Azure Active Directory](../active-directory/develop/v1-protocols-oauth-code.md)
  
  Tento článek popisuje úplný proces OAuth 2.0 pro ověřování v Azure Active Directory.
* [Rozhraní REST API správy trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Tento dokument slouží jako reference k rozhraním REST API na správu trezoru klíčů (včetně nastavení jeho zásad přístupu) prostřednictvím programového kódu.
* [Rozhraní REST API trezoru klíčů](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
  Odkaz na referenční dokumentaci k rozhraní API REST trezoru klíčů.
* [Řízení přístupu ke klíčům](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
  Odkaz na referenční dokumentaci pro řízení přístupu k tajným klíčům.
* [Řízení přístupu k tajným klíčům](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
  Odkaz na referenční dokumentaci pro řízení přístupu k tajným klíčům.
* [Nastavení](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy) a [odebrání](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Remove-AzureRmKeyVaultAccessPolicy) zásady přístupu trezoru klíčů pomocí PowerShellu
  
  Odkazy na referenční dokumentaci pro rutiny PowerShell na správu zásad přístupu trezoru klíčů.

## <a name="next-steps"></a>Další kroky
[Konfigurace bran firewall a virtuálních sítí pro Key Vault](key-vault-network-security.md)

Úvodní kurz pro správce najdete v tématu [Začínáme s Azure Key Vault](key-vault-get-started.md).

Další informace o protokolování využití trezoru klíčů najdete v tématu [Protokolování Azure Key Vault](key-vault-logging.md).

Další informace o používání klíčů a tajných klíčů se službou Azure Key Vault najdete v tématu [Informace o klíčích a tajných klíčích](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Pokud máte dotazy k trezorům klíčů, navštivte [fóra služby Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

