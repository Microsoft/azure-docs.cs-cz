---
title: Zabezpečení trezoru klíčů | Dokumentace Microsoftu
description: Spravujte přístupová oprávnění pro trezor klíčů pro správu trezorů, klíčů a tajných klíčů. Model ověřování a autorizace pro trezor klíčů a jak trezor klíčů zabezpečit
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
ms.date: 08/31/2018
ms.author: ambapat
ms.openlocfilehash: d8c5e660b460e763fb4a500463543316c8c914ed
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159399"
---
# <a name="secure-your-key-vault"></a>Zabezpečení trezoru klíčů
Azure Key Vault je cloudová služba, která chrání šifrovací klíče a tajné klíče (například certifikáty, připojovací řetězce a hesla) a pro vaše cloudové aplikace. Jelikož tato data jsou citlivá a zcela klíčová pro vaši obchodní (i jinou) činnost, je na místě zabezpečit přístup k trezorům klíčů tak, aby k nim mohli přistupovat jen autorizované aplikace a autorizovaní uživatelé. Tento článek představuje model přístupu k trezoru klíčů, vysvětluje ověření a autorizaci a na příkladu názorně popisuje, jak lze zabezpečit přístup k trezoru klíčů pro vaše cloudové aplikace.

## <a name="overview"></a>Přehled
Přístup k trezoru klíčů je řízen prostřednictvím dvou oddělených rozhraní: rovina správy a rovina dat. Pro obě roviny je požadováno řádné ověření a autorizace, než může volající (uživatel nebo aplikace) získat k trezoru klíčů přístup. Ověření určí identitu volajícího a autorizace následně určí, které operace má daný volající povoleno provádět.

K ověření využívají rovina správy i rovina dat službu Azure Active Directory. K autorizaci ale rovina správy používá řízení přístupu podle role (RBAC), zatímco rovina dat používá zásady přístupu trezoru klíčů.

Stručný přehled tímto článkem pokrytých témat:

