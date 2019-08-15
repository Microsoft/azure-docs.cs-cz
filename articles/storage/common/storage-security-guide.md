---
title: Azure Storage Průvodce zabezpečením | Microsoft Docs
description: Obsahuje podrobnosti o řadě způsobů zabezpečení Azure Storage, včetně omezení RBAC, Šifrování služby Storage, šifrování na straně klienta, SMB 3,0 a Azure Disk Encryption.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 548f37d6a0d4390fb98ceaee7b59314400debb38
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68986548"
---
# <a name="azure-storage-security-guide"></a>Azure Storage Průvodce zabezpečením

Azure Storage poskytuje komplexní sadu funkcí zabezpečení, které vývojářům umožňují vytvářet zabezpečené aplikace:

- Všechna data (včetně metadat) zapsaná do Azure Storage se automaticky šifrují pomocí [šifrování služby Storage (SSE)](storage-service-encryption.md). Další informace najdete v tématu [oznámení výchozího šifrování pro objekty blob Azure, soubory, tabulky a Queue Storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Azure Active Directory (Azure AD) a Access Control na základě rolí (RBAC) se podporují pro Azure Storage operací správy prostředků i operací s daty, a to takto:   
    - Role RBAC můžete přiřadit oboru úložiště k objektům zabezpečení a pomocí Azure AD autorizovat operace správy prostředků, jako je Správa klíčů.
    - Integrace Azure AD je podporovaná pro operace s daty BLOB a Queue. K objektu zabezpečení nebo spravované identitě prostředků Azure můžete přiřadit role RBAC v oboru pro předplatné, skupinu prostředků, účet úložiště nebo jednotlivý kontejner nebo frontu. Další informace najdete v tématu [ověření přístupu k Azure Storage pomocí Azure Active Directory](storage-auth-aad.md).   
- Data je možné zabezpečit při přenosu mezi aplikací a Azure pomocí šifrování na [straně klienta](../storage-client-side-encryption.md), HTTPS nebo SMB 3,0.  
- Operační systémy a datové disky používané virtuálními počítači Azure je možné šifrovat pomocí [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
- Delegovaný přístup k datovým objektům v Azure Storage lze udělit pomocí sdíleného přístupového podpisu. Další informace najdete v tématu [udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)](storage-sas-overview.md).

Tento článek obsahuje přehled každé z těchto funkcí zabezpečení, které lze použít s Azure Storage. Odkazy jsou uvedeny v článcích, které poskytují podrobné informace o jednotlivých funkcích, abyste mohli snadno provádět další šetření v každém tématu.

Tady jsou témata, která se týkají tohoto článku:

* [Zabezpečení roviny správy](#management-plane-security) – zabezpečení účtu úložiště

  Rovina správy se skládá z prostředků, které se používají ke správě svého účtu úložiště. Tato část popisuje model nasazení Azure Resource Manager a jak používat Access Control na základě rolí (RBAC) k řízení přístupu k vašim účtům úložiště. Také řeší správu klíčů účtu úložiště a jejich opětovné vygenerování.
* [Zabezpečení roviny dat](#data-plane-security) – zabezpečení přístupu k datům

  V této části se podíváme na povolení přístupu k skutečným datovým objektům ve vašem účtu úložiště, jako jsou objekty blob, soubory, fronty a tabulky, pomocí sdílených přístupových podpisů a zásad uloženého přístupu. Zaměříme SAS na úrovni služby i SAS na úrovni účtu. Také se dozvíte, jak omezit přístup k konkrétní IP adrese (nebo rozsahu IP adres), jak omezit protokol používaný na HTTPS a jak odvolat sdílený přístupový podpis bez čekání na vypršení platnosti.
* [Šifrování během přenosu](#encryption-in-transit)

  Tato část popisuje, jak zabezpečit data při jejich přenosu do Azure Storage nebo z ní. Budeme mluvit o doporučeném použití HTTPS a šifrování, které používá SMB 3,0 pro sdílené složky Azure. Také se podíváme na šifrování na straně klienta, které vám umožní šifrovat data před jejich přenosem do úložiště v klientské aplikaci a dešifrovat data po přenosu z úložiště.
* [Šifrování v klidovém stavu](#encryption-at-rest)

  Budeme hovořit o Šifrování služby Storage (SSE), která je teď automaticky povolená pro nové a existující účty úložiště. Také se podíváme na to, jak můžete Azure Disk Encryption použít a prozkoumat základní rozdíly a případy šifrování disku proti SSE a šifrování na straně klienta. Krátce se podíváme na dodržování standardu FIPS pro USA. Státní počítače.
* Auditování přístupu Azure Storage pomocí [Analýza úložiště](#storage-analytics)

  Tato část popisuje, jak najít informace v protokolech analýzy úložiště pro požadavek. Podíváme se na data protokolu pro analýzu reálného úložiště a zjistěte, jak nerozlišuje, jestli se požadavek provádí pomocí klíče účtu úložiště, se sdíleným přístupovým podpisem nebo anonymně a jestli byl úspěšný nebo neúspěšný.
* [Povolení klientů založených na prohlížeči pomocí CORS](#cross-origin-resource-sharing-cors)

  V této části se seznámíte s postupem povolení sdílení prostředků mezi zdroji (CORS). Budeme hovořit o přístupu mezi doménami a o tom, jak ho zpracovat pomocí možností CORS integrovaných do Azure Storage.

## <a name="management-plane-security"></a>Zabezpečení roviny správy
Rovina správy se skládá z operací, které mají vliv na samotný účet úložiště. Můžete například vytvořit nebo odstranit účet úložiště, získat seznam účtů úložiště v rámci předplatného, načíst klíče účtu úložiště nebo znovu vygenerovat klíče účtu úložiště.

Když vytváříte nový účet úložiště, vyberete model nasazení Classic nebo Správce prostředků. Klasický model vytváření prostředků v Azure umožňuje jenom přístup k předplatnému a k tomuto předplatnému a zároveň účet úložiště.

Tato příručka se zaměřuje na model Správce prostředků, který je doporučeným prostředkem pro vytváření účtů úložiště. Pomocí Správce prostředků účtů úložiště místo poskytnutí přístupu k celému předplatnému můžete řídit přístup k úrovni správy na více omezenou úroveň pomocí Access Control na základě rolí (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Jak zabezpečit svůj účet úložiště pomocí Access Control na základě rolí (RBAC)
Pojďme se obrátit na to, co je RBAC, a jak se dá používat. Každé předplatné Azure zahrnuje službu (adresář) Azure Active Directory. Uživatelům, skupinám a aplikacím z tohoto adresáře se dá udělit přístup ke správě prostředků v předplatném Azure, které používají model nasazení Správce prostředků. Tento typ zabezpečení se označuje jako Access Control na základě rolí (RBAC). Ke správě tohoto přístupu můžete použít [Azure Portal](https://portal.azure.com/), [nástroje Azure CLI](../../cli-install-nodejs.md), [POWERSHELL](/powershell/azureps-cmdlets-docs)nebo [poskytovatele prostředků Azure Storage REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Pomocí modelu Správce prostředků umístíte účet úložiště do skupiny prostředků a řídíte přístup k rovině správy tohoto konkrétního účtu úložiště pomocí Azure Active Directory. Můžete například udělit konkrétním uživatelům přístup k klíčům účtu úložiště, zatímco jiní uživatelé mohou zobrazit informace o účtu úložiště, ale nemají přístup k klíčům účtu úložiště.

#### <a name="granting-access"></a>Udělení přístupu
Přístup je udělen přiřazením příslušné role RBAC uživatelům, skupinám a aplikacím ve správném oboru. Pokud chcete udělit přístup k celému předplatnému, přiřadíte roli na úrovni předplatného. Přístup ke všem prostředkům ve skupině prostředků můžete udělit tím, že udělíte oprávnění k samotné skupině prostředků. Můžete také přiřadit konkrétní role konkrétním prostředkům, jako jsou například účty úložiště.

Tady jsou hlavní body, které potřebujete znát o použití RBAC pro přístup k operacím správy účtu Azure Storage:

* Když přiřadíte přístup, v zásadě přiřadíte roli účtu, ke kterému chcete mít přístup. Můžete řídit přístup k operacím, které slouží ke správě tohoto účtu úložiště, ale nikoli k datovým objektům v účtu. Můžete například udělit oprávnění k načtení vlastností účtu úložiště (například redundanci), ale ne kontejneru nebo dat v rámci kontejneru v Blob Storage.
* Aby někdo mohl mít oprávnění k přístupu k datovým objektům v účtu úložiště, můžete jim udělit oprávnění ke čtení klíčů účtu úložiště a tento uživatel pak může tyto klíče použít pro přístup k objektům blob, frontám, tabulkám a souborům.
* Role je možné přiřadit k určitému uživatelskému účtu, skupině uživatelů nebo konkrétní aplikaci.
* Každá role má seznam akcí a nikoli akcí. Například role Přispěvatel virtuálních počítačů má akci "klíče listkey", která umožňuje čtení klíčů účtu úložiště. Přispěvatel nemá akce, jako je například aktualizace přístupu pro uživatele ve službě Active Directory.
* Mezi role pro úložiště patří (ale nejsou omezené na) následující role:

  * Vlastník – může spravovat všechno, včetně přístupu.
  * Přispěvatel – můžou dělat cokoli, co vlastník může dělat s výjimkou přiřazení přístupu. Uživatel s touto rolí může zobrazit a znovu vygenerovat klíče účtu úložiště. Klíče účtu úložiště mají přístup k datovým objektům.
  * Čtenář – může zobrazit informace o účtu úložiště s výjimkou tajných klíčů. Pokud například přiřadíte roli s oprávněním čtenáře k účtu úložiště někomu jinému, můžou si zobrazit vlastnosti účtu úložiště, ale nemůžou dělat žádné změny vlastností ani zobrazit klíče účtu úložiště.
  * Přispěvatel účtu úložiště – může spravovat účet úložiště – může číst skupiny prostředků a prostředky předplatného a vytvářet a spravovat nasazení skupiny prostředků předplatného. Můžou také přistupovat k klíčům účtu úložiště, což zase znamená, že mají přístup k rovině dat.
  * Správce přístupu uživatelů – může spravovat přístup uživatelů k účtu úložiště. Můžou například udělit přístup čtenář konkrétnímu uživateli.
  * Přispěvatel virtuálních počítačů – může spravovat virtuální počítače, ale ne účet úložiště, ke kterému jsou připojené. Tato role může vypsat klíče účtu úložiště, což znamená, že uživatel, kterému tuto roli přiřadíte, může aktualizovat rovinu dat.

    Aby mohl uživatel vytvořit virtuální počítač, musí být schopný vytvořit odpovídající soubor VHD v účtu úložiště. Aby to bylo možné, musí být schopni načíst klíč účtu úložiště a předat ho rozhraní API pro vytvoření virtuálního počítače. Proto musí mít toto oprávnění, aby mohli vypsat klíče účtu úložiště.
* Možnost definovat vlastní role je funkce, která umožňuje vytvořit sadu akcí ze seznamu dostupných akcí, které je možné provést v prostředcích Azure.
* Aby bylo možné přiřadit roli, musí být uživatel nastaven v Azure Active Directory.
* Můžete vytvořit sestavu, která udělila nebo odvolala druh přístupu a v jakém rozsahu pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI.

#### <a name="resources"></a>Zdroje a prostředky
* [Řízení přístupu na základě role v Azure Active Directory](../../role-based-access-control/role-assignments-portal.md)

  Tento článek popisuje řízení přístupu podle role v Azure Active Directory a vysvětluje, jak funguje.
* [RBAC: Předdefinované role](../../role-based-access-control/built-in-roles.md)

  Tento článek podrobně popisuje všechny předdefinované role, které jsou k dispozici v RBAC.
* [Principy nasazení podle modelu Resource Manager a klasického nasazení](../../azure-resource-manager/resource-manager-deployment-model.md)

  Tento článek vysvětluje Správce prostředků nasazení a klasický model nasazení a vysvětluje výhody použití Správce prostředků a skupin prostředků. Vysvětluje, jak fungují poskytovatelé výpočtů, sítě a úložiště Azure v rámci modelu Správce prostředků.
* [Správa řízení přístupu na základě role pomocí REST API](../../role-based-access-control/role-assignments-rest.md)

  Tento článek popisuje, jak používat rozhraní REST API ke správě RBAC.
* [Referenční informace o rozhraní REST API pro poskytovatele prostředků Azure Storage](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Tato reference k rozhraní API popisují rozhraní API, která můžete použít ke správě svého účtu úložiště prostřednictvím kódu programu.
* [Použití rozhraní API pro ověřování Správce prostředků pro přístup k předplatným](../../azure-resource-manager/resource-manager-api-authentication.md)

  V tomto článku se dozvíte, jak ověřit pomocí rozhraní Správce prostředků API.
* [Řízení přístupu na základě role pro Microsoft Azure z Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Odkaz na video na Channel 9 z konference MS Ignite 2015. Na tomto sezení se hovoří o možnostech správy přístupu a generování sestav v Azure a probírají se osvědčené postupy pro zabezpečení přístupu k předplatným Azure pomocí Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>Správa klíčů účtu úložiště
Klíče účtu úložiště jsou 512 – bitové řetězce vytvořené Azure, které spolu s názvem účtu úložiště můžete použít pro přístup k datovým objektům uloženým v účtu úložiště, například blobům, entitám v tabulce, zprávám fronty a souborům ve sdílené složce Azure. Řízení přístupu k klíčům účtu úložiště řídí přístup k rovině dat pro tento účet úložiště.

Každý účet úložiště má dva klíče označované jako "klíč 1" a "Key 2" v [Azure Portal](https://portal.azure.com/) a v rutinách PowerShellu. Je možné je znovu vygenerovat ručně pomocí jedné z několika metod, mimo jiné použití [Azure Portal](https://portal.azure.com/), PowerShellu, rozhraní příkazového řádku Azure nebo programově pomocí klientské knihovny pro úložiště .net nebo REST API služeb Azure Storage.

Pro obnovení klíčů účtu úložiště je k dispozici několik důvodů.

* Z bezpečnostních důvodů je možné je pravidelně znovu vygenerovat.
* Klíče účtu úložiště byste znovu vygenerovali, pokud někdo spravovalý z nějakého napadení do aplikace a načetl klíč, který se pevně zakódované nebo uložil do konfiguračního souboru, a poskytne tak úplný přístup k vašemu účtu úložiště.
* Dalším případem při opětovném generování klíče je, že pokud váš tým používá Průzkumník služby Storage aplikaci, která uchovává klíč účtu úložiště, a jeden ze členů týmu opustí. Aplikace bude i nadále fungovat a poskytne jim přístup k vašemu účtu úložiště, až se odešlou. V podstatě je to hlavní důvod, kdy se vytvořily sdílené přístupové podpisy na úrovni účtu – místo uložení přístupových klíčů do konfiguračního souboru můžete použít SAS na úrovni účtu.

#### <a name="key-regeneration-plan"></a>Plán opětovného generování klíče
Nechcete jenom znovu vygenerovat klíč, který používáte, bez plánování. Pokud to uděláte, mohli byste omezit veškerý přístup k tomuto účtu úložiště, což může způsobit výrazné přerušení. To je důvod, proč existují dva klíče. Vždy byste měli znovu vygenerovat jeden klíč.

Než znovu vygenerujete klíče, ujistěte se, že máte seznam všech aplikací, které jsou závislé na účtu úložiště, a také všechny další služby, které používáte v Azure. Pokud například používáte Azure Media Services, které jsou závislé na vašem účtu úložiště, je po opětovném vygenerování klíče nutné znovu synchronizovat přístupové klíče se službou Media Service. Pokud používáte nějaké aplikace, jako je například Průzkumník služby Storage, budete také muset těmto aplikacím zadat nové klíče. Pokud máte virtuální počítače, jejichž soubory VHD jsou uložené v účtu úložiště, nebude to mít vliv na opětovné generování klíčů účtu úložiště.

Klíče můžete znovu vygenerovat v Azure Portal. Po opětovném vygenerování klíčů může trvat až 10 minut, než se synchronizují napříč službami úložiště.

Až budete připraveni, tady je obecný postup, který podrobně popisuje, jak byste měli svůj klíč změnit. V tomto případě předpokládáme, že aktuálně používáte klíč 1 a chcete změnit všechno tak, aby místo toho používalo klíč 2.

1. Znovu vygenerujte klíč 2 a ujistěte se, že je zabezpečený. To můžete provést v Azure Portal.
2. Ve všech aplikacích, kde je uložený klíč úložiště, změňte klíč úložiště tak, aby používal novou hodnotu klíče 2. Otestujte a publikujte aplikaci.
3. Po úspěšném zprovoznění všech aplikací a služeb znovu vygenerujte klíč 1. Tím se zajistí, že kdokoli, ke kterému jste nevyjádřili přístup, už nebude mít přístup k účtu úložiště.

Pokud aktuálně používáte klíč 2, můžete použít stejný postup, ale převrátíte názvy klíčů.

Můžete provést migraci za několik dní, změnit každou aplikaci tak, aby používala nový klíč a publikovat ji. Po dokončení všech těchto klíčů byste pak měli přejít zpátky a znovu vygenerovat starý klíč, aby už nefungoval.

Další možností je vložit klíč účtu úložiště do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) jako tajný klíč a nechat si z něj získat klíč. Když pak znovu vygenerujete klíč a aktualizujete Azure Key Vault, aplikace se nemusí znovu nasadit, protože se automaticky z Azure Key Vault vybere nový klíč. Všimněte si, že aplikaci můžete nechat číst klíč pokaždé, když ji potřebujete, nebo ji můžete ukládat do mezipaměti, a pokud ji při použití neproběhne, načtěte klíč znovu z Azure Key Vault.

Použití Azure Key Vault také přidává další úroveň zabezpečení klíčů úložiště. Pokud použijete tuto metodu, nikdy nebudete mít klíč úložiště pevně zakódované v konfiguračním souboru, což odstraní způsob někoho, kdo získá přístup k klíčům bez konkrétního oprávnění.

Další výhodou použití Azure Key Vault je přístup k vašim klíčům pomocí Azure Active Directory můžete také řídit. To znamená, že můžete udělit přístup k několik aplikacím, které potřebují načíst klíče z Azure Key Vault, a víte, že ostatní aplikace nebudou mít přístup k klíčům bez udělení oprávnění výslovně.

> [!NOTE]
> Microsoft doporučuje používat ve všech aplikacích současně jenom jeden z klíčů. Pokud na některých místech a v dalších klíčích 2 použijete klíč 1, nebudete moct tyto klíče otočit, aniž by aplikace ztratila přístup.

#### <a name="resources"></a>Zdroje a prostředky

* [Správa nastavení účtu úložiště na webu Azure Portal](storage-account-manage.md)
* [Referenční informace o rozhraní REST API pro poskytovatele prostředků Azure Storage](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Zabezpečení roviny dat
Zabezpečení roviny dat odkazuje na metody používané k zabezpečení datových objektů uložených v Azure Storage – objekty blob, fronty, tabulky a soubory. Zjistili jsme metody, jak šifrovat data a zabezpečení během přenosu dat, ale jak se dostanete k řízení přístupu k objektům?

Máte tři možnosti pro autorizaci přístupu k datovým objektům v Azure Storage, včetně:

- Použití Azure AD k autorizaci přístupu k kontejnerům a frontám. Azure AD poskytuje výhody proti jiným přístupům k autorizaci, včetně odebrání nutnosti ukládat tajné klíče do kódu. Další informace najdete v tématu [ověření přístupu k Azure Storage pomocí Azure Active Directory](storage-auth-aad.md). 
- Použití klíčů účtu úložiště k autorizaci přístupu přes sdílený klíč. Autorizace prostřednictvím sdíleného klíče vyžaduje uložení klíčů účtu úložiště ve vaší aplikaci, takže Microsoft doporučuje použít Azure AD, pokud je to možné.
- Použití sdílených přístupových podpisů k udělení řízených oprávnění konkrétním datovým objektům po určitou dobu.

Pro Blob Storage můžete navíc k vašim objektům blob dovolit veřejný přístup nastavením úrovně přístupu pro kontejner, který odpovídajícím způsobem obsahuje objekty blob. Pokud nastavíte přístup ke kontejneru do objektu BLOB nebo kontejneru, povolíte veřejný přístup pro čtení objektů BLOB v tomto kontejneru. To znamená, že kdokoli, kdo má adresu URL odkazující na objekt BLOB v tomto kontejneru, ho může otevřít v prohlížeči bez použití sdíleného přístupového podpisu nebo klíče účtu úložiště.

Kromě omezení přístupu přes autorizaci můžete také použít [brány firewall a virtuální sítě](storage-network-security.md) pro omezení přístupu k účtu úložiště na základě pravidel sítě.  Tento přístup umožňuje odepřít přístup k veřejnému internetovému provozu a udělovat přístup jenom ke konkrétním virtuálním sítím Azure nebo rozsahům veřejných IP adres.

### <a name="storage-account-keys"></a>Klíče účtu úložiště
Klíče účtu úložiště jsou 512 – bitové řetězce vytvořené Azure, které jsou společně s názvem účtu úložiště možné použít pro přístup k datovým objektům uloženým v účtu úložiště.

Můžete například číst objekty blob, zapisovat do front, vytvářet tabulky a upravovat soubory. Mnohé z těchto akcí je možné provést prostřednictvím Azure Portal nebo pomocí některé z mnoha Průzkumník služby Storagech aplikací. Můžete také napsat kód pro použití REST API nebo některé z klientských knihoven úložiště k provedení těchto operací.

Jak je popsáno v části [zabezpečení roviny správy](#management-plane-security), je možné udělit přístup k klíčům úložiště pro klasický účet úložiště tím, že se poskytne úplný přístup k předplatnému Azure. Přístup k klíčům úložiště pro účet úložiště pomocí modelu Azure Resource Manager můžete ovládat pomocí Access Control na základě rolí (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Jak delegovat přístup k objektům ve vašem účtu pomocí sdílených přístupových podpisů a zásad uloženého přístupu
Sdílený přístupový podpis je řetězec, který obsahuje token zabezpečení, který může být připojen k identifikátoru URI, který umožňuje delegovat přístup k objektům úložiště a zadat omezení, jako jsou oprávnění a rozsah přístupu a data/času.

Můžete udělit přístup k objektům blob, kontejnerům, zprávám fronty, souborům a tabulkám. Díky tabulkám můžete ve skutečnosti udělit oprávnění k přístupu k rozsahu entit v tabulce zadáním rozsahu oddílů a klíčů řádků, ke kterým chcete, aby uživatel měl přístup. Například pokud máte data uložená pomocí klíče oddílu zeměpisného stavu, mohli by někomu dát přístup jenom k datům pro Kalifornie.

V jiném příkladu můžete předat webové aplikaci token SAS, který umožňuje zapisování položek do fronty a přidělení role pracovního procesu k získání zpráv z fronty a jejich zpracování. Nebo můžete jednomu zákazníkovi poskytnout token SAS, který můžou použít k nahrání obrázků do kontejneru v Blob Storage a poskytnutí oprávnění webové aplikace pro čtení těchto obrázků. V obou případech se jedná o oddělení obav – každá aplikace může mít k dispozici jenom přístup, který potřebují k provedení jejich úkolu. To je možné prostřednictvím použití sdílených přístupových podpisů.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Proč chcete použít sdílené přístupové podpisy
Proč byste místo toho chtěli použít SAS místo jenom pro svůj klíč účtu úložiště, což je mnohem jednodušší? Zadáním klíče účtu úložiště se dozvíte, jak se sdílí klíče vašeho úložného království. Udělí úplný přístup. Někdo může použít vaše klíče a nahrát celou knihovnu hudby do svého účtu úložiště. Mohly by také nahradit soubory napadenými verzemi nebo ukrást vaše data. Neomezený přístup k vašemu účtu úložiště je něco, co by nemělo být nejenom lehce.

Pomocí sdílených přístupových podpisů můžete klientovi udělit pouze oprávnění potřebná po omezené množství času. Pokud třeba někdo do svého účtu nahrává objekt blob, můžete jim udělit přístup pro zápis, který vám stačí dostatek času k nahrání objektu BLOB (v závislosti na velikosti objektu blob, samozřejmě). A pokud změníte svůj názor, můžete tento přístup odvolat.

Navíc můžete určit, že požadavky vytvořené pomocí SAS jsou omezené na určitou IP adresu nebo rozsah IP adres, který je externí pro Azure. Můžete taky požadovat, aby se žádosti provedly pomocí konkrétního protokolu (HTTPS nebo HTTP/HTTPS). To znamená, že pokud chcete pouze provoz HTTPS, můžete nastavit požadovaný protokol pouze na HTTPS a přenosy protokolu HTTP budou zablokovány.

#### <a name="definition-of-a-shared-access-signature"></a>Definice sdíleného přístupového podpisu
Sdílený přístupový podpis je sada parametrů dotazů připojených k adrese URL odkazující na prostředek.

poskytuje informace o povoleném přístupu a dobu, po kterou je povolen přístup. Tady je příklad; Tento identifikátor URI poskytuje přístup pro čtení k objektu BLOB po dobu pěti minut. Všimněte si, že parametry dotazu SAS musí být kódované v adrese URL, například% 3A pro dvojtečku (:) nebo% 20 pro mezeru.

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Způsob autorizace sdíleného přístupového podpisu službou Azure Storage
Když služba úložiště požadavek přijme, provede vstupní parametry dotazu a vytvoří podpis pomocí stejné metody jako volající program. Pak porovnává tyto dva signatury. Pokud souhlasí, služba úložiště může zkontrolovat verzi služby úložiště a ověřit, zda je aktuální datum a čas v zadaném okně. Zkontrolujte, zda je požadovaný přístup v rámci zadaného okna, a pak ověřte, že požadovaný přístup odpovídá žádosti, atd.

Například když adresa URL výše odkazovala na soubor namísto objektu blob, tato žádost by se nezdařila, protože určuje, že je sdílený přístupový podpis pro objekt BLOB. Pokud se volá příkaz REST, který by měl aktualizovat objekt blob, může selhat, protože sdílený přístupový podpis určuje, že je povolený jenom přístup pro čtení.

#### <a name="types-of-shared-access-signatures"></a>Typy podpisů sdíleného přístupu
* SAS na úrovni služby se dá použít pro přístup ke konkrétním prostředkům v účtu úložiště. V některých příkladech se načítá seznam objektů BLOB v kontejneru, stažení objektu blob, aktualizace entity v tabulce, přidání zpráv do fronty nebo nahrání souboru do sdílené složky.
* SAS na úrovni účtu se dá použít pro přístup k cokoli, k němuž se dá použít SAS na úrovni služby pro. Kromě toho může poskytnout možnosti pro prostředky, které nejsou povoleny u SAS na úrovni služby, jako je například schopnost vytvářet kontejnery, tabulky, fronty a sdílené složky. Můžete také zadat přístup k více službám najednou. Můžete například někomu udělit přístup k objektům blob i k souborům v účtu úložiště.

#### <a name="creating-a-sas-uri"></a>Vytváření identifikátoru URI SAS
1. Můžete vytvořit identifikátor URI na vyžádání a pokaždé definovat všechny parametry dotazu.

   Tento přístup je flexibilní, ale pokud máte logickou sadu parametrů, které jsou pokaždé podobné, použití zásad uloženého přístupu je lepší nápad.
2. Můžete vytvořit zásadu uloženého přístupu pro celý kontejner, sdílenou složku, tabulku nebo frontu. Pak můžete použít jako základ pro identifikátory URI SAS, které vytvoříte. Oprávnění založená na uložených zásadách přístupu lze snadno odvolat. Pro každý kontejner, frontu, tabulku nebo sdílenou složku můžete definovat až pět zásad.

   Například pokud chcete, aby objekty blob četli spousta lidí v konkrétním kontejneru, mohli byste vytvořit zásadu uloženého přístupu, která říká "udělení přístupu pro čtení" a všechna další nastavení, která budou pokaždé stejná. Pak můžete vytvořit identifikátor URI SAS pomocí nastavení zásad uloženého přístupu a zadat datum a čas vypršení platnosti. Výhodou tohoto je, že nemusíte pokaždé zadávat všechny parametry dotazu.

#### <a name="revocation"></a>Odvolání
Předpokládejme, že vaše SAS bylo ohrožené, nebo je chcete změnit z důvodu zabezpečení podnikových nebo legislativních požadavků. Jak odvoláte přístup k prostředku pomocí tohoto SAS? Záleží na tom, jak jste vytvořili identifikátor URI SAS.

Pokud používáte ad hoc identifikátory URI, máte tři možnosti. Tokeny SAS můžete vystavovat pomocí krátkých zásad vypršení platnosti a počkat na vypršení platnosti SAS. Prostředek můžete přejmenovat nebo odstranit (za předpokladu, že byl token omezený na jeden objekt). Můžete změnit klíče účtu úložiště. Tato poslední možnost může mít významný dopad v závislosti na tom, kolik služeb tento účet úložiště používá, a pravděpodobně není něco, co chcete udělat bez plánování.

Pokud používáte SAS odvozený od uložených zásad přístupu, můžete odebrat přístup odvoláním zásad uloženého přístupu – můžete ho jednoduše změnit, aby už vypršela jeho platnost, nebo ho můžete úplně odebrat. To se projeví okamžitě a zruší platnost všech SAS vytvořených pomocí těchto uložených zásad přístupu. Aktualizace nebo odebrání zásad uloženého přístupu může mít vliv na lidi, kteří přistupují k tomuto konkrétnímu kontejneru, sdílené složce, tabulce nebo frontě prostřednictvím SAS, ale pokud budou klienti zapsáni, aby si vyžádali nové SAS, když se stará položka neaktivuje, bude to fungovat správně.

Vzhledem k tomu, že použití SAS odvozeného z uložených zásad přístupu umožňuje okamžitě odvolat toto SAS, je doporučený osvědčený postup, pokud je to možné, vždy používejte uložené zásady přístupu.

#### <a name="resources"></a>Zdroje a prostředky
Podrobnější informace o použití sdílených přístupových podpisů a uložených zásad přístupu najdete v tématu s příklady najdete v následujících článcích:

* Toto jsou referenční články.

  * [SAS služby](https://msdn.microsoft.com/library/dn140256.aspx)

    Tento článek popisuje příklady použití SAS na úrovni služby s objekty blob, frontami zpráv, rozsahy tabulek a soubory.
  * [Sestavování SAS služby](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Sestavování SAS účtu](https://msdn.microsoft.com/library/mt584140.aspx)

* Ověřování

  * [Ověřování pro služby Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Začínáme kurz pro sdílené přístupové podpisy

  * [Kurz Začínáme SAS](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Šifrování při přenosu
### <a name="transport-level-encryption--using-https"></a>Šifrování na úrovni přenosu – pomocí protokolu HTTPS
Další krok, který byste měli vzít v úvahu, je zajistit zabezpečení Azure Storage dat pro šifrování dat mezi klientem a Azure Storage. Prvním doporučením je vždycky používat protokol [https](https://en.wikipedia.org/wiki/HTTPS) , který zajišťuje zabezpečenou komunikaci prostřednictvím veřejného Internetu.

Pokud chcete mít zabezpečený komunikační kanál, měli byste při volání rozhraní REST API nebo přístupu k objektům v úložišti vždycky používat protokol HTTPS. **Sdílené přístupové podpisy**, které se dají použít k delegování přístupu k objektům Azure Storage, zahrnují taky možnost určit, že při použití sdílených přístupových podpisů se dá použít jenom protokol HTTPS, což zajistí, že kdokoli posílá odkazy s TOKENy SAS. použije správný protokol.

Při volání rozhraní REST API pro přístup k objektům v účtech úložiště můžete vynutili použití protokolu HTTPS, a to tak, že pro účet úložiště povolíte [zabezpečený přenos vyžadovaný](../storage-require-secure-transfer.md) . Jakmile je tato možnost povolena, budou připojení pomocí protokolu HTTP odmítnuta.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Použití šifrování během přenosu s využitím sdílených složek Azure
[Soubory Azure](../files/storage-files-introduction.md) podporují šifrování prostřednictvím protokolu SMB 3,0 a HTTPS při použití REST API souboru. Když se připojujete mimo oblast Azure, ve které se nachází sdílená složka Azure, například v místním prostředí nebo v jiné oblasti Azure, je vždy potřeba protokol SMB 3,0 se šifrováním. SMB 2,1 nepodporuje šifrování, takže ve výchozím nastavení jsou připojení povolená jenom v rámci stejné oblasti v Azure, ale k vykonání [zabezpečeného přenosu](../storage-require-secure-transfer.md) pro účet úložiště se dá vyžádat protokol SMB 3,0 s šifrováním.

SMB 3,0 s šifrováním je k dispozici ve [všech podporovaných operačních systémech Windows a Windows Server](../files/storage-how-to-use-files-windows.md) s výjimkou systémů Windows 7 a windows Server 2008 R2, které podporují pouze protokol SMB 2,1. SMB 3,0 se podporuje také v [MacOS](../files/storage-how-to-use-files-mac.md) a v distribucích systému [Linux](../files/storage-how-to-use-files-linux.md) pomocí jádra Linux 4,11 a vyšší. Podpora šifrování pro protokol SMB 3,0 se také rozšířila na starší verze jádra systému Linux o několik distribucí systému Linux. Přečtěte si článek [Principy požadavků klientů SMB](../files/storage-how-to-use-files-linux.md#smb-client-reqs).

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Použití šifrování na straně klienta k zabezpečení dat, která odesíláte do úložiště
Další možnost, která vám pomůže zajistit, že vaše data jsou zabezpečená při přenosu mezi klientskou aplikací a úložištěm, je šifrování na straně klienta. Data se před přenosem do Azure Storage šifrují. Při načítání dat z Azure Storage se data po přijetí na straně klienta dešifrují. I když se data zašifrují napříč vodiči, doporučujeme také použít protokol HTTPS, protože obsahuje kontroly integrity dat, které pomáhají zmírnit chyby sítě ovlivňující integritu dat.

Šifrování na straně klienta je také metoda pro šifrování uložených dat, protože data jsou uložena v zašifrované podobě. O tom podrobněji hovoříme v části o šifrování v klidovém [umístění](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Šifrování v klidovém umístění
Existují tři funkce Azure, které zajišťují šifrování v klidovém umístění. Azure Disk Encryption slouží k šifrování disků s operačním systémem a datovými disky v Virtual Machines IaaS. Šifrování na straně klienta a SSE slouží k šifrování dat v Azure Storage. 

I když můžete použít šifrování na straně klienta k šifrování přenášených dat (což je také Uloženo v zašifrované podobě v úložišti), můžete chtít během přenosu použít protokol HTTPS a určitý způsob, jak budou data při uložení automaticky zašifrovaná. Existují dva způsoby, jak to provést Azure Disk Encryption a SSE. Ten se používá k přímému šifrování dat na discích s operačním systémem a datových discích používaných virtuálními počítači a druhý slouží k šifrování dat zapsaných do Azure Blob Storage.

### <a name="storage-service-encryption-sse"></a>Šifrování služby Storage (SSE)

SSE je povolená pro všechny účty úložiště a nedá se zakázat. SSE automaticky šifruje vaše data při jejich zápisu do Azure Storage. Když načtete data z Azure Storage, před vrácením se dešifruje pomocí Azure Storage. SSE umožňuje zabezpečit data, aniž by bylo nutné upravovat kód nebo přidávat kód do žádné aplikace.

Můžete použít buď klíče spravované společností Microsoft, nebo vlastní klíče. Společnost Microsoft generuje spravované klíče a zpracuje jejich zabezpečené úložiště i jejich běžné otočení, jak je definováno interními zásadami Microsoftu. Další informace o používání vlastních klíčů najdete v tématu [šifrování služby Storage používání klíčů spravovaných zákazníkem v Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

SSE automaticky šifruje data na všech úrovních výkonu (Standard a Premium), ve všech modelech nasazení (Azure Resource Manager a Classic) a ve všech službách Azure Storage (Blob, Queue, Table a File). 

### <a name="client-side-encryption"></a>Šifrování na straně klienta
Při projednávání šifrování dat při přenosu jsme uvedli šifrování na straně klienta. Tato funkce umožňuje programově šifrovat data v klientské aplikaci předtím, než je pošle do sítě, aby se mohla zapisovat do Azure Storage a programově dešifrovat data po jejich načtení z Azure Storage.

To zajišťuje šifrování při přenosu, ale poskytuje také funkci šifrování v klidovém režimu. I když jsou data při přenosu zašifrovaná, pořád doporučujeme používat protokol HTTPS k využití integrovaných kontrol integrity dat, které pomáhají zmírnit chyby sítě, které mají vliv na integritu dat.

Příkladem toho, kde můžete použít, je, pokud máte webovou aplikaci, která ukládá objekty BLOB a načítá objekty blob, a chcete, aby byla aplikace a data co možná nejbezpečnější. V takovém případě byste použili šifrování na straně klienta. Přenos dat mezi klientem a službou Azure Blob Service obsahuje zašifrovaný prostředek, ale nikdo nedokáže překládat data při přenosu a přetvořit je do privátních objektů BLOB.

Šifrování na straně klienta je integrované do klientských knihoven Java a .NET pro úložiště, které zase používají rozhraní API Azure Key Vault, což usnadňuje implementaci. Proces šifrování a dešifrování dat používá techniku obálky a ukládá metadata používaná šifrováním v jednotlivých objektech úložiště. Například pro objekty BLOB je uložíte v metadatech objektů blob, ale u front je přidá do každé zprávy fronty.

Pro vlastní šifrování můžete vygenerovat a spravovat vlastní šifrovací klíče. Můžete také použít klíče vygenerované knihovnou klienta Azure Storage, nebo můžete nechat Azure Key Vault vygenerovat klíče. Šifrovací klíče můžete ukládat do místního úložiště klíčů nebo je můžete uložit do Azure Key Vault. Azure Key Vault umožňuje udělit přístup k tajným klíčům v Azure Key Vault konkrétním uživatelům pomocí Azure Active Directory. To znamená, že nejenom kdokoli může přečíst Azure Key Vault a načíst klíče, které používáte pro šifrování na straně klienta.

#### <a name="resources"></a>Zdroje a prostředky
* [Šifrování a dešifrování objektů BLOB v Microsoft Azure Storage pomocí Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  V tomto článku se dozvíte, jak používat šifrování na straně klienta s Azure Key Vault, včetně toho, jak vytvořit KEK a jak ho uložit do trezoru pomocí PowerShellu.
* [Šifrování na straně klienta a Azure Key Vault pro Microsoft Azure Storage](../storage-client-side-encryption.md)

  Tento článek obsahuje vysvětlení šifrování na straně klienta a poskytuje příklady použití klientské knihovny pro úložiště k šifrování a dešifrování prostředků ze čtyř služeb úložiště. Také mluví o Azure Key Vault.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Použití Azure Disk Encryption k šifrování disků používaných vašimi virtuálními počítači
Azure Disk Encryption slouží k šifrování disků s operačním systémem a datových disků, které používá virtuální počítač s IaaS. V systému Windows se jednotky šifrují pomocí standardní technologie šifrování BitLockeru v oboru. Pro Linux jsou disky šifrované pomocí technologie DM-crypt. Tato možnost je integrovaná s Azure Key Vault, aby vám umožnila řídit a spravovat klíče pro šifrování disků.

Řešení podporuje následující scénáře pro virtuální počítače s IaaS, když jsou povolené v Microsoft Azure:

* Integrace s Azure Key Vault
* Virtuální počítače úrovně Standard: [Virtuální počítače řady a, D, DS, G, GS a tak dále. IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Povolení šifrování na virtuálních počítačích s Windows a Linux IaaS
* Zakázání šifrování v operačním systému a datových jednotkách pro virtuální počítače s Windows IaaS
* Zakázání šifrování u datových jednotek pro virtuální počítače s IaaS Linux
* Povolení šifrování na virtuálních počítačích s IaaS, na kterých běží klientský operační systém Windows
* Povolení šifrování u svazků s cestami připojení
* Povolení šifrování pro virtuální počítače se systémem Linux, které jsou konfigurovány pomocí diskového disku RAID, pomocí mdadm
* Povolení šifrování pro virtuální počítače se systémem Linux pomocí LVM pro datové disky
* Povolení šifrování u virtuálních počítačů s Windows nakonfigurovaných pomocí prostorů úložiště
* Podporují se všechny veřejné oblasti Azure.

Řešení nepodporuje následující scénáře, funkce a technologie v této verzi:

* Virtuální počítače IaaS úrovně Basic
* Zakázání šifrování na jednotce operačního systému pro virtuální počítače s IaaS Linux
* IaaS virtuální počítače, které jsou vytvořené pomocí metody vytváření virtuálních počítačů Classic
* Integrace s místní službou správy klíčů
* Soubory Azure (Shared File System), systém souborů NFS (Network File System), dynamické svazky a virtuální počítače s Windows, které jsou nakonfigurované s využitím softwarových systémů RAID


> [!NOTE]
> Na následujících distribucích systému Linux se momentálně podporuje šifrování disku s operačním systémem Linux: RHEL 7,2, CentOS 7.2 n a Ubuntu 16,04.
>
>

Tato funkce zajišťuje, že všechna data na discích virtuálních počítačů jsou v klidovém stavu zašifrovaná v Azure Storage.

#### <a name="resources"></a>Zdroje a prostředky
* [Azure Disk Encryption pro virtuální počítače s Windows a Linux IaaS](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Porovnání Azure Disk Encryption, SSE a šifrování na straně klienta

#### <a name="iaas-vms-and-their-vhd-files"></a>Virtuální počítače s IaaS a jejich soubory VHD

Pro datové disky používané virtuálními počítači IaaS se doporučuje Azure Disk Encryption. Pokud vytvoříte virtuální počítač s nespravovanými disky pomocí image z Azure Marketplace, Azure provede [kopii](https://en.wikipedia.org/wiki/Object_copying) image na účet úložiště v Azure Storage a bez ohledu na to, jestli máte povolenou SSE, se nešifruje. Po vytvoření virtuálního počítače a zahájení aktualizace image SSE začne šifrovat data. Z tohoto důvodu je nejvhodnější použít Azure Disk Encryption na virtuálních počítačích s nespravovanými disky vytvořenými z imagí v Azure Marketplace, pokud je chcete plně šifrovat. Pokud vytvoříte virtuální počítač s Managed Disks, SSE šifruje všechna data ve výchozím nastavení pomocí klíčů spravovaných platformou. 

Pokud přenesete předem zašifrovaný virtuální počítač do Azure z místního prostředí, budete moct nahrát šifrovací klíče do Azure Key Vault a nadále používat šifrování pro tento virtuální počítač, který jste používali místně. Azure Disk Encryption je povolený pro zpracování tohoto scénáře.

Pokud máte nešifrovaný virtuální pevný disk z místního prostředí, můžete ho nahrát do galerie jako vlastní image a zřídit z něj virtuální počítač. Pokud to uděláte pomocí Správce prostředků šablony, můžete požádat, aby při spuštění virtuálního počítače zapnuli Azure Disk Encryption.

Když přidáte datový disk a připojíte ho k virtuálnímu počítači, můžete na tento datový disk zapnout Azure Disk Encryption. Zašifruje tento datový disk jako první a pak bude vrstva klasického modelu nasazení provádět opožděné zápisy do úložiště, aby byl obsah úložiště zašifrovaný.

#### <a name="client-side-encryption"></a>Šifrování na straně klienta
Šifrování na straně klienta představuje nejbezpečnější metodu šifrování dat, protože šifruje data před průjezdem.  Nicméně vyžaduje, abyste do svých aplikací přidali kód pomocí úložiště, které nebudete chtít dělat. V těchto případech můžete k zabezpečení vašich dat při přenosu použít HTTPS. Jakmile data dosáhnou Azure Storage, zašifrují SSE.

Při šifrování na straně klienta můžete šifrovat entity tabulek, zprávy fronty a objekty blob. 

Šifrování na straně klienta je zcela spravováno aplikací. Jedná se o nejbezpečnější přístup, ale vyžaduje, abyste v aplikaci napravili programové změny a zavedli procesy správy klíčů na místo. Tuto část byste měli použít, když chcete během přenosu požadovat zvýšené zabezpečení a chcete, aby byla uložená data zašifrovaná.

Šifrování na straně klienta je v klientovi více zatíženo a je nutné si ho v plánech škálovatelnosti brát v úvahu, zejména pokud šifrujete a převádíte velké množství dat.

#### <a name="storage-service-encryption-sse"></a>Šifrování služby Storage (SSE)

SSE je spravován pomocí Azure Storage. SSE neposkytuje zabezpečení dat při přenosu, ale šifruje data při jejich zápisu do Azure Storage. SSE nemá vliv na výkon služby Azure Storage.

Libovolný typ dat účtu úložiště můžete šifrovat pomocí SSE (objekty blob bloku, doplňovací objekty blob, objekty blob stránky, data tabulky, data front a soubory).

Pokud máte archiv nebo knihovnu souborů VHD, které používáte jako základ pro vytváření nových virtuálních počítačů, můžete vytvořit nový účet úložiště a potom do něj nahrát soubory VHD. Tyto soubory VHD budou zašifrovány Azure Storage.

Pokud jste pro disky ve virtuálním počítači povolili Azure Disk Encryption, všechna nově zapsaná data jsou šifrována pomocí SSE a pomocí Azure Disk Encryption.

## <a name="storage-analytics"></a>Storage Analytics
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Monitorování typu autorizace pomocí Analýza úložiště
U každého účtu úložiště můžete povolit Analýza úložiště Azure provádět protokolování a ukládat data metrik. Toto je skvělý nástroj, který se použije, když chcete kontrolovat metriky výkonu účtu úložiště, nebo potřebujete řešit problémy s výkonem, protože máte problémy s výkonem.

Další část dat, kterou vidíte v protokolech služby Storage Analytics, je metoda ověřování, kterou někdo použil při přístupu k úložišti. Například pomocí Blob Storage se můžete podívat, jestli používali sdílený přístupový podpis nebo klíče účtu úložiště, nebo pokud byl přístup k objektu BLOB veřejný.

To může být užitečné, pokud úzce chráníte přístup k úložišti. Například v Blob Storage můžete nastavit všechny kontejnery na soukromé a implementovat používání služby SAS v rámci svých aplikací. Pak můžete protokoly pravidelně kontrolovat a zjistit, jestli k objektům blob přistupovali pomocí klíčů účtu úložiště, což může znamenat porušení zabezpečení, nebo pokud jsou objekty blob veřejné, ale neměly by být.

#### <a name="what-do-the-logs-look-like"></a>Jaké protokoly vypadají jako?
Jakmile povolíte metriky účtu úložiště a přihlásíte se pomocí Azure Portal, data analýzy se začnou rychle hromadit. Protokolování a metriky pro každou službu jsou oddělené. protokolování se zapisuje jenom v případě, že v tomto účtu úložiště je aktivita, zatímco metriky se budou protokolovat každou minutu, každou hodinu nebo každý den v závislosti na tom, jak ji nakonfigurujete.

Protokoly se ukládají do objektů blob bloku v kontejneru s názvem $logs v účtu úložiště. Tento kontejner se automaticky vytvoří, když je povolený Analýza úložiště. Po vytvoření tohoto kontejneru ho nemůžete odstranit, ale jeho obsah můžete odstranit.

V kontejneru $logs existuje složka pro každou službu a pak jsou podsložky pro rok/měsíc/den/hodinu. Za hodinu se protokoly číslují. To vypadá, že adresářová struktura bude vypadat takto:

![Zobrazení souborů protokolu](./media/storage-security-guide/image1.png)

Všechny požadavky na Azure Storage jsou protokolovány. Zde je snímek souboru protokolu, který zobrazuje několik prvních polí.

![Snímek souboru protokolu](./media/storage-security-guide/image2.png)

Vidíte, že protokoly můžete použít ke sledování libovolného typu volání účtu úložiště.

#### <a name="what-are-all-of-those-fields-for"></a>Jaká jsou všechna tato pole pro?
V níže uvedených zdrojích je uvedený článek, který obsahuje seznam mnoha polí v protokolech a informace o tom, k čemu se používají. Tady je seznam polí v daném pořadí:

![Snímek polí v souboru protokolu](./media/storage-security-guide/image3.png)

Zajímáme se o položky pro getblob a o tom, jak mají oprávnění, takže musíme vyhledat položky s typem operace Get-BLOB a zkontrolovat stav žádosti (čtvrtý</sup> sloupec) a typ autorizace (osmý</sup> sloupec).

Například v prvních několika řádcích v seznamu výše je požadavek-stav "úspěch" a typ autorizace je "ověřeno". To znamená, že žádost byla autorizována pomocí klíče účtu úložiště.

#### <a name="how-is-access-to-my-blobs-being-authorized"></a>Jak je povolený přístup k objektům blob?
Máme tři případy, které vás zajímají.

1. Objekt BLOB je veřejný a přistupuje se k němu pomocí adresy URL bez sdíleného přístupového podpisu. V tomto případě je požadavek-status "AnonymousSuccess" a typ autorizace je "anonymní".

   1,0; 2015-11-17T02:01:29.0488963 Z; GetBlob **AnonymousSuccess**; 200; 124; 37; **anonymní**;; mystorage...
2. Objekt BLOB je privátní a byl použit se sdíleným přístupovým podpisem. V tomto případě je požadavek-status "SASSuccess" a typ autorizace je "SAS".

   1,0; 2015-11-16T18:30:05.6556115 Z; GetBlob **SASSuccess**; 200; 416; 64; **SAS**;; mystorage...
3. Objekt BLOB je privátní a k přístupu k němu se použil klíč úložiště. V tomto případě je stav požadavku "**úspěch**" a typ autorizace je "**ověřeno**".

   1,0; 2015-11-16T18:32:24.3174537 Z; GetBlob **Úspěch**; 206; 59; 22; **ověřeno**; mystorage...

Pomocí nástroje Microsoft Message Analyzer můžete zobrazit a analyzovat tyto protokoly. Obsahuje možnosti hledání a filtrování. Například můžete chtít vyhledat instance getblob, abyste viděli, jestli využití očekáváte, to znamená, abyste se ujistili, že někdo nemá přístup k vašemu účtu úložiště nevhodným způsobem.

#### <a name="resources"></a>Zdroje a prostředky
* [Storage Analytics](../storage-analytics.md)

  Tento článek popisuje přehled služby Storage Analytics a jejich povolení.
* [Formát protokolu Analýza úložiště](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Tento článek ukazuje formát protokolu Analýza úložiště a podrobně obsahuje pole, která jsou k dispozici, včetně ověřování – typ, který označuje typ ověřování použitý pro požadavek.
* [Monitorování účtu úložiště v Azure Portal](../storage-monitor-storage-account.md)

  Tento článek ukazuje, jak nakonfigurovat monitorování metrik a protokolování pro účet úložiště.
* [Komplexní řešení potíží pomocí Azure Storage metrik a protokolování, AzCopy a analyzátoru zpráv](../storage-e2e-troubleshooting.md)

  Tento článek pojednává o řešení potíží pomocí Analýza úložiště a ukazuje, jak používat Microsoft Message Analyzer.
* [Provozní příručka pro Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)

  Tento článek je referenční příručka pro Microsoft Message Analyzer a obsahuje odkazy na kurz, rychlý Start a souhrn funkcí.

## <a name="cross-origin-resource-sharing-cors"></a>Sdílení prostředků mezi zdroji (CORS)
### <a name="cross-domain-access-of-resources"></a>Přístup k prostředkům mezi doménami
Když webový prohlížeč, který běží v jedné doméně, vytvoří požadavek HTTP na prostředek z jiné domény, nazývá se to požadavek HTTP mezi zdroji. Například stránka HTML obsluhováná z contoso.com vytvoří požadavek na JPEG hostovaný v fabrikam.blob.core.windows.net. Z bezpečnostních důvodů prohlížeče omezují požadavky HTTP mezi zdroji iniciované ve skriptech, jako je například JavaScript. To znamená, že pokud některý kód JavaScriptu na webové stránce v contoso.com požaduje, aby JPEG v fabrikam.blob.core.windows.net, nebude tento požadavek povolený v prohlížeči.

Co to udělat s Azure Storage? Při ukládání statických prostředků, jako jsou JSON nebo datové soubory XML v Blob Storage pomocí účtu úložiště s názvem Fabrikam, se doména pro prostředky fabrikam.blob.core.windows.net a webová aplikace contoso.com k nim nebude mít přístup pomocí JavaScript, protože domény se liší. To platí také v případě, že se pokoušíte zavolat jednu z Azure Storage Services – například Table Storage –, které vrátí data JSON, která budou zpracována klientem jazyka JavaScript.

#### <a name="possible-solutions"></a>Možná řešení
Jedním ze způsobů, jak tento problém vyřešit, je přiřadit vlastní doménu, jako je například "storage.contoso.com", do fabrikam.blob.core.windows.net. Problémem je, že tuto vlastní doménu můžete přiřadit jenom k jednomu účtu úložiště. Co když jsou prostředky uložené v několika účtech úložiště?

Dalším způsobem, jak tento problém vyřešit, je, aby webová aplikace fungovala jako proxy pro volání úložiště. To znamená, že pokud do Blob Storage nahráváte soubor, Webová aplikace ho buď napíše místně, a pak ho zkopíruje do Blob Storage, nebo ho načte do paměti a pak ho zapíše do Blob Storage. Alternativně můžete napsat vyhrazenou webovou aplikaci (například webové rozhraní API), která soubory nahrává místně, a zapisuje je do Blob Storage. V obou případech je nutné při určování potřeb škálovatelnosti přihlédnout k této funkci.

#### <a name="how-can-cors-help"></a>Jak může řešení CORS pomáhat?
Azure Storage umožňuje povolit CORS – mezi zdroji sdílení prostředků. Pro každý účet úložiště můžete zadat domény, které budou mít přístup k prostředkům v daném účtu úložiště. Například v našem případě můžeme na účtu úložiště fabrikam.blob.core.windows.net povolit CORS a nakonfigurovat ho tak, aby povoloval přístup k contoso.com. Pak může webová aplikace contoso.com získat přímý přístup k prostředkům v fabrikam.blob.core.windows.net.

K tomu je potřeba si uvědomit, že CORS umožňuje přístup, ale neposkytuje ověřování, které se vyžaduje pro veškerý neveřejný přístup k prostředkům úložiště. To znamená, že přístup k objektům blob máte jenom v případě, že jsou veřejné, nebo pokud jste zahrnuli sdílený přístupový podpis, který vám poskytne příslušné oprávnění. Tabulky, fronty a soubory nemají žádný veřejný přístup a vyžadují SAS.

Ve výchozím nastavení je CORS ve všech službách zakázaná. CORS můžete povolit pomocí REST API nebo klientské knihovny úložiště pro volání jedné z metod pro nastavení zásad služby. Když to uděláte, zahrnete pravidlo CORS, které je v XML. Tady je příklad pravidla CORS, které se nastavilo pomocí operace nastavení vlastností služby pro službu BLOB Service pro účet úložiště. Tuto operaci můžete provést pomocí klientské knihovny pro úložiště nebo rozhraní REST API pro Azure Storage.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Jednotlivé řádky znamenají:

* **AllowedOrigins** Tím se dozvíte, které nevyhovující domény můžou vyžádat a přijímat data ze služby úložiště. To říká, že contoso.com i fabrikam.com můžou požadovat data z Blob Storage pro určitý účet úložiště. Můžete ji také nastavit na zástupný znak (\*), aby všechny domény mohly přistupovat k žádostem.
* **Hodnota AllowedMethods** Toto je seznam metod (operací požadavků HTTP), které se dají použít při vytváření žádosti. V tomto příkladu jsou povoleny pouze operace PUT a GET. Tuto možnost můžete nastavit na zástupný znak (\*), aby bylo možné používat všechny metody.
* **AllowedHeaders** Toto jsou hlavičky požadavků, které může zdrojová doména zadat při vytváření žádosti. V tomto příkladu jsou povoleny všechny hlavičky metadat počínaje x-MS-meta-data, x-MS-meta-Target a x-MS-meta-ABC. Zástupný znak (\*) označuje, že je povolená jakákoli hlavička začínající zadanou předponou.
* **ExposedHeaders** Tím se dozvíte, která záhlaví odpovědi by měla být v prohlížeči zpřístupněna vystaviteli žádosti. V tomto příkladu budou zpřístupněna všechna záhlaví začínající řetězcem "x-MS-meta-".
* **MaxAgeInSeconds** Toto je maximální doba, po kterou bude prohlížeč ukládat do mezipaměti žádost o možnosti kontroly před výstupem. (Další informace o žádosti o kontrolu před výstupem najdete v prvním článku níže.)

#### <a name="resources"></a>Zdroje a prostředky
Další informace o CORS a o tom, jak ji povolit, najdete v těchto zdrojích.

* [Podpora sdílení prostředků mezi zdroji (CORS) pro služby Azure Storage v Azure.com](../storage-cors-support.md)

  Tento článek poskytuje přehled CORS a nastavení pravidel pro různé služby úložiště.
* [Podpora sdílení prostředků mezi zdroji (CORS) pro službu Azure Storage Services na webu MSDN](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Toto je referenční dokumentace k podpoře CORS pro služby Azure Storage Services. Obsahuje odkazy na články, které se použijí na jednotlivé služby úložiště, a ukazuje příklad a vysvětluje jednotlivé prvky v souboru CORS.
* [Microsoft Azure Storage: Představení CORS](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Toto je odkaz na úvodní článek na blogu, který oznamuje CORS a ukazuje, jak ho používat.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Nejčastější dotazy týkající se zabezpečení Azure Storage
1. **Jak mohu ověřit integritu objektů blob, které se přenáší do Azure Storage nebo z nich, když nemůžu použít protokol HTTPS?**

   Pokud z nějakého důvodu potřebujete použít protokol HTTP místo protokolu HTTPS a pracujete s objekty blob bloku, můžete k ověření integrity přenesených objektů BLOB použít kontrolu MD5. To vám pomůže s ochranou před chybami v síťové/transportní vrstvě, ale ne nutně u zprostředkovatelských útoků.

   Pokud můžete použít protokol HTTPS, který poskytuje zabezpečení na úrovni přenosu, je použití kontroly MD5 redundantní a zbytečné.

   Další informace najdete v článku [Přehled MD5 Azure Blob](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **Co je dodržování standardu FIPS pro USA Schod?**

   USA Standard FIPS (Federal Information Processing Standard) definuje kryptografické algoritmy schválené pro použití v USA. Počítačové systémy federální správy pro ochranu citlivých dat. Zapnutí režimu FIPS na Windows serveru nebo na ploše oznamuje operačnímu systému, že by se měly používat jenom šifrovací algoritmy ověřované standardem FIPS. Pokud aplikace používá algoritmy, které nedodržují předpisy, dojde k přerušení aplikací. With.NET Framework verze 4.5.2 nebo vyšší aplikace automaticky přepíná algoritmy kryptografie tak, aby používaly algoritmy kompatibilní se standardem FIPS, pokud je počítač v režimu FIPS.

   Microsoft je ponechá pro každého zákazníka a rozhodne se, jestli má být povolený režim FIPS. Věříme, že neexistují žádné přesvědčivé důvody pro zákazníky, kteří nepodléhají předpisům z oblasti státní správy, aby ve výchozím nastavení povolily režim FIPS.

### <a name="resources"></a>Zdroje a prostředky
* [Proč už nedoporučujeme "režim FIPS".](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Tento článek na blogu poskytuje přehled standardu FIPS a vysvětluje, proč ve výchozím nastavení nepovolí režim FIPS.
* [Ověřování FIPS 140](https://technet.microsoft.com/library/cc750357.aspx)

  Tento článek poskytuje informace o tom, jak produkty a kryptografické moduly Microsoftu dodržují Standard FIPS standard pro USA. Federální vláda.
* ["Kryptografie systému: Použití algoritmů kompatibilních se standardem FIPS pro šifrování, algoritmus hash a podepisování: efekty nastavení zabezpečení v systému Windows XP a novějších verzích Windows](https://support.microsoft.com/kb/811833)

  Tento článek hovoří o použití režimu FIPS ve starších počítačích s Windows.
