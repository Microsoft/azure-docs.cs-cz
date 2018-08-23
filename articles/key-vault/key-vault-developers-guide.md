---
title: Příručka pro vývojáře Azure Key Vault
description: Vývojáři můžou používat Azure Key Vault ke správě šifrovacích klíčů v rámci prostředí Microsoft Azure.
services: key-vault
author: bryanla
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 10/12/2017
ms.author: bryanla
ms.openlocfilehash: d8554d235fdfeb5b192ddf1268e60b7bc32aabe7
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "42059075"
---
# <a name="azure-key-vault-developers-guide"></a>Příručka pro vývojáře Azure Key Vault

Key Vault umožňuje bezpečný přístup k citlivým informacím z v rámci vašich aplikací:

- Klíče a tajné klíče jsou chráněny bez nutnosti psát kód a budete moct snadno použít z vašich aplikací.
- Budete moct vlastní zákazníky a spravovat vlastní klíče, takže můžete soustředit na poskytování základních softwarových funkcí. Tímto způsobem vaše aplikace nebude vlastní odpovědnosti nebo potenciální odpovědnosti pro vaše zákazníky klientské klíče a tajné kódy.
- Vaše aplikace může používat klíče pro podepisování a šifrování ještě zajišťuje správu klíčů externí z vaší aplikace. Díky tomu vaše řešení bude vhodný jako geograficky distribuované aplikace.
- Od září 2016 verzi služby Key Vault vaše aplikace teď můžete spravovat certifikáty služby Key Vault. Další informace najdete v tématu [informace o klíčích, tajných kódů a certifikátů](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates).

Další obecné informace o službě Azure Key Vault najdete v tématu [co je Key Vault](key-vault-whatis.md).

## <a name="public-previews"></a>Verze Public Preview

Pravidelně vydáváme nové funkce služby Key Vault verze public preview. Vyzkoušejte si tyto a dejte nám vědět, co si myslíte, prostřednictvím azurekeyvault@microsoft.com, náš názor e-mailovou adresu.

### <a name="storage-account-keys---july-10-2017"></a>Klíče účtu úložiště – 10. července 2017

>[!NOTE]
>Pro tuto aktualizaci Azure Key Vault jenom **klíče účtu úložiště** funkce je ve verzi preview.