[Ověření s použitím Azure Active Directory](#authentication-using-azure-active-directory): Tato část vysvětluje, jak se volající ověřuje ve službě Azure Active Directory za účelem přístupu k trezoru klíčů prostřednictvím roviny správy i roviny dat. 

[Rovina správy a rovina dat](#management-plane-and-data-plane): Rovina správy a rovina dat jsou dvě roviny přístupu, které se používají pro přístup k vašemu trezoru klíčů. Každá rovina podporuje určité operace. Tato část popisuje koncové body přístupu, podporované operace a metody řízení přístupu používané těmito rovinami. 

[Řízení přístupu roviny správy](#management-plane-access-control): V této části se podíváme na povolení přístupu k operacím roviny správy s použitím řízení přístupu podle role.

[Řízení přístupu roviny dat](#data-plane-access-control): Tato část popisuje, jak použít zásady přístupu trezoru klíčů k řízení přístupu k rovině dat.

[Příklad](#example): Tento příklad popisuje, jak nastavit řízení přístupu pro váš trezor klíčů, abyste umožnili třem různým týmům (bezpečnostní tým, vývojářský/provozní tým a auditoři) provádět konkrétní úkoly za účelem vývoje, správy a monitorování aplikace v Azure.

## <a name="authentication-using-azure-active-directory"></a>Ověření pomocí služby Azure Active Directory
Když v rámci předplatného Azure vytvoříte trezor klíčů, je automaticky přidružen k tenantovi Azure Active Directory pro dané předplatné. Všichni volající (uživatelé a aplikace) musí být v tomto tenantovi registrováni, aby mohli k trezoru klíčů přistupovat. Aplikace nebo uživatel se musí nejdříve ověřit ve službě Azure Active Directory, až potom může přistupovat k trezoru klíčů. To platí pro přístup k rovině správy i přístup k rovině dat. V obou případech může aplikace přistupovat k trezoru klíčů dvěma způsoby:

* **přístup uživatele a aplikace** – obvykle se používá pro aplikace, které přistupují k trezoru klíčů jménem přihlášeného uživatele. Příklady tohoto typu přístupu jsou Azure PowerShell a Azure Portal. Jsou dva způsoby, jak udělit přístup uživatelům. Jeden způsob je udělit přístup uživatelům tak, aby mohli přistupovat k trezoru klíčů z libovolné aplikace, a druhý způsob je udělit uživatelům přístup k trezoru klíčů jen při použití konkrétní aplikace (označováno jako složená identita). 
* **přístup pouze aplikace** – používá se pro aplikace, které spouští služby démonů, úlohy na pozadí a podobně. Přístup k trezoru klíčů je udělen identitě aplikace.

U obou typů aplikací s aplikace nejprve ověří ve službě Azure Active Directory s použitím libovolné [podporované metody ověření](../active-directory/develop/authentication-scenarios.md) a získá token. Použitá metoda ověření závisí na typu aplikace. Aplikace pak tento token použije a odešle požadavek REST API na trezor klíčů. V případě přístupu k rovině správy jsou požadavky směrovány přes koncový bod Azure Resource Manager. Při přístupu k rovině dat aplikace komunikují přímo s koncovým bodem trezoru klíčů. Další podrobnosti najdete v [úplném diagramu procesu ověření](../active-directory/develop/v1-protocols-oauth-code.md). 

Název prostředku, pro který aplikace žádá token, je různý podle toho, jestli aplikace přistupuje k rovině správy nebo rovině dat. Název prostředku je tak buď koncový bod roviny správy, nebo koncový bod roviny dat, jak je popsáno v tabulce níže, v závislosti na prostředí Azure.

Používání jediného mechanismu ověřování pro rovinu správy i rovinu dat má svoje výhody:

* Organizace mohou centrálně řídit přístup ke všem trezorům klíčů v organizaci.
* Když uživatel organizaci opustí, ztratí okamžitě přístup ke všem trezorům klíčů v organizaci.
* Organizace si mohou ověření přizpůsobit prostřednictvím možností v Azure Active Directory (mohou například povolit vícefaktorové ověřování pro vyšší bezpečnost).

## <a name="management-plane-and-data-plane"></a>Rovina správy a rovina dat
Azure Key Vault je služba Azure, která je dostupná prostřednictvím modelu nasazení Azure Resource Manager. Když vytvoříte trezor klíčů, získáte virtuální kontejner, ve kterém můžete vytvářet další objekty, jako jsou klíče, tajné klíče a certifikáty. Následně pak přistupujete ke svému trezoru klíčů pomocí roviny správy nebo roviny dat, abyste prováděli konkrétní operace. Rozhraní roviny správy slouží ke správě samotného trezoru klíčů – například vytváření, odstraňování a aktualizace atributů trezoru klíčů nebo nastavení zásad přístupu pro rovinu dat. Rozhraní roviny dat se používá k přidávání, odstraňování, změnám a používání klíčů, tajných klíčů a certifikátů uložených v trezoru klíčů.

K rozhraní roviny správy a roviny dat se přistupuje prostřednictvím různých koncových bodů (viz tabulka). Druhý sloupec v tabulce popisuje názvy DNS pro tyto koncové body v různých prostředích Azure. Třetí sloupec popisuje operace, které můžete z každé roviny přístupu provádět. Každá rovina přístupu má také vlastní mechanismus řízení přístupu: pro rovinu správy se řízení přístupu nastavuje pomocí služby řízení přístupu podle role (RBAC) služby Azure Resource Manager, pro rovinu dat se řízení přístupu nastavuje pomocí zásad přístupu trezoru klíčů.

| Rovina přístupu | Koncové body přístupu | Operace | Mechanismus řízení přístupu |
| --- | --- | --- | --- |
| Rovina správy |**Globální:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 |Vytvořit (create), číst (read), aktualizovat (update), odstranit (delete) trezor klíčů <br> Nastavit zásady přístupu pro trezor klíčů<br>Nastavit značky pro trezor klíčů |Řízení přístupu podle role (RBAC) služby Azure Resource Manager |
| Rovina dat |**Globální:**<br> &lt;název_trezoru&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;název_trezoru&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;název_trezoru&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;název_trezoru&gt;.vault.microsoftazure.de:443 |Pro klíče: dešifrovat (decrypt), zašifrovat (encrypt), rozbalit (UnwrapKey), zabalit (WrapKey), ověřit (verify), podepsat (sign), získat (get), vypsat (list), aktualizovat (update), vytvořit (create), importovat (import), odstranit (delete), zálohovat (backup), obnovit (restore)<br><br> Pro tajné klíče: získat (get), vypsat (list), nastavit (set), odstranit (delete) |Zásady přístupu trezoru klíčů |

Řízení přístupu roviny správy a roviny dat fungují nezávisle. Pokud například chcete aplikaci udělit přístup k používání klíčů v trezoru klíčů, je třeba jí jen udělit přístup k rovině dat pomocí zásad přístup trezoru klíčů. Aplikace nepotřebuje přístup k rovině správy. A obráceně, pokud chcete, aby uživatel mohl číst vlastnosti a značky trezoru, ale neměl přístup k žádným klíčům, tajným klíčům ani certifikátům, můžete tomuto uživateli udělit přístup pro čtení pomocí RBAC a není potřeba žádný přístup k rovině dat.

## <a name="management-plane-access-control"></a>Řízení přístupu roviny správy
Rovina správy se skládá z operací, které mají vliv na samotný trezor klíčů. Můžete například vytvořit nebo odstranit trezor klíčů. Můžete získat seznam trezorů klíčů v určitém předplatném. Můžete načíst vlastnosti trezoru klíčů (například SKU, značky) a nastavit zásady přístupu trezoru klíčů, které řídí, kteří uživatelé a aplikace mají přístup ke klíčům a tajným klíčům v trezoru klíčů. Řízení přístupu roviny správy používá RBAC. Úplný seznam operací trezoru klíčů, které lze provádět prostřednictvím roviny správy, najdete v tabulce v předchozí části. 

### <a name="role-based-access-control-rbac"></a>Řízení přístupu podle role (RBAC)
Každé předplatné Azure zahrnuje službu (adresář) Azure Active Directory. Uživatelům, skupinám a aplikacím z tohoto adresáře lze udělit přístup ke správě prostředků v tomto předplatném Azure, které používají model nasazení Azure Resource Manager. Tento typ řízení přístupu je označován řízení přístupu podle role (RBAC). Ke správě tohoto přístupu můžete používat [Azure Portal](https://portal.azure.com/), [nástroje Azure CLI](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) nebo [rozhraní Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx).

S modelem Azure Resource Manager vytváříte trezor klíčů ve skupině prostředků a řídíte přístup k rovině správy tohoto trezoru klíčů pomocí služby Azure Active Directory. Můžete například uživatelům nebo skupině udělit schopnost spravovat trezory klíčů v určité skupině prostředků.

Přístup lze udělit uživatelům, skupinám nebo aplikacím v konkrétním oboru přiřazením odpovídajících rolí RBAC. Když například chcete uživateli udělit přístup ke správě trezorů klíčů, přiřadíte tomuto uživateli v konkrétním oboru předdefinovanou roli Přispěvatel trezoru klíčů (key vault Contributor). Oborem by v tomto případě bylo předplatné, skupina prostředků nebo určitý trezor klíčů. Role přiřazená na úrovni předplatného se bude vztahovat na všechny skupiny prostředků a prostředky v tomto předplatném. Role přiřazená na úrovni skupiny prostředků se bude vztahovat na všechny prostředky v této skupině. Role přiřazená pouze pro určitý prostředek se vztahuje pouze na tento prostředek. Je k dispozici několik předdefinovaných rolí (viz [RBAC: vestavěné role](../role-based-access-control/built-in-roles.md)). Pokud tyto neodpovídají vašim potřebám, můžete si definovat i vlastní role.

> [!IMPORTANT]
> Všimněte si, že pokud uživatel má oprávnění role Přispěvatel (RBAC) k rovině správy trezoru klíčů, může sám sobě udělit i přístup k rovině dat – může totiž nastavovat zásady přístupu trezoru klíčů, které řídí přístup k rovině dat. Doporučujeme proto důsledně kontrolovat, kdo má k vašim trezorům klíčů přístup jako Přispěvatel, aby bylo zajištěno, že jen autorizované osoby budou mít přístup a budou moci spravovat vaše trezory klíčů, klíče, tajné klíče a certifikáty.
> 
> 

## <a name="data-plane-access-control"></a>Řízení přístupu roviny dat
Rovina dat trezoru klíčů se skládá z operací, které mají vliv na objekty v trezoru klíčů, jako jsou klíče, tajné klíče a certifikáty.  To zahrnuje operace s klíči, jako je vytvoření, import, aktualizace, výpis, zálohování a obnova klíčů, kryptografické operace jako podepsání, ověření, zašifrování, dešifrování, zabalení a rozbalení a nastavení značek a dalších atributů pro klíče. Pro tajné klíče sem podobně patří operace získat, nastavit, vypsat a odstranit.

Přístup k rovině dat je udělován nastavením zásad přístupu pro trezor klíčů. Uživatel, skupina nebo aplikace musí mít oprávnění role Přispěvatel (RBAC) pro rovinu správy trezoru klíčů, aby mohla nastavovat zásady pro tento trezor klíčů. Uživateli, skupině nebo aplikaci lze udělit přístup k provádění konkrétních operací pro klíče nebo tajné klíče v trezoru klíčů. Trezor klíčů podporuje až 1 024 položek zásad přístupu na jeden trezor klíčů. Vytvořte skupinu zabezpečení Azure Active Directory a do této skupiny přidejte uživatele, kterým chcete udělit přístup k rovině dat trezoru klíčů.

### <a name="key-vault-access-policies"></a>Zásady přístupu trezoru klíčů
Zásady přístupu trezoru klíčů udělují odděleně oprávnění pro klíče, tajné klíče a certifikáty. Můžete tak například uživateli udělit přístup pouze ke klíčům, ale žádná oprávnění k tajným klíčům. Nicméně oprávnění pro přístup ke klíčům, tajným klíčům a certifikátům se nastavují na úrovni trezoru. Zásady přístupu trezoru klíčů tedy nepodporují oprávnění na úrovni objektu. K nastavení zásad přístupu pro trezor klíčů můžete použít [Azure Portal](https://portal.azure.com/), [nástroje Azure CLI](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) nebo [rozhraní REST API správy trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Zásady přístupu trezoru klíčů se aplikuj a úrovni trezoru. Když má například uživatel oprávnění vytvářet a odstraňovat klíče, může tyto operace provádět na všech klíčích v daném trezoru klíčů.

Kromě zásad přístupu můžete přístup k rovině dat omezit [koncovými body služby Virtual Network pro Azure Key Vault](key-vault-overview-vnet-service-endpoints.md) tím, že nakonfigurujete [brány firewall a pravidla virtuální sítě](key-vault-network-security.md) a tím přidáte další vrstvu zabezpečení.

## <a name="example"></a>Příklad:
Řekněme, že vyvíjíte aplikaci, která používá certifikát pro SSL, službu Azure Storage k ukládání dat a používá 2048bitový klíč RSA pro podpisové operace. A tato aplikace běží ve virtuálním počítači (nebo ve škálovací sadě virtuálních počítačů). Můžete použít trezor klíčů, ve kterém budou uloženy všechny tajné klíče aplikace, a použít tento trezor klíčů k uložení zaváděcího certifikátu, který aplikace používá k ověření v Azure Active Directory.

Takže souhrn všech klíčů a tajných klíčů, které budou uložené v trezoru klíčů.

* **Certifikát SSL** – používá se pro SSL.
* **Klíč úložiště** – používá se k získání přístupu k účtu úložiště.
* **2048bitový klíč RSA** – používá se pro podpisové operace.
* **Zaváděcí certifikát** – používá se k ověření v Azure Active Directory pro získání přístupu k trezoru klíčů, ze kterého se načte klíč úložiště a použije klíč RSA k podpisu.

Nyní se podívejme na lidi, kteří budou spravovat, nasazovat a auditovat tuto aplikaci. V tomto příkladu použijeme tři role.

* **Bezpečnostní tým**: Obvykle pracovníci IT z kanceláře bezpečnostního ředitele (CSO, Chief Security Officer) nebo ekvivalentní úrovně, kteří odpovídají za řádné a zabezpečené ukládání tajných klíčů, například certifikátů SSL, klíčů RSA pro podpisové operace, připojovacích řetězců pro databáze a klíčů účtu úložiště.
* **Vývojáři/operátoři**: To jsou lidé, kteří tuto aplikaci vyvíjejí a potom nasazují v Azure. Obvykle nejsou přímo součástí bezpečnostního týmu, a proto by neměli mít přístup k žádným citlivým datům, jako jsou certifikáty SSL a klíče RSA, nicméně jimi nasazovaná aplikace k těmto datům přístup potřebuje.
* **Auditoři**: Obvykle zcela odlišná skupina lidí, izolovaná od vývojářů i obecných pracovníků IT. Jejich odpovědností je kontrolovat, jestli se certifikáty, klíče a podobně řádně používají a udržují, jak to předepisují standardy zabezpečení dat. 

Ještě je tu jedna role, která je mimo obor této aplikace, nicméně je relevantní a měli bychom jí tu zmínit. Jde o roli správce předplatného (nebo skupiny prostředků). Správce předplatného nastavuje počáteční přístupová oprávnění pro bezpečnostní tým. Tady předpokládáme, že správce předplatného udělil bezpečnostnímu týmu přístup ke skupině prostředků, ve které jsou všechny prostředky potřebné pro tuto aplikaci.

Teď se podívejme, jaké akce každá role v kontextu aplikace provádí.

* **Bezpečnostní tým**
  * Vytváření trezorů klíčů
  * Zapnutí protokolování trezoru klíčů
  * Přidání klíčů / tajných klíčů
  * Vytvoření zálohy klíčů pro zotavení po havárii
  * Nastavení zásad přístupu trezoru klíčů, aby se uživatelům a aplikacím udělila oprávnění provádět konkrétní operace
  * Pravidelná obměna klíčů / tajných klíčů
* **Vývojáři/operátoři**
  * Získání reference na zaváděcí certifikáty a certifikáty SSL (kryptografické otisky), klíč úložiště (URI tajného klíče) a podpisový klíč (URI klíče) od bezpečnostního týmu
  * Vývoj a nasazení aplikace, která přistupuje ke klíčům a tajným klíčům prostřednictvím programového kódu
* **Auditoři**
  * Kontrola protokolů použití, aby bylo možné potvrdit, že se klíče / tajné klíče používají řádně a jsou plněny standardy zabezpečení dat

Teď se podívejme, jaká přístupová oprávnění k trezoru klíčů každá role (a aplikace) potřebuje, aby mohla provádět úkoly, které jí přísluší. 

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

Vedle oprávnění k trezoru klíčů budou všechny tři role potřebovat přístup k dalším prostředkům. Například vývojáři a operátoři potřebují nasazovat virtuální počítače (nebo webové aplikace a podobně) a budou tedy potřebovat přístup úrovně Přispěvatel i k těmto typům prostředků. Auditoři potřebují přístup ke čtení k účtu úložiště, kam se ukládají protokoly trezoru klíčů.

Jelikož tento článek se soustředí na zabezpečení přístupu k trezoru klíčů, popisujeme tu podrobněji jen části, které jsou relevantní pro toto téma. Podrobnosti ohledně nasazení certifikátů, přístupu ke klíčům a tajným klíčům prostřednictvím programového kódu a dalších témat zde přeskakujeme. Tyto podrobnosti jsou popsány v jiných článcích. Nasazením certifikátů uložených v trezoru klíčů do virtuálního počítače se zabývá [tento blogový příspěvek](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) a je k dispozici [ukázkový kód](https://www.microsoft.com/download/details.aspx?id=45343), který ilustruje použití zaváděcího certifikátu pro ověření v Azure AD, aby se získal přístup k trezoru klíčů.

Většinu přístupových oprávnění lze udělit prostřednictvím webu Azure Portal, nicméně pokud potřebujete udělovat různě odstupňovaná oprávnění, může být nutné použít Azure PowerShell (nebo Azure CLI), abyste docílili požadovaného výsledku. 

Následující fragmenty kódu PowerShellu předpokládají:

* Správce Azure Active Directory vytvořil skupiny zabezpečení, které reprezentují uvedené tři role, a sice Contoso Security Team, Contoso App Devops, Contoso App Auditors. Správce již také přidal uživatele do skupin, do kterých patří.
* **ContosoAppRG** je skupina prostředků, ve které jsou všechny tyto prostředky. **contosologstorage** je úložiště, kam se ukládají protokoly. 
* Trezor klíčů **ContosoKeyVault** a účet úložiště používaný pro protokoly trezoru klíčů **contosologstorage** musí být ve stejném umístění Azure

Nejprve správce předplatného přiřadí bezpečnostnímu týmu role „key vault Contributor“ a „User Access Administrator“. To dovolí bezpečnostnímu týmu spravovat přístup pro další prostředky a spravovat trezory klíčů ve skupině prostředků ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Následující skript ilustruje, jak může bezpečnostní tým vytvořit trezor klíčů, nastavit protokolování a nastavit přístupová oprávnění pro ostatní role a aplikaci. 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets all

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

Definovanou vlastní roli je možné přiřadit jenom v předplatném, ve kterém je vytvořená skupina prostředků ContosoAppRG. Pokud budou stejné vlastní role používány v dalších projektech v jiných předplatných, je možné do jejich oboru přidat další předplatná.

Přiřazení vlastní role pro vývojáře/operátory pro oprávnění „deploy/action“ má nastavený obor na skupinu prostředků. Tímto způsobem bude moci tajné klíče (certifikát SSL a zaváděcí certifikát) získat jen virtuální počítače vytvořené ve skupině prostředků ContosoAppRG. Jakýkoli virtuální počítač, který člen týmu vývojářů a operátorů vytvoří v jiné skupině prostředků, nebude moci získat tyto tajné klíče, a to i navzdory znalosti URI tajných klíčů.

Tento příklad znázorňuje jednoduchý scénář. Reálné scénáře v praxi mohou být komplexnější a může se stát, že bude nutné oprávnění k vašemu trezoru klíčů upravit, aby odpovídala vašim potřebám. V našem příkladu třeba předpokládáme, že bezpečnostní tým poskytne reference klíčů a tajných klíčů (URI a kryptografické otisky), které tým vývojářů a operátorů potřebuje k odkazování ve svých aplikacích. Není tu tedy nutné udělovat vývojářům a operátorům přístup k rovině dat. Také připomínáme, že tento příklad se zaměřuje na zabezpečení trezoru klíčů. Obdobnou péči byste nicméně měli věnovat i zabezpečení [virtuálních počítačů](https://azure.microsoft.com/services/virtual-machines/security/), [účtů úložiště](../storage/common/storage-security-guide.md) a dalších prostředků Azure.

> [!NOTE]
> Poznámka: Tento příklad ukazuje, jak bude přístup k trezoru klíčů uzamčen v produkčním prostředí. Vývojáři by měli mít vedle toho vlastní předplatné nebo skupinu prostředků, ve kterých budou mít oprávnění k plné správě svých trezorů, virtuálních počítačů a účtů úložiště při vývoji aplikace.

Důrazně doporučujeme ještě více zabezpečit přístup k trezoru klíčů tím, že [pro Key Vault nakonfigurujete brány firewall a virtuální sítě](key-vault-network-security.md).

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
  
  Odkaz na video na Channel 9 z konference MS Ignite 2015. Na tomto sezení se hovoří o možnostech správy přístupu a generování sestav v Azure a probírají se osvědčené postupy pro zabezpečení přístupu k předplatným Azure pomocí Azure Active Directory.
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

