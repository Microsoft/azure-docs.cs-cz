---
title: Zabezpečený přístup k trezoru klíčů – Trezor klíčů Azure | Dokumenty společnosti Microsoft
description: Spravujte přístupová oprávnění pro Azure Key Vault, klíče a tajné klíče. Zahrnuje model ověřování a autorizace pro trezor klíčů a způsob zabezpečení trezoru klíčů.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: eac3850cfa0684bd1751cf7b88b4ff8e92667293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284379"
---
# <a name="secure-access-to-a-key-vault"></a>Zabezpečený přístup k trezoru klíčů

Azure Key Vault je cloudová služba, která chrání šifrovací klíče a tajné klíče, jako jsou certifikáty, připojovací řetězce a hesla. Vzhledem k tomu, že tato data jsou citlivá a důležitá pro podnikání, je třeba zabezpečit přístup k trezorům klíčů povolením pouze autorizovaných aplikací a uživatelů. Tento článek obsahuje přehled modelu přístupu trezoru klíčů. Vysvětluje ověřování a autorizaci a popisuje, jak zabezpečit přístup k trezorům klíčů.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Přehled modelu aplikace Access

Přístup k trezoru klíčů je řízen prostřednictvím dvou rozhraní: **roviny správy** a **roviny dat**. Rovina správy je místo, kde můžete spravovat trezor klíčů sám. Operace v této rovině zahrnují vytváření a odstraňování trezorů klíčů, načítání vlastností trezoru klíčů a aktualizaci zásad přístupu. Rovina dat je místo, kde pracujete s daty uloženými v trezoru klíčů. Klíče, tajné klíče a certifikáty můžete přidávat, odstraňovat a upravovat.

Chcete-li získat přístup k trezoru klíčů v jedné rovině, musí mít všichni volající (uživatelé nebo aplikace) správné ověřování a autorizaci. Ověřování stanoví identitu volajícího. Autorizace určuje, které operace může volající provést.

Obě letadla používají Azure Active Directory (Azure AD) pro ověřování. Pro autorizaci rovina správy používá řízení přístupu na základě rolí (RBAC) a rovina dat používá zásady přístupu trezoru klíčů.

## <a name="active-directory-authentication"></a>Ověřování služby Active Directory

Když vytvoříte trezor klíčů v předplatném Azure, automaticky se přidruží k tenantovi Azure AD předplatného. Všichni volající v obou rovinách se musí zaregistrovat v tomto tenantovi a ověřit přístup k trezoru klíčů. V obou případech mohou aplikace přistupovat k trezoru klíčů dvěma způsoby:

- **Přístup k aplikaci**plus: Aplikace přistupuje k trezoru klíčů jménem přihlášeného uživatele. Příklady tohoto typu přístupu patří Azure PowerShell a portál Azure. Přístup uživatelů je udělovován dvěma způsoby. Uživatelé mohou přistupovat k trezoru klíčů z libovolné aplikace nebo musí používat určitou aplikaci (označovanou jako _složená identita)._
- **Přístup pouze k aplikaci**: Aplikace je spuštěna jako služba daemon nebo úloha na pozadí. Identita aplikace je udělena přístup k trezoru klíčů.

Pro oba typy přístupu aplikace ověřuje pomocí Azure AD. Aplikace používá libovolnou [podporovanou metodu ověřování](../active-directory/develop/authentication-scenarios.md) založenou na typu aplikace. Aplikace získá token pro prostředek v rovině udělit přístup. Prostředek je koncový bod v rovině správy nebo dat na základě prostředí Azure. Aplikace používá token a odešle požadavek rozhraní REST API do trezoru klíčů. Další informace naleznete v [celém toku ověřování](../active-directory/develop/v2-oauth2-auth-code-flow.md).

Model jednoho mechanismu pro ověřování obou rovin má několik výhod:

- Organizace mohou centrálně řídit přístup ke všem trezorům klíčů ve své organizaci.
- Pokud uživatel odejde, okamžitě ztratí přístup ke všem trezorům klíčů v organizaci.
- Organizace můžou přizpůsobit ověřování pomocí možností ve službě Azure AD, například povolit vícefaktorové ověřování pro zvýšení zabezpečení.

## <a name="resource-endpoints"></a>Koncové body prostředků

