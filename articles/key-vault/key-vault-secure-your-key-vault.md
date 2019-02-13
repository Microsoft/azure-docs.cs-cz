---
title: Zabezpečený přístup k trezoru klíčů – Azure Key Vault | Dokumentace Microsoftu
description: Spravujte přístupová oprávnění pro Azure Key Vault, klíče a tajné kódy. Popisuje model ověřování a autorizace pro Key Vault a postupy zabezpečení trezoru klíčů.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 320a23e425ecb11e36af3efe988b25e598948132
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118509"
---
# <a name="secure-access-to-a-key-vault"></a>Zabezpečený přístup k trezoru klíčů

Služba Azure Key Vault je Cloudová služba, která chrání šifrovací klíče a tajné kódy jako hesla, certifikáty a připojovací řetězce. Protože tato data jsou citlivá a pro důležité obchodní informace, je potřeba zabezpečit přístup k vašim trezorům klíčů tím, že jen autorizované aplikace a uživatele. Tento článek obsahuje přehled modelu access Key Vault. Vysvětluje ověření a autorizaci a popisuje, jak zabezpečit přístup k vašim trezorům klíčů.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Přehled přístupu k modelu

Přístup k trezoru klíčů je řízen prostřednictvím dvou rozhraní: *roviny správy* a *rovina dat*. Rovina správy je, kde Správa služby Key Vault, samotného. Operace v této rovině zahrnovat vytváření a odstraňování trezorů klíčů, načítání vlastností služby Key Vault a aktualizují se zásady přístupu. Rovina dat je místo, kde můžete pracovat s daty uloženými ve službě key vault. Můžete přidat, odstranit a upravit klíče, tajné kódy a certifikáty.

Pro přístup k službě key vault v jedné rovině, musí mít všichni volající (uživatelé a aplikace) řádné ověření a autorizace. Ověření určí identitu volajícího. Autorizace následně určí, které operace má volající může spustit. 

Obě roviny pomocí Azure Active Directory (Azure AD) pro ověřování. K autorizaci ale rovina správy používá řízení přístupu na základě role (RBAC) a rovina dat používá zásady přístupu trezoru klíčů.

## <a name="active-directory-authentication"></a>Ověřování Active Directory

Když vytvoříte trezor klíčů v předplatném Azure, automaticky se přidruží k tenantovi Azure AD předplatného. Všichni volající v obě roviny musí registrovat v tomto tenantovi a ověření přístupu k trezoru klíčů. V obou případech se aplikace můžou k trezoru klíčů dvěma způsoby:

- **Přístup uživatelů a aplikací**: Aplikace má přístup k trezoru klíčů jménem přihlášeného uživatele. Příkladem tento typ přístupu jsou Azure PowerShell a na webu Azure portal. Uživatelský přístup je udělen dvěma způsoby. Uživatele můžete přístup ke Key Vault z libovolné aplikace nebo uživatel musí použít konkrétní aplikace (označované jako _složené identity_).
- **Přístup jen pro aplikace**: Aplikace běží jako proces démon služby nebo na pozadí úlohu. Přístup k trezoru klíčů je udělen identitě aplikace.

Pro oba typy přístupu s aplikace nejprve ověří pomocí Azure AD. Aplikace používá některé [podporované metody ověřování](../active-directory/develop/authentication-scenarios.md) na základě typu aplikace. Aplikace získá token pro prostředek v rovině udělit přístup. Prostředek je koncový bod v rovině správy nebo dat založené na prostředí Azure. Aplikace použije token a odešle žádost o rozhraní REST API služby Key Vault. Další informace najdete v tématu [celý tok ověřování](../active-directory/develop/v1-protocols-oauth-code.md).

Model jediného mechanismu ověřování pro obě roviny má několik výhod:

- Organizace můžou řídit přístup centrálně ke všem trezorům klíčů v organizaci.
- Pokud uživatel opustí, ztratí okamžitě přístup ke všem trezorům klíčů v organizaci.
- Organizace mohou ověření přizpůsobit pomocí možností v Azure AD, například k povolení služby Multi-Factor authentication pro zvýšení zabezpečení.

## <a name="resource-endpoints"></a>Koncové body prostředků

