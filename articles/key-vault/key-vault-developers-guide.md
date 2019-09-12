---
title: Azure Key Vault příručka pro vývojáře
description: Vývojáři můžou pomocí Azure Key Vault spravovat kryptografické klíče v rámci Microsoft Azure prostředí.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mbaldwin
ms.openlocfilehash: ca640b058a1d91d15c5accb9367936368511a3ef
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881604"
---
# <a name="azure-key-vault-developers-guide"></a>Azure Key Vault příručka pro vývojáře

Key Vault umožňuje zabezpečený přístup k citlivým informacím v rámci svých aplikací:

- Klíče a tajné klíče jsou chráněné bez nutnosti psát kód sami a vy je snadno budete moct použít z vašich aplikací.
- Vaše zákazníky si můžete nechat vlastnit a spravovat své vlastní klíče, abyste se mohli soustředit na poskytování základních softwarových funkcí. Tímto způsobem vaše aplikace nebudou vlastnit odpovědnost ani potenciální odpovědnost za klíče tenanta vašich zákazníků a tajné klíče.
- Vaše aplikace může používat klíče pro podepisování a šifrování. zatím udržuje klíčovou správu externí z vaší aplikace, takže vaše řešení bude vhodné jako geograficky distribuované aplikace.
- Od verze 2016 Key Vault můžou vaše aplikace spravovat certifikáty Key Vault. Další informace najdete v tématu [o klíčích, tajných klíčích a certifikátech](/rest/api/keyvault/about-keys--secrets-and-certificates).

Obecnější informace o Azure Key Vault najdete v tématu [co je Key Vault](key-vault-whatis.md).

## <a name="public-previews"></a>Veřejné náhledy

Pravidelně vydáváme ve verzi Public Preview novou funkci Key Vault. Vyzkoušejte si tyto informace a sdělte nám, co si myslíte prostřednictvím azurekeyvault@microsoft.comnaší e-mailové adresy pro zpětnou vazbu.

### <a name="storage-account-keys---july-10-2017"></a>Klíče účtu úložiště – 10. července 2017

>[!NOTE]
>Pro tuto aktualizaci Azure Key Vault jenom funkce **klíče účtu úložiště** je ve verzi Preview.

