---
title: Zabezpečený přístup k trezoru klíčů – Azure Key Vault | Microsoft Docs
description: Spravujte přístupová oprávnění pro Azure Key Vault, klíče a tajné kódy. Popisuje model ověřování a autorizace Key Vault a způsob zabezpečení trezoru klíčů.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: sudbalas
ms.openlocfilehash: 463ebf429889968474af5630eb99c41a06916d01
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448605"
---
# <a name="secure-access-to-a-key-vault"></a>Zabezpečený přístup k trezoru klíčů

Azure Key Vault je cloudová služba, která chrání šifrovací klíče a tajné kódy, jako jsou certifikáty, připojovací řetězce a hesla. Vzhledem k tomu, že tato data jsou citlivá a důležitá pro podnikání, je nutné zabezpečený přístup k vašim trezorům klíčů povolit pouze autorizovaným aplikacím a uživatelům. Tento článek poskytuje přehled modelu přístupu Key Vault. Vysvětluje ověřování a autorizaci a popisuje, jak zabezpečit přístup k vašim trezorům klíčů.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Přehled modelu přístupu

Přístup k trezoru klíčů se ovládá prostřednictvím dvou rozhraní: **rovina správy** a **rovina dat**. Rovina správy je místo, kde spravujete Key Vault sebe sama. Mezi operace v této rovině patří vytváření a odstraňování trezorů klíčů, načítání vlastností Key Vault a aktualizace zásad přístupu. Rovina dat je místo, kde pracujete s daty uloženými v trezoru klíčů. Můžete přidávat, odstraňovat a upravovat klíče, tajné klíče a certifikáty.

Pro přístup k trezoru klíčů v kterékoli rovině musí mít všichni volající (uživatelé nebo aplikace) správné ověřování a autorizaci. Ověřování vytváří identitu volajícího. Autorizace určuje, které operace může volající spustit.

Obě roviny používají pro ověřování Azure Active Directory (Azure AD). Pro autorizaci rovina správy používá řízení přístupu na základě role (RBAC) a rovina dat používá zásady přístupu Key Vault.

## <a name="active-directory-authentication"></a>Ověřování služby Active Directory

Když vytvoříte Trezor klíčů v rámci předplatného Azure, automaticky se přiřadí k tenantovi Azure AD daného předplatného. Všichni volající v obou rovinách se musí zaregistrovat v tomto tenantovi a ověřit pro přístup k trezoru klíčů. V obou případech můžou aplikace získat přístup k Key Vault dvěma způsoby:

- **Uživatel plus přístup k aplikaci**: aplikace přistupuje Key Vault jménem přihlášeného uživatele. Příklady tohoto typu přístupu zahrnují Azure PowerShell a Azure Portal. Přístup uživatele je udělen dvěma způsoby. Uživatelé mají přístup k Key Vault z libovolné aplikace nebo musí používat konkrétní aplikaci (označovanou jako _složená identita_).
- **Přístup jen pro aplikace**: aplikace běží jako služba démona nebo úloha na pozadí. Identitě aplikace je udělen přístup k trezoru klíčů.

