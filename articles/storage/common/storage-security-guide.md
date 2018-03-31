---
title: Průvodce zabezpečením služby Azure Storage | Microsoft Docs
description: Podrobnosti o mnoho způsobů zabezpečení Azure Storage, včetně RBAC, šifrování služby úložiště, šifrování na straně klienta, protokolu SMB 3.0 a Azure Disk Encryption bez omezení.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: cshoe
ms.openlocfilehash: 73353d3b27b65298d804a138b33cdf2de23726fe
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="azure-storage-security-guide"></a>Průvodce zabezpečením služby Azure Storage

## <a name="overview"></a>Přehled

Úložiště Azure poskytuje komplexní sadu funkcí zabezpečení, které společně umožňují vývojářům vytvářet aplikace zabezpečené:

- Všechna data zapsaná do služby Azure Storage se automaticky šifruje pomocí [šifrování služby úložiště (SSE)](storage-service-encryption.md). Další informace najdete v tématu [uvedení výchozí šifrování pro Azure BLOB, soubory, Table a Queue Storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Účet úložiště, samotné lze zabezpečit pomocí řízení přístupu na základě Role a Azure Active Directory. 
- Data byla zabezpečená během přenosu mezi aplikací a Azure pomocí [šifrování na straně klienta](../storage-client-side-encryption.md), HTTPS nebo SMB 3.0.  
- Operačního systému a datové disky, které jsou používané virtuální počítače Azure se dá zašifrovat pomocí [Azure Disk Encryption](../../security/azure-security-disk-encryption.md). 
- Delegovaný přístup k datové objekty ve službě Azure Storage můžete udělit pomocí [sdílené přístupové podpisy](../storage-dotnet-shared-access-signature-part-1.md).

Tento článek obsahuje přehled každé z těchto funkcí zabezpečení, které lze použít s Azure Storage. Odkazy jsou k dispozici na články, které vám poskytne podrobnosti o jednotlivých funkcí, takže můžete snadno provést další šetření na každého tématu.

Zde jsou témata, která má být v tomto článku:

* [Zabezpečení roviny Management](#management-plane-security) – zabezpečení vašeho účtu úložiště

  Roviny správu se skládá z prostředky používat ke správě vašeho účtu úložiště. Tato část obsahuje modelu nasazení Azure Resource Manager a jak používat řízení přístupu na základě Role (RBAC) pro řízení přístupu účty úložiště. Také řeší, Správa klíčů k účtu úložiště a jak se je znovu vygenerovat.
* [Data roviny zabezpečení](#data-plane-security) – zabezpečení přístupu k datům

  V této části se podíváme povolení přístupu k objektům skutečná data ve vašem účtu úložiště, jako jsou objekty BLOB, soubory, fronty a tabulky, pomocí uložené zásady přístupu a podpisy sdíleného přístupu. Vám nabídneme SAS úrovně služby a SAS účtu úrovni. Dozvíte se jsme taky, jak omezit přístup ke konkrétní IP adresu (nebo rozsah IP adres), jak omezit protokol použitý k HTTPS a postup odvolat sdíleného přístupového podpisu bez čekání na vypršení platnosti.
* [Šifrování během přenosu](#encryption-in-transit)

  Tato část popisuje, jak k zabezpečení dat při přenosu do nebo z Azure Storage. Budeme mluvit o doporučené použití protokolu HTTPS a šifrování používá protokol SMB 3.0 pro sdílené složky Azure File. Také jsme bude trvat podívejte se na straně klienta šifrování, které vám umožní šifrování dat před přenosu do úložiště v aplikaci klienta a k dešifrování dat po se přenáší z úložiště.
* [Šifrování v klidovém stavu](#encryption-at-rest)

  Mluvíme o úložiště služby šifrování (SSE), který je teď automaticky povolené pro účty úložiště nových nebo stávajících. Podíváme se také na tom, jak můžete použít Azure Disk Encryption a prozkoumat základní rozdíly a případů šifrování disku versus SSE versus šifrování na straně klienta. Podíváme se stručně na soulad se standardem FIPS pro USA Počítače, Government.
* Pomocí [Storage Analytics](#storage-analytics) auditovat přístup k Azure Storage

  Tato část popisuje, jak najít informace o do úložiště analýzy protokolů pro žádost. Jsme budete si prohlédněte analytika úložiště skutečná data protokolu a zjistit, jak rozpoznat, zda je žádosti s s sdílený přístupový podpis, klíče účtu úložiště nebo anonymně a jestli byla úspěšná nebo neúspěšná.
* [Povolení založené na prohlížeči klientů pomocí CORS](#Cross-Origin-Resource-Sharing-CORS)

  Tato část pojednává o tom, aby prostředků mezi zdroji (CORS) pro sdílení. Budeme mluvit o přístupu mezi doménami a jak budete nakládat s možností CORS integrovaných v Azure Storage.

## <a name="management-plane-security"></a>Zabezpečení roviny Management
Roviny správu se skládá z operace, které ovlivňují přímo účet úložiště. Můžete například vytvořit nebo odstranit účet úložiště, získáte seznam účtů úložiště v předplatném, načtení klíčů účtu úložiště nebo obnovit klíče účtu úložiště.

Když vytvoříte nový účet úložiště, můžete vybrat model nasazení Classic nebo Resource Manager. Klasický model vytváření prostředků v Azure umožňuje pouze vše nebo nic přístup k předplatnému a naopak účet úložiště.

Tato příručka se zaměřuje na modelu Resource Manager, který je doporučené prostředky pro vytvoření účtů úložiště. Pomocí účty správce prostředků úložiště, na rozdíl od které umožňují přístup k celé předplatné můžete řídit přístup na určité úrovni více konečné rovinou správy pomocí řízení přístupu na základě Role (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Postupy pro zabezpečení vašeho účtu úložiště pomocí řízení přístupu na základě Role (RBAC)
Budeme mluvit o novinky RBAC a jak můžete použít. Každé předplatné Azure zahrnuje službu (adresář) Azure Active Directory. Uživatelé, skupiny a aplikací z adresáře mohou mít udělen přístup ke správě prostředků v rámci předplatného Azure, které používají model nasazení Resource Manager. Tento typ zabezpečení se označuje jako řízení přístupu na základě Role (RBAC). Chcete-li spravovat tento přístup, můžete použít [portál Azure](https://portal.azure.com/), [nástrojů příkazového řádku Azure](../../cli-install-nodejs.md), [prostředí PowerShell](/powershell/azureps-cmdlets-docs), nebo [API REST poskytovatele prostředků úložiště Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Pomocí modelu Resource Manager vložíte účet úložiště v prostředku skupiny a řízení přístupu pro správu roviny tohoto účtu konkrétní úložiště pomocí služby Azure Active Directory. Například můžete uživatelům konkrétní přístup klíče účtu úložiště jiných uživatelů můžete zobrazit informace o účtu úložiště, ale nemůže přístupové klíče účtu úložiště.

#### <a name="granting-access"></a>Udělení přístupu
Přístup uděluje přiřazením příslušné role RBAC uživatelům, skupinám a aplikace, v pravém oboru. Abyste mohli udělit přístup k celé předplatné, přiřadíte roli na úrovni předplatného. Můžete udělit přístup k všechny prostředky ve skupině prostředků tak, že udělíte oprávnění ke skupině prostředků, sám sebe. Konkrétní role můžete také přiřadit ke konkrétním prostředkům, například účty úložiště.

Zde jsou hlavní body, které potřebujete vědět o použití RBAC pro přístup k operace správy účtu úložiště Azure:

* Při přiřazení přístupu v podstatě přiřazení role k účtu, který chcete mít přístup. Můžete řídit přístup k operacím použít ke správě tohoto účtu úložiště, ale nechcete datové objekty v účtu. Například můžete udělit oprávnění k načtení vlastnosti účtu úložiště (například redundance), ale ne na kontejner nebo dat v rámci kontejneru uvnitř úložiště objektů Blob.
* Pro někoho, aby měli přístup datové objekty v účtu úložiště je můžete udělit oprávnění ke čtení klíče účtu úložiště a tento uživatel pak použije tyto klíče pro přístup k objektům BLOB, fronty, tabulky a soubory.
* Role jde přiřadit k účtu konkrétního uživatele, skupiny uživatelů nebo na konkrétní aplikaci.
* Každá role má seznam akcí a není akce. Například role Přispěvatel virtuálních počítačů má akce "listKeys", který umožňuje klíče účtu úložiště ke čtení. Přispěvatel má "Není akce" jako aktualizace přístup pro uživatele ve službě Active Directory.
* Role pro úložiště zahrnují (ale nejsou omezeny) následujících rolí:

  * Vlastník – můžou spravovat všechno včetně přístupu.
  * Přispěvatel – mohou provádět nic vlastníka můžete kromě přiřadit přístup. Někdo s touto rolí můžete zobrazit a obnovit klíče účtu úložiště. Klíče účtu úložiště se získat přístup k datové objekty.
  * Čtečka – mohou prohlížet informace o účtu úložiště, s výjimkou tajných klíčů. Například pokud přiřadíte někdo role s čtečky oprávnění pro účet úložiště, mohou prohlížet vlastnosti účtu úložiště, ale nesmí provádět změny vlastností ani zobrazení klíče účtu úložiště.
  * Přispěvatel účet úložiště – můžou spravovat účet úložiště – může číst odběru skupiny prostředků a prostředků a vytvářet a spravovat předplatné nasazení skupiny prostředků. Můžete také přístup k klíče účtu úložiště, které pak znamená, že bude mít přístup k datové roviny.
  * Správce přístupu uživatele – budou spravovat přístup uživatelů k účtu úložiště. Například se může udělit čtečky přístup pro konkrétního uživatele.
  * Virtuální počítač Přispěvatel – můžou spravovat virtuální počítače, ale není na účet úložiště, ke kterému jsou připojené. Tato role může seznam klíče účtu úložiště, což znamená, že uživatele, kterým přiřadíte tuto roli můžete aktualizovat data roviny.

    Aby uživatel k vytvoření virtuálního počítače mají nebude moci vytvořit odpovídající soubor virtuálního pevného disku v účtu úložiště. K tomu, musí být schopen načíst klíč účtu úložiště a předejte ji do rozhraní API, vytvoření virtuálního počítače. Proto musí mít toto oprávnění, může být uveden klíče účtu úložiště.
* Schopnost definovat vlastní role je funkce, která umožňuje vytvořit sadu akcí ze seznamu dostupných akcí, které lze provést na prostředky Azure.
* Předtím, než můžete jim přiřadit roli, musí uživatel nastavil v Azure Active Directory.
* Můžete vytvořit sestavu kdo udělit nebo odvolat jaký typ přístupu do a z kterého a v jaké oboru pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure.

#### <a name="resources"></a>Zdroje a prostředky
* [Řízení přístupu na základě role v Azure Active Directory](../../active-directory/role-based-access-control-configure.md)

  Tento článek popisuje řízení přístupu podle role v Azure Active Directory a vysvětluje, jak funguje.
* [RBAC: vestavěné role](../../active-directory/role-based-access-built-in-roles.md)

  Tento článek podrobně všech předdefinovaných rolí, které jsou k dispozici v RBAC.
* [Principy nasazení podle modelu Resource Manager a klasického nasazení](../../azure-resource-manager/resource-manager-deployment-model.md)

  Tento článek popisuje nasazení Resource Manager a modely nasazení classic a vysvětluje výhody použití skupin pro správce prostředků a prostředků. Vysvětluje, jak funguje Azure výpočetní, síťové a poskytovatelé úložiště v rámci modelu Resource Manager.
* [Správa řízení přístupu na základě role pomocí REST API](../../active-directory/role-based-access-control-manage-access-rest.md)

  Tento článek popisuje, jak používat rozhraní REST API ke správě RBAC.
* [Referenci rozhraní API REST poskytovatele prostředků úložiště Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Tento referenční dokumentace rozhraní API popisuje rozhraní API můžete použít ke správě vašeho účtu úložiště prostřednictvím kódu programu.
* [Příručka pro vývojáře k ověření s rozhraním API pro Azure Resource Manager](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)

  Tento článek ukazuje, jak chcete ověřit pomocí rozhraní API Resource Manager.
* [Řízení přístupu na základě role pro Microsoft Azure z Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Odkaz na video na Channel 9 z konference MS Ignite 2015. Na tomto sezení se hovoří o možnostech správy přístupu a generování sestav v Azure a probírají se osvědčené postupy pro zabezpečení přístupu k předplatným Azure pomocí Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>Správa klíčů k účtu úložiště
Klíče účtu úložiště jsou 512 bitů řetězce vytvořené Azure, které společně s názvem účtu úložiště je možné získat přístup k objektům data uložená v účtu úložiště, například, objekty BLOB, entit v rámci tabulky, fronty zpráv a soubory ve sdílené složce Azure File. Řízení přístupu k úložiště účet klíče řídí přístup k rovině data pro tento účet úložiště.

Každý účet úložiště obsahuje dva klíče, označuje jako "Klíč 1" a "Klíčem 2" v [portál Azure](http://portal.azure.com/) a rutiny prostředí PowerShell. To je možné znovu generovat ručně pomocí několika metod, včetně, mimo jiné pomocí [portál Azure](https://portal.azure.com/), prostředí PowerShell, rozhraní příkazového řádku Azure nebo programově pomocí klientské knihovny .NET úložiště nebo služby Azure Storage ROZHRANÍ REST API.

Existují z nejrůznějších důvodů znovu vygenerovat klíče účtu úložiště.

* V pravidelných intervalech z bezpečnostních důvodů může vygenerovat je.
* Klíče účtu úložiště by obnovit, pokud někdo spravovat tak, aby proniknout do aplikace a načíst klíč pevně zakódované nebo uložený v konfiguračním souboru, bude mít plný přístup k účtu úložiště.
* Jiné případ opětovné generování klíče je Pokud váš tým používá aplikaci Průzkumník úložišť, která uchovává klíč účtu úložiště a některého z členů týmu opustí. Aplikace bude nadále fungovat, bude mít přístup k účtu úložiště po jsou pryč. Toto je ve skutečnosti, které se vytvářely úrovni účtu sdílené přístupové podpisy hlavním důvodem – můžete úrovni účtu SAS místo ukládání přístupové klíče v konfiguračním souboru.

#### <a name="key-regeneration-plan"></a>Plán obnovení klíče
Nechcete právě znovu vygenerovat klíč, který používáte bez některé plánování. Pokud tak učiníte, může oříznutí veškerý přístup k tomuto účtu úložiště, což může způsobit přerušení hlavní. Z tohoto důvodu existují dva klíče. Vždy znovu generujte jeden klíč najednou.

Předtím, než můžete znovu vygenerovat klíče, ujistěte se, že máte seznam všechny aplikace, které jsou závislé na účet úložiště, a také všech služeb, které používáte v Azure. Například pokud používáte službu Azure Media Services, které jsou závislé na vašem účtu úložiště, můžete musí synchronizován, přístupové klíče s touto službou po opětovném vygenerování klíče. Pokud používáte jakékoli aplikace, jako je například Průzkumníka úložiště, musíte poskytnout nových klíčů pro tyto aplikace také. Pokud máte virtuální počítače, jejichž soubory VHD jsou uloženy v účtu úložiště, se nebude mít vliv opakované generování klíčů účtu úložiště.

Můžete znovu vygenerovat klíče na portálu Azure. Jakmile budou klíče obnovovány, že může trvat až 10 minut k synchronizaci mezi služby úložiště.

Až budete připraveni, tady je obecný postup s podrobnostmi o tom, jak byste měli změnit váš klíč. V takovém případě se předpokládá je, že používáte klíč 1 a chcete změnit všechno místo toho použít klíč 2.

1. Znovu vygenerujte klíč 2: Zajistěte, aby byl zabezpečený. Můžete provést na portálu Azure.
2. Ve všech aplikacích, které se uloží klíč úložiště změňte klíč úložiště použít novou hodnotu 2 klíč. Testování a publikování aplikace.
3. Po všech aplikací a služeb jsou a úspěšně spuštěn, znovu vygenerovat klíč 1. To zajišťuje, že kdokoliv kterému jste nedali výslovně nový klíč bude už nebude mít přístup k účtu úložiště.

Pokud aktuálně používáte klíč 2, můžete použít stejný postup, ale reverse názvy klíčů.

Můžete migrovat přes několik dní, změna každou aplikaci, aby pomocí nového klíče a jeho publikování. Poté, co se dokončí všechny z nich, by měl poté přejděte zpět a obnovit původní klíč, přestane fungovat.

Další možností je se umístí klíč účtu úložiště [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) jako tajný klíč a mít aplikace načíst klíč z ní. Pak když znovu vygenerovat klíč a aktualizovat Azure Key Vault, aplikace nebude muset znovu nasadit, protože jejich vyzvedne, až bude nový klíč z Azure Key Vault automaticky. Všimněte si, zda máte aplikaci načíst klíč pokaždé, když to potřebujete, nebo můžete mezipaměti v paměti a pokud se nezdaří, při použití, načíst klíč znovu z Azure Key Vault.

Použití Azure Key Vault, přidá se také další úroveň zabezpečení pro vaše klíče úložiště. Pokud použijete tuto metodu, budete mít klíče pevně zakódované úložiště nikdy v konfiguračním souboru, který odebere tento způsob někdo získání přístupu ke klíčům bez konkrétní oprávnění.

Další výhodou použití Azure Key Vault je že také můžete řídit přístup k vaše klíče pomocí služby Azure Active Directory. To znamená, že můžete udělit přístup k několik aplikací, které potřebují k načtení klíčů z Azure Key Vault a znát jiné aplikace nebudou mít přístup k klíče bez toho, abyste je konkrétně oprávnění.

Poznámka: doporučujeme používat pouze jeden z klíčů ve všech aplikací ve stejnou dobu. Pokud používáte klíč 1 některé místech a 2 klíč k ostatním, nebudete moci otočit klíče bez některé aplikace ztráty přístupu.

#### <a name="resources"></a>Zdroje a prostředky
* [Informace o účtech úložiště Azure](storage-create-storage-account.md#regenerate-storage-access-keys)

  Tento článek nabízí přehled účty úložiště a popisuje zobrazení, kopírování a opětovné generování přístupových klíčů k úložišti.
* [Referenci rozhraní API REST poskytovatele prostředků úložiště Azure](https://msdn.microsoft.com/library/mt163683.aspx)

  Tento článek obsahuje odkazy na konkrétní články o získávání klíčů účtu úložiště a opakované generování klíčů účtu úložiště pro účet Azure pomocí rozhraní REST API. Poznámka: Toto je pro účty úložiště Resource Manager.
* [Operace na účty úložiště](https://msdn.microsoft.com/library/ee460790.aspx)

  Tento článek v referenci rozhraní API REST úložiště Service Manager obsahuje odkazy na konkrétní články týkající se načítání a opakované generování klíčů účtu úložiště, pomocí rozhraní REST API. Poznámka: Toto je pro účty úložiště Classic.
* [Můžete dát sbohem všem Správa klíčů – řízení přístupu k datům Azure Storage pomocí služby Azure AD](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)

  Tento článek ukazuje, jak pomocí služby Active Directory k řízení přístupu k vaší službě Azure Storage klíče v Azure Key Vault. Také ukazuje, jak používat úlohu Azure Automation znovu vygenerovat klíče hodinu.

## <a name="data-plane-security"></a>Zabezpečení dat roviny
Zabezpečení dat roviny odkazuje na metodu sloužící k zabezpečení datové objekty uložené ve službě Azure Storage – objekty BLOB, fronty, tabulky a soubory. Jsme viděli metody k šifrování dat a zabezpečení během přenosu dat, ale jak tedy můžete o řízení přístupu k objektům?

Existují dvě metody pro ověřování přístupu k objektům data sami. Ty zahrnují řízení přístupu ke klíči účtu úložiště a pomocí podpisy sdíleného přístupu k udělení přístupu k konkrétní datové objekty pro konkrétní množství času.

Kromě toho pro úložiště objektů Blob, můžete povolit veřejný přístup do objektů BLOB podle nastavení úrovně přístupu pro kontejner, který obsahuje objekty BLOB odpovídajícím způsobem. Pokud jste nastavili přístup pro kontejner objektů Blob nebo kontejner, vám umožní veřejný přístup pro čtení pro objekty BLOB v tomto kontejneru. To znamená, že každý, kdo má URL ukazující na objekt blob v tomto kontejneru ho otevřete v prohlížeči bez pomocí sdíleného přístupového podpisu nebo s klíče účtu úložiště.

Kromě omezení přístupu prostřednictvím autorizace, můžete také použít [brány firewall a virtuální sítě](storage-network-security.md) omezit přístup k účtu úložiště na základě pravidel sítě.  Tento přístup umožňuje odepřít přístup k veřejné internetové přenosy a udělit přístup jenom ke konkrétním virtuálních sítí Azure nebo veřejného Internetu rozsahy IP adres.


### <a name="storage-account-keys"></a>Klíče účtu úložiště
Klíče účtu úložiště jsou 512 bitů řetězce vytvořené Azure, který společně s názvem účtu úložiště můžete použít pro přístup k objektům data uložená v účtu úložiště.

Můžete například čtení objektů BLOB, zápis do front, vytvářet tabulky a upravit soubory. Mnoho z těchto akcí lze provést prostřednictvím portálu Azure nebo jedním z mnoha aplikací Průzkumníka úložiště. Můžete taky napsat kód používat rozhraní REST API nebo jeden z knihovny klienta úložiště k provádění těchto operací.

Jak je popsáno v části na [zabezpečení roviny Management](#management-plane-security), přístup k úložiště klíčů pro účet úložiště Classic lze udělit tím, že úplný přístup k předplatnému Azure. Přístup k úložišti klíče pro účet úložiště pomocí modelu Azure Resource Manager lze řídit prostřednictvím řízení přístupu na základě Role (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Postup delegovat přístup k objektům v účtu pomocí sdílené přístupové podpisy a uložené zásad přístupu
Sdíleného přístupového podpisu je řetězec, který obsahuje token zabezpečení, které lze připojit k identifikátoru URI, který umožňuje delegovat přístup k objektům úložiště a zadejte omezení například oprávnění a datum a čas rozsah přístupu.

Můžete udělit přístup k objektům BLOB, kontejnery, fronty zpráv, soubory a tabulky. S tabulkami ve skutečnosti můžete udělit oprávnění k přístupu k rozsahu entit v tabulce zadáním oddílu a řádku klíče rozsahy, ke kterým má uživatel přístup. Například pokud máte data uložená se klíč oddílu zeměpisné stavu, vám může poskytnout někdo přístup k pouze data pro kalifornské.

Například můžete poskytnout webovou aplikaci SAS token, který umožní, aby zápisu položek do fronty a poskytnout pracovní role aplikace token SAS, pokud chcete získat zprávy z fronty a jejich zpracování. Nebo jedno zákazníka může poskytnout token SAS, můžete použít k nahrání obrázky do kontejneru v úložiště objektů Blob a poskytnout webové aplikace oprávnění ke čtení těchto obrázků. V obou případech je oddělené oblasti zájmu – každá aplikace je možné přidělit jen přístup, který vyžadují za účelem provádění svých úloh. To je možné prostřednictvím podpisy sdíleného přístupu.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Proč chcete používat sdílené přístupové podpisy
Proč by chcete použít SAS místo právě poskytnutí klíč účtu úložiště, což je mnohem jednodušší? Poskytnutí klíč účtu úložiště je třeba sdílení klíčů království vašeho úložiště. Zajišťuje úplný přístup. Někdo může pomocí klíče a odeslat jejich knihovna Hudba celý do svého účtu úložiště. Můžou taky nahradit nakažené viry verzemi souborů, nebo ukrást vaše data. Udělíte neomezený přístup, k účtu úložiště je něco, co by neměly být lehce brány.

S podpisy sdíleného přístupu můžete udělit pouze oprávnění požadovaná pro omezené množství času klienta. Například pokud někdo je nahrát objekt blob do účtu, můžete jim udělíte oprávnění k zápisu pro právě dostatek času k načtení objektu blob (v závislosti na velikosti objektu blob, samozřejmě). A pokud změníte své rozhodnutí, můžete tento přístup odvolat.

Kromě toho můžete určit, že požadavky pomocí SAS jsou omezeny na IP adresu nebo rozsah IP adres externí do Azure. Můžete taky vyžadovat, že jsou vytvářeny požadavky pomocí konkrétní protokolu (HTTPS nebo HTTP nebo HTTPS). To znamená, pokud chcete povolit komunikaci přes protokol HTTPS, požadovaného protokolu můžete nastavit na HTTPS pouze a přenos HTTP se zablokuje.

#### <a name="definition-of-a-shared-access-signature"></a>Definice sdílený přístupový podpis
Sdíleného přístupového podpisu je sada parametrů dotazu, připojí k adrese URL odkazující na prostředek

která obsahuje informace o přístup k povolený a dobu, pro který je povolen přístup. Tady je příklad; Tento identifikátor URI poskytuje přístup pro čtení do objektu blob pro pět minut. Všimněte si, že SAS parametrů dotazu musí být kódovaná v řetězci URL, například 3A % pro dvojtečkou (:) nebo % 20 prostoru.

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

#### <a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>Jak službu Azure Storage ověřit podpis sdíleného přístupu
Když službu úložiště obdrží žádost, používá parametry vstupní dotaz a vytvoří podpis stejným způsobem jako volací program. Pak porovná dvě podpisů. Pokud souhlasí, službu úložiště můžete zkontrolovat verze služby úložiště Ujistěte se, že je platný, ověřte, zda jsou v rámci zadané okno aktuální datum a čas, ujistěte se, že požadovaný přístup odpovídá požadavek atd.

Například s naše uvedenou adresu URL, pokud adresa URL byla odkazující na soubor namísto objektu blob, tento požadavek by nezdaří, protože se určuje, že sdíleného přístupového podpisu je pro objekt blob. Pokud byl příkaz REST volané aktualizovat objekt blob, by se nezdařila, protože podpis sdíleného přístupu určuje, že jsou povoleny pouze přístup pro čtení.

#### <a name="types-of-shared-access-signatures"></a>Druhy sdílených přístupových podpisů
* SAS úrovni služby slouží k přístupu konkrétním prostředkům v účtu úložiště. Příkladem takových načítají seznam objektů BLOB v kontejneru, stahování objekt blob, aktualizuje entitu v tabulce, přidání zprávy do fronty nebo nahrání souboru do sdílené složky.
* Úrovni účtu SAS slouží pro všechno, co SAS úrovně služby lze použít pro přístup. Kromě toho se může poskytnout možnosti na prostředky, které se nedá vymezit přes SAS úrovně služeb, jako je například schopnost vytvářet kontejnery, tabulky, fronty a sdílené složky. Přístup k více službám můžete také zadat současně. Například někdo může poskytnout přístup k objektům BLOB a soubory ve vašem účtu úložiště.

#### <a name="creating-a-sas-uri"></a>Vytváření identifikátor URI SAS
1. Identifikátor URI můžete vytvořit na vyžádání, definovat všechny parametry dotazu pokaždé, když.

   Tento přístup je flexibilní, ale pokud máte logickou sadu parametrů, které jsou podobné pokaždé, když, pomocí zásad přístupu uložené je lepší představu.
2. Můžete vytvořit zásady přístupu uložené pro celý kontejneru, sdílené složky, tabulku nebo frontu. Pak je můžete použít jako základ pro identifikátory URI SAS, které vytvoříte. Oprávnění na základě zásad přístupu uložené dají snadno odvolat. Může mít až pět zásad definovaných na každý kontejner, fronty, tabulka nebo sdílené složky.

   Pokud byly budete mít mnoho uživatelů čtení objektů BLOB v konkrétním kontejneru, můžete například vytvořit uložené zásady přístupu, která říká "udělit přístup pro čtení" a další nastavení, které budou stejné pokaždé, když. Potom můžete vytvořit identifikátor URI SAS pomocí nastavení zásad přístupu uložené a zadání datum a čas vypršení platnosti. To výhodou je, že nemáte zadejte všechny parametry dotazu pokaždé, když.

#### <a name="revocation"></a>Odvolání
Předpokládejme, že došlo k ohrožení zabezpečení vaší SAS, nebo chcete změnit podnikové zabezpečení a dodržování legislativních požadavků. Jak můžete odvolat přístup k prostředku pomocí tohoto SAS? To závisí na tom, jak vytvořit identifikátor URI SAS.

Pokud používáte ad hoc identifikátory URI, máte tři možnosti. Může vystavovat tokeny SAS zásadám krátké vypršení platnosti a počkejte SAS vyprší. Můžete přejmenovat nebo odstranit prostředek (za předpokladu, že byl token obor pro jediný objekt). Klíče účtu úložiště, můžete změnit. Tato možnost poslední může mít významný dopad, v závislosti na tom, kolik služby jsou pomocí tohoto účtu úložiště a pravděpodobně není něco, co chcete udělat bez některé plánování.

Pokud používáte SAS odvozené od zásadu přístupu uložené, můžete odebrat přístup k odvolání zásady přístupu uložené – můžete právě ho změnit tak, aby již vypršela platnost, nebo ji úplně odebrat. To se projeví okamžitě a zruší všechny SAS vytvořené pomocí této zásady přístupu uložené. Aktualizace nebo odebrání zásad přístupu uložené může tabulky dopad uživatelé s přístupem k této konkrétní kontejner, sdílené složky, nebo fronty přes SAS, ale pokud jsou klienti zapisují, takže požádají nové SAS, když se stane starý neplatná, to bude pracovat správně.

Protože pomocí SAS odvozené od zásadu uložené přístupu vám dává možnost odvolat tuto SAS okamžitě, je vždy nutné použít uložené Pokud je to možné zásady přístupu doporučený osvědčený postup.

#### <a name="resources"></a>Zdroje a prostředky
Podrobnější informace o používání sdílené přístupové podpisy uložené zásady přístupu a dokončení s příklady naleznete v následujících článcích:

* Toto jsou články odkaz.

  * [SAS služby](https://msdn.microsoft.com/library/dn140256.aspx)

    Tento článek obsahuje příklady použití SAS úroveň služby s objekty BLOB, fronty zpráv, rozsahy tabulky a soubory.
  * [Vytváření SAS služby](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Vytváření SAS účtu](https://msdn.microsoft.com/library/mt584140.aspx)
* Jedná se o podrobné pokyny pro vytvoření sdílené přístupové podpisy a uložené zásady přístupu pomocí klientské knihovny .NET.

  * [Použití sdílených přístupových podpisů (SAS)](../storage-dotnet-shared-access-signature-part-1.md)
  * [Sdílené přístupové podpisy, část 2: Vytvoření a použití SAS s služby objektů Blob](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Tento článek obsahuje vysvětlení modelu SAS, příklady sdílené přístupové podpisy a doporučení pro nejlepší postup pomocí SAS. Je také popsána odvolání udělí oprávnění.

* Authentication

  * [Ověřování služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Sdílené přístupové podpisy kurzu Začínáme

  * [SAS kurzu Začínáme](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Šifrování během přenosu
### <a name="transport-level-encryption--using-https"></a>Šifrování transportní vrstvy – pomocí protokolu HTTPS
Další krok, měli byste podniknout zajistit zabezpečení dat úložiště Azure je k šifrování dat mezi klientem a Azure Storage. První doporučení je vždy nutné použít [HTTPS](https://en.wikipedia.org/wiki/HTTPS) protokol, který zajišťuje zabezpečenou komunikaci prostřednictvím veřejného Internetu.

Pokud chcete, aby zabezpečenou komunikaci, byste měli vždycky používat protokol HTTPS, při volání rozhraní API REST nebo přístupu k nim objektů v úložišti. Navíc **sdílené přístupové podpisy**, který slouží k delegovat přístup k objektům Azure Storage, zahrnují možnost určit, že při použití sdílené přístupové podpisy, kontrolu, kdokoliv odesílání lze použít pouze protokol HTTPS se propojení s SAS tokeny budou používat protokol správné.

Můžete vynutit používání protokolu HTTPS, při volání rozhraní REST API pro přístup k objekty v účtech úložiště, povolením [zabezpečení přenosu požadované](../storage-require-secure-transfer.md) pro účet úložiště. Připojení pomocí protokolu HTTP bude odmítnuto, když je tato možnost povolena.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Pomocí šifrování během přenosu se sdílenými složkami Azure
Soubory Azure podporuje protokol HTTPS, když pomocí rozhraní REST API, ale je víc často používá jako sdílené složky SMB připojený k virtuálnímu počítači. SMB 2.1 nepodporuje šifrování, takže připojení jsou povoleny pouze v rámci stejné oblasti v Azure. Ale protokolu SMB 3.0 podporuje šifrování a je k dispozici v systému Windows Server 2012 R2, Windows 8, Windows 8.1 a Windows 10, povolení přístupu mezi oblastmi i přístup na ploše.

I když se systémem Unix můžete použít sdílené složky Azure File, klient Linux SMB ještě nepodporuje šifrování, takže přístup je povoleno pouze v rámci oblasti Azure. Podpora šifrování pro Linux je plánovaná Linux vývojářů zodpovědná za funkce protokolu SMB. Při přidávání šifrování, máte stejné možnosti pro přístup k Azure sdílenou v systému Linux, jako je tomu u systému Windows.

Můžete vynutit šifrování se službou Azure Files povolením [zabezpečení přenosu požadované](../storage-require-secure-transfer.md) pro účet úložiště. Pokud používáte rozhraní API REST, je požadován protokol HTTPs. Pouze připojení protokolu SMB, která podporují šifrování protokolu SMB, bude připojovat úspěšně.

#### <a name="resources"></a>Zdroje a prostředky
* [Soubory Azure Úvod](../files/storage-files-introduction.md)
* [Začínáme s Azure soubory v systému Windows](../files/storage-how-to-use-files-windows.md)

  Tento článek nabízí přehled Azure sdílené složky a jak připojit a používat je v systému Windows.

* [Jak používat Soubory Azure s Linuxem](../files/storage-how-to-use-files-linux.md)

  Tento článek ukazuje, jak se připojit Azure sdílenou složkou na Linux systému a nahrávání nebo stahování souborů.

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>K zabezpečení dat, který odešlete do úložiště pomocí šifrování na straně klienta
Jinou možnost, která vám pomůže zajistit, aby vaše data byla zabezpečené při přenášení mezi klientskou aplikaci a úložiště je šifrování na straně klienta. Data se šifrují před přenášením do úložiště Azure. Při načítání dat z Azure Storage, data se dešifrují po přijetí na straně klienta. I když jsou data zašifrována, přejdete v síti, doporučujeme také používat protokol HTTPS, protože má kontrolu integrity dat součástí, které zmírnění chyby sítě by to ovlivnilo integritu dat.

Šifrování na straně klienta je také metodu šifrování dat v klidovém stavu, jako jsou data uložená v šifrované podobě. Budeme mluvit o to podrobněji v části na [šifrování v klidovém stavu](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Šifrování v klidovém stavu
Existují tři Azure funkce, které poskytují šifrování v klidovém stavu. Azure Disk Encryption se používá k šifrování disků operačního systému a dat v virtuální počítače IaaS. Šifrování na straně klienta a SSE se používají k šifrování dat ve službě Azure Storage. 

Při šifrování na straně klienta můžete používat k šifrování dat během přenosu (který je také uložený v zašifrované podobě v úložišti), dáváte přednost používání protokolu HTTPS během přenosu a mají některé způsob pro data, která mají být šifrují automaticky, když je uložena. Existují dva způsoby, jak tomu – Azure Disk Encryption a SSE. Jeden se používá k přímo šifrování dat na operačního systému a datové disky, které jsou používány virtuálními počítači a dalších se používá k šifrování dat zapsaných na Azure Blob Storage.

### <a name="storage-service-encryption-sse"></a>Šifrování služby úložiště (SSE)

SSE je povolená pro všechny účty úložiště a nejde zakázat. Při zápisu do služby Azure Storage, SSE automaticky šifruje vaše data. Při čtení dat z Azure Storage se dešifrují úložiště Azure před vrácením. SSE umožňuje zabezpečit vaše data bez nutnosti měnit kód nebo přidávání kódu do žádné aplikace.

Klíčů používaných pro SSE spravuje Microsoft. Společnost Microsoft generuje klíče původně a spravuje jejich zabezpečeného úložiště, jakož i jejich regulární otočení, jak je definována interní zásady společnosti Microsoft. Spravované zákazníkem klíče nakonec budou k dispozici, společně s cesty migrace z klíče spravované Microsoftem na klíče spravovaného zákazníkem.

SSE automaticky šifruje data na všech úrovních výkonu (Standard a Premium), ve všech modelech nasazení (Azure Resource Manager a Classic) a ve všech službách Azure Storage (Blob, Queue, Table a File). 

### <a name="client-side-encryption"></a>Šifrování na straně klienta
Jsme uvedených šifrování na straně klienta, když hovoříte o šifrování dat při přenosu. Tato funkce umožňuje prostřednictvím kódu programu šifrování dat v aplikaci klienta před odesláním přes přenosu má být zapsán do služby Azure Storage a prostřednictvím kódu programu dešifrovat data po načtení z úložiště Azure.

To poskytuje šifrování během přenosu, ale také poskytuje funkci šifrování v klidovém stavu. I když data se šifrují během přenosu, doporučujeme využít integrované dat kontroly integrity, které zmírnění chyby sítě by to ovlivnilo integritu dat pomocí protokolu HTTPS.

Příklad kde lze jej použít, je, pokud máte webovou aplikaci, která ukládá objekty BLOB a načte objekty BLOB a chcete, aby aplikace a data, být co možná. V takovém případě byste použili šifrování na straně klienta. Přenosy mezi klientem a službou objektů Blob Azure obsahuje šifrované prostředků a nikdo interpretovat data při přenosu a rekonstruovat do privátní objektů BLOB.

Šifrování na straně klienta jsou součástí jazyce Java a knihovny klienta úložiště .NET, které pak používají Azure klíč trezoru rozhraní API, a usnadňuje pro vás k implementaci. Proces šifrování a dešifrování dat používá metodu obálky a ukládá metadata používá šifrování v každém objektu úložiště. Například pro objekty BLOB, ukládají se v metadata objektu blob, zatímco pro fronty, přidá se každá zpráva fronty.

K šifrování sám může vygenerovat a spravovat vlastní šifrovací klíče. Klíče generované Klientská knihovna pro úložiště Azure můžete také použít, nebo nemáte Azure Key Vault, generování klíčů. Můžete uložit šifrovací klíče ve vaší místní úložiště klíčů a uložit je do Azure Key Vault. Azure Key Vault můžete udělit přístup k tajných klíčů v Azure Key Vault pro konkrétní uživatele pomocí služby Azure Active Directory. To znamená, že nejen Kdokoliv může číst Azure Key Vault a načtení klíčů, které používáte pro šifrování na straně klienta.

#### <a name="resources"></a>Zdroje a prostředky
* [Šifrování a dešifrování objektů BLOB v úložišti Microsoft Azure pomocí Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  Tento článek ukazuje, jak používat šifrování na straně klienta s Azure Key Vault, včetně toho, jak vytvořit klíče KEK a uložit ho v úložišti pomocí prostředí PowerShell.
* [Šifrování na straně klienta a Azure Key Vault pro Microsoft Azure Storage](../storage-client-side-encryption.md)

  Tento článek vysvětluje šifrování na straně klienta a obsahuje příklady použití Klientská knihovna pro úložiště pro šifrování a dešifrování prostředky z čtyři služby úložiště. Také pojednává o Azure Key Vault.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Pomocí Azure Disk Encryption k šifrování disků používat virtuální počítače
Azure Disk Encryption je nová funkce. Tato funkce umožňuje šifrování disků operačního systému a datové disky použít podle IaaS virtuálního počítače. Pro systém Windows jsou šifrované jednotky pomocí standardních technologií šifrování nástroje BitLocker. Pro systémy Linux jsou disky šifrované pomocí technologie DM-Crypt. Toto jsou integrované s Azure Key Vault umožňují řídit a spravovat disku šifrovací klíče.

Řešení podporuje následující scénáře pro virtuální počítače IaaS, pokud jsou povolené v Microsoft Azure:

* Integrace s Azure Key Vault
* Úroveň Standard virtuálních počítačů: [A, D, DS, G, GS a atd. řady virtuální počítače IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Povolení šifrování v systému Windows a virtuálních počítačů IaaS Linux
* Zakázáním šifrování na operačního systému a datové disky pro virtuální počítače IaaS Windows
* Zakázáním šifrování na datových jednotkách pro virtuální počítače IaaS Linux
* Povolení šifrování na virtuální počítače IaaS se systémem Windows klientského operačního systému
* Povolení šifrování u svazků s připojení cesty
* Povolení šifrování u virtuálních počítačů Linux, které jsou nakonfigurované s diskem s použitím mdadm prokládání (RAID)
* Povolení šifrování na virtuální počítače s Linuxem pomocí LVM pro datové disky
* Povolení šifrování na virtuálních počítačích Windows, které jsou nakonfigurované pomocí prostorů úložiště
* Jsou podporovány všechny veřejné oblasti Azure

Řešení nepodporuje následující scénáře, funkce a technologie ve verzi:

* Úroveň Basic virtuálních počítačů IaaS
* Zakázáním šifrování na jednotce operačního systému pro virtuální počítače IaaS Linux
* Virtuální počítače IaaS, která jsou vytvořená pomocí klasického metody vytvoření virtuálního počítače
* Integrace s vaší místní služby správy klíčů
* Soubory Azure (systém souborů sdíleného), Network File System (NFS), dynamické svazky a virtuální počítače Windows, které jsou nakonfigurované s systémy na bázi softwaru diskového pole RAID


> [!NOTE]
> Na následující Linuxových distribucích v současné době podporuje šifrování disku operačního systému Linux: RHEL 7.2, CentOS 7.2n a Ubuntu 16.04.
>
>

Tato funkce zajišťuje, že všechna data na disky virtuálního počítače je zašifrovaná přinejmenším ve službě Azure Storage.

#### <a name="resources"></a>Zdroje a prostředky
* [Azure Disk Encryption pro systém Windows a virtuálních počítačů Linux IaaS](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Porovnání Azure Disk Encryption, SSE a šifrování na straně klienta

#### <a name="iaas-vms-and-their-vhd-files"></a>Virtuální počítače IaaS a jejich soubory virtuálního pevného disku

Pro datové disky používat virtuální počítače IaaS se doporučuje Azure Disk Encryption. Pokud vytvoříte virtuální počítač s využitím obrázku z Azure Marketplace nespravované disky, provede Azure [Nedávná kopie](https://en.wikipedia.org/wiki/Object_copying) bitové kopie do úložiště není šifrován účtu v Azure Storage ale i v případě, že máte SSE povoleno. Po vytvoří virtuální počítač a spustí aktualizaci bitové kopie, začne SSE šifrování data. Z tohoto důvodu je nejvhodnější použít Azure Disk Encryption na virtuálních počítačích s nespravované disky vytvořené z bitových kopií v Azure Marketplace, pokud chcete, aby je plně zašifrované. Pokud vytvoříte virtuální počítač s spravované disky, SSE šifruje všechna data ve výchozím nastavení pomocí platformy spravované klíče. 

Pokud aktivujete předem šifrované virtuálních počítačů do Azure z místního, bude možné nahrát šifrovací klíče do Azure Key Vault a pokračovat v používání šifrování pro tento virtuální počítač, že jste používali místní. Azure Disk Encryption zapnutá tohoto scénáře.

Pokud máte bez šifrování virtuálního pevného disku z místně, můžete odeslat do Galerie jako vlastní image a zřídit virtuální počítač z něj. Pokud to uděláte pomocí šablony Resource Manageru, požádejte ho chcete zapnout Azure Disk Encryption po spuštění virtuálního počítače.

Když přidáte datový disk a připojit ho ve virtuálním počítači, můžete zapnout na Azure Disk Encryption na tento datový disk. Zašifruje tento disk data místně nejprve a vrstva modelu nasazení classic pak provede opožděné zápisu pro úložiště, takže obsah úložiště je šifrován.

#### <a name="client-side-encryption"></a>Šifrování na straně klienta
Šifrování na straně klienta je nejbezpečnější metodou šifrování dat, protože se šifruje data před přenosu.  Ale vyžadují, přidávání kódu do vaší aplikace s použitím úložiště, které nemusí chcete provést. V takových případech můžete zabezpečit data při přenosu pomocí protokolu HTTPS. Jakmile data dosáhne Azure Storage, je zašifrovaná pomocí SSE.

Pomocí šifrování na straně klienta můžete šifrovat entity tabulky, fronty zpráv a objekty BLOB. 

Šifrování na straně klienta je zcela spravuje aplikace. To je nejbezpečnější přístup, ale vyžadují, abyste programový změnit do vaší aplikace a umístí procesy správy klíčů na místě. To byste použili, když chcete zvýšit zabezpečení během přenosu a chcete, aby vaše uložená data chcete šifrovat.

Šifrování na straně klienta je další zatížení na straně klienta je nutné pro tento účet v plánu škálovatelnost, zejména v případě, že jsou šifrování a přenosu velké množství dat.

#### <a name="storage-service-encryption-sse"></a>Šifrování služby úložiště (SSE)

SSE spravuje Azure Storage. SSE neposkytuje pro zabezpečení přenášených dat, ale šifrování dat, jako je zapsán do úložiště Azure. SSE nemá vliv na výkon služby Azure Storage.

Můžete šifrovat jakýkoli druh data účtu úložiště pomocí SSE (bloku, doplňovací objekty BLOB, objekty BLOB stránky, data tabulky, fronty data a soubory).

Pokud máte archiv nebo knihovna soubory virtuálního pevného disku, které můžete použít jako základ pro vytváření nových virtuálních počítačů, můžete vytvořit nový účet úložiště a pak nahrajte soubory virtuálního pevného disku k tomuto účtu. Tyto soubory virtuálního pevného disku, bude se šifrovat úložiště Azure.

Pokud je povoleno pro disky ve virtuálním počítači Azure Disk Encryption, všechny nově vytvořené data se šifrují SSE a také Azure Disk Encryption.

## <a name="storage-analytics"></a>Storage Analytics
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Pomocí Storage Analytics k monitorování typ autorizace
Pro každý účet úložiště můžete povolit Azure Storage Analytics k provedení protokolování a ukládání dat metriky. Toto je skvělý nástroj pro použijte, pokud chcete zkontrolovat metriky výkonu účet úložiště, nebo třeba řešit účet úložiště, protože dochází k problémům s výkonem.

Další část dat, která se zobrazí v protokolech analytics úložiště je metoda ověřování používá někdo při přístupu k úložišti. Například pomocí úložiště objektů Blob, uvidíte pokud používají sdíleného přístupového podpisu nebo klíče účtu úložiště, nebo pokud byl veřejný přístup k objektu blob.

To může být užitečné, pokud jsou úzce ochrana přístup k úložišti. V úložišti objektů Blob můžete například nastavit všechny kontejnery do privátního a implementovat použití služby SAS v celé vaší aplikace. Zkontrolujte protokoly pravidelně zobrazíte Pokud objektů BLOB ke kterým se přistupuje pomocí klíče účtu úložiště, které mohou indikovat porušení zabezpečení, nebo pokud jsou veřejné objekty BLOB, ale nemělo by být.

#### <a name="what-do-the-logs-look-like"></a>Co v protokolech vypadat?
Po povolení metrik účtu úložiště a protokolování prostřednictvím portálu Azure, analytická data začnou hromadit rychle. Protokolování a metriky pro každou službu je samostatný; protokolování je zapisovány pouze v případě, že je aktivita v daném účtu úložiště, zatímco metriky se zaznamená do protokolu, každou minutu, každou hodinu nebo každý den, v závislosti na tom, jak ho nakonfigurujete.

Protokoly jsou uloženy v objekty BLOB bloku v kontejneru nazvaném $logs v účtu úložiště. Tento kontejner se automaticky vytvoří, když je povolená analytika úložiště. Po vytvoření tohoto kontejneru, nelze odstranit i když můžete odstranit jeho obsah.

V kontejneru $logs nachází složka pro každou službu a pak podsložky nejsou za rok/měsíc/den/hodinu. V části hod. jsou číslované protokoly. Toto je, jak bude vypadat adresářovou strukturu:

![Zobrazení souborů protokolu](./media/storage-security-guide/image1.png)

Každý požadavek do služby Azure Storage je protokolována. Zde je snímek souboru protokolu, první několik polí.

![Snímek souboru protokolu](./media/storage-security-guide/image2.png)

Uvidíte, že můžete protokoly sledovat jakýkoli druh volání na účet úložiště.

#### <a name="what-are-all-of-those-fields-for"></a>Jaké jsou všechna tyto pole pro?
Je zobrazeno v seznamu prostředků nižší, než je článek poskytuje seznam mnoho polí na protokoly a jejich použití. Tady je seznam polí v pořadí:

![Snímek polí v souboru protokolu](./media/storage-security-guide/image3.png)

Zajímají nás položky pro getblob – a jak jsou ověřeni, takže je třeba vyhledejte položky s typ operace "Get-Blob" a zkontrolujte stav žádosti o (čtvrté</sup> sloupec) a typ autorizace (osmého</sup> sloupce).

Například v několik prvních řádků v seznamu nahoře, je stav žádosti o "ÚSPĚCH" a typ autorizace je "ověřit". To znamená, že požadavek se ověřila pomocí klíče účtu úložiště.

#### <a name="how-are-my-blobs-being-authenticated"></a>Jak jsou ověřovaného Moje objekty BLOB?
Máme tři případů, které jsme zájem.

1. Objekt blob je veřejná a je přístupný pomocí adresy URL bez sdíleného přístupového podpisu. V tomto případě stav žádosti o je "AnonymousSuccess" a "anonymní" je typ autorizace.

   1.0; 2015-11-17T02:01:29.0488963Z; Getblob –; **AnonymousSuccess**, 200, 124, 37; **Anonymní**; mystorage...
2. Objekt blob je privátní a se použila se sdíleným přístupovým podpisem. V tomto případě stav žádosti o je "SASSuccess" a "sas" je typ autorizace.

   1.0; 2015-11-16T18:30:05.6556115Z; Getblob –; **SASSuccess**, 200, 416, 64; **SAS**; mystorage...
3. Objekt blob je privátní a klíč úložiště použila k přístupu. V takovém případě je stav žádosti o "**úspěch**"a typ autorizace je"**ověřený**".

   1.0; 2015-11-16T18:32:24.3174537Z; Getblob –; **Úspěch**; 206; 59; 22; **ověření**; mystorage...

Můžete zobrazit a analyzovat tyto protokoly můžete použít Microsoft Message Analyzer. Obsahuje možnosti Hledat a filtr. Například můžete chtít vyhledat instance getblob – Pokud chcete zobrazit, pokud využití očekávání, to znamená, abyste měli jistotu, někdo není přístup k účtu úložiště nesprávně.

#### <a name="resources"></a>Zdroje a prostředky
* [Storage Analytics](../storage-analytics.md)

  Tento článek je přehled analytika úložiště a povolit je.
* [Formát úložiště analýzy protokolů](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Tento článek ukazuje formát úložiště analýzy protokolů a v něm, podrobnosti dostupná pole, včetně ověřování typu, který určuje typ ověřování použité pro daný požadavek.
* [Monitorování účtu úložiště na portálu Azure](../storage-monitor-storage-account.md)

  Tento článek ukazuje, jak nakonfigurovat sledování metrik a protokolování pro účet úložiště.
* [Začátku do konce Poradce při potížích s použitím Azure Storage Metrics a protokolování, AzCopy a Message Analyzer](../storage-e2e-troubleshooting.md)

  Tento článek pojednává o řešení problémů pomocí Storage Analytics a ukazuje, jak používat Microsoft Message Analyzer.
* [Microsoft Message Analyzer operační Průvodce](https://technet.microsoft.com/library/jj649776.aspx)

  Tento článek je referenční dokumentace pro Microsoft Message Analyzer a obsahuje odkazy na kurz, rychlý start a funkce souhrnu.

## <a name="cross-origin-resource-sharing-cors"></a>Sdílení prostředků mezi zdroji (CORS)
### <a name="cross-domain-access-of-resources"></a>Přístup do jiných domén prostředků
Při spuštění v jedné doméně webový prohlížeč provede požadavek HTTP pro prostředek z jiné domény, se nazývá žádosti HTTP nepůvodního zdroje. Například stránky HTML z contoso.com odešle požadavek pro hostované na fabrikam.blob.core.windows.net jpeg. Z bezpečnostních důvodů omezit prohlížeče HTTP žádostí napříč zdroji spustily z funkce skripty, jako je JavaScript. To znamená, že pokud určitý kód JavaScript na webové stránce v doméně contoso.com požádá o tomto jpeg na fabrikam.blob.core.windows.net, prohlížeč nebude žádosti.

Co to má dělat s Azure Storage? Dobře, pokud ukládáte statické prostředky, jako jsou soubory dat JSON nebo XML v Blob Storage pomocí účtu úložiště volána Fabrikam, v doméně prostředků bude fabrikam.blob.core.windows.net a webové aplikace contoso.com nebudou moci přistupovat k nim pomocí JavaScript protože domény se liší. Toto je true, pokud se pokoušíte volání jednoho služby Azure Storage – například Table Storage – které vrací data JSON, které mají být zpracovány klientem JavaScript.

#### <a name="possible-solutions"></a>Možná řešení
Jeden způsob, jak to vyřešit je přiřadit fabrikam.blob.core.windows.net vlastní doménu jako "storage.contoso.com". Problém je, že lze přiřadit vlastní domény pouze pro jeden účet úložiště. Co když prostředky jsou uloženy ve více účtů úložiště?

Jiný způsob, jak to vyřešit je k dispozici webová aplikace sloužil jako proxy pro volání úložiště. To znamená, pokud odesíláte soubor do úložiště objektů Blob, webová aplikace by buď zapisuje místně a zkopírujte jej do úložiště objektů Blob nebo by všechny načtení do paměti a zapíše jej do úložiště objektů Blob. Alternativně můžete napsat vyhrazené webové aplikace (například webového rozhraní API), která odešle soubory lokálně a zapisuje je do úložiště objektů Blob. V obou případech je nutné účet pro tuto funkci při určování škálovatelnost potřebuje.

#### <a name="how-can-cors-help"></a>Jak může pomoci CORS?
Úložiště Azure umožňuje povolit CORS – křížové sdílení prostředků původu. Pro každý účet úložiště můžete zadat domén, které mají přístup k prostředkům v daném účtu úložiště. V našem případě uvedených výše jsme například povolení CORS v účtu úložiště fabrikam.blob.core.windows.net a nakonfigurovat ji pro povolení přístupu k contoso.com. Potom můžete contoso.com aplikace webové přímo přístup k prostředkům v fabrikam.blob.core.windows.net.

Poznámka je, že CORS umožňuje přístup, ale neposkytuje ověřování, který je nutný pro všechny jiný veřejný přístup k prostředkům úložiště. To znamená, že objekty BLOB lze otevřít pouze, pokud jsou veřejné nebo zahrnete sdíleného přístupového podpisu je udělení příslušných oprávnění. Tabulky, fronty a soubory nemají veřejný přístup a vyžadují SAS.

Ve výchozím nastavení je CORS na všechny služby zakázána. CORS můžete povolit pomocí rozhraní REST API nebo knihovny klienta úložiště pro volání jedné z metod k nastavení zásad služby. Pokud můžete udělat, můžete zahrnout CORS pravidlo, které je v kódu XML. Tady je příklad pravidla CORS, která byla nastavena pomocí operace nastavit vlastnosti služby pro službu Blob pro účet úložiště. Můžete provést tuto operaci pomocí rozhraní API REST nebo Klientská knihovna pro úložiště pro úložiště Azure.

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

Tady je co znamená každý řádek:

* **AllowedOrigins** sdělí které neodpovídající domény můžete požadovat a přijímat data ze služby storage. To říká, že contoso.com a fabrikam.com můžete data žádosti z úložiště objektů Blob pro účet konkrétní úložiště. Tuto vlastnost nastavit na zástupný znak (\*) umožňující všechny domény pro přístup k žádosti.
* **AllowedMethods** Toto je seznam metod (příkazy požadavku HTTP), které lze použít při vytváření požadavku. V tomto příkladu jsou povoleny pouze PUT a GET. To můžete nastavit na zástupný znak (\*) mají povolit všechny metody, který se má použít.
* **AllowedHeaders** Toto je seznam hlaviček požadavku, které doménu původu můžete zadat při vytváření požadavku. V tomto příkladu jsou povoleny všechny hlavičky metadata počínaje x-ms-meta-data x-ms-meta cíl a x-ms-meta-abc. Zástupný znak (\*) označuje, že je povolit všechny hlavičky začíná zadanou předponou.
* **ExposedHeaders** to informuje o tom, které hlavičky odpovědi by měly být vystaveny v prohlížeči Issuer požadavku. V tomto příkladu všechny hlavičky počínaje "x-ms - meta-" k dispozici.
* **MaxAgeInSeconds** Toto je maximální množství času, že prohlížeč bude mezipaměti předběžný požadavek možnosti. (Další informace o předběžný požadavek, zkontrolujte, zda první následujícího článku.)

#### <a name="resources"></a>Zdroje a prostředky
Další informace o CORS a jak ji povolit projděte si tyto prostředky.

* [Podpora (CORS) pro služby Azure Storage na Azure.com sdílení prostředků různého původu](../storage-cors-support.md)

  Tento článek obsahuje přehled CORS a jak nastavit pravidla pro služby jiného úložiště.
* [Podpora služby Azure Storage na webu MSDN (CORS) pro sdílení prostředků různého původu](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Toto je referenční dokumentaci k nástroji pro podporu CORS pro služby Azure Storage. To obsahuje odkazy na články pro každou službu úložiště a ukazuje příklad a vysvětluje každý prvek v souboru CORS.
* [Microsoft Azure Storage: Úvod CORS](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Toto je odkaz na článek počáteční blog uvedení CORS a zobrazující způsobu jeho použití.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Nejčastější dotazy o zabezpečení Azure Storage
1. **Jak můžete ověřit integritu objekty BLOB, které I mě přenosu do nebo z Azure Storage, když nelze použít protokol HTTPS?**

   Pokud z nějakého důvodu, které je třeba používat protokol HTTP místo protokolu HTTPS a práci s objekty BLOB bloku, můžete použít MD5 kontroluje k ověření integrity objektů BLOB přenášení. To vám pomůže s ochranou z chyb sítě/transport layer, ale nemusí nutně jít s zprostředkující útoky.

   Pokud použijete protokol HTTPS, který poskytuje zabezpečení na úrovni přenosu, pak pomocí MD5 kontrola je redundantní a zbytečné.

   Další informace najdete v článku [přehled MD5 objektu Blob Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **Co o FIPS – dodržování předpisů pro USA Státní?**

   USA informace o zpracování FIPS (Federal Standard) definuje kryptografické algoritmy pro použití schváleno USA Federální vládou počítačích pro ochranu citlivá data. Povolení FIPS režimu na Windows serveru nebo plochy informuje operačního systému se použije pouze ověřených algoritmem FIPS kryptografické algoritmy. Pokud aplikace používá nevyhovující algoritmy, dojde k přerušení aplikace. With.NET Framework verze 4.5.2 nebo vyšší, aplikace se automaticky nepřepne kryptografické algoritmy používat algoritmus kompatibilní se standardem FIPS, když je počítač v režimu FIPS.

   Microsoft nechávají každého zákazníka můžete rozhodnout, jestli chcete povolit režim FIPS. Věříme, že neexistuje žádný důvod poutavé pro zákazníky, kteří nejsou v souladu government předpisy a povolení režimu FIPS ve výchozím nastavení.

   **Prostředky**

* [Proč se není doporučujeme "Režimu FIPS" už](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Tento článek na blogu shrnovat standardu FIPS a vysvětluje, proč nemáte umožňují režimu FIPS ve výchozím nastavení.
* [FIPS 140 ověření](https://technet.microsoft.com/library/cc750357.aspx)

  Tento článek obsahuje informace o tom, jak produkty společnosti Microsoft a kryptografické moduly v souladu se standardem FIPS pro USA Federální vládou.
* ["Kryptografie systému: použití standardu FIPS kompatibilní s algoritmy pro šifrování, hašování a podpisování" důsledky nastavení zabezpečení v systému Windows XP a v novějších verzích systému Windows](https://support.microsoft.com/kb/811833)

  Tento článek pojednává o použití režimu FIPS ve starší počítače se systémem Windows.