Aplikace k roviny prostřednictvím koncových bodů. Řízení přístupu pro dvě roviny pracovat nezávisle. Chcete-li aplikaci udělit přístup k používání klíčů v trezoru klíčů, udělíte přístup k rovině dat pomocí zásad přístupu trezoru klíčů. Udělit uživatelům přístup pro čtení k vlastnosti služby Key Vault a značky, ale ne přístup k datům (klíče, tajné kódy nebo certifikáty), udělit přístup k rovině správy pomocí RBAC.

V následující tabulce jsou uvedeny koncových bodů správy a roviny dat.

| Přístup&nbsp;rovina | Koncové body přístupu | Operace | Přístup&nbsp;kontrolní mechanismus |
| --- | --- | --- | --- |
| Rovina správy | **Globální:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 | Vytvářet, číst, aktualizovat a odstraňovat trezory klíčů<br><br>Nastavit zásady přístupu trezoru klíčů<br><br>Nastavení služby Key Vault značek | RBAC pro Azure Resource Manageru |
| Rovina dat | **Globální:**<br> &lt;název_trezoru&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;název_trezoru&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;název_trezoru&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;název_trezoru&gt;.vault.microsoftazure.de:443 | Klíče: dešifrovat, šifrování,<br> rozbalení, zabalení, ověřte, podepsání,<br> získání, seznam, aktualizovat, vytvářet,<br> Import, odstranění, zálohování, obnovení<br><br> Tajné kódy: získání, seznam, nastavení, odstranit | Zásady přístupu trezoru klíčů |

## <a name="management-plane-and-rbac"></a>Rovina správy a RBAC

