---
title: Průvodce vývojáře pro službu Azure Key Vault
description: Vývojáři můžou pomocí Azure Key Vault spravovat kryptografické klíče v rámci Microsoft Azure prostředí.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: 2edf1574784ec93448495086fdd05c988a3e55a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090531"
---
# <a name="azure-key-vault-developers-guide"></a>Průvodce vývojáře pro službu Azure Key Vault

Key Vault umožňuje zabezpečený přístup k citlivým informacím v rámci svých aplikací:

- Klíče a tajné klíče jsou chráněné bez nutnosti psát kód sami a vy je snadno budete moct použít z vašich aplikací.
- Vaše zákazníky si můžete nechat vlastnit a spravovat své vlastní klíče, abyste se mohli soustředit na poskytování základních softwarových funkcí. Tímto způsobem vaše aplikace nebudou vlastnit odpovědnost ani potenciální odpovědnost za klíče tenanta vašich zákazníků a tajné klíče.
- Vaše aplikace může používat klíče pro podepisování a šifrování. zatím udržuje klíčovou správu externí z vaší aplikace, takže vaše řešení bude vhodné jako geograficky distribuované aplikace.
- Správa certifikátů Key Vault. Další informace najdete v tématu [certifikáty](../certificates/about-certificates.md) .

Obecnější informace o Azure Key Vault najdete v tématu [co je Key Vault](overview.md)).

## <a name="public-previews"></a>Veřejné náhledy

Pravidelně vydáváme ve verzi Public Preview novou funkci Key Vault. Vyzkoušejte si tyto informace a sdělte nám, co si myslíte prostřednictvím azurekeyvault@microsoft.com naší e-mailové adresy pro zpětnou vazbu.

## <a name="creating-and-managing-key-vaults"></a>Vytváření a Správa trezorů klíčů

Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů a dalších klíčů a tajných kódů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst. Spravované identity prostředků Azure usnadňují řešení tohoto problému tím, že poskytuje službám Azure automaticky spravovanou identitu ve službě Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření pro jakoukoli službu, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje. 

Další informace o spravovaných identitách pro prostředky Azure najdete v tématu [Přehled spravovaných identit](../../active-directory/managed-identities-azure-resources/overview.md). Další informace o práci s Azure AD najdete v tématu [Integrace aplikací s Azure Active Directory](../../active-directory/develop/active-directory-integrating-applications.md).

Než budete pracovat s klíči, tajnými klíči a certifikáty ve vašem trezoru klíčů, vytvoříte a spravujete svůj Trezor klíčů prostřednictvím rozhraní příkazového řádku, PowerShellu, Správce prostředků šablon nebo REST, jak je popsáno v následujících článcích:

- [Vytváření a Správa trezorů klíčů pomocí rozhraní příkazového řádku](../secrets/quick-create-cli.md)
- [Vytváření a Správa trezorů klíčů pomocí PowerShellu](../secrets/quick-create-powershell.md)
- [Vytváření a Správa trezorů klíčů pomocí Azure Portal](../secrets/quick-create-portal.md)
- [Vytváření a Správa trezorů klíčů pomocí Pythonu](../secrets/quick-create-python.md)
- [Vytváření a Správa trezorů klíčů pomocí Java](../secrets/quick-create-java.md)
- [Vytváření a Správa trezorů klíčů pomocí Node.js](../secrets/quick-create-node.md)
- [Vytváření a Správa trezorů klíčů pomocí .NET (v4 SDK)](../secrets/quick-create-net.md)
- [Vytvoření trezoru klíčů a přidání tajného klíče pomocí šablony Azure Resource Manager](../secrets/quick-create-template.md)
- [Vytváření a Správa trezorů klíčů pomocí REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Kódování pomocí Key Vault

Systém správy Key Vault pro programátory se skládá z několika rozhraní. Tato část obsahuje odkazy na všechny jazyky a příklady kódu. 

### <a name="supported-programming-and-scripting-languages"></a>Podporované programovací a skriptovací jazyky

#### <a name="rest"></a>REST

Všechny prostředky Key Vault jsou přístupné prostřednictvím rozhraní REST; trezory, klíče, tajné kódy atd. 

[Odkaz na Key Vault REST API](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Reference k rozhraní .NET API pro Key Vault](/dotnet/api/overview/azure/key-vault?view=azure-dotnet).

Další informace o verzi sady .NET SDK 2. x najdete v [poznámkách k verzi](dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Sada Java SDK pro Key Vault](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

V Node.js jsou rozhraní API pro správu Key Vault a rozhraní Key Vault objektů API oddělené. Následující přehledový článek vám umožní přístup k oběma. 

[Azure Key Vault moduly pro Node.js](https://docs.microsoft.com/javascript/api/overview/azure/key-vault-index?view=azure-node-latest)

#### <a name="python"></a>Python

[Knihovny Azure Key Vault pro Python](https://docs.microsoft.com/python/api/overview/azure/key-vault-index?view=azure-python)

#### <a name="azure-cli"></a>Azure CLI

[Rozhraní příkazového řádku Azure pro Key Vault](/cli/azure/keyvault?view=azure-cli-latest)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell pro Key Vault](/powershell/module/az.keyvault/?view=azps-3.6.1#key_vault)

### <a name="code-examples"></a>Příklady kódu

Kompletní příklady použití Key Vault s vašimi aplikacemi najdete v těchto tématech:

- [Ukázky kódu Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) – ukázky kódu pro Azure Key Vault 
- [Použití Azure Key Vault z webové aplikace](../secrets/quick-create-net.md) – kurz vám pomůžou naučit se používat Azure Key Vault z webové aplikace v Azure. 

## <a name="how-tos"></a>Postupy

Následující články a scénáře poskytují pokyny pro práci s Azure Key Vault pro konkrétní úkoly:

- [Změna ID tenanta trezoru klíčů po přesunu předplatného](move-subscription.md) – když přesunete předplatné Azure z tenanta A na tenanta b, stávající trezory klíčů budou pro objekty zabezpečení (uživatelé a aplikace) v tenantovi b nepřístupné. Opravte to pomocí této příručky.
- Přístup k trezoru klíčů v [Key Vault za bránou firewall](access-behind-firewall.md) – klientská aplikace trezoru klíčů musí mít přístup k několika koncovým bodům pro různé funkce.
- [Postup generování a přenosu klíčů chráněných modulem HSM pro Azure Key Vault](../keys/hsm-protected-keys.md) – pomůže vám naplánovat, vygenerovat a následně přenést vlastní klíče chráněné HSM, které se budou používat s Azure Key Vault.
- [Jak během nasazení předat zabezpečené hodnoty (třeba hesla)](../../azure-resource-manager/templates/key-vault-parameter.md) – Pokud potřebujete před nasazením předat zabezpečenou hodnotu (třeba heslo), můžete tuto hodnotu Uložit jako tajný klíč v Azure Key Vault a odkazovat na hodnotu v jiných šablonách správce prostředků.
- [Jak používat Key Vault pro rozšiřitelnou správu klíčů pomocí SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) – Konektor SQL Serveru pro Azure Key Vault umožňuje SQL Server a SQL-in-VM k využití služby Azure Key Vault jako poskytovatele technologie EKM (Extensible Key Management) k ochraně svých šifrovacích klíčů pro aplikace. Transparentní šifrování dat, šifrování záloh a šifrování na úrovni sloupce.
- [Postup nasazení certifikátů do virtuálních počítačů z Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) – cloudová aplikace spuštěná ve virtuálním počítači v Azure potřebuje certifikát. Jak tento certifikát do tohoto virtuálního počítače získat ještě dnes?
- [Nasazení certifikátu webové aplikace Azure prostřednictvím Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) poskytuje podrobné pokyny pro nasazení certifikátů uložených v Key Vault jako součást nabídky [App Service Certificate](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) .
- [Udělení oprávnění mnoha aplikacím pro přístup k trezoru klíčů](group-permissions-for-apps.md) Key Vault zásada řízení přístupu podporuje až 1024 záznamů. Můžete však vytvořit Azure Active Directory skupinu zabezpečení. Přidejte do této skupiny zabezpečení všechny přidružené objekty služby a pak udělte přístup k této skupině zabezpečení Key Vault.
- Další pokyny pro konkrétní úkoly týkající se integrace a používání trezorů klíčů s Azure najdete v tématu [Ryan Novák ' Azure Resource Manager příklady šablon pro Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Postup použití Key Vaultho obnovitelného odstranění pomocí](soft-delete-cli.md) rozhraní příkazového řádku vás provede použitím a životního cyklu trezoru klíčů a různých objektů trezoru klíčů s povoleným obnovitelném odstraněním.
- [Postup použití Key Vaultho obnovitelného odstranění pomocí prostředí PowerShell](soft-delete-powershell.md) vás provede použitím a životního cyklu trezoru klíčů a různých objektů trezoru klíčů s povoleným obnovitelném odstraněním.

## <a name="integrated-with-key-vault"></a>Integrace s Key Vault

Tyto články se týkají dalších scénářů a služeb, které používají nebo integrují s Key Vault.

- [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md) využívá standardní funkci [nástroje BitLocker](https://technet.microsoft.com/library/cc732774.aspx) systému Windows a funkci [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systému Linux k poskytnutí šifrování svazku pro operační systém a datové disky. Řešení je integrované s Azure Key Vault, které vám pomůžou řídit a spravovat klíče a tajné kódy disku v předplatném trezoru klíčů, a přitom zajistit, aby všechna data v discích virtuálních počítačů byla v klidovém úložišti Azure šifrovaná.
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md) poskytuje možnost šifrování dat uložených v účtu. Pro správu klíčů poskytuje Data Lake Store dva režimy pro správu hlavních šifrovacích klíčů (hlavních šifrovacích klíčů), které jsou potřeba k dešifrování všech dat uložených v Data Lake Store. Můžete buď nechat Data Lake Store spravovat hlavních šifrovacích klíčů za vás, nebo se rozhodnout zachovat vlastnictví hlavních šifrovacích klíčů pomocí účtu Azure Key Vault. Při vytváření Data Lake Store účtu zadáte režim správy klíčů.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) umožňuje správce vlastního klíče tenanta. Místo toho, aby váš klíč tenanta spravovala společnost Microsoft (výchozí možnost), můžete spravovat vlastní klíč tenanta, abyste vyhověli určitým předpisům, které se vztahují na vaši organizaci. Správa vlastního klíče tenanta se také označuje jako funkce Přineste si vlastní klíč (BYOK).

## <a name="key-vault-overviews-and-concepts"></a>Key Vault přehledy a koncepty

- [Key Vault chování při obnovitelném odstranění](overview-soft-delete.md)) popisuje funkci, která umožňuje obnovení odstraněných objektů bez ohledu na to, zda došlo k náhodnému nebo úmyslnému odstranění.
- [Key Vault omezení klienta](overview-throttling.md) vás orientuje na základní koncepty omezování a nabízí přístup k vaší aplikaci.
- [Přehled klíčů účtu úložiště Key Vault](../secrets/overview-storage-keys.md)) Popisuje klíče Key Vault Integration Azure Storage accounts.
- [Key Vault Security světů](overview-security-worlds.md) popisuje vztahy mezi oblastmi a oblastmi zabezpečení.

## <a name="social"></a>Sociální sítě

- [Blog Key Vault](https://aka.ms/kvblog)
- [Fórum Key Vault](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Podpůrné knihovny

- [Microsoft Azure Key Vault Core Library](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) poskytuje rozhraní **IKey** a **IKeyResolver** pro hledání klíčů z identifikátorů a provádění operací s klíči.
- [Rozšíření Microsoft Azure Key Vault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) poskytují rozšířené možnosti pro Azure Key Vault.