Aplikace přistupují k rovinamatým koncovým bodům. Ovládací prvky přístupu pro dvě roviny pracují nezávisle. Chcete-li aplikaci udělit přístup k použití klíčů v trezoru klíčů, udělte přístup k rovině dat pomocí zásad přístupu trezoru klíčů. Chcete-li uživateli udělit přístup ke čtení vlastností a značek trezoru klíčů, ale nikoli přístup k datům (klíče, tajné klíče nebo certifikáty), udělit přístup k rovině správy s RBAC.

V následující tabulce jsou uvedeny koncové body pro správu a datové roviny.

| Přístupová&nbsp;rovina | Koncové body přístupu | Provoz | Mechanismus kontroly přístupu&nbsp; |
| --- | --- | --- | --- |
| Rovina správy | **Globální:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure USA – vláda:**<br> management.usgovcloudapi.net:443<br><br> **Azure Německo:**<br> management.microsoftazure.de:443 | Vytváření, čtení, aktualizace a odstraňování trezorů klíčů<br><br>Nastavení zásad přístupu trezoru klíčů<br><br>Nastavení značek trezoru klíčů | Azure Resource Manager RBAC |
| Rovina dat | **Globální:**<br> &lt;název_trezoru&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;název_trezoru&gt;.vault.azure.cn:443<br><br> **Azure USA – vláda:**<br> &lt;název_trezoru&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Německo:**<br> &lt;název_trezoru&gt;.vault.microsoftazure.de:443 | Klíče: dešifrovat, šifrovat,<br> rozbalit, zabalit, ověřit, podepsat,<br> získat, seznam, aktualizovat, vytvářet,<br> import, odstranění, zálohování, obnovení<br><br> Tajemství: získat, seznam, nastavit, odstranit | Zásady přístupu k trezoru klíčů |

## <a name="management-plane-and-rbac"></a>Řídící letadlo a RBAC

