---
title: Průvodce vývojáře pro Azure Key Vault
description: Vývojáři můžou pomocí azure key vaultu spravovat kryptografické klíče v prostředí Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: a8cb0ea9fb3c6e8388271c4274baf4ecc7282cda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247227"
---
# <a name="azure-key-vault-developers-guide"></a>Průvodce vývojáře pro Azure Key Vault

Trezor klíčů umožňuje bezpečný přístup k citlivým informacím z vašich aplikací:

- Klíče a tajné klíče jsou chráněny bez nutnosti psát kód sami a můžete je snadno používat z vašich aplikací.
- Můžete mít své zákazníky vlastní a spravovat své vlastní klíče, takže se můžete soustředit na poskytování základních softwarových funkcí. Tímto způsobem vaše aplikace nebude vlastnit odpovědnost nebo potenciální odpovědnost za klíče a tajné klíče zákazníků.
- Vaše aplikace může používat klíče pro podepisování a šifrování, ale přesto udržuje správu klíčů externí z vaší aplikace, což umožňuje, aby vaše řešení bylo vhodné jako geograficky distribuovaná aplikace.
- Správa certifikátů trezoru klíčů. Další informace naleznete v [tématu O klíčích, tajných klíčích a certifikátech](about-keys-secrets-and-certificates.md).

Obecnější informace o Azure Key Vault najdete v tématu [Co je trezor klíčů](key-vault-overview.md).

## <a name="public-previews"></a>Veřejné náhledy

Pravidelně vydáváme veřejnou verzi preview nové funkce trezoru klíčů. Vyzkoušejte tyto a dejte nám azurekeyvault@microsoft.comvědět, co si myslíte, přes , naše zpětná vazba e-mailovou adresu.

## <a name="creating-and-managing-key-vaults"></a>Vytváření a správa trezorů klíčů

Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů a dalších klíčů a tajných kódů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst. Spravované identity pro prostředky Azure zjednodušují řešení tohoto problému tím, že poskytují službám Azure automaticky spravovanou identitu ve službě Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření pro jakoukoli službu, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje. 