Tato verze Preview zahrnuje naši novou funkci klíčů účtu úložiště, která je dostupná prostřednictvím těchto rozhraní. [.NET/C#](/dotnet/api/microsoft.azure.keyvault/), [REST](/rest/api/keyvault/) a [PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault). 

Další informace o nové funkci klíčů účtu úložiště najdete v tématu [Přehled klíčů účtu úložiště Azure Key Vault](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>Videa

V tomto videu se dozvíte, jak vytvořit vlastní Trezor klíčů a jak ho používat z ukázkové aplikace "Hello Key Vault".

- [Úvodní příručka pro Key Vault vývojáře](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

Prostředky zmíněné ve výše uvedeném videu:

- [Azure PowerShell](https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Azure Key Vault vzorový kód](https://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Vytváření a Správa trezorů klíčů

Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů a dalších klíčů a tajných kódů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst. Spravované identity prostředků Azure usnadňují řešení tohoto problému tím, že poskytuje službám Azure automaticky spravovanou identitu ve službě Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření pro jakoukoli službu, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje. 

Další informace o spravovaných identitách pro prostředky Azure najdete v tématu [Přehled spravovaných identit](../active-directory/managed-identities-azure-resources/overview.md). Další informace o práci s AAD najdete v tématu [integrování aplikací pomocí Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

Než budete pracovat s klíči, tajnými klíči a certifikáty ve vašem trezoru klíčů, vytvoříte a spravujete svůj Trezor klíčů prostřednictvím rozhraní příkazového řádku, PowerShellu, Správce prostředků šablon nebo REST, jak je popsáno v následujících článcích:

- [Vytváření a Správa trezorů klíčů pomocí rozhraní příkazového řádku](key-vault-manage-with-cli2.md)
- [Vytváření a Správa trezorů klíčů pomocí PowerShellu](key-vault-overview.md)
- [Vytvoření trezoru klíčů a přidání tajného klíče pomocí šablony Azure Resource Manager](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Vytváření a Správa trezorů klíčů pomocí REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Kódování pomocí Key Vault

Systém správy Key Vault pro programátory se skládá z několika rozhraní. Tato část obsahuje odkazy na všechny jazyky a příklady kódu. 

### <a name="supported-programming-and-scripting-languages"></a>Podporované programovací a skriptovací jazyky

#### <a name="rest"></a>REST

Všechny prostředky Key Vault jsou přístupné prostřednictvím rozhraní REST; trezory, klíče, tajné kódy atd. 

[Odkaz na Key Vault REST API](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Reference k rozhraní .NET API pro Key Vault](/dotnet/api/microsoft.azure.keyvault).

Další informace o verzi sady .NET SDK 2. x najdete v poznámkách k [verzi](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Sada Java SDK pro Key Vault](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

V Node. js rozhraní API pro správu Key Vault a rozhraní API pro Key Vault objektů jsou oddělené. Následující přehledový článek vám umožní přístup k oběma. 

[Moduly Azure Key Vault pro Node. js](/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Knihovny Azure Key Vault pro Python](/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Azure CLI 2

[Rozhraní příkazového řádku Azure pro Key Vault](/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell pro Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

### <a name="quick-start-guides"></a>Úvodní příručky

- [Vytvořit Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Začínáme s Key Vault v Node. js](https://github.com/Azure-Samples/key-vault-node-getting-started)

### <a name="code-examples"></a>Příklady kódu

Kompletní příklady použití Key Vault s vašimi aplikacemi najdete v těchto tématech:

- [Ukázky kódu Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) – ukázky kódu pro Azure Key Vault 
- [Použití Azure Key Vault z webové aplikace](quick-create-net.md) – kurz vám pomůžou naučit se používat Azure Key Vault z webové aplikace v Azure. 

## <a name="how-tos"></a>Postupy

Následující články a scénáře poskytují pokyny pro práci s Azure Key Vault pro konkrétní úkoly:

- [Změna ID tenanta trezoru klíčů po přesunu](key-vault-subscription-move-fix.md) předplatného – když přesunete předplatné Azure z tenanta A na tenanta b, stávající trezory klíčů budou pro objekty zabezpečení (uživatelé a aplikace) v tenantovi b nepřístupné. Opravte to pomocí této příručky.
- Přístup k trezoru klíčů v [Key Vault za bránou firewall](key-vault-access-behind-firewall.md) – klientská aplikace trezoru klíčů musí mít přístup k několika koncovým bodům pro různé funkce.
- [Postup generování a přenosu klíčů chráněných modulem HSM pro Azure Key Vault](key-vault-hsm-protected-keys.md) – pomůže vám naplánovat, vygenerovat a následně přenést vlastní klíče chráněné HSM, které se budou používat s Azure Key Vault.
- [Jak během nasazení předat zabezpečené hodnoty (třeba hesla)](../azure-resource-manager/resource-manager-keyvault-parameter.md) – Pokud potřebujete před nasazením předat zabezpečenou hodnotu (třeba heslo), můžete tuto hodnotu Uložit jako tajný klíč v Azure Key Vault a odkazovat na hodnotu v jiných správce prostředků šablony.
- [Jak používat Key Vault pro rozšiřitelnou správu klíčů pomocí SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) – Konektor SQL Serveru pro Azure Key Vault umožňuje SQL Server a SQL-in-VM využít službu Azure Key Vault jako poskytovatele technologie EKM (Extensible Key Management) k ochraně svého šifrovací klíče pro aplikace – odkazy; Transparentní šifrování dat, šifrování záloh a šifrování na úrovni sloupce.
- [Postup nasazení certifikátů do virtuálních počítačů z Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) – cloudová aplikace spuštěná ve virtuálním počítači v Azure potřebuje certifikát. Jak tento certifikát do tohoto virtuálního počítače získat ještě dnes?
- [Jak nastavit Key Vault s koncovým otočením klíčů a auditováním](key-vault-key-rotation-log-monitoring.md) – Toto je návod, jak nastavit rotaci klíčů a auditování pomocí Azure Key Vault.
- [Nasazení certifikátu webové aplikace Azure prostřednictvím Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) poskytuje podrobné pokyny pro nasazení certifikátů uložených v Key Vault jako součást nabídky [App Service Certificate](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) .
- [Udělení oprávnění mnoha aplikacím pro přístup k trezoru klíčů](key-vault-group-permissions-for-apps.md) Key Vault zásada řízení přístupu podporuje až 1024 záznamů. Můžete však vytvořit Azure Active Directory skupinu zabezpečení. Přidejte do této skupiny zabezpečení všechny přidružené objekty služby a pak udělte přístup k této skupině zabezpečení Key Vault.
- Další pokyny pro konkrétní úkoly týkající se integrace a používání trezorů klíčů s Azure najdete v tématu [Ryan Novák ' Azure Resource Manager příklady šablon pro Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Postup použití Key Vaultho obnovitelného odstranění pomocí](key-vault-soft-delete-cli.md) rozhraní příkazového řádku vás provede použitím a životního cyklu trezoru klíčů a různých objektů trezoru klíčů s povoleným obnovitelném odstraněním.
- [Postup použití Key Vaultho obnovitelného odstranění pomocí prostředí PowerShell](key-vault-soft-delete-powershell.md) vás provede použitím a životního cyklu trezoru klíčů a různých objektů trezoru klíčů s povoleným obnovitelném odstraněním.

## <a name="integrated-with-key-vault"></a>Integrace s Key Vault

Tyto články se týkají dalších scénářů a služeb, které používají nebo integrují s Key Vault.

- [Azure Disk Encryption](../security/azure-security-disk-encryption.md) využívá standardní funkci [nástroje BitLocker](https://technet.microsoft.com/library/cc732774.aspx) systému Windows a funkci [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) systému Linux k poskytnutí šifrování svazku pro operační systém a datové disky. Řešení je integrované s Azure Key Vault, které vám pomůžou řídit a spravovat klíče a tajné kódy disku v předplatném trezoru klíčů, a přitom zajistit, aby všechna data v discích virtuálních počítačů byla v klidovém úložišti Azure šifrovaná.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) poskytuje možnost šifrování dat uložených v účtu. Pro správu klíčů poskytuje Data Lake Store dva režimy pro správu hlavních šifrovacích klíčů (hlavních šifrovacích klíčů), které jsou potřeba k dešifrování všech dat uložených v Data Lake Store. Můžete buď nechat Data Lake Store spravovat hlavních šifrovacích klíčů za vás, nebo se rozhodnout zachovat vlastnictví hlavních šifrovacích klíčů pomocí účtu Azure Key Vault. Při vytváření Data Lake Store účtu zadáte režim správy klíčů.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) umožňuje správce vlastního klíče tenanta. Místo toho, aby váš klíč tenanta spravovala společnost Microsoft (výchozí možnost), můžete spravovat vlastní klíč tenanta, abyste vyhověli určitým předpisům, které se vztahují na vaši organizaci. Správa vlastního klíče tenanta se také označuje jako Přineste si vlastní klíč nebo BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Key Vault přehledy a koncepty

- [Key Vault chování podmíněného odstranění](key-vault-ovw-soft-delete.md) popisuje funkci, která umožňuje obnovení odstraněných objektů bez ohledu na to, zda bylo odstranění náhodné nebo úmyslné.
- [Key Vault omezení klienta](key-vault-ovw-throttling.md) vás orientuje na základní koncepty omezování a nabízí přístup k vaší aplikaci.
- [Key Vault přehled klíčů účtu úložiště](key-vault-ovw-storage-keys.md) popisuje klíče Azure Storage účty Key Vault Integration.
- [Key Vault Security světů](key-vault-ovw-security-worlds.md) popisuje vztahy mezi oblastmi a oblastmi zabezpečení.

## <a name="social"></a>Sociální sítě

- [Blog Key Vault](https://aka.ms/kvblog)
- [Fórum Key Vault](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Podpůrné knihovny

- [Microsoft Azure Key Vault Core Library](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) poskytuje rozhraní **IKey** a **IKeyResolver** pro hledání klíčů z identifikátorů a provádění operací s klíči.
- [Rozšíření Microsoft Azure Key Vault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) poskytují rozšířené možnosti pro Azure Key Vault.