U obou typů přístupu se aplikace ověřuje pomocí Azure AD. Aplikace používá jakoukoli [podporovanou metodu ověřování](../../active-directory/develop/authentication-scenarios.md) založenou na typu aplikace. Aplikace získá token pro prostředek v rovině pro udělení přístupu. Prostředek je koncový bod v rovině pro správu nebo data na základě prostředí Azure. Aplikace použije token a pošle REST API žádost o Key Vault. Pokud se chcete dozvědět víc, Projděte si [celý tok ověřování](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Model jednoho mechanismu ověřování do obou rovin má několik výhod:

- Organizace můžou centrálně řídit přístup ke všem trezorům klíčů ve své organizaci.
- Pokud uživatel opustí, okamžitě ztratí přístup ke všem trezorům klíčů v organizaci.
- Organizace můžou přizpůsobit ověřování pomocí možností ve službě Azure AD, jako je například povolení služby Multi-Factor Authentication pro zvýšení zabezpečení.

## <a name="resource-endpoints"></a>Koncové body prostředků

Aplikace přistupují k rovinám prostřednictvím koncových bodů. Ovládací prvky přístupu pro tyto dvě roviny pracují nezávisle. Abyste aplikaci udělili přístup k používání klíčů v trezoru klíčů, udělíte přístup k rovině dat pomocí zásad přístupu Key Vault. Pokud chcete uživateli udělit oprávnění ke čtení Key Vault vlastností a značek, ale ne přístup k datům (klíčům, tajným klíčům nebo certifikátům), udělíte přístup k rovině správy pomocí RBAC.

V následující tabulce jsou uvedeny koncové body pro řídicí a datové roviny.

| &nbsp;Rovina přístupu | Koncové body přístupu | Operace | &nbsp;Mechanismus řízení přístupu |
| --- | --- | --- | --- |
| Rovina správy | **Globální**<br> management.azure.com:443<br><br> **Azure Čína 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Státní správa USA Azure:**<br> management.usgovcloudapi.net:443<br><br> **Azure Německo:**<br> management.microsoftazure.de:443 | Vytváření, čtení, aktualizace a odstraňování trezorů klíčů<br><br>Nastavení zásad přístupu Key Vault<br><br>Nastavení značek Key Vault | Azure RBAC |
| Rovina dat | **Globální**<br> &lt;název_trezoru&gt;.vault.azure.net:443<br><br> **Azure Čína 21Vianet:**<br> &lt;název_trezoru&gt;.vault.azure.cn:443<br><br> **Státní správa USA Azure:**<br> &lt;název_trezoru&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Německo:**<br> &lt;název_trezoru&gt;.vault.microsoftazure.de:443 | Klíče: dešifrování, šifrování,<br> rozbalení, zalomení, ověření, podepsání,<br> získat, vypsat, aktualizovat, vytvořit,<br> Import, odstranění, zálohování, obnovení<br><br> Tajné kódy: získat, vypsat, nastavit, odstranit | Zásada přístupu Key Vault |

## <a name="management-plane-and-rbac"></a>Rovina správy a RBAC

Na rovině správy použijte RBAC (řízení přístupu na základě role Azure (Azure RBAC)) k autorizaci operací, které volající může spustit. V modelu RBAC má každé předplatné Azure instanci Azure AD. Přístup k uživatelům, skupinám a aplikacím udělíte z tohoto adresáře. Přístup se uděluje pro správu prostředků v předplatném Azure, které používají model nasazení Azure Resource Manager. K udělení přístupu použijte [Azure Portal](https://portal.azure.com/), rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azure/)nebo [Azure Resource Manager rozhraní REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx).

V rámci skupiny prostředků můžete vytvořit Trezor klíčů a spravovat přístup pomocí Azure AD. Uživatelům nebo skupinám udělíte možnost spravovat trezory klíčů ve skupině prostředků. Přístup na konkrétní úroveň oboru udělíte tak, že jim přiřadíte příslušné role RBAC. Chcete-li uživateli udělit přístup ke správě trezorů klíčů, přiřaďte uživatele předdefinované `key vault Contributor` role v konkrétním oboru. Role RBAC může přiřadit tyto úrovně oborů:

- **Předplatné**: role RBAC přiřazená na úrovni předplatného se vztahuje na všechny skupiny prostředků a prostředky v rámci daného předplatného.
- **Skupina prostředků**: role RBAC přiřazená na úrovni skupiny prostředků se vztahuje na všechny prostředky v této skupině prostředků.
- **Konkrétní prostředek**: na tento prostředek se vztahuje role RBAC přiřazená pro konkrétní prostředek. V tomto případě je prostředkem konkrétní Trezor klíčů.

Existuje několik předdefinovaných rolí. Pokud předdefinovaná role nevyhovuje vašim potřebám, můžete definovat vlastní roli. Další informace naleznete v části [RBAC: předdefinované role](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Pokud má uživatel `Contributor` oprávnění k rovině správy trezoru klíčů, uživatel může udělit přístup k rovině dat nastavením zásad přístupu Key Vault. Měli byste přesně řídit, kdo má `Contributor` roli přístup k vašim trezorům klíčů. Ujistěte se, že k vašim trezorům klíčů, klíčům, tajným klíčům a certifikátům mají přístup jenom autorizovaní uživatelé.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Rovina dat a zásady přístupu

Nastavením zásad přístupu Key Vault pro Trezor klíčů udělíte přístup k rovině dat. Aby bylo možné nastavit tyto zásady přístupu, musí mít uživatel, skupina nebo aplikace `Contributor` oprávnění pro rovinu správy pro daný Trezor klíčů.

Uživateli, skupině nebo aplikaci udělíte přístup k provádění konkrétních operací pro klíče nebo tajné klíče v trezoru klíčů. Key Vault podporuje až 1 024 záznamů zásad přístupu pro Trezor klíčů. Pokud chcete udělit přístup k rovině dat několika uživatelům, vytvořte skupinu zabezpečení Azure AD a přidejte do této skupiny uživatele.

Zobrazí se úplný seznam operací trezoru a tajného klíče a pochopení operací povolených při konfiguraci zásad přístupu trezoru klíčů zobrazením následujícího odkazu. [Odkaz na operaci Key Vault](https://docs.microsoft.com/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a>Zásady přístupu Key Vault udělují oprávnění nezávisle na klíčích, tajných klíčích a certifikátech. Uživatelům můžete udělit přístup jenom ke klíčům a nikoli k tajným klíčům. Přístupová oprávnění pro klíče, tajné klíče a certifikáty jsou na úrovni trezoru. Zásady přístupu Key Vault nepodporují podrobné oprávnění na úrovni objektu, jako je konkrétní klíč, tajný klíč nebo certifikát. K nastavení zásad přístupu pro Trezor klíčů použijte [Azure Portal](https://portal.azure.com/), rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azure/)nebo [rozhraní REST API pro správu Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Zásady přístupu Key Vault platí na úrovni trezoru. Když je uživateli udělené oprávnění k vytváření a odstraňování klíčů, můžou tyto operace provádět u všech klíčů v tomto trezoru klíčů.
>

Přístup k rovině dat můžete omezit pomocí [koncových bodů služby virtuální sítě pro Azure Key Vault](overview-vnet-service-endpoints.md)). Můžete nakonfigurovat [brány firewall a pravidla virtuální sítě](network-security.md) pro další vrstvu zabezpečení.

## <a name="example"></a>Příklad

V tomto příkladu vyvíjíme aplikaci, která používá certifikát pro TLS/SSL, Azure Storage k ukládání dat a klíč RSA 2 048-bit pro operace Signing. Naše aplikace běží na virtuálním počítači Azure (nebo v sadě škálování virtuálního počítače). K ukládání tajných klíčů aplikací můžeme použít Trezor klíčů. Pro ověření pomocí Azure AD můžeme uložit spouštěcí certifikát, který používá aplikace.

Potřebujeme přístup k následujícím uloženým klíčům a tajným klíčům:
- **Certifikát TLS/SSL**: používá se pro TLS/SSL.
- **Klíč úložiště**: používá se pro přístup k účtu úložiště.
- **RSA 2 048-bitový klíč**: používá se pro operace podepisování.
- **Spouštěcí certifikát**: používá se k ověřování pomocí Azure AD. Po udělení přístupu můžeme načíst klíč úložiště a použít klíč RSA k podepisování.

Abychom určili, kdo může spravovat, nasazovat a auditovat naši aplikaci, je potřeba definovat následující role:
- **Bezpečnostní tým**: zaměstnanci oddělení IT od kanceláře CSO (hlavní bezpečnostní důstojník) nebo podobné přispěvatelé. Bezpečnostní tým je zodpovědný za řádné zabezpečení tajných kódů. Tajné kódy můžou zahrnovat certifikáty TLS/SSL, klíče RSA pro podepisování, připojovací řetězce a klíče účtu úložiště.
- **Vývojáři a operátoři**: zaměstnanci, kteří aplikaci vyvíjejí a nasazují v Azure. Členové tohoto týmu nejsou součástí bezpečnostních pracovníků. Nemají přístup k citlivým datům, jako jsou certifikáty TLS/SSL a klíče RSA. Pouze aplikace, kterou nasazují, by měli mít přístup k citlivým datům.
- **Auditori**: Tato role je určena pro přispěvatele, kteří nejsou členy vývoje nebo obecných zaměstnanců IT. Kontrolují použití a údržbu certifikátů, klíčů a tajných klíčů, aby bylo zajištěno dodržování standardů zabezpečení.

Existuje jiná role, která je mimo rozsah naší aplikace: Správce předplatného (nebo skupiny prostředků). Správce předplatného nastavuje počáteční přístupová oprávnění pro bezpečnostní tým. Poskytují přístup k bezpečnostnímu týmu pomocí skupiny prostředků, která má prostředky požadované aplikací.

Pro naše role musíme autorizovat tyto operace:

**Bezpečnostní tým**
- Vytvořte trezory klíčů.
- Zapněte protokolování Key Vault.
- Přidejte klíče a tajné kódy.
- Vytvořte zálohy klíčů pro zotavení po havárii.
- Nastavte zásady přístupu Key Vault pro udělení oprávnění uživatelům a aplikacím pro konkrétní operace.
- Pravidelně převeďte klíče a tajné kódy.

**Vývojáři a operátoři**
- Získejte odkazy od týmu zabezpečení pro certifikáty Bootstrap a TLS/SSL (kryptografické otisky), klíč úložiště (tajný identifikátor URI) a klíč RSA (klíč identifikátoru URI) pro podepisování.
- Vytvořte a nasaďte aplikaci pro přístup k klíčům a tajným klíčům prostřednictvím kódu programu.

**Auditoři**
- Zkontrolujte protokoly Key Vault a potvrďte správné použití klíčů a tajných kódů a dodržování standardů zabezpečení dat.

Následující tabulka shrnuje přístupová oprávnění pro naše role a aplikace.

| Role | Oprávnění k rovině správy | Oprávnění k rovině dat |
| --- | --- | --- |
| Bezpečnostní tým | Přispěvatel Key Vault | Klíče: zálohovat (backup), vytvořit (create), odstranit (delete), získat (get), importovat (import), vypsat (list), obnovit (restore)<br>Tajné kódy: všechny operace |
| Vývojáři a &nbsp; operátoři | Oprávnění k nasazení Key Vault<br><br> **Poznámka**: Toto oprávnění umožňuje nasazeným virtuálním počítačům načíst tajné kódy z trezoru klíčů. | Žádné |
| Auditoři | Žádné | Klíče: vypsat (list)<br>Tajné klíče: vypsat (list)<br><br> **Poznámka**: Toto oprávnění umožňuje auditorům kontrolovat atributy (značky, data aktivace, data vypršení platnosti) pro klíče a tajné klíče, které nejsou v protokolech vygenerovány. |
| Aplikace | Žádné | Klíče: podepsat (sign)<br>Tajné klíče: získat (get) |

Tři role týmu potřebují přístup k dalším prostředkům spolu s oprávněními Key Vault. K nasazení virtuálních počítačů (nebo Web Apps funkce Azure App Service) potřebují vývojáři a operátoři `Contributor` přístup k těmto typům prostředků. Auditoři potřebují přístup pro čtení k účtu úložiště, ve kterém jsou uložené protokoly Key Vault.

Další informace o tom, jak programově nasazovat certifikáty, přístupové klíče a tajné kódy, najdete v těchto zdrojích:
- Naučte se, jak [nasadit certifikáty do virtuálních počítačů z trezoru klíčů spravovaného zákazníkem](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (Blogový příspěvek).
- Podívejte se na [ukázky Azure Key Vault klienta](https://docs.microsoft.com/samples/browse/?term=Key%20Vault). Tento obsah ukazuje, jak použít spouštěcí certifikát k ověření ve službě Azure AD pro přístup k trezoru klíčů.

Většinu přístupových oprávnění můžete udělit pomocí Azure Portal. K udělení podrobných oprávnění můžete použít Azure PowerShell nebo rozhraní příkazového řádku Azure CLI.

Fragmenty kódu prostředí PowerShell v této části jsou sestavené s následujícími předpoklady:
- Správce Azure AD vytvořil skupiny zabezpečení, které reprezentují tři role: tým zabezpečení contoso, contoso App DevOps a contoso App auditory. Správce přidal uživatele do příslušných skupin.
- Všechny prostředky jsou umístěné ve skupině prostředků **ContosoAppRG** .
- Protokoly Key Vault se ukládají do účtu úložiště **contosologstorage** .
- Trezor klíčů **ContosoKeyVault** a účet úložiště **contosologstorage** jsou ve stejném umístění Azure.

Správce předplatného přiřadí `key vault Contributor` `User Access Administrator` k bezpečnostnímu týmu role a. Tyto role umožňují bezpečnostnímu týmu spravovat přístup k dalším prostředkům a trezorům klíčů, které jsou ve skupině prostředků **ContosoAppRG** .

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Bezpečnostní tým vytvoří Trezor klíčů a nastaví oprávnění pro protokolování a přístup.

```powershell
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

Naše definované vlastní role se přiřazují jenom k předplatnému, ve kterém je vytvořená skupina prostředků **ContosoAppRG** . Chcete-li použít vlastní roli pro jiné projekty v jiných předplatných, přidejte do oboru role další odběry.

Pro naše zaměstnance DevOps je přiřazení vlastní role pro oprávnění trezoru klíčů `deploy/action` vymezeno pro skupinu prostředků. Přístup k tajným klíčům (TLS/SSL a spouštěcí certifikáty) jsou povolené jenom virtuálním počítačům vytvořeným ve skupině prostředků **ContosoAppRG** . Virtuální počítače vytvořené v jiných skupinách prostředků DevOps členem nemají přístup k těmto tajným klíčům, i když má virtuální počítač tajné identifikátory URI.

Náš příklad popisuje jednoduchý scénář. Scénáře reálného života můžou být složitější. V závislosti na vašich potřebách můžete upravit oprávnění k vašemu trezoru klíčů. Předpokládali jsme, že tým zabezpečení poskytuje klíče a tajné odkazy (identifikátory URI a kryptografické otisky), které jsou používány DevOps zaměstnanci ve svých aplikacích. Vývojáři a operátoři nevyžadují přístup k rovině dat. Zaměřili jsme se na to, jak váš Trezor klíčů zabezpečit. Při zabezpečování [virtuálních počítačů](https://azure.microsoft.com/services/virtual-machines/security/), [účtů úložiště](../../storage/blobs/security-recommendations.md)a dalších prostředků Azure dejte k podobným pozornost.

> [!NOTE]
> Tento příklad ukazuje, jak je přístup Key Vault uzamčen v produkčním prostředí. Vývojáři by měli mít vlastní předplatné nebo skupinu prostředků s úplnými oprávněními ke správě trezorů, virtuálních počítačů a účtu úložiště, kde aplikace vyvíjí.

Pro Trezor klíčů doporučujeme nastavit další zabezpečený přístup [konfigurací Key Vault bran firewall a virtuálních sítí](network-security.md).

## <a name="resources"></a>Zdroje a prostředky

* [Azure AD RBAC](../../role-based-access-control/role-assignments-portal.md)

* [RBAC: předdefinované role](../../role-based-access-control/built-in-roles.md)

* [Principy nasazení Správce prostředků a klasického nasazení](../../azure-resource-manager/management/deployment-models.md)

* [Správa RBAC pomocí Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

* [Správa RBAC pomocí REST API](../../role-based-access-control/role-assignments-rest.md)

* [RBAC pro Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Toto 2015 konference Microsoft Ignite se zabývá možnostmi správy přístupu a vytváření sestav v Azure. Také prozkoumá osvědčené postupy pro zabezpečení přístupu k předplatným Azure pomocí Azure AD.

* [Autorizace přístupu k webovým aplikacím pomocí OAuth 2,0 a Azure AD](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [Rozhraní REST API pro správu Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Odkaz na rozhraní REST API pro programovou správu trezoru klíčů, včetně nastavení zásad Key Vault přístupu

* [Rozhraní REST API pro Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Řízení přístupu ke klíčům](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

* [Řízení přístupu k tajným klíčům](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

* [Nastavení](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) a [odebrání](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) zásad přístupu Key Vault pomocí prostředí PowerShell.

## <a name="next-steps"></a>Další kroky

Nakonfigurujte [Key Vault brány firewall a virtuální sítě](network-security.md).

Úvodní kurz pro správce najdete v tématu [co je Azure Key Vault?](overview.md)).

Další informace o protokolování využití pro Key Vault najdete v tématu [Azure Key Vault protokolování](logging.md)).

Další informace o používání klíčů a tajných kódů s Azure Key Vault najdete v tématu [o klíčích a tajných](https://msdn.microsoft.com/library/azure/dn903623.aspx)klíčích.

Pokud máte dotazy týkající se Key Vault, navštivte [stránku s dotazem na Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-key-vault.html).