V rovině správy použijete RBAC(Řízení přístupu na základě rolí) k autorizaci operací, které může volající provést. V modelu RBAC má každé předplatné Azure instanci Azure AD. Přístup uživatelům, skupinám a aplikacím z tohoto adresáře udělíte. Přístup je udělen ke správě prostředků v předplatném Azure, které používají model nasazení Azure Resource Manager. Chcete-li udělit přístup, použijte [portál Azure](https://portal.azure.com/), [Azure CLI](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs)nebo [Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Vytvoříte trezor klíčů ve skupině prostředků a spravovat přístup pomocí Azure AD. Uživatelům nebo skupinám udělujete možnost spravovat trezory klíčů ve skupině prostředků. Přístup na určité úrovni oboru udělíte přiřazením příslušných rolí RBAC. Chcete-li uživateli udělit přístup ke správě `key vault Contributor` trezorů klíčů, přiřadíte uživateli předdefinovanou roli v určitém oboru. Roli RBAC lze přiřadit následující úrovně oborů:

- **Předplatné**: Role RBAC přiřazená na úrovni předplatného platí pro všechny skupiny prostředků a prostředky v rámci tohoto předplatného.
- **Skupina prostředků**: Role RBAC přiřazená na úrovni skupiny prostředků se vztahuje na všechny prostředky v této skupině prostředků.
- **Konkrétní zdroj**: Pro daný prostředek se vztahuje role RBAC přiřazená pro určitý zdroj. V tomto případě je prostředek konkrétní trezor klíčů.

Existuje několik předdefinovaných rolí. Pokud předdefinovaná role nevyhovuje vašim potřebám, můžete definovat vlastní roli. Další informace naleznete v tématu [RBAC: Předdefinované role](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Pokud má `Contributor` uživatel oprávnění k rovině správy trezoru klíčů, může si udělit přístup k rovině dat nastavením zásad přístupu trezoru klíčů. Měli byste přísně řídit, kdo má `Contributor` přístup k vašim trezorům klíčů. Zajistěte, aby k vašim trezorům klíčů, klíčům, tajným klíčům a certifikátům měly přístup a spravovat je pouze oprávněné osoby.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Rovina dat a zásady přístupu

Přístup k rovině dat udělíte nastavením zásad přístupu trezoru klíčů pro trezor klíčů. Chcete-li nastavit tyto zásady přístupu, `Contributor` musí mít uživatel, skupina nebo aplikace oprávnění pro rovinu správy pro tento trezor klíčů.

Udělujete uživateli, skupině nebo aplikaci přístup ke spuštění určitých operací pro klíče nebo tajné klíče v trezoru klíčů. Trezor klíčů podporuje až 1 024 položek zásad přístupu pro trezor klíčů. Chcete-li udělit přístup k rovině dat několika uživatelům, vytvořte skupinu zabezpečení Azure AD a přidejte uživatele do této skupiny.

<a id="key-vault-access-policies"></a>Zásady přístupu trezoru klíčů udělují oprávnění samostatně klíčům, tajným klíčům a certifikátům. Uživateli můžete udělit přístup pouze ke klíčům, nikoli k tajným kódům. Přístupová oprávnění pro klíče, tajné klíče a certifikáty jsou na úrovni úschovny. Zásady přístupu trezoru klíčů nepodporují podrobná oprávnění na úrovni objektů, jako je konkrétní klíč, tajný klíč nebo certifikát. Pokud chcete nastavit zásady přístupu pro trezor klíčů, použijte [portál Azure](https://portal.azure.com/), [Azure CLI](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs)nebo [REST API správy trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Zásady přístupu trezoru klíčů platí na úrovni trezoru. Pokud je uživateli uděleno oprávnění k vytváření a odstraňování klíčů, může tyto operace provádět se všemi klíči v tomto trezoru klíčů.
>

Přístup k rovině dat můžete omezit pomocí [koncových bodů služby virtuální sítě pro Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). [Brány firewall a pravidla virtuální sítě](key-vault-network-security.md) můžete nakonfigurovat pro další vrstvu zabezpečení.

## <a name="example"></a>Příklad

V tomto příkladu vyvíjíme aplikaci, která používá certifikát pro TLS/SSL, Azure Storage pro ukládání dat a RSA 2 048bitový klíč pro operace podepisování. Naše aplikace běží ve virtuálním počítači Azure (VM) (nebo škálovací sadě virtuálních strojů). Můžeme použít trezor klíčů k uložení tajných kódů aplikace. Můžeme uložit zaváděcí certifikát, který používá aplikace k ověření pomocí Azure AD.

Potřebujeme přístup k následujícím uloženým klíčům a tajným kódům:
- **Certifikát TLS/SSL**: Používá se pro TLS/SSL.
- **Klíč úložiště**: Používá se pro přístup k účtu úložiště.
- **RSA 2 048bitový klíč**: Používá se pro operace s podepisování.
- **Zaváděcí certifikát**: Používá se k ověření pomocí služby Azure AD. Po udělení přístupu můžeme načíst klíč úložiště a použít klíč RSA pro podepisování.

Musíme definovat následující role, abychom určili, kdo může spravovat, nasazovat a auditovat naši aplikaci:
- **Bezpečnostní tým**: It pracovníci z kanceláře ČSÚ (Chief Security Officer) nebo podobných přispěvatelů. Bezpečnostní tým je zodpovědný za řádné uchování tajemství. Tajné klíče mohou zahrnovat certifikáty TLS/SSL, klíče RSA pro podepisování, připojovací řetězce a klíče účtů úložiště.
- **Vývojáři a operátoři**: Zaměstnanci, kteří vyvíjejí aplikaci a nasazují ji v Azure. Členové tohoto týmu nejsou součástí bezpečnostního personálu. Neměli by mít přístup k citlivým datům, jako jsou certifikáty TLS/SSL a klíče RSA. Pouze aplikace, kterou nasazují, by měla mít přístup k citlivým datům.
- **Auditoři**: Tato role je pro přispěvatele, kteří nejsou členy vývojového nebo obecného it personálu. Kontrolují používání a údržbu certifikátů, klíčů a tajných kódů, aby zajistili dodržování bezpečnostních standardů.

Existuje další role, která je mimo rozsah naší aplikace: správce předplatného (nebo skupiny prostředků). Správce předplatného nastaví počáteční přístupová oprávnění pro tým zabezpečení. Udělí přístup týmu zabezpečení pomocí skupiny prostředků, která má prostředky vyžadované aplikací.

Pro naše role musíme autorizovat následující operace:

**Bezpečnostní tým**
- Vytvořte trezory klíčů.
- Zapněte protokolování trezoru klíčů.
- Přidejte klíče a tajné klíče.
- Vytvořte zálohy klíčů pro zotavení po havárii.
- Nastavte zásady přístupu trezoru klíčů, které udělují oprávnění uživatelům a aplikacím pro konkrétní operace.
- Pravidelně přetáčte klíče a tajné klíče.

**Vývojáři a operátoři**
- Získejte reference od týmu zabezpečení pro bootstrap a TLS/SSL certifikáty (kryptografické otisky), klíč úložiště (tajný identifikátor URI) a klíč RSA (identifikátor URI klíče) pro podepisování.
- Vývoj a nasazení aplikace pro přístup ke klíčům a tajným klíčům programově.

**Auditoři**
- Zkontrolujte protokoly trezoru klíčů a potvrďte správné použití klíčů a tajných kódů a dodržování standardů zabezpečení dat.

Následující tabulka shrnuje přístupová oprávnění pro naše role a aplikace.

| Role | Oprávnění k rovině správy | Oprávnění k rovině dat |
| --- | --- | --- |
| Bezpečnostní tým | Přispěvatel trezoru klíčů | Klíče: zálohovat (backup), vytvořit (create), odstranit (delete), získat (get), importovat (import), vypsat (list), obnovit (restore)<br>Tajemství: všechny operace |
| Vývojáři&nbsp;a operátoři | Oprávnění k nasazení trezoru klíčů<br><br> **Poznámka:** Toto oprávnění umožňuje nasazeným virtuálním mům načíst tajné klíče z trezoru klíčů. | Žádný |
| Auditoři | Žádný | Klíče: vypsat (list)<br>Tajné klíče: vypsat (list)<br><br> **Poznámka:** Toto oprávnění umožňuje auditorům kontrolovat atributy (značky, data aktivace, data vypršení platnosti) pro klíče a tajné klíče, které nejsou vyzařovány v protokolech. |
| Aplikace | Žádný | Klíče: podepsat (sign)<br>Tajné klíče: získat (get) |

Tři týmové role potřebují přístup k dalším prostředkům spolu s oprávněními trezoru klíčů. K nasazení virtuálních počítačů (nebo funkce webových aplikací služby Azure App Service) potřebují `Contributor` vývojáři a operátoři přístup k těmto typům prostředků. Auditoři potřebují přístup pro čtení k účtu úložiště, kde jsou uloženy protokoly trezoru klíčů.

Další informace o tom, jak nasadit certifikáty, přístupové klíče a tajné klíče programově, naleznete v těchto zdrojích:
- Zjistěte, jak [nasadit certifikáty do virtuálních aplikací z trezoru klíčů spravovaného zákazníkem](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (blogový příspěvek).
- Stáhněte si [ukázky klienta Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343). Tento obsah ukazuje, jak pomocí zaváděcí certifikát k ověření azure ad pro přístup k trezoru klíčů.

Většinu přístupových oprávnění můžete udělit pomocí portálu Azure. Chcete-li udělit podrobná oprávnění, můžete použít Azure PowerShell nebo Azure CLI.

Úryvky prostředí PowerShell v této části jsou sestaveny s následujícími předpoklady:
- Správce Azure AD vytvořil skupiny zabezpečení představující tři role: Contoso Security Team, Contoso App DevOps a Contoso App Auditors. Správce přidal uživatele do svých skupin.
- Všechny prostředky jsou umístěny ve skupině prostředků **ContosoAppRG.**
- Protokoly trezoru klíčů jsou uloženy v účtu úložiště **contosologstorage.**
- Trezor klíčů **ContosoKeyVault** a účet úložiště **úložiště contosologstorage** jsou ve stejném umístění Azure.

Správce předplatného přiřadí `key vault Contributor` `User Access Administrator` a role týmu zabezpečení. Tyto role umožňují týmu zabezpečení spravovat přístup k dalším prostředkům a trezorům klíčů, které jsou ve skupině prostředků **ContosoAppRG.**

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Tým zabezpečení vytvoří trezor klíčů a nastaví oprávnění k protokolování a přístupu. Podrobnosti o oprávněních zásad přístupu trezoru klíčů naleznete v [tématu O klíčích, tajných klíčích a certifikátech trezoru klíčů Azure](about-keys-secrets-and-certificates.md).

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

Naše definované vlastní role jsou přiřaditelné pouze k předplatnému, kde je vytvořena skupina prostředků **ContosoAppRG.** Chcete-li použít vlastní roli pro jiné projekty v jiných předplatných, přidejte další předplatná do oboru pro roli.

Pro naše pracovníky DevOps je přiřazení `deploy/action` vlastní role pro oprávnění trezoru klíčů vymezeno na skupinu prostředků. Pouze virtuální počítače vytvořené ve skupině prostředků **ContosoAppRG** mají povolený přístup k tajným kódům (TLS/SSL a bootstrap certifikáty). Virtuální aplikace vytvořené v jiných skupinách prostředků členem DevOps nemají přístup k těmto tajným kódům, i když virtuální ho virtuální ho dispoziční správce má tajné identifikátory URI.

Náš příklad popisuje jednoduchý scénář. Reálné scénáře mohou být složitější. Oprávnění k trezoru klíčů můžete upravit podle svých potřeb. Předpokládali jsme, že tým zabezpečení poskytuje klíčové a tajné odkazy (identifikátory URI a kryptografické otisky), které používají pracovníci DevOps ve svých aplikacích. Vývojáři a operátoři nevyžadují přístup k datovým rovině. Zaměřili jsme se na to, jak zabezpečit trezor s klíči. Při zabezpečení [virtuálních počítačů](https://azure.microsoft.com/services/virtual-machines/security/), [účtů úložiště](../storage/blobs/security-recommendations.md)a dalších prostředků Azure přihlížejte podobným způsobem.

> [!NOTE]
> Tento příklad ukazuje, jak je přístup trezoru klíčů uzamčen v produkčním prostředí. Vývojáři by měli mít vlastní předplatné nebo skupinu prostředků s úplnými oprávněními ke správě svých trezorů, virtuálních her a účtu úložiště, kde vyvíjejí aplikaci.

Doporučujeme nastavit další zabezpečený přístup k trezoru klíčů [konfigurací brány firewall úložiště klíčů a virtuálních sítí](key-vault-network-security.md).

## <a name="resources"></a>Prostředky

* [Azure AD RBAC](../role-based-access-control/role-assignments-portal.md)

* [RBAC: Předdefinované role](../role-based-access-control/built-in-roles.md)

* [Principy nasazení Správce prostředků a klasického nasazení](../azure-resource-manager/management/deployment-models.md)

* [Správa RBAC pomocí Azure PowerShellu](../role-based-access-control/role-assignments-powershell.md)

* [Správa RBAC pomocí rozhraní REST API](../role-based-access-control/role-assignments-rest.md)

* [RBAC pro Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Toto konferenční video Microsoft Ignite z roku 2015 pojednává o možnostech správy přístupu a vytváření sestav v Azure. Také zkoumá osvědčené postupy pro zabezpečení přístupu k předplatným Azure pomocí Azure AD.

* [Autorizace přístupu k webovým aplikacím pomocí OAuth 2.0 a Azure AD](../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [API REST správy trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Odkaz na rest API pro programovou správu trezoru klíčů, včetně nastavení zásad přístupu trezoru klíčů.

* [API REST trezoru klíčů](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Řízení přístupu ke klíčům](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

* [Řízení přístupu k tajným klíčům](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

* [Nastavte](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) a [odeberte](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) zásady přístupu k trezoru klíčů pomocí Prostředí PowerShell.

## <a name="next-steps"></a>Další kroky

Konfigurace [bran firewall úložiště klíčů a virtuálních sítí](key-vault-network-security.md).

Kurz začínáme pro správce najdete v tématu [Co je Azure Key Vault?](key-vault-overview.md).

Další informace o protokolování využití pro trezor klíčů naleznete v [tématu Protokolování trezoru klíčů Azure](key-vault-logging.md).

Další informace o používání klíčů a tajných kódů pomocí služby Azure Key Vault naleznete [v tématu O klíčích a tajných klíčích](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Máte-li dotazy týkající se trezoru klíčů, navštivte [fóra](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
