---
title: Příručka pro vývojáře Azure Key Vault
description: Vývojáři mohou spravovat kryptografické klíče v rámci prostředí Microsoft Azure pomocí Azure Key Vault.
services: key-vault
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 10/12/2017
ms.author: alleonar
ms.openlocfilehash: 35e84a22eabce0b7361c2227317baab62421c301
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-key-vault-developers-guide"></a>Příručka pro vývojáře Azure Key Vault

Key Vault umožňuje bezpečný přístup k citlivé informace z v rámci aplikace:

- Klíče a tajné klíče jsou chráněné bez nutnosti napsat kód a budete moci snadno použít z aplikace.
- Budete moci mít vlastní zákazníkům a spravovat vlastní klíče, takže se můžete soustředit na poskytování základních softwarových funkcí. Tímto způsobem vaše aplikace nebude vlastní odpovědnost nebo potenciální odpovědnosti pro vaše zákazníky klientské klíče a tajné klíče.
- Aplikace může používat klíče pro podepisování a šifrování ještě udržuje správy klíčů externí z vaší aplikace, umožňuje řešení jako vhodný jako geograficky distribuované aplikace.
- Od září 2016 vydání služby Key Vault se vaše aplikace teď můžete použít Key Vault [certifikáty](https://docs.microsoft.com/rest/api/keyvault/certificate-operations). Další informace najdete v tématu [informace o klíčích, tajných klíčů a certifikátů](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates).

Další obecné informace o Azure Key Vault najdete v tématu [co je Key Vault](key-vault-whatis.md).

## <a name="public-previews"></a>Veřejné verze Preview

Pravidelně jsme verze public preview systému novou funkci Key Vault. Vyzkoušejte tyto a dejte nám vědět, co si myslíte prostřednictvím azurekeyvault@microsoft.com, naše zpětnou vazbu e-mailovou adresu.

### <a name="storage-account-keys---july-10-2017"></a>Klíče účtu úložiště - 10 července 2017

>[!NOTE]
>Pro tuto aktualizaci Azure Key Vault pouze **klíče účtu úložiště** funkce je ve verzi preview.

Tato verze preview zahrnuje naše nové klíče účtu úložiště funkce k dispozici prostřednictvím těchto rozhraní; [.NET / C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault/), [REST](https://docs.microsoft.com/rest/api/keyvault/) a [prostředí PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/). 

Další informace o nové funkci klíče účtu úložiště najdete v tématu [přehled klíče účtu úložiště Azure Key Vault](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>Videa

Následující video ukazuje postup vytvoření trezoru klíčů a způsobu jeho použití v ukázkové aplikaci 'Hello Key Vault'.

- [Vývojáře Key Vault – úvodní příručka](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

Prostředky v zmíněné video:

- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Azure Key Vault ukázkový kód](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Vytváření a správa trezorů klíčů

Azure Key Vault poskytuje způsob, jak bezpečně uložit přihlašovací údaje a další klíče a tajné klíče, je však nutné kódu k ověření Key Vault je znovu načíst. Identita spravované služby (MSI) usnadňuje řešení tohoto problému jednodušší tím, že služby Azure automaticky spravované identity v Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault, bez nutnosti všechny přihlašovací údaje ve vašem kódu. 

Další informace o MSI najdete v tématu [spravované služby Identity (MSI) pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/msi-overview).

Další informace o práci s AAD najdete v tématu [integrace aplikací s Azure Active Directory](/active-directory/develop/active-directory-integrating-applications).

Před zahájením práce s klíče, tajné klíče ani certifikáty v trezoru klíčů, můžete vytvářet a spravovat váš trezor klíčů prostřednictvím rozhraní příkazového řádku, prostředí PowerShell, šablony Resource Manageru nebo REST, jak je popsáno v následujících článcích:

- [Vytvoření a správa trezorů klíč pomocí rozhraní příkazového řádku](key-vault-manage-with-cli2.md)
- [Vytvoření a správa trezorů klíče pomocí prostředí PowerShell](key-vault-get-started.md)
- [Vytvoření trezoru klíčů a přidat tajný klíč prostřednictvím šablonu Azure Resource Manager](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Vytvoření a správa trezorů klíč s REST](https://docs.microsoft.com/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Psaní kódu s použitím trezoru klíčů

Systém správy Key Vault pro programátory v jazyce se skládá z několika rozhraní. Tato část obsahuje odkazy na všechny jazyky a také některé exampls kódu. 

### <a name="supported-programming-and-scripting-languages"></a>Podporované programovací a skriptovací jazyky

#### <a name="rest"></a>REST

Všechny vaše prostředky Key Vault jsou přístupné přes rozhraní REST; trezory klíčů, tajné klíče, atd. 

[Referenční dokumentace rozhraní API REST trezoru klíčů](https://docs.microsoft.com/rest/api/keyvault/). 

#### <a name="net"></a>.NET

[Referenční .NET API pro Key Vault](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault) 

Další informace o verze 2.x sady SDK rozhraní .NET najdete v tématu [poznámky k verzi](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK pro trezor klíčů](https://docs.microsoft.com/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

V Node.js jsou oddělené rozhraní API pro správu Key Vault a objekt Key Vault rozhraní API. V následujícím článku Přehled umožňuje přístup do obou. 

[Moduly Azure Key Vault pro Node.js](https://docs.microsoft.com/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Knihovny Azure Key Vault pro jazyk Python](https://docs.microsoft.com/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Azure CLI 2

[Azure CLI pro trezor klíčů](https://docs.microsoft.com/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Prostředí Azure PowerShell pro trezor klíčů](https://docs.microsoft.com/powershell/module/azurerm.keyvault)

### <a name="quick-start-guides"></a>Úvodní příručky

- [Vytvoření trezoru klíčů](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Začínáme s Key Vault v Node.js](https://azure.microsoft.com/resources/samples/key-vault-node-getting-started/)

### <a name="code-examples"></a>Příklady kódu

Pomocí Key Vault s vašimi aplikacemi dokončení příklady najdete v tématu:

- [Ukázky kódu Azure Key Vault](http://www.microsoft.com/download/details.aspx?id=45343) – ukázková aplikace .NET *HelloKeyVault* a příklad Azure webové služby. 
- [Použití Azure Key Vault z webové aplikace](key-vault-use-from-web-application.md) -kurzu můžete Naučte se používat Azure Key Vault z webové aplikace v Azure. 

## <a name="how-tos"></a>Postupy

V následujících článcích a scénáře obsahují pokyny úlohy specifické pro práci s Azure Key Vault:

- [ID klienta trezoru klíčů změnu po předplatné přesunout](key-vault-subscription-move-fix.md) – když přesouváte vašeho předplatného Azure z klienta A do klienta B, vaše stávající trezorů klíčů jsou nedostupné objekty zabezpečení (uživatele a aplikace) v klientovi B. oprava pomocí tohoto průvodce.
- [Přístup k Key Vault za bránou firewall](key-vault-access-behind-firewall.md) – pro přístup k trezoru klíčů klientské aplikace trezoru klíčů, je potřeba mít přístup k více koncových bodů pro různé funkce.
- [Jak vygenerovat a Transfer HSM-Protected klíče pro Azure Key Vault](key-vault-hsm-protected-keys.md) – to vám pomůže naplánovat, generovat a potom přeneste vlastní klíčů chráněných pomocí HSM pro použití s Azure Key Vault.
- [Jak předat zabezpečené hodnoty (například hesla) během nasazení](../azure-resource-manager/resource-manager-keyvault-parameter.md) – Pokud je třeba předat hodnotu zabezpečení (třeba heslo) jako parametr během nasazení, můžete ukládat tuto hodnotu jako tajný klíč v Azure Key Vault a odkazují na hodnotu v jiných prostředků Správce šablony.
- [Jak používat Key Vault pro ekm s SQL serverem](https://msdn.microsoft.com/library/dn198405.aspx) – konektor služby SQL Server pro Azure Key Vault umožňuje systému SQL Server a SQL v VM využívat službu Azure Key Vault jako poskytovatele Extensible Key Management (EKM) k ochraně jeho šifrovací klíče pro propojení aplikace; Transparentní šifrování dat, zálohování šifrování a šifrování na úrovni sloupce.
- [Jak nasadit certifikáty do virtuálních počítačů z Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) – cloudových aplikací spuštěných ve virtuálním počítači na Azure potřebám certifikát. Jak můžete získat tento certifikát do tohoto virtuálního počítače dnes?
- [Jak nastavit Key Vault s koncová střídání klíče a auditování](key-vault-key-rotation-log-monitoring.md) – Toto provede jak nastavit střídání klíče a auditování s Azure Key Vault.
- [Nasazení certifikát webové aplikace Azure prostřednictvím Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) poskytuje podrobné pokyny pro nasazení certifikátů uložené v Key Vault jako součást [certifikát služby aplikace](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) nabídky.
- [Udělit oprávnění k mnoha aplikace pro přístup k trezoru klíčů](key-vault-group-permissions-for-apps.md) zásad řízení přístupu Key Vault podporuje pouze 16 položky. Ale můžete vytvořit skupiny zabezpečení služby Azure Active Directory. Přidejte všechny objekty přidružené služby do této skupiny zabezpečení a pak udělují přístup k této skupině zabezpečení Key Vault.
- Další pokyny specifických úkolů na integraci a trezory klíč pomocí Azure najdete v tématu [příklady šablony Azure Resource Manager Ryan Petr pro Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Jak používat Key Vault konfigurace soft odstranění pomocí rozhraní příkazového řádku](key-vault-soft-delete-cli.md) provede vás procesem použití a životního cyklu trezoru klíčů a různých objektů trezor klíčů s povoleno obnovitelného odstranění.
- [Jak používat Key Vault konfigurace soft odstranění pomocí prostředí PowerShell](key-vault-soft-delete-powershell.md) provede vás procesem použití a životního cyklu trezoru klíčů a různých objektů trezor klíčů s povoleno obnovitelného odstranění.

## <a name="integrated-with-key-vault"></a>Integrovaná se trezor klíčů

Tyto články jsou o další scénáře a službách, které používají nebo integrovat Key Vault.

- [Azure Disk Encryption](../security/azure-security-disk-encryption.md) využívá oborový standard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkce systému Windows a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkce systému Linux zajistit šifrování svazku operačního systému a datové disky. Řešení jsou integrované s Azure Key Vault můžete řídit a spravovat disku šifrovacích klíčů a tajných klíčů v trezoru klíčů předplatného, a zajistit, že všechna data v disky virtuálního počítače jsou zašifrovaná přinejmenším ve službě Azure storage.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) poskytuje možnost pro šifrování dat, který je uložený v účtu. Pro správu klíčů Data Lake Store poskytuje dva režimy pro správu hlavní šifrovacích klíčů (MEKs), které jsou požadovány pro dešifrování žádná data, která je uložená v Data Lake Store. Můžete je nechat buď Data Lake Store můžete spravovat MEKs nebo zachovejte vlastnictví MEKs pomocí účtu Azure Key Vault. Zadejte režim správy klíčů při vytváření účtu Data Lake Store. 
- [Azure Information Protection](/information-protection/plan-design/plan-implement-tenant-key) umožňuje správci vlastní klíč tenanta. Například místo Microsoft správě klíče klienta (výchozí), můžete spravovat vlastní klíč tenanta povinnost dodržovat určité předpisy, které se vztahují k vaší organizaci. Správa vlastního klíče klienta se také označuje jako přineste si vlastní klíč neboli BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Přehledy Key Vault a koncepty

- [Chování konfigurace soft odstranění Key Vault](key-vault-ovw-soft-delete.md) popisuje funkce, která umožňuje obnovení odstraněných objektů, zda byl odstraněn náhodnému nebo záměrnému.
- [Omezení klienta Key Vault](key-vault-ovw-throttling.md) vás seznámí se základními koncepty omezení a nabízí přístup pro vaši aplikaci.
- [Přehled klíčů účtu úložiště Key Vault](key-vault-ovw-storage-keys.md) popisuje klíče integrace účtech úložiště Azure Key Vault.
- [Rozdílné architektury security World Key Vault](key-vault-ovw-security-worlds.md) popisuje vztahy mezi regiony a oblasti zabezpečení.

## <a name="social"></a>Sociální

- [Blog trezoru klíčů](http://aka.ms/kvblog)
- [Fórum trezoru klíčů](http://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Podpora knihovny

- [Microsoft Azure Key Vault základní knihovna](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) poskytuje **IKey** a **IKeyResolver** rozhraní pro vyhledání klíče z identifikátory a provádění operací s klíči.
- [Rozšíření Microsoft Azure Key Vault](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) poskytuje rozšířené možnosti pro Azure Key Vault.