Další informace o spravovaných identitách pro prostředky Azure [najdete v tématu přehled spravovaných identit](../active-directory/managed-identities-azure-resources/overview.md). Další informace o práci s aad, najdete [v tématu Integrace aplikací s Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

Než začnete pracovat s klíči, tajnými klíči nebo certifikáty v trezoru klíčů, vytvoříte a spravujete trezor klíčů prostřednictvím cli, PowerShellu, šablon Správce prostředků nebo REST, jak je popsáno v následujících článcích:

- [Vytváření a správa trezorů klíčů pomocí cli](quick-create-cli.md)
- [Vytváření a správa trezorů klíčů pomocí PowerShellu](quick-create-powershell.md)
- [Vytvoření a správa trezorů klíčů pomocí portu Azure](quick-create-portal.md)
- [Vytváření a správa trezorů klíčů pomocí Pythonu](quick-create-python.md)
- [Vytváření a správa trezorů klíčů pomocí javy](quick-create-java.md)
- [Vytvoření a správa trezorů klíčů pomocí souboru Node.js](quick-create-node.md)
- [Vytvoření a správa trezorů klíčů pomocí rozhraní .NET (v4 SDK)](quick-create-net.md)
- [Vytvoření trezoru klíčů a přidání tajného klíče pomocí šablony Azure Resource Manager](quick-create-template.md)
- [Vytváření a správa trezorů klíčů pomocí rest](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Kódování pomocí trezoru klíčů

Systém správy trezoru klíčů pro programátory se skládá z několika rozhraní. Tato část obsahuje odkazy na všechny jazyky, stejně jako některé příklady kódu. 

### <a name="supported-programming-and-scripting-languages"></a>Podporované programovací a skriptovací jazyky

#### <a name="rest"></a>REST

Všechny prostředky trezoru klíčů jsou přístupné prostřednictvím rozhraní REST. trezory, klíče, tajemství atd. 

[Key Vault REST API Reference](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Odkaz rozhraní .NET API pro trezor klíčů](/dotnet/api/overview/azure/key-vault?view=azure-dotnet).

Další informace o verzi sady .NET SDK 2.x naleznete v [poznámkách k verzi](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK pro trezor klíčů](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

V souboru Node.js jsou rozhraní API pro správu úložiště klíčů a rozhraní API objektu key vault samostatné. Následující přehled článek poskytuje přístup k oběma. 

[Moduly Azure Key Vault pro soubor Node.js](/javascript/api/overview/azure/key-vault?view=azure-node-latest)

#### <a name="python"></a>Python

[Knihovny Azure Key Vault pro Python](/python/api/overview/azure/key-vault?view=azure-python)

#### <a name="azure-cli"></a>Azure CLI

[Azure CLI pro trezor klíčů](/cli/azure/keyvault?view=azure-cli-latest)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell pro trezor klíčů](/powershell/module/az.keyvault/?view=azps-3.6.1#key_vault)

### <a name="code-examples"></a>Příklady kódu

Úplné příklady použití trezoru klíčů s aplikacemi najdete v tématu:

- [Ukázky kódu úložiště klíčů Azure](https://azure.microsoft.com/resources/samples/?service=key-vault) – ukázky kódu pro Azure Key Vault. 
- [Použijte Azure Key Vault z webové aplikace](quick-create-net.md) – kurz, který vám pomůže naučit se používat Azure Key Vault z webové aplikace v Azure. 

## <a name="how-tos"></a>Postupy

Následující články a scénáře poskytují pokyny pro práci s Azure Key Vault konkrétní úkol:

- [Změna ID klienta trezoru klíčů po přesunutí předplatného](key-vault-subscription-move-fix.md) – Při přesunutí předplatného Azure z tenanta A do tenanta B, vaše existující trezory klíčů jsou nepřístupné objekty (uživatelé a aplikace) v tenantovi B. Opravte to pomocí této příručky.
- [Přístup k trezoru klíčů za bránou firewall](key-vault-access-behind-firewall.md) - Pro přístup k trezoru klíčů musí mít klientská aplikace trezoru klíčů přístup k více koncovým bodům pro různé funkce.
- [Jak generovat a přenášet klíče chráněné hsm pro Azure Key Vault](key-vault-hsm-protected-keys.md) – to vám pomůže naplánovat, generovat a pak přenést vlastní klíče chráněné hsm pro použití s Azure Key Vault.
- [Jak předat zabezpečené hodnoty (například hesla) během nasazení](../azure-resource-manager/templates/key-vault-parameter.md) – Když potřebujete předat zabezpečenou hodnotu (jako heslo) jako parametr během nasazení, můžete tuto hodnotu uložit jako tajný klíč v trezoru klíčů Azure a odkazovat na hodnotu v jiných šablonách Správce prostředků.
- [Jak používat trezor klíčů pro rozšiřitelnou správu klíčů s SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) – SQL Server Connector pro Azure Key Vault umožňuje SQL Server a SQL-in-a-VM využít službu Azure Key Vault jako zprostředkovatele rozšiřitelné správy klíčů (EKM) k ochraně jeho šifrovací klíče pro propojení aplikací; Transparentní šifrování dat, šifrování zálohování a šifrování na úrovni sloupce.
- [Jak nasadit certifikáty do virtuálních počítačů z trezoru klíčů](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) – cloudová aplikace spuštěná ve virtuálním počítači v Azure potřebuje certifikát. Jak se dostanete tento certifikát do tohoto virtuálního trhu dnes?
- [Jak nastavit trezor klíčů s střídání klíčů od začátku do konce](key-vault-key-rotation-log-monitoring.md) – to vás provede, jak nastavit střídání klíčů a auditování pomocí azure key vault.
- [Nasazení certifikátu Azure Web App prostřednictvím trezoru klíčů]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) poskytuje podrobné pokyny pro nasazení certifikátů uložených v trezoru klíčů jako součást nabídky [certifikátů služby App Service.](https://azure.microsoft.com/blog/internals-of-app-service-certificate/)
- [Udělení oprávnění mnoha aplikacím pro přístup k trezoru klíčů](key-vault-group-permissions-for-apps.md) Zásady řízení přístupu trezoru klíčů podporují až 1024 položek. Můžete však vytvořit skupinu zabezpečení služby Azure Active Directory. Přidejte všechny přidružené objekty zabezpečení do této skupiny zabezpečení a poté udělte přístup této skupině zabezpečení do trezoru klíčů.
- Další pokyny pro konkrétní úkoly při integraci a používání trezorů klíčů s Azure najdete v [tématu Ryan Jones 'Azure Resource Manager šablony příklady pro trezor klíčů](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Použití obnovitelného odstranění trezoru klíčů pomocí rozhraní PŘÍKAZOVÉHO příkazu](key-vault-soft-delete-cli.md) vás provede použitím a životním cyklem trezoru klíčů a různými objekty trezoru klíčů s povoleným softwarovým odstraněním.
- [Použití obnovitelného odstranění trezoru klíčů pomocí prostředí PowerShell](key-vault-soft-delete-powershell.md) vás provede použitím a životním cyklem trezoru klíčů a různých objektů trezoru klíčů s povoleným softwarovým odstraněním.

## <a name="integrated-with-key-vault"></a>Integrovaná s trezorem klíčů

Tyto články jsou o jiných scénářích a službách, které používají nebo integrují s trezorem klíčů.

- [Azure Disk Encryption](../security/fundamentals/encryption-overview.md) využívá standardní funkci [Nástroje bitlockeru](https://technet.microsoft.com/library/cc732774.aspx) systému Windows a funkce [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) systému Linux k zajištění šifrování svazku pro operační systém a datové disky. Toto řešení je integrované s Azure Key Vault, které vám pomůže řídit a spravovat šifrovací klíče a tajné kódy disku v předplatném trezoru klíčů a zároveň zajistí, že všechna data na discích virtuálních počítačů budou šifrována v klidovém stavu ve vašem úložišti Azure.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) poskytuje možnost šifrování dat, která je uložená v účtu. Pro správu klíčů poskytuje Úložiště dat data pro správu hlavních šifrovacích klíčů (MEK), které jsou nutné pro dešifrování všech dat uložených v úložišti data lake store. Můžete buď nechat Úložiště dat lake spravovat MEKs za vás, nebo se rozhodnete zachovat vlastnictví MEKs pomocí účtu Azure Key Vault. Při vytváření účtu Data Lake Store můžete určit režim správy klíčů.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) umožňuje správce vlastního klíče klienta. Například místo microsoftu, který spravuje váš klíč klienta (výchozí), můžete spravovat vlastní klíč klienta tak, aby splňoval konkrétní předpisy, které platí pro vaši organizaci. Správa vlastního klíče tenanta se také označuje jako funkce Přineste si vlastní klíč (BYOK).

## <a name="key-vault-overviews-and-concepts"></a>Přehledy a koncepty trezoru klíčů

- [Chování obnovitelného odstranění trezoru klíčů](key-vault-ovw-soft-delete.md) popisuje funkci, která umožňuje obnovení odstraněných objektů bez ohledu na to, zda bylo odstranění náhodné nebo úmyslné.
- [Omezení klienta trezoru klíčů](key-vault-ovw-throttling.md) vás orientuje na základní koncepty omezení a nabízí přístup pro vaši aplikaci.
- [Key Vault storage account keys overview](key-vault-ovw-storage-keys.md) describes the Key Vault integration Azure Storage Accounts keys.
- [Světy zabezpečení trezoru klíčů](key-vault-ovw-security-worlds.md) popisují vztahy mezi oblastmi a oblastmi zabezpečení.

## <a name="social"></a>Sociální

- [Blog o trezoru klíčů](https://aka.ms/kvblog)
- [Key Vault fórum](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Podpůrné knihovny

- [Základní knihovna úložiště klíčů Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) poskytuje rozhraní **IKey** a **IKeyResolver** pro vyhledání klíčů z identifikátorů a provádění operací s klíči.
- [Rozšíření trezoru klíčů Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) poskytuje rozšířené funkce pro Azure Key Vault.