Tato verze preview zahrnuje naše nové klíče účtu úložiště funkci k dispozici prostřednictvím těchto rozhraní; [.NET / C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault/), [REST](https://docs.microsoft.com/rest/api/keyvault/) a [Powershellu](https://docs.microsoft.com/powershell/module/azurerm.keyvault/). 

Další informace o nové funkci klíče účtu úložiště najdete v tématu [přehled klíčů účtu úložiště Azure Key Vault](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>Videa

Toto video ukazuje, jak vytvořit trezor klíčů a jak ji používat z "Hello" ukázkovou aplikaci Key Vault.

- [Vývojáře pro Key Vault – úvodní příručka](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

Prostředky ve zmíněné videa:

- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Ukázky kódu Azure Key Vault](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Vytváření a správa trezorů klíčů

Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů a dalších klíčů a tajných kódů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst. Identita spravované služby (MSI) usnadňuje řešení tohoto problému tím, že poskytuje službám Azure automaticky spravovanou identitu v Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření pro jakoukoli službu, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje. 

Další informace o MSI najdete v tématu [Identity spravované služby (MSI) pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/msi-overview).

Další informace o práci s AAD najdete v tématu [integrace aplikací s Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).

Před zahájením práce s klíče, tajné kódy a certifikáty v trezoru klíčů, můžete vytvářet a spravovat váš trezor klíčů pomocí rozhraní příkazového řádku, PowerShell, šablon Resource Manageru nebo REST, jak je popsáno v následujících článcích:

- [Vytvářet a spravovat trezory klíčů pomocí rozhraní příkazového řádku](key-vault-manage-with-cli2.md)
- [Vytvářet a spravovat trezory klíčů pomocí Powershellu](key-vault-get-started.md)
- [Vytvoření trezoru klíčů a přidat tajný klíč pomocí šablony Azure Resource Manageru](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Vytvářet a spravovat trezory klíčů s využitím REST](https://docs.microsoft.com/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Psaní kódu s využitím Key Vaultu

Systému pro správu služby Key Vault pro programátory se skládá z několika rozhraní. Tato část obsahuje odkazy na všechny jazyky a také některé exampls kódu. 

### <a name="supported-programming-and-scripting-languages"></a>Podporované programovací a skriptovacích jazyků

#### <a name="rest"></a>REST

Všechny vaše prostředky služby Key Vault jsou přístupné prostřednictvím rozhraní REST trezory klíčů, tajných kódů, atd. 

[Reference k REST API trezoru klíčů](https://docs.microsoft.com/rest/api/keyvault/). 

#### <a name="net"></a>.NET

[Referenční rozhraní API .NET pro Key Vault](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault) 

Další informace o verzi 2.x sady .NET SDK najdete v článku [poznámky k verzi](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK pro službu Key Vault](https://docs.microsoft.com/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

V Node.js jsou samostatné rozhraní API pro správu služby Key Vault a objekt rozhraní API služby Key Vault. V následujícím článku Přehled poskytuje přístup k oběma. 

[Moduly Azure Key Vault pro Node.js](https://docs.microsoft.com/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Azure Key Vault knihovny pro Python](https://docs.microsoft.com/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Azure CLI 2

[Azure CLI pro službu Key Vault](https://docs.microsoft.com/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Prostředí Azure PowerShell pro službu Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault)

### <a name="quick-start-guides"></a>Úvodní příručky

- [Vytvoření služby Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Začínáme se službou Key Vault v Node.js](https://azure.microsoft.com/resources/samples/key-vault-node-getting-started/)

### <a name="code-examples"></a>Příklady kódu

Kompletní příklady použití služby Key Vault s aplikací naleznete v tématu:

- [Ukázky kódu pro Azure Key Vault](http://www.microsoft.com/download/details.aspx?id=45343) – ukázková aplikace .NET *HelloKeyVault* a tu je ukázka služby Azure web. 
- [Použití Azure Key Vault z webové aplikace](key-vault-use-from-web-application.md) -kurzu vám pomůžou získat informace tom, jak používat Azure Key Vault z webové aplikace v Azure. 

## <a name="how-tos"></a>Postupy

Následující články a scénáře obsahují pokyny týkající se úloh pro práci s Azure Key Vault:

- [Změna ID tenanta trezoru klíčů po předplatného přesunout](key-vault-subscription-move-fix.md) – když přesunete předplatné Azure z tenanta A na tenanta B, stávající trezory klíčů budou pro objekty zabezpečení (uživatelé a aplikace) v tenantu B. oprava pomocí této příručky.
- [Přístup ke službě Key Vault za bránou firewall](key-vault-access-behind-firewall.md) – Pokud chcete přistupovat k trezoru klíčů klientská aplikace trezoru klíčů musí být mít přístup k více koncovým bodům kvůli různým funkcím.
- [Postup generování a Transfer HSM-Protected klíčů pro Azure Key Vault](key-vault-hsm-protected-keys.md) – to vám pomůže při plánování, generovat a potom přeneste svůj vlastní klíče chráněné HSM pro použití s Azure Key Vault.
- [Jak předání zabezpečených hodnot (jako jsou hesla) během nasazení](../azure-resource-manager/resource-manager-keyvault-parameter.md) – když je potřeba předat zabezpečenou hodnotu (jako jsou hesla) jako parametr během nasazení, můžete ukládat tuto hodnotu jako tajný klíč v Azure Key Vault a odkazují na hodnoty v jiných prostředků Správce šablon.
- [Použití služby Key Vault pro rozšiřitelná Správa klíčů s SQL serverem](https://msdn.microsoft.com/library/dn198405.aspx) – konektor služby serveru SQL pro Azure Key Vault umožňuje systému SQL Server a SQL v počítači využívat službu Azure Key Vault jako zprostředkovatel Extensible Key Management (EKM) k ochraně jeho šifrovací klíče pro propojení aplikací; Transparentní šifrování dat, šifrování záloh a šifrování na úrovni sloupce.
- [Nasazení certifikátů do virtuálních počítačů ze služby Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) – Cloudová aplikace spuštěná ve virtuálním počítači Azure potřeb certifikát. Jak můžete získat tento certifikát do tohoto virtuálního počítače už dnes?
- [Jak nastavit službu Key Vault s kompletní obměny klíčů a auditování](key-vault-key-rotation-log-monitoring.md) – Toto provede procesem nastavení obměny klíčů a auditování pomocí služby Azure Key Vault.
- [Nasazení certifikát webové aplikace Azure pomocí služby Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) poskytuje podrobné pokyny pro nasazení certifikátů uložených v Key Vault v rámci [App Service Certificate](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) nabídky.
- [Udělení oprávnění k velkému počtu aplikací pro přístup k trezoru klíčů](key-vault-group-permissions-for-apps.md) zásad řízení přístupu služby Key Vault podporuje jenom 16 položek. Ale můžete vytvořit skupiny zabezpečení služby Azure Active Directory. Přidejte všechny přidružené instanční objekty do této skupiny zabezpečení a potom jim udělit přístup k této skupině zabezpečení do služby Key Vault.
- Další pokyny týkající se úloh na integraci a pomocí Azure Key Vault, naleznete v tématu [Ryanem Jones Příklady šablon Azure Resource Manageru pro službu Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Jak používat obnovitelné odstranění Key Vault pomocí rozhraní příkazového řádku](key-vault-soft-delete-cli.md) provede vás procesem použití a životní cyklus služby key vault a různé objekty služby key vault s obnovitelným odstraněním povolena.
- [Jak používat obnovitelné odstranění Key Vaultu s využitím Powershellu](key-vault-soft-delete-powershell.md) provede vás procesem použití a životní cyklus služby key vault a různé objekty služby key vault s obnovitelným odstraněním povolena.

## <a name="integrated-with-key-vault"></a>Integrované s využitím Key Vaultu

Tyto články jsou o další scénáře a služby, které používají nebo integrovat se službou Key Vault.

- [Azure Disk Encryption](../security/azure-security-disk-encryption.md) využívá standardní oborový [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkce Windows a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkce Linux zajišťuje šifrování pro operační systém a datové disky. Toto řešení je integrovaná s Azure Key Vault a pomáhá řídit a spravovat klíče pro šifrování disků a tajné kódy ve vašem předplatném služby key vault, přitom zajistit, že všechna data na discích virtuálních počítačů jsou zašifrovaná rest ve službě Azure storage.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) poskytuje možnost pro šifrování dat uložených v účtu. Pro správu klíčů Data Lake Store nabízí dva režimy pro správu vašich hlavních šifrovacích klíčů (MEK), které jsou požadovány pro dešifrování všechna data, která je uložena v Data Lake Store. Můžete buď nechat Data Lake Store správu hlavních šifrovacích klíčů pro vás nebo zachovejte vlastnictví hlavních šifrovacích klíčů pomocí svého účtu služby Azure Key Vault. Určení režimu správy klíčů při vytváření účtu Data Lake Store. 
- [Služba Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) vám umožní vedoucímu vlastní klíč tenanta. Například namísto Microsoft správě klíče tenanta (výchozí), můžete spravovat vlastní klíč tenanta, abyste vyhověli určitým předpisům platným, které se vztahují k vaší organizaci. Správa vlastního klíče tenanta se také označuje jako přineste si vlastní klíč, nebo BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Přehled služby Key Vault a koncepty

- [Chování obnovitelné odstranění Key Vaultu](key-vault-ovw-soft-delete.md) popisuje funkce, která umožňuje obnovení odstraněných objektů, zda byl odstraněn náhodného nebo záměrného.
- [Omezení klienta služby Key Vault](key-vault-ovw-throttling.md) vás seznámí se základními koncepty omezení využití sítě a nabízí přístup pro vaši aplikaci.
- [Přehled klíčů účtu úložiště služby Key Vault](key-vault-ovw-storage-keys.md) popisuje klíčů účtů úložiště Azure Key Vault integrace.
- [Světy zabezpečení služby Key Vault](key-vault-ovw-security-worlds.md) najdete zde popis vztahů mezi oblasti a oblasti zabezpečení.

## <a name="social"></a>Sociální

- [Blog služby Key Vault](http://aka.ms/kvblog)
- [Fórum služby Key Vault](http://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Podpora knihovny

- [Microsoft Azure Key Vault Core Library](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) poskytuje **Instrumentační klíč** a **IKeyResolver** rozhraní pro hledání klíčů z identifikátory a provádění operací s klíči.
- [Rozšíření Microsoft Azure Key Vault](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) nabízí rozšířené možnosti pro Azure Key Vault.