V rovině správy pomocí RBAC můžete autorizovat operace, které můžete provést volající. Každé předplatné Azure má v modelu RBAC, instance služby Azure AD. Udělit přístup k uživatelům, skupinám a aplikacím z tohoto adresáře. Ke správě prostředků v předplatném Azure, které používají model nasazení Azure Resource Manageru je udělen přístup. Chcete-li udělit přístup, použijte [webu Azure portal](https://portal.azure.com/), [rozhraní příkazového řádku Azure](../cli-install-nodejs.md), [prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs), nebo [rozhraní REST API Azure Resource Manageru](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Vytvoření trezoru klíčů ve skupině prostředků a správa přístupu pomocí služby Azure AD. Uživatelům nebo skupinám schopnost spravovat trezory klíčů ve skupině prostředků. Udělit přístup na úrovni konkrétním oboru přiřazením odpovídajících rolí RBAC. K udělení přístupu uživatelům ke správě trezorů klíčů, přiřadíte i předdefinovanou `key vault Contributor` role pro uživatele v konkrétním oboru. Následující úrovně obory je možné přiřadit k roli RBAC:

- **Předplatné**: Roli RBAC přiřazená na úrovni předplatného se vztahuje na všechny skupiny prostředků a prostředky v tomto předplatném.
- **Skupina prostředků**: Roli RBAC přiřazené na úrovni skupiny prostředků se vztahuje na všechny prostředky v příslušné skupině prostředků.
- **Konkrétní prostředek**: Roli RBAC přiřazené pro určitý prostředek se vztahuje na tento prostředek. Prostředek je v tomto případě určitý trezor klíčů.

Existuje několik předdefinovaných rolí. Pokud předdefinované role nevejde vašim potřebám, můžete definovat vlastní role. Další informace najdete v tématu [RBAC: Předdefinované role](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Pokud má uživatel `Contributor` oprávnění k rovině správy trezoru klíčů, uživatel může udělit přístup k rovině dat pomocí nastavení zásad přístupu trezoru klíčů. By měl důsledně řídit, kdo má `Contributor` přístup role k vašim trezorům klíčů. Ujistěte se, že jen autorizované osoby můžete přístup k a spravovat trezory klíčů, klíče, tajné kódy a certifikáty.
>

<a id="data-plane-access-control"></a> 
## <a name="data-plane-and-access-policies"></a>Zásady pro data roviny a přístup

Udělit přístup k rovině dat tím, že nastavíte zásady přístupu trezoru klíčů pro trezor klíčů. Pokud chcete nastavit tyto zásady přístupu, musí mít uživatele, skupinu nebo aplikaci `Contributor` oprávnění pro rovinu správy pro tento trezor klíčů.

Udělíte uživatele, skupiny nebo přístup k aplikaci k provedení konkrétních operací pro klíče nebo tajné kódy ve službě key vault. Key Vault podporuje až 1 024 položky zásad přístupu pro trezor klíčů. Udělit několika uživatelům přístup k rovině dat, vytvoření skupiny zabezpečení služby Azure AD a přidání uživatelů do této skupiny.

<a id="key-vault-access-policies"></a> Zásady přístupu trezoru klíčů udělují oprávnění samostatně pro klíče, tajné klíče a certifikátu. Můžete udělit přístup uživatelů jenom na klíče a tajné kódy. Přístupová oprávnění pro klíče, tajné kódy a certifikáty jsou na úrovni trezoru. Zásady přístupu trezoru klíčů nepodporují podrobné, na úrovni objektů oprávnění jako konkrétní klíč, tajný klíč nebo certifikát. Chcete-li nastavit zásady přístupu pro trezor klíčů, použijte [webu Azure portal](https://portal.azure.com/), [rozhraní příkazového řádku Azure](../cli-install-nodejs.md), [prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs), nebo [Key Vault Management REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Zásady přístupu trezoru klíčů použít na úrovni trezoru. Když uživatel jsou udělena oprávnění vytvářet a odstraňovat klíče, mohou provádět tyto operace na všech klíčích v daném trezoru klíčů.
>

Můžete omezit přístup k rovině dat pomocí [koncové body služeb virtuální sítě pro Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Můžete nakonfigurovat [brány firewall a pravidla virtuální sítě](key-vault-network-security.md) pro další vrstvu zabezpečení.

## <a name="example"></a>Příklad:

V tomto příkladu jsme vyvíjíte aplikaci, která používá certifikát pro protokol SSL, Azure Storage k ukládání dat a klíč RSA 2 048 bitů pro podpisové operace. Naše aplikace spuštěná v Azure virtuální počítač (VM) (nebo škálovací sady virtuálních počítačů). Můžeme použít trezor klíčů k ukládání tajných klíčů aplikací. Abychom mohli uložit zaváděcího certifikátu, který používá aplikace k ověření ve službě Azure AD.

Je potřeba přístup k následujícím uložené klíčů a tajných kódů:
- **Certifikát SSL**: Používá se pro protokol SSL.
- **Klíč úložiště**: Používá pro přístup k účtu úložiště.
- **2048bitový klíč RSA**: Použít pro podpisové operace.
- **Zaváděcího certifikátu**: Používá k ověřování pomocí Azure AD. Po udělení přístupu jsme načte klíč úložiště a použije klíč RSA k podpisu.

Budeme muset definovat následující role k určení, kdo může spravovat, nasazovat a auditovat naší aplikace:
- **Bezpečnostní tým**: Pracovníci IT z kanceláře CSO (Chief Security Officer) nebo podobné přispěvatelé. Bezpečnostní tým je zodpovědná za řádné a zabezpečené ukládání tajných klíčů. Tajné klíče může zahrnovat certifikáty SSL, klíčů RSA pro přihlašování, připojovací řetězce a klíče účtu úložiště.
- **Vývojáři a operátoři**: Zaměstnanci, kteří vyvíjet aplikace a nasazovat v Azure. Členové tohoto týmu nejsou součástí zabezpečení pracovníků. Neměli mít přístup k citlivým datům, jako jsou certifikáty SSL a klíče RSA. Pouze aplikace, která nasadí by měl mít přístup k citlivým datům.
- **Auditoři**: Tato role je pro přispěvatele, kteří nejsou členy vývoj nebo obecných pracovníků IT. Zkontrolujte jejich použití a zachování certifikáty, klíče a tajné klíče, aby se zajistila shoda se standardy zabezpečení. 

Existuje jiná role, která je mimo rozsah této aplikace: předplatné (nebo skupinu prostředků) správce. Správce předplatného nastavuje počáteční přístupová oprávnění pro bezpečnostní tým. Pomocí skupiny prostředků obsahující prostředky, které aplikace vyžaduje udělují přístup k bezpečnostnímu týmu.

Musíme povolit následující operace pro naše role:

**Bezpečnostní tým**
- Vytváření trezorů klíčů.
- Zapnutí protokolování v Key Vault.
- Přidání klíčů a tajných kódů.
- Vytvoření zálohy klíčů pro zotavení po havárii.
- Nastavit zásady přístupu trezoru klíčů pro udělení oprávnění pro uživatele nebo aplikace pro konkrétní operace.
- Pravidelně vrátit klíče a tajné kódy.

**Vývojářů a operátorů**
- Získání odkazů od týmu zabezpečení pro spuštění a certifikáty SSL (kryptografické otisky), klíč úložiště (URI tajného kódu) a klíč RSA (identifikátor URI klíče) pro podpis.
- Vyvíjejte a nasazujte aplikace pro přístup ke klíčům a tajným klíčům prostřednictvím kódu programu.

**Auditoři**
- Zkontrolujte protokoly Key Vault pro potvrzení správné použití klíče a tajné kódy a dodržování bezpečnostních standardů data.

Následující tabulka shrnuje přístupová oprávnění pro naše role a aplikace. 

| Role | Oprávnění k rovině správy | Oprávnění k rovině dat |
| --- | --- | --- |
| Bezpečnostní tým | Přispěvatel Key Vaultu | Klíče: zálohovat, vytvořit, odstranit, získat, import, seznam, obnovit<br>Tajnými klíči: všechny operace |
| Vývojáři a&nbsp;operátory | Oprávnění k nasazení služby Key Vault<br><br> **Poznámka:** Toto oprávnění uděluje nasazené virtuální počítače se načíst tajné kódy z trezoru klíčů. | Žádný |
| Auditoři | Žádný | Klíče: vypsat (list)<br>Tajné klíče: vypsat (list)<br><br> **Poznámka:** Toto oprávnění umožňuje auditoři kontrolu i těch atributů (značky, aktivace data, data vypršení platnosti) klíče a tajné kódy, které nejsou zaznamenávány do protokolů. |
| Aplikace | Žádný | Klíče: podepsat (sign)<br>Tajné klíče: získat (get) |

Tři role tým potřebovat přístup k dalším prostředkům spolu s oprávnění služby Key Vault. Pokud chcete nasadit virtuální počítače (nebo funkce Web Apps služby Azure App Service), potřeby vývojářů a operátorů `Contributor` přístup k těmto typům prostředků. Auditoři potřebují přístup pro čtení k účtu úložiště, kde se ukládají protokoly trezoru klíčů.

Další informace o tom, jak nasadit certifikáty přístupové klíče a tajné klíče prostřednictvím kódu programu, naleznete v následujících zdrojích:
- Zjistěte, jak [nasazení certifikátů do virtuálních počítačů z trezoru klíčů spravovaných zákazníkem](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (příspěvek na blogu).
- Stáhněte si [ukázky klienta služby Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343). Tento obsah ilustruje použití zaváděcího certifikátu pro ověření do služby Azure AD pro přístup k trezoru klíčů.

Většinu přístupových oprávnění lze udělit pomocí webu Azure portal. Chcete-li udělovat různě odstupňovaná oprávnění, že můžete použít Azure PowerShell nebo rozhraní příkazového řádku Azure.

Fragment kódu Powershellu v této části jsou vybaveny následující předpoklady:
- Správce Azure AD vytvořil skupiny zabezpečení reprezentují uvedené tři role: Contoso Security Team, Contoso App DevOps a Contoso App Auditors. Správce má k jejich odpovídajících skupin přidané další uživatele.
- Všechny prostředky jsou umístěné v **ContosoAppRG** skupinu prostředků.
- Protokoly Key Vault se ukládají v **contosologstorage** účtu úložiště. 
- **ContosoKeyVault** služby key vault a **contosologstorage** účtu úložiště jsou ve stejném umístění Azure.

Správce předplatného přiřadí `key vault Contributor` a `User Access Administrator` bezpečnostnímu týmu role. Pracovníci v těchto rolích povolit bezpečnostnímu týmu spravovat přístup k dalším prostředkům a trezorů klíčů, které v **ContosoAppRG** skupinu prostředků.

```PowerShell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Bezpečnostní tým vytvoří trezor klíčů a nastaví oprávnění přístupu a protokolování. Podrobnosti o oprávnění zásad přístupu trezoru klíčů najdete v tématu [o službě Azure Key Vault klíče, tajné kódy a certifikáty](about-keys-secrets-and-certificates.md).

```PowerShell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Naše definované vlastní role se lze přiřadit pouze k předplatnému kde **ContosoAppRG** se vytvoří skupina prostředků. Pokud chcete použít vlastní role pro jiné projekty v jiných předplatných, přidáte další předplatná pro obor pro roli.

Pro naši pracovníci DevOps, přiřazení vlastní role pro trezor klíčů `deploy/action` má obor oprávnění ke skupině prostředků. Pouze virtuální počítače vytvořené v **ContosoAppRG** skupiny prostředků je povolen přístup k tajným kódům (SSL a zaváděcí certifikáty). Virtuální počítače vytvořené v jiných skupinách prostředků DevOps člen nemá přístup k tyto tajné kódy, i v případě, že virtuální počítač má identifikátory URI tajného kódu.

Náš příklad popisuje jednoduchý scénář. Reálné scénáře může být složitější. Nastavení oprávnění k trezoru klíčů na základě vašich potřeb. Jsme předpokládá, že bezpečnostní tým poskytuje klíče a tajného kódu odkazy (identifikátory URI a kryptografické otisky), které používají pracovníci DevOps ve svých aplikacích. Vývojáři a operátoři nevyžadují žádné přístup k rovině dat. Zaměřili jsme se o tom, jak zabezpečení trezoru klíčů. Poskytují obdobnou při zabezpečování [vaše virtuální počítače](https://azure.microsoft.com/services/virtual-machines/security/), [účty úložiště](../storage/common/storage-security-guide.md)a dalších prostředků Azure.

> [!NOTE]
> Tento příklad ukazuje, jak je přístup k trezoru klíčů uzamčen v produkčním prostředí. Vývojáři by měli mít vlastní předplatné nebo skupinu prostředků s úplnými oprávněními ke správě svých trezorů, virtuálních počítačů a účet úložiště, kde vývoji aplikace.

Doporučujeme, abyste nastavili další zabezpečený přístup k vašemu trezoru klíčů pomocí [konfigurace virtuálních sítí a bran firewall služby Key Vault](key-vault-network-security.md).

## <a name="resources"></a>Zdroje a prostředky

* [Azure AD RBAC](../role-based-access-control/role-assignments-portal.md)

* [RBAC: Předdefinované role](../role-based-access-control/built-in-roles.md)

* [Principy nasazení podle modelu Resource Manager a modelu nasazení classic](../azure-resource-manager/resource-manager-deployment-model.md) 

* [Správa RBAC pomocí Azure Powershellu](../role-based-access-control/role-assignments-powershell.md) 

* [Správa RBAC pomocí rozhraní REST API](../role-based-access-control/role-assignments-rest.md)

* [RBAC for Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Tato 2015 popisuje video konference Microsoft Ignite správy přístupu a možností vytváření sestav v Azure. Také popisuje osvědčené postupy pro zabezpečení přístupu k předplatným Azure pomocí Azure AD.

* [Povolit přístup k webovým aplikacím pomocí OAuth 2.0 a Azure AD](../active-directory/develop/v1-protocols-oauth-code.md)

* [Rozhraní API REST pro správu služby Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Referenční dokumentace k rozhraním REST API ke správě vašeho klíče trezoru prostřednictvím kódu programu, včetně nastavení zásad přístupu trezoru klíčů.

* [Rozhraní REST API služby Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Řízení přístupu ke klíčům](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Řízení přístupu k tajným klíčům](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* [Nastavte](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) a [odebrat](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) zásad přístupu trezoru klíčů pomocí Powershellu.
  
## <a name="next-steps"></a>Další postup

Konfigurace [virtuální sítí a bran firewall služby Key Vault](key-vault-network-security.md).

Úvodní kurz pro správce najdete v tématu [co je Azure Key Vault?](key-vault-overview.md).

Další informace o protokolování využití služby Key Vault najdete v tématu [Protokolování v Azure Key Vault](key-vault-logging.md).

Další informace o používání klíčů a tajných klíčů se službou Azure Key Vault najdete v tématu [informace o klíčích a tajných kódů](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Pokud máte dotazy týkající se služby Key Vault, navštivte [fóra](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
