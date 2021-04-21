---
title: Průvodce vývojáře pro službu Azure Key Vault
description: Vývojáři můžou pomocí Azure Key Vault spravovat kryptografické klíče v rámci Microsoft Azure prostředí.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 08ac1ae09741b63648aec2b51b6a774a46b9af7c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818435"
---
# <a name="azure-key-vault-developers-guide"></a>Průvodce vývojáře pro službu Azure Key Vault

Key Vault umožňuje zabezpečený přístup k citlivým informacím v rámci svých aplikací:

- Klíče, tajné klíče a certifikáty jsou chráněné bez nutnosti psát kód sami a vy je snadno budete moct použít z vašich aplikací.
- Zákazníkům umožníte vlastnit a spravovat vlastní klíče, tajné klíče a certifikáty, abyste se mohli soustředit na poskytování základních softwarových funkcí. Tímto způsobem vaše aplikace nebudou vlastnit odpovědnost ani potenciální odpovědnost za klíče tenanta vašich zákazníků, tajné klíče a certifikáty.
- Vaše aplikace může používat klíče pro podepisování a šifrování. zatím udržuje správu klíčů externí z vaší aplikace. Další informace o klíčích najdete v tématu [o klíčích](../keys/about-keys.md) .
- Přihlašovací údaje, jako jsou hesla, přístupové klíče a tokeny SAS, můžete spravovat tak, že je uložíte v Key Vault jako [tajné kódy.](../secrets/about-secrets.md)
- Správa certifikátů. Další informace najdete v tématu [o certifikátech](../certificates/about-certificates.md) .

Obecnější informace o Azure Key Vault najdete v tématu [co je Key Vault](overview.md).

## <a name="public-previews"></a>Veřejné náhledy

Pravidelně vydáváme ve verzi Public Preview novou funkci Key Vault. Vyzkoušejte si funkce veřejné verze Preview a dejte nám vědět, co si myslíte prostřednictvím azurekeyvault@microsoft.com naší e-mailové adresy pro zpětnou vazbu.

## <a name="creating-and-managing-key-vaults"></a>Vytváření a Správa trezorů klíčů

Správa Key Vault, podobně jako jiné služby Azure, se provádí prostřednictvím služby Azure Resource Manager. Azure Resource Manager je služba nasazování a správy pro Azure. Poskytuje úroveň správy, která vám umožňuje vytvářet, aktualizovat a odstraňovat prostředky v účtu Azure. Další informace najdete v tématu [Azure Resource Manager](../../azure-resource-manager/management/overview.md)

Přístup k vrstvě správy řídí [řízení přístupu na základě role v Azure](../../role-based-access-control/overview.md). V Key Vault vrstva správy, označovaná také jako správa nebo řídicí plocha, umožňuje vytvářet a spravovat trezory klíčů a jejich atributy včetně zásad přístupu, ale ne klíčů, tajných klíčů a certifikátů, které jsou spravovány na rovině dat. K `Key Vault Contributor` udělení přístupu pro správu Key Vault můžete použít předdefinovanou roli.     

**Rozhraní API a sady SDK pro správu trezoru klíčů:**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Odkaz](/cli/azure/keyvault)<br>[Rychlý start](quick-create-cli.md)|[Odkaz](/powershell/module/az.keyvault)<br>[Rychlý start](quick-create-powershell.md)|[Odkaz](/rest/api/keyvault/)|[Odkaz](/azure/templates/microsoft.keyvault/vaults)<br>[Rychlý start](./vault-create-template.md)|[Odkaz](/dotnet/api/microsoft.azure.management.keyvault)|[Odkaz](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Odkaz](/java/api/com.microsoft.azure.management.keyvault)|[Odkaz](/javascript/api/@azure/arm-keyvault)|

Instalační balíčky a zdrojový kód najdete v tématu [klientské knihovny](client-libraries.md) .

Další informace o Key Vault rovině správy najdete v tématu [Azure Key Vault funkce zabezpečení](security-features.md) .

## <a name="authenticate-to-key-vault-in-code"></a>Ověřování pro Key Vault v kódu

Key Vault používá ověřování Azure AD, které pro udělení přístupu vyžaduje objekt zabezpečení služby Azure AD. Objekt zabezpečení služby Azure AD může být uživatel, instanční objekt aplikace, [spravovaná identita pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md)nebo skupina libovolného typu objektů zabezpečení.

### <a name="authentication-best-practices"></a>Osvědčené postupy ověřování

Pro aplikace nasazené do Azure se doporučuje používat spravovanou identitu. Pokud používáte služby Azure, které nepodporují spravovanou identitu, nebo pokud jsou aplikace nasazené místně, [instanční objekt s certifikátem](../../active-directory/develop/howto-create-service-principal-portal.md) je možné alternativou. V takovém případě by certifikát měl být uložený v Key Vault a často otočený. Instanční objekt s tajným klíčem se dá použít pro vývojová a testovací prostředí a lokálně nebo v Cloud Shell se doporučuje použít objekt zabezpečení uživatele.

Doporučené objekty zabezpečení na prostředí:
- **Provozní prostředí**:
  - Spravovaná identita nebo instanční objekt s certifikátem
- **Testovací a vývojové prostředí**:
  - Spravovaná identita, instanční objekt s certifikátem nebo instančním objektem s tajným klíčem
- **Místní vývoj**:
  - Uživatel nebo instanční objekt s tajným klíčem

Scénáře ověřování jsou podporované **klientskou knihovnou identity Azure** a jsou integrované s Key Vault SDK. Knihovna identit Azure se dá používat v různých prostředích a platformách beze změny kódu. Identita Azure taky automaticky načte ověřovací token z přihlášeného k uživateli Azure pomocí Azure CLI, sady Visual Studio, Visual Studio Code a dalších. 

Další informace o službě Azure identity Client libarary najdete v těchto tématech:

**Klientské knihovny identit Azure**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Sada Azure identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure identity SDK Python](/python/api/overview/azure/identity-readme)|[Sada Azure identity SDK Java](/java/api/overview/azure/identity-readme)|[JavaScript sady Azure identity SDK](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> [Knihovna ověřování aplikací](/dotnet/api/overview/azure/service-to-service-authentication) , která se doporučuje pro sadu Key Vault .NET SDK verze 3, která je aktuálně depracated. Postupujte prosím podle pokynů [AppAuthentication do Azure. pokyny k migraci identit](/dotnet/api/overview/azure/app-auth-migration) pro migraci na sadu Key Vault .NET SDK verze 4.

Výukové programy, jak ověřit Key Vault v aplikacích, najdete v tématech:
- [Ověřování pro Key Vault v aplikaci hostované na virtuálním počítači v .NET](./tutorial-net-virtual-machine.md)
- [Ověřování pro Key Vault v aplikaci hostované na virtuálním počítači v Pythonu](./tutorial-python-virtual-machine.md)
- [Ověřování pro Key Vault s App Service](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Správa klíčů, certifikátů a tajných klíčů

Přístup k klíčům, tajným klíčům a certifikátům je řízen rovinou dat. Řízení přístupu roviny dat se dá provádět pomocí zásad přístupu k místnímu trezoru nebo Azure RBAC.

**Rozhraní API pro klíče a sady SDK**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Odkaz](/cli/azure/keyvault/key)<br>[Rychlý start](../keys/quick-create-cli.md)|[Odkaz](/powershell/module/az.keyvault/)<br>[Rychlý start](../keys/quick-create-powershell.md)|[Odkaz](/rest/api/keyvault/#key-operations)|[Odkaz](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Rychlý start](../keys/quick-create-template.md)|[Odkaz](/dotnet/api/azure.security.keyvault.keys)<br>[Rychlý start](../keys/quick-create-net.md)|[Odkaz](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Rychlý start](../keys/quick-create-python.md)|[Odkaz](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Rychlý start](../keys/quick-create-java.md)|[Odkaz](/javascript/api/@azure/keyvault-keys/)<br>[Rychlý start](../keys/quick-create-node.md)|

**Rozhraní API a sady SDK pro certifikáty**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Odkaz](/cli/azure/keyvault/certificate)<br>[Rychlý start](../certificates/quick-create-cli.md)|[Odkaz](/powershell/module/az.keyvault)<br>[Rychlý start](../certificates/quick-create-powershell.md)|[Odkaz](/rest/api/keyvault/#certificate-operations)|–|[Odkaz](/dotnet/api/azure.security.keyvault.certificates)<br>[Rychlý start](../certificates/quick-create-net.md)|[Odkaz](/python/api/overview/azure/keyvault-certificates-readme)<br>[Rychlý start](../certificates/quick-create-python.md)|[Odkaz](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Rychlý start](../certificates/quick-create-java.md)|[Odkaz](/javascript/api/@azure/keyvault-certificates/)<br>[Rychlý start](../certificates/quick-create-node.md)|

**Rozhraní API a sady SDK tajných klíčů**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Odkaz](/cli/azure/keyvault/secret)<br>[Rychlý start](../secrets/quick-create-cli.md)|[Odkaz](/powershell/module/az.keyvault/)<br>[Rychlý start](../secrets/quick-create-powershell.md)|[Odkaz](/rest/api/keyvault/#secret-operations)|[Odkaz](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Rychlý start](../secrets/quick-create-template.md)|[Odkaz](/dotnet/api/azure.security.keyvault.secrets)<br>[Rychlý start](../secrets/quick-create-net.md)|[Odkaz](/python/api/overview/azure/keyvault-secrets-readme)<br>[Rychlý start](../secrets/quick-create-python.md)|[Odkaz](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Rychlý start](../secrets/quick-create-java.md)|[Odkaz](/javascript/api/@azure/keyvault-secrets/)<br>[Rychlý start](../secrets/quick-create-node.md)|

Instalační balíčky a zdrojový kód najdete v tématu [klientské knihovny](client-libraries.md) .

Další informace o Key Vault Zabezpečení roviny dat najdete v tématu [Azure Key Vault funkce zabezpečení](security-features.md).

### <a name="code-examples"></a>Příklady kódu

Kompletní příklady použití Key Vault s vašimi aplikacemi najdete v těchto tématech:

- [Ukázky kódu Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) – ukázky kódu pro Azure Key Vault 

## <a name="how-tos"></a>Postupy

Následující články a scénáře poskytují pokyny pro práci s Azure Key Vault pro konkrétní úkoly:

- Přístup k trezoru klíčů v [Key Vault za bránou firewall](access-behind-firewall.md) – klientská aplikace trezoru klíčů musí mít přístup k několika koncovým bodům pro různé funkce.
- Postup nasazení certifikátů do virtuálních počítačů z Key Vault – [Windows](../../virtual-machines/extensions/key-vault-windows.md), [Linux](../../virtual-machines/extensions/key-vault-linux.md) – cloudová aplikace spuštěná ve virtuálním počítači v Azure potřebuje certifikát. Jak tento certifikát do tohoto virtuálního počítače získat ještě dnes?
- [Nasazení certifikátu webové aplikace Azure prostřednictvím Key Vault](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)
- Přiřaďte zásady přístupu ([](assign-access-policy-cli.md)  |  [](assign-access-policy-powershell.md)  |  [portál](assign-access-policy-portal.md)PowerShell CLI). 
- [Postup použití Key Vaultho obnovitelného odstranění pomocí](./key-vault-recovery.md) rozhraní příkazového řádku vás provede použitím a životního cyklu trezoru klíčů a různých objektů trezoru klíčů s povoleným obnovitelném odstraněním.
- [Jak během nasazení předat zabezpečené hodnoty (třeba hesla)](../../azure-resource-manager/templates/key-vault-parameter.md) – Pokud potřebujete před nasazením předat zabezpečenou hodnotu (třeba heslo), můžete tuto hodnotu Uložit jako tajný klíč v Azure Key Vault a odkazovat na hodnotu v jiných šablonách správce prostředků.

## <a name="integrated-with-key-vault"></a>Integrace s Key Vault

Tyto články se týkají dalších scénářů a služeb, které používají nebo integrují s Key Vault.

- [Šifrování v klidovém](../../security/fundamentals/encryption-atrest.md) případě umožňuje kódování (šifrování) dat, když jsou trvalá. Šifrovací klíče dat se často šifrují pomocí klíčového šifrovacího klíče v Azure Key Vault k dalšímu omezení přístupu.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) umožňuje správce vlastního klíče tenanta. Místo toho, aby váš klíč tenanta spravovala společnost Microsoft (výchozí možnost), můžete spravovat vlastní klíč tenanta, abyste vyhověli určitým předpisům, které se vztahují na vaši organizaci. Správa vlastního klíče tenanta se také označuje jako funkce Přineste si vlastní klíč (BYOK).
- [Služba privátního propojení Azure](private-link-service.md) vám umožňuje přístup ke službám Azure (například Azure Key Vault, Azure Storage a Azure Cosmos DB) a hostovaným zákaznickým a partnerským službám Azure prostřednictvím privátního koncového bodu ve vaší virtuální síti.
- Key Vault integrace s [Event Grid](../../event-grid/event-schema-key-vault.md)  umožňuje uživatelům upozornit na změnu stavu tajného klíče uloženého v trezoru klíčů. Můžete distribuovat nové verze tajných kódů do aplikací nebo je otáčet v blízkosti tajných klíčů s vypršenou platností, abyste zabránili výpadkům.
- Tajné kódy [Azure DevOps](/azure/devops/pipelines/release/azure-key-vault) můžete chránit před nevyžádaným přístupem v Key Vault.
- [Pomocí tajného kódu uloženého v Key Vault datacihly se připojte k Azure Storage](./integrate-databricks-blob-storage.md)
- Konfigurace a spuštění poskytovatele Azure Key Vault pro [ovladač tajných klíčů úložiště CSI](./key-vault-integrate-kubernetes.md) na Kubernetes

## <a name="key-vault-overviews-and-concepts"></a>Key Vault přehledy a koncepty

- [Key Vault chování podmíněného odstranění](soft-delete-overview.md) popisuje funkci, která umožňuje obnovení odstraněných objektů bez ohledu na to, zda bylo odstranění náhodné nebo úmyslné.
- [Key Vault omezení klienta](overview-throttling.md) vás orientuje na základní koncepty omezování a nabízí přístup k vaší aplikaci.
- [Key Vault Security světů](overview-security-worlds.md) popisuje vztahy mezi oblastmi a oblastmi zabezpečení.

## <a name="social"></a>Sociální sítě

- [Blog Key Vault](/archive/blogs/kv/)
- [Fórum Key Vault](https://aka.ms/kvforum)