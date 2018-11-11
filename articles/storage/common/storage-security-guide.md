---
title: Průvodci zabezpečením Azure Storage | Dokumentace Microsoftu
description: Podrobnosti o mnoho metod zabezpečení Azure Storage, včetně, ale nikoli výhradně RBAC, šifrování služby Storage, šifrování na straně klienta, protokolu SMB 3.0 a Azure Disk Encryption.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/31/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 3d9da96e5bf6c88f76089dea930b02248cfa1d24
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243790"
---
# <a name="azure-storage-security-guide"></a>Průvodci zabezpečením Azure Storage

Azure Storage nabízí komplexní sadu možností zabezpečení, které společně umožňují vývojářům vytvářet zabezpečené aplikace:

- Všechna data zapsaná do služby Azure Storage budou automaticky šifrována pomocí [šifrování služby Storage (SSE)](storage-service-encryption.md). Další informace najdete v tématu [oznamujeme výchozí šifrování objektů BLOB Azure, soubory, tabulky a fronty úložiště](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Azure Active Directory (Azure AD) a řízení přístupu na základě Role (RBAC) jsou podporovány pro službu Azure Storage pro operace správy zdrojů a operace s daty, následujícím způsobem:   
    - Můžete přiřadit role RBAC omezená na účet úložiště pro objekty zabezpečení a pomocí služby Azure AD povolit operace správy zdrojů, například správu klíčů.
    - Integrace se službou Azure AD je podporovaná ve verzi preview pro operace s daty ve službách objektů Blob a fronty. Můžete přiřadit role RBAC obor na předplatné, skupinu prostředků, účet úložiště nebo kontejner nebo fronty k objektu zabezpečení nebo spravovanou identitu pro prostředky Azure. Další informace najdete v tématu [ověření přístupu ke službě Azure Storage pomocí Azure Active Directory (Preview)](storage-auth-aad.md).   
- Může být šifrují data přenášená mezi aplikací a službou Azure pomocí [šifrování na straně klienta](../storage-client-side-encryption.md), HTTPS nebo SMB 3.0.  
- Operační systém a datové disky, které používají virtuální počítače Azure se dají zašifrovat pomocí [Azure Disk Encryption](../../security/azure-security-disk-encryption.md). 
- Delegovaný přístup k datové objekty ve službě Azure Storage lze udělit prostřednictvím [sdílené přístupové podpisy](../storage-dotnet-shared-access-signature-part-1.md).

Tento článek obsahuje základní informace o každé z těchto funkcí zabezpečení, které lze použít s Azure Storage. Odkazy jsou k dispozici na články, které vám poskytne podrobné údaje o jednotlivých funkcí, takže můžete snadno provést další šetření na každého tématu.

Tady jsou témata popsaná v tomto článku:

* [Zabezpečení roviny správy](#management-plane-security) – zabezpečení vašeho účtu úložiště

  Rovina správy se skládá z prostředků používá ke správě vašeho účtu úložiště. Tento oddíl popisuje model nasazení Azure Resource Manageru a jak řídit přístup k účtům úložiště pomocí řízení přístupu na základě Role (RBAC). Také řeší, správu klíčů účtu úložiště a jak znovu vygenerovat je.
* [Zabezpečení RP roviny dat](#data-plane-security) – zabezpečení přístupu k datům

  V této části se podíváme na povolení přístupu k objektům skutečná data ve vašem účtu úložiště, jako jsou objekty BLOB, soubory, fronty a tabulky, pomocí sdílené přístupové podpisy a uložené zásady přístupu. Budeme se zabývat podpis SAS na úrovni služby a SAS na úrovni účtu. Uvidíme také tom, jak omezit přístup ke konkrétní IP adresu (nebo rozsah IP adres), jak omezit protokol použitý pro protokol HTTPS a odvoláte sdíleného přístupového podpisu bez čekání na vypršení platnosti.
* [Šifrování během přenosu](#encryption-in-transit)

  Tato část popisuje, jak zabezpečit data při přenosu do nebo z úložiště Azure. Budeme mluvit o doporučené použití protokolů HTTPS a šifrování používá protokol SMB 3.0 pro sdílené složky Azure. Můžeme se taky podívejte se na šifrování na straně klienta, který umožňuje šifrování dat před přenosu do služby Storage v klientské aplikaci a k dešifrování dat po převedení mimo úložiště.
* [Šifrování v klidovém stavu](#encryption-at-rest)

  Mluvíme o šifrování služby Storage (SSE), která je teď automaticky povolena pro nové i stávající účty. Podíváme se také na tom, jak můžete použít Azure Disk Encryption a prozkoumat základní rozdíly a případy šifrování disku a SSE a šifrování na straně klienta. Stručně podíváme na kompatibilita se standardem FIPS pro oblast USA Státní správy počítačů.
* Pomocí [Storage Analytics](#storage-analytics) auditovat přístup k Azure Storage

  Tato část popisuje, jak najít informace do úložiště analýzy protokolů pro požadavek. Vytvoříme podívejte se na skutečné storage analytics data protokolů a informace o tom, jak ověřit, zda se požadavek s klíč účtu úložiště pomocí sdíleného přístupového podpisu, nebo anonymně a určuje, zda bylo úspěšné nebo neúspěšné.
* [Povolení založené na prohlížeči klientů pomocí CORS](#Cross-Origin-Resource-Sharing-CORS)

  Tato část pojednává o povolení prostředků různého původu (CORS) pro sdílení obsahu. Budeme mluvit o přístupu napříč doménami a jak ji zpracovat pomocí možnosti CORS integrovaná do služby Azure Storage.

## <a name="management-plane-security"></a>Zabezpečení roviny správy
Rovina správy se skládá z operací, které ovlivňují samotného účtu úložiště. Můžete například vytvořit nebo odstranit účet úložiště, získání seznamu účtů úložiště v rámci předplatného, načtení klíčů účtu úložiště nebo obnovit klíče účtu úložiště.

Když vytvoříte nový účet úložiště, vyberte model nasazení Classic nebo Resource Manageru. Model Classic vytváření prostředků v Azure umožňuje pouze rigidní přístup k předplatnému a v důsledku, účet úložiště.

Tato příručka se zaměřuje na model Resource Manageru, který je doporučené prostředky pro vytváření účtů úložiště. Účty úložiště Resource Manageru, spíše než poskytne přístup k celé předplatné můžete řídit přístup k více omezený úroveň k rovině správy pomocí řízení přístupu na základě Role (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Jak zabezpečit svůj účet úložiště pomocí řízení přístupu na základě Role (RBAC)
Povězme si o co RBAC je a jak ji použít. Každé předplatné Azure zahrnuje službu (adresář) Azure Active Directory. Uživatelům, skupinám a aplikacím z tohoto adresáře lze udělit přístup ke správě prostředků v předplatném Azure, které používají model nasazení Resource Manager. Tento typ zabezpečení se označuje jako řízení přístupu na základě Role (RBAC). Ke správě tohoto přístupu můžete použít [webu Azure portal](https://portal.azure.com/), [nástroje rozhraní příkazového řádku Azure](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), nebo [Azure Storage Resource Provider rozhraní REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Pomocí modelu Resource Manager kam si ukládáte účtu úložiště v prostředku skupiny a řízení přístupu k rovině správy tohoto účtu konkrétní úložiště pomocí Azure Active Directory. Například můžete konkrétním uživatelům udělit přístup klíče účtu úložiště, zatímco ostatní uživatelé mohou zobrazit informace o účtu úložiště, ale nelze přístupové klíče účtu úložiště.

#### <a name="granting-access"></a>Udělení přístupu
Přístup uděluje přiřazením příslušné role RBAC pro uživatele, skupiny nebo aplikace v oboru pravé. Chcete-li udělit přístup k celé předplatné, přiřaďte role na úrovni předplatného. Tím, že uděluje oprávnění na samotnou skupinu prostředků můžete udělit přístup ke všem prostředkům ve skupině prostředků. Konkrétní role můžete také přiřadit ke konkrétním prostředkům, jako je například účty úložiště.

Tady jsou hlavní body, které je potřeba vědět o použití RBAC pro přístup k operace správy účtu úložiště Azure:

* Při přiřazení přístupu v podstatě přiřazení role na účet, který chcete mít přístup. Můžete řídit přístup k operacím používá ke správě tohoto účtu úložiště, ale není k datovým objektům v účtu. Například můžete udělit oprávnění k načtení vlastnosti účtu úložiště (třeba redundance), ale ne ke kontejneru nebo dat v rámci kontejneru ve službě Blob Storage.
* Možnost mají oprávnění pro přístup k objektům data v účtu úložiště jim můžete udělit oprávnění ke čtení klíčů účtu úložiště a tento uživatel potom můžete pomocí těchto klíčů přístup k objektům BLOB, fronty, tabulky a soubory.
* Role je možné přiřadit k účtu konkrétního uživatele, skupiny uživatelů nebo na konkrétní aplikaci.
* Každá role má seznam akcí a ne akce. Třeba role Přispěvatel virtuálních počítačů má akci "klíče Listkey", která umožňuje klíče účtu úložiště pro čtení. Přispěvatel má "Není akce", jako je aktualizace přístup pro uživatele ve službě Active Directory.
* Role pro úložiště zahrnují (ale nejsou omezeny) následující role:

  * Vlastník – můžou spravovat všechno včetně přístupu.
  * Přispěvatel –, může dělat něco vlastníka můžete kromě přiřadit přístup. Někdo s touto rolí můžete zobrazit a znovu vygenerovat klíče účtu úložiště. Díky klíče účtu úložiště jejich přístup k datové objekty.
  * Čtenář – můžou zobrazit informace o účtu úložiště, s výjimkou tajných kódů. Například pokud někomu přiřadíte role se stejnými oprávněními čtečky v účtu úložiště, můžete si prohlédnout vlastnosti účtu úložiště, ale nemůže provádět žádné změny vlastností nebo zobrazení klíče účtu úložiště.
  * Přispěvatel účtů úložiště – můžou spravovat účet úložiště – můžete číst odběru skupiny prostředků a prostředky a vytvářet a spravovat nasazení skupiny prostředků předplatného. Můžete také přístup k klíče účtu úložiště, které pak znamená, že bude mít přístup k rovině dat.
  * Správce uživatelských přístupů – můžou spravovat přístup uživatelů k účtu úložiště. Například se může udělit přístup čtečky pro konkrétního uživatele.
  * Přispěvatel virtuálních počítačů – můžou spravovat virtuální počítače, ale není účet úložiště, ke kterému jsou připojené. Tuto roli můžete zobrazit seznam klíčů účtu úložiště, což znamená, že uživatel, kterým přiřadíte tuto roli můžete aktualizovat rovina dat.

    Uživatel k vytvoření virtuálního počítače, mít nebude moci vytvořit odpovídající souboru VHD v účtu úložiště. K tomuto účelu musí být schopni načíst klíč účtu úložiště a předejte jej do rozhraní API pro vytváření virtuálního počítače. Proto musí mít toto oprávnění, že můžete vypsat klíče účtu úložiště.
* Schopnost definovat vlastní role je funkce, která umožňuje vytvořit sadu akcí ze seznamu dostupných akcí, které lze provést u prostředků Azure.
* Předtím, než k nim můžete přiřadit roli, musí uživatel nastavil v Azure Active Directory.
* Můžete vytvořit sestavu kdo udělit nebo odvolat jaký typ přístupu do nebo z kterého a na jaké oboru pomocí Powershellu nebo rozhraní příkazového řádku Azure.

#### <a name="resources"></a>Zdroje a prostředky
* [Řízení přístupu na základě role v Azure Active Directory](../../role-based-access-control/role-assignments-portal.md)

  Tento článek popisuje řízení přístupu podle role v Azure Active Directory a vysvětluje, jak funguje.
* [RBAC: vestavěné role](../../role-based-access-control/built-in-roles.md)

  Tento článek podrobně popisuje všechny vestavěné role dostupné v RBAC.
* [Principy nasazení podle modelu Resource Manager a klasického nasazení](../../azure-resource-manager/resource-manager-deployment-model.md)

  Tento článek vysvětluje nasazení podle modelu Resource Manager a modely nasazení classic a vysvětluje výhody použití Resource Manageru a prostředek skupiny. Vysvětluje, jak fungují Azure Compute, Network a poskytovatelé úložiště zřízených podle modelu Resource Manageru.
* [Správa řízení přístupu na základě role pomocí REST API](../../role-based-access-control/role-assignments-rest.md)

  Tento článek popisuje, jak používat rozhraní REST API ke správě RBAC.
* [Reference k REST API služby Azure Storage Resource Provider](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Reference k rozhraní API popisuje rozhraní API můžete použít ke správě vašeho účtu úložiště prostřednictvím kódu programu.
* [Ověřování pomocí Správce prostředků rozhraní API pro přístup k předplatným](../../azure-resource-manager/resource-manager-api-authentication.md)

  Tento článek popisuje, jak provést ověření pomocí rozhraní API Resource Manageru.
* [Řízení přístupu na základě role pro Microsoft Azure z Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Odkaz na video na Channel 9 z konference MS Ignite 2015. Na tomto sezení se hovoří o možnostech správy přístupu a generování sestav v Azure a probírají se osvědčené postupy pro zabezpečení přístupu k předplatným Azure pomocí Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>Správa klíčů účtu úložiště
Klíče účtu úložiště jsou 512 bitů řetězce, které vytvořil Azure, který spolu s názvem účtu úložiště, může být použita pro přístup k objektům data uložená v účtu úložiště, například, objekty BLOB, entity do tabulky, fronty zpráv a souborům ve sdílené složky Azure. Řízení přístupu k úložiště účtu klíče řídí přístup k rovině dat pro daný účet úložiště.

Každý účet úložiště má dva klíče, které jsou označovány jako "Klíč 1" a "Klíč 2" v [webu Azure portal](http://portal.azure.com/) a v rutinách prostředí PowerShell. Toto může být znovu vygenerován ručně pomocí jedné z několika metod, včetně, ale není možné použít [webu Azure portal](https://portal.azure.com/), prostředí PowerShell, rozhraní příkazového řádku Azure nebo programově pomocí klientské knihovny úložiště .NET nebo služby Azure Storage ROZHRANÍ REST API.

Existuje spousta důvodů se znovu vygenerovat své klíče účtu úložiště.

* Možná je znovu v pravidelných intervalech z bezpečnostních důvodů.
* Pokud někdo spravovat tak, aby proniknout do aplikace a získat klíč pevně zakódované nebo uložený v konfiguračním souboru jim poskytne úplný přístup k účtu úložiště, bude znovu vygenerovat své klíče účtu úložiště.
* Další možnost je pro generování klíče je, pokud váš tým používá aplikace Průzkumníka služby Storage, který bude mít klíč účtu úložiště a jeden z členů týmu opustí. Aplikace bude pokračovat v práci, že jim udělíte přístup k vašemu účtu úložiště po jejich pryč. Toto je ve skutečnosti primárním důvodem, proč, které vytvořil účet úrovni sdílených přístupových podpisů – místo ukládání přístupových klíčů v konfiguračním souboru můžete použít SAS na úrovni účtu.

#### <a name="key-regeneration-plan"></a>Plán obnovení klíče
Nechcete stačí znovu vygenerovat klíč, který používáte bez některé plánování. Pokud to uděláte, může oříznou veškerý přístup k tomuto účtu úložiště, což může způsobit narušení hlavní. To je důvod, proč existují dva klíče. Vždy byste měli nově vygenerovat jeden klíč.

Předtím, než můžete znovu vygenerovat klíče, ujistěte se, že máte seznam všech aplikací, které jsou závislé na účet úložiště, stejně jako jiné služby, které použijete v Azure. Například pokud používáte Azure Media Services, které jsou závislé na vašem účtu úložiště, můžete musí znovu synchronizovat přístupové klíče s touto službou po opětovném vygenerování klíče. Pokud používáte žádné aplikace, jako je Průzkumník úložišť, je potřeba zadat nové klíče pro tyto aplikace také. Pokud máte virtuální počítače, jejichž soubory virtuálního pevného disku se ukládají v účtu úložiště, že nebudou ovlivněny obnovování klíčů účtu úložiště.

Můžete znovu vygenerovat klíče na webu Azure Portal. Jakmile budou znovu vygenerovány klíčů, se může trvat až 10 minut, které se mají synchronizovat napříč službami úložiště.

Jakmile budete připraveni, tady je obecný postup s podrobnostmi o tom, jak byste měli změnit váš klíč. V tomto případě předpokladem je, že používáte klíč 1 a se chystáte změnit všechno, co je místo toho použít klíč 2.

1. Obnovit klíč 2 a ujistěte se, že se jedná o zabezpečení. Můžete to provést na webu Azure Portal.
2. Ve všech aplikací, kde se má uložit klíč úložiště změňte klíč úložiště, který chcete použít novou hodnotu klíče 2. Testování a publikování aplikace.
3. Všechny aplikace a služby jsou v provozu a spuštěna úspěšně, znovu vygenerovat klíč 1. Tím se zajistí, že kdokoli, kterým jste nedali výslovně nový klíč už mají přístup k účtu úložiště.

Pokud aktuálně používáte klíče 2, můžete použít stejný postup, ale zpětné názvy klíčů.

Za několik dní, můžete migrovat změna každou aplikaci, aby používala nový klíč a jeho publikování. Po dokončení všechny z nich by pak přejděte zpět a znovu vygenerovat starého klíče, takže už funguje.

Další možností je umístění klíče účtu úložiště do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) jako tajný klíč a mít vaše aplikace načíst z něj klíč. Potom když se znovu vygenerovat klíč a aktualizace služby Azure Key Vault, aplikace nebudete muset znovu nasadit, protože jejich vyzvedne, až bude nový klíč ze služby Azure Key Vault automaticky. Všimněte si, že může mít jen pro čtení klíče pokaždé, když je potřebujete, nebo můžete mezipaměti v paměti a pokud selže při jeho použití, klíč znovu načíst ze služby Azure Key Vault.

Pomocí služby Azure Key Vault také přidává další úroveň zabezpečení pro vaše klíče úložiště. Pokud použijete tuto metodu, budete mít klíče pevně zakódované úložiště nikdy v konfiguračním souboru, který odebere tento způsob případného někdo získal přístup ke klíčům bez konkrétní oprávnění.

Další výhodou používání služby Azure Key Vault je, že můžete také řídit přístup k svoje klíče pomocí služby Azure Active Directory. To znamená, že můžete udělit přístup k několika málo až po aplikace, které potřebují k načtení klíče ze služby Azure Key Vault a vědět, že jiné aplikace nebudou moct bez nutnosti přidělit jim konkrétně oprávnění pro přístup.

> [!NOTE]
> Microsoft doporučuje používat pouze jeden z klíčů ve všech vašich aplikací ve stejnou dobu. Pokud používáte klíč 1 na některých místech a klíč 2 v jiných, nebude možné obměna klíčů bez některé aplikace došlo ke ztrátě přístupu.

#### <a name="resources"></a>Zdroje a prostředky

* [Spravovat nastavení účtu úložiště na webu Azure Portal](storage-account-manage.md)
* [Reference k REST API služby Azure Storage Resource Provider](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Zabezpečení roviny dat
Zabezpečení roviny dat odkazuje na metody používané k zabezpečení datové objekty uložené ve službě Azure Storage – objekty BLOB, fronty, tabulky a soubory. Zaznamenali jsme metody k šifrování dat a zabezpečení během přenosu dat, ale jak do můžete přejít o řízení přístupu k objektům?

Existují tři možnosti pro autorizaci přístupu k datovým objektům ve službě Azure Storage, včetně:

- Používání služby Azure AD k autorizaci přístupu ke kontejnerům a front (Preview). Azure AD poskytuje výhody oproti další přístupy k ověřování, včetně odstraňují potřebu tajné kódy ukládat v kódu. Další informace najdete v tématu [ověření přístupu ke službě Azure Storage pomocí Azure Active Directory (Preview)](storage-auth-aad.md). 
- K ověřování přístupu prostřednictvím sdíleného klíče pomocí klíče účtu úložiště. Ověřování pomocí sdíleného klíče vyžaduje uložení klíče účtu úložiště ve vaší aplikaci, takže Microsoft doporučuje místo toho pomocí služby Azure AD, kde je to možné. Pro produkční aplikace, nebo pro autorizaci přístupu k Azure tabulky a soubory pokračujte v používání sdíleného klíče, integrace služby Azure AD je ve verzi preview.
- Použití sdílených přístupových podpisů udělit řízenými oprávněními pro konkrétní datové objekty určitou dobu.

Kromě toho pro úložiště objektů Blob, můžete povolit veřejný přístup k objektům BLOB tak, že nastavíte úroveň přístupu pro kontejner, který obsahuje objekty BLOB odpovídajícím způsobem. Pokud nastavíte přístupu pro kontejner objektů Blob nebo kontejneru, vám umožní veřejný přístup pro čtení pro objekty BLOB v tomto kontejneru. To znamená, že každý, kdo má adresu URL odkazující na objekt blob v tomto kontejneru ho otevřete v prohlížeči bez použití sdíleného přístupového podpisu nebo s klíče účtu úložiště.

Kromě omezení přístupu prostřednictvím ověřování, můžete také použít [virtuální sítí a bran firewall](storage-network-security.md) omezit přístup k účtu úložiště na základě pravidel sítě.  Tento přístup umožňuje odepřít přístup k veřejné internetové přenosy a udělte přístup jenom ke konkrétním virtuálním sítím Azure nebo veřejná internetová rozsahy IP adres.

### <a name="storage-account-keys"></a>Klíče účtu úložiště
Klíče účtu úložiště jsou 512 bitů řetězce, které vytvořil Azure, kterou, spolu s názvem účtu úložiště, můžete použít pro přístup k objektům dat uložených v účtu úložiště.

Může číst objekty BLOB, zapisovat do fronty, vytvářet tabulky a upravovat soubory. Mnohé z těchto akcí lze provést prostřednictvím webu Azure portal nebo pomocí jednoho z mnoha aplikací Průzkumníka služby Storage. Můžete také napsat kód pro použití rozhraní REST API nebo některou z klientských knihoven pro úložiště k provádění těchto operací.

Jak je popsáno v části na [zabezpečení roviny správy](#management-plane-security), přístup k úložišti klíčů pro účet klasického úložiště lze udělit tím, že úplný přístup k předplatnému Azure. Přístup ke klíčům úložiště pro účet úložiště pomocí modelu Azure Resource Manager je možné řídit prostřednictvím řízení přístupu na základě Role (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Jak delegovat přístup k objektům ve vašem účtu pomocí sdílené přístupové podpisy a uložené zásady přístupu
Sdílený přístupový podpis je řetězec obsahující token zabezpečení, který lze připojit k identifikátoru URI, který umožňuje delegovat přístup k objektům úložiště a zadat omezení, jako jsou oprávnění a datový/časový rozsah přístupu.

Můžete udělit přístup k blobům, kontejnery, zprávy do fronty, soubory a tabulky. S tabulkami ve skutečnosti můžete udělit oprávnění pro přístup k rozsahu entit v tabulce tak, že zadáte oddílu a řádku klíče rozsahů, do kterých chcete uživateli udělit přístup. Například pokud máte data uložená pomocí klíče oddílu zeměpisné stavu, může někomu poskytnete přístup k pouze data pro akci.

Například může poskytnout token SAS, který umožňuje zapisovat položky do fronty webovou aplikaci a poskytnout pracovní role aplikace k získání zpráv z fronty a jejich zpracování tokenu SAS. Nebo jednoho zákazníka může poskytnout token SAS, můžete použít k nahrávání obrázků do kontejneru v úložišti objektů Blob a udělit oprávnění k aplikaci pro webové, přečtěte si tyto obrázky. V obou případech je oddělené oblasti zájmu – každá aplikace mohou být zadány pouze přístup, který vyžadují, abyste mohli provádět jejich úloh. Je to možné prostřednictvím použití sdílených přístupových podpisů.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Proč chcete použít sdílené přístupové podpisy
Proč byste měli použít SAS místo pouze poskytnutí klíč účtu úložiště, což je mnohem snadnější? Poskytnutí klíč účtu úložiště je třeba sdílení klíčů království vašeho úložiště. Uděluje plný přístup. Někdo by mohla používat klíče a jejich celá knihovna nahrát do účtu úložiště. Také se může nahradit soubory verze napadeným viry nebo ukrást vaše data. Poskytne neomezený přístup, k vašemu účtu úložiště je objekt, který by se neměl brát lehce.

Se sdílenými přístupovými podpisy můžete udělit pouze oprávnění požadovaná pro omezené množství času klienta. Například pokud chcete ke svému účtu, někdo je nahrát objekt blob, můžete jim udělíte přístup pro zápis pro právě dostatek času k nahrání objektu blob (v závislosti na velikosti objektu blob, samozřejmě). A pokud změníte své rozhodnutí, můžete odvolat tento přístup.

Kromě toho můžete určit, aby byly omezené na IP adresu nebo rozsah IP adres pro Azure externí žádosti pomocí SAS. Můžete také vyžadovat, požadavky přicházejí pomocí konkrétní protokolu (HTTPS nebo HTTP/HTTPS). To znamená, že pokud chcete povolit provoz protokolu HTTPS, můžete nastavit požadovaný protokol HTTPS pouze a budou blokovány přenosy HTTP.

#### <a name="definition-of-a-shared-access-signature"></a>Definice sdíleného přístupového podpisu
Sdílený přístupový podpis je sada připojí k adrese URL odkazující na prostředek parametry dotazu.

který poskytuje informace o povoleného přístupu a dobu, pro který je povolen přístup. Tady je příklad; Pomocí tohoto identifikátoru URI poskytuje přístup pro čtení do objektu blob pro pět minut. Všimněte si, že parametrů dotazu SAS musí být kódovaná v řetězci URL, jako je například 3A % dvojtečkou (:) nebo % 20 mezerou.

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

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Jak je sdílený přístupový podpis povolení služby Azure Storage
Požadavku přijetí na službu storage má vstupní parametry a vytvoří podpis stejným způsobem jako volající program. Pak porovná dva podpisy. Pokud souhlasí, můžete zkontrolovat na službu storage verze služby úložiště, ujistěte se, že je platný, ověřte, že aktuální datum a čas jsou v zadaném okně, ujistěte se, že požadovaný přístup odpovídá požadavku atd.

Například s naší adresy URL výše, pokud se adresa URL odkazuje na soubor místo objektu blob, tento požadavek by selhat, protože určuje, že je sdílený přístupový podpis pro objekt blob. Pokud byl příkaz REST volané aktualizovat objekt blob, ho selže, protože sdílený přístupový podpis Určuje, zda smí obsahovat pouze oprávnění ke čtení.

#### <a name="types-of-shared-access-signatures"></a>Druhy sdílených přístupových podpisů
* SAS úrovni služby slouží pro přístup k určitým prostředkům v účtu úložiště. Některé příklady načítají seznam objektů BLOB v kontejneru, stažení objektu blob, aktualizuje entity v tabulce, přidání zprávy do fronty nebo nahrání souboru do sdílené složky.
* Cokoli, co SAS úrovni služby slouží pro přístup k lze SAS úrovni účtu. Kromě toho poskytuje možnosti pro prostředky, které se nedá vymezit přes SAS úrovně služeb, jako je například schopnost vytvářet kontejnery, tabulky, fronty a sdílené složky. Přístup k více službám můžete také zadat najednou. Například někdo může udělit přístup k objektům BLOB a souborů ve vašem účtu úložiště.

#### <a name="creating-a-sas-uri"></a>Vytváří se identifikátor URI SAS
1. Vytvořit identifikátor URI na vyžádání, definovat všechny parametry dotazu pokaždé, když.

   Tento přístup je flexibilní, ale pokud máte logickou sadu parametrů, které jsou podobné pokaždé, když pomocí zásady přístupu uložené je lepší představu.
2. Můžete vytvořit zásadu přístupu uložené pro celý kontejner, sdílené složky, tabulek nebo front. Pak je můžete použít jako základ pro identifikátory URI SAS, které vytvoříte. Oprávnění podle principu uložené zásady přístupu můžete snadno odvolat. Můžete mít až o pěti zásad definovaných na každý kontejner, fronty, tabulky nebo sdílené složky.

   Například pokud budeme mít mnoho lidí čtení objektů BLOB v kontejneru pro konkrétní, můžete vytvořit uložené zásady přístupu, který říká "udělit přístup pro čtení" a další nastavení, které budou stejné pokaždé, když. Potom můžete vytvořit identifikátor URI SAS pomocí nastavení zásad přístupu uložené a zadáte datum a čas vypršení platnosti. Výhodou je, že není nutné zadat všechny parametry dotazu pokaždé, když.

#### <a name="revocation"></a>Odvolání
Předpokládejme, že došlo k napadení vašeho SAS, nebo chcete změnit z důvodu podnikového zabezpečení nebo požadavky na dodržování legislativních předpisů. Jak odvoláte přístup k prostředkům pomocí tohoto SAS To závisí na tom, jak vytvořit identifikátor URI SAS.

Pokud používáte ad hoc identifikátory URI, máte tři možnosti. Může vystavovat tokeny SAS se zásadami pro konec platnosti krátký a počkejte SAS vyprší. Můžete přejmenovat nebo odstranit prostředek (za předpokladu, že token, který byl omezená na jeden objekt). Klíče účtu úložiště můžete změnit. Poslední možnost může mít významný dopad, v závislosti na tom, kolik services používají tento účet úložiště a pravděpodobně není něco, co chcete udělat bez některé plánování.

Pokud používáte SAS odvozené ze zásad přístupu uložené, může přístup odebrat zrušením uložené zásady přístupu – můžete jenom ho změnit tak, aby již vypršela, nebo ji úplně odebrat. To se projeví okamžitě a zruší platnost všech SAS vytvořené pomocí této zásady přístupu uložené. Aktualizace nebo odebrání zásady přístupu uložené může dopad uživatelé s přístupem k této konkrétní kontejner, sdílené složky, tabulky nebo fronty přes SAS, ale pokud klienti se zapisují, požádá o novou SAS při původní stane neplatná, bude to fungovat bez problémů.

Protože pomocí SAS odvozené ze zásad přístupu uložené poskytuje možnost neprodleně odvolat tento SAS, je doporučených osvědčených postupů a vždy uložené zásady přístupu použít pokud je to možné.

#### <a name="resources"></a>Zdroje a prostředky
Podrobnější informace o použití sdílené přístupové podpisy a uložené zásady přístupu, včetně příkladů najdete v následujících článcích:

* Jedná se o odkaz na články.

  * [SAS služby](https://msdn.microsoft.com/library/dn140256.aspx)

    Tento článek obsahuje příklady použití SAS úrovni služby s objekty BLOB, fronty zpráv, rozsahy tabulky a soubory.
  * [Vytváření SAS služby](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Vytváření SAS účtu](https://msdn.microsoft.com/library/mt584140.aspx)
* Jedná se o kurzy zaměřené na používání klientské knihovny .NET pro vytvoření sdílené přístupové podpisy a uložené zásady přístupu.

  * [Použití sdílených přístupových podpisů (SAS)](../storage-dotnet-shared-access-signature-part-1.md)
  * [Sdílené přístupové podpisy, část 2: Vytvoření a použití SAS pomocí služby Blob Service](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    Tento článek obsahuje vysvětlení modelu SAS, příklady sdílené přístupové podpisy a doporučení k osvědčeným postupem pomocí SAS. Odvolání oprávnění udělené popsány také je.

* Authentication

  * [Ověřování pro služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Sdílené přístupové podpisy, jak začít kurz

  * [SAS, jak začít kurz](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Šifrování během přenosu
### <a name="transport-level-encryption--using-https"></a>Šifrování na úrovni přenosu – pomocí protokolu HTTPS
Další krok, který byste měli provést k zajištění bezpečnosti vašich dat služby Azure Storage je k šifrování dat mezi klientem a Azure Storage. První doporučení je vždycky potřeba použít [HTTPS](https://en.wikipedia.org/wiki/HTTPS) protokol, který zajišťuje bezpečnou komunikaci přes veřejný Internet.

Pokud chcete, aby zabezpečený komunikační kanál, abyste používali vždy HTTPS při volání rozhraní REST API nebo přístup k objektům ve službě storage. Navíc **sdílené přístupové podpisy**, který umožňuje delegovat přístup k objektům služby Azure Storage, zahrnují možnost určit, že při použití sdílených přístupových podpisů, zajistit, aby kdokoli odesílání lze použít pouze protokolu HTTPS si propojení s SAS bude používat tokeny správný protokol.

Při volání rozhraní REST API pro přístup k objektům v účtech úložiště tím, že můžete vynutit použití protokolu HTTPS [vyžadovat zabezpečený přenos](../storage-require-secure-transfer.md) pro účet úložiště. Připojení pomocí protokolu HTTP bude odmítnuto, když je tato možnost povolena.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Šifrování během přenosu pomocí sdílených složek Azure
[Služba soubory Azure](../files/storage-files-introduction.md) podporuje šifrování pomocí protokolu SMB 3.0 a HTTPS, při použití souborového rozhraní REST API. Při připojení sdílenou složku Azure mimo oblast Azure je umístěn, například v místním prostředí nebo v jiné oblasti Azure, se vždy vyžaduje se šifrování protokolu SMB 3.0. Protokol SMB 2.1 nepodporuje šifrování, tak ve výchozím nastavení připojení jsou povolené jenom v rámci stejné oblasti v Azure, ale můžete vynutit pomocí šifrování SMB 3.0 [vyžaduje zabezpečený přenos](../storage-require-secure-transfer.md) pro účet úložiště.

Pomocí šifrování SMB 3.0 je k dispozici v [všechny podporované operační systémy Windows a Windows Server](../files/storage-how-to-use-files-windows.md) s výjimkou Windows 7 a Windows Server 2008 R2, které podporují jenom protokol SMB 2.1. Protokol SMB 3.0 je také podporována na [macOS](../files/storage-how-to-use-files-mac.md) a distribuce [Linux](../files/storage-how-to-use-files-linux.md) pomocí linuxového jádra 4.11 a vyšší. Podpora šifrování protokolu SMB 3.0, přijal se rovněž přeneseny zpět ke starším verzím sady jádro Linuxu pomocí několika Linuxových distribucích najdete [požadavky na klienta SMB Principy](../files/storage-how-to-use-files-linux.md#smb-client-reqs).

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Pomocí šifrování na straně klienta k zabezpečení dat, která odesíláte do služby storage
Další možnost, která vám pomůže zajistit, že vaše data jsou zabezpečená při přenosu mezi klientskou aplikací a úložiště se šifrování na straně klienta. Data se zašifrují před odesláním do služby Azure Storage. Při načítání dat ze služby Azure Storage, data se dešifrují po přijetí na straně klienta. I když se data zašifrují trvajícího přenosu, doporučujeme také používat protokol HTTPS, je na něm kontrolu integrity dat součástí, které zmírnění chyby sítě by to mělo dopad integritu dat.

Šifrování na straně klienta je také metodu k šifrování vašich dat v klidovém stavu, jako jsou data uložena v šifrované podobě. Budeme mluvit o to podrobněji v oddílu na [šifrování v klidovém stavu](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Šifrování v klidovém stavu
Existují tři funkce Azure, které poskytují šifrování v klidovém stavu. Azure Disk Encryption se používá k šifrování operačního systému a datové disky ve virtuálních počítačích IaaS. SSE a šifrování na straně klienta se používají k šifrování dat ve službě Azure Storage. 

Při šifrování na straně klienta můžete použít k šifrování dat při přenosu (které jsou také uloženy v zašifrované podobě v úložišti), můžete chtít používat protokol HTTPS během přenosu a mají některé způsob, jak data, která mají být automaticky šifrují, pokud je uložen. Existují dva způsoby, jak tomu – Azure Disk Encryption a SSE. Jeden se používá k přímo šifrování dat na operační systém a datové disky, které jsou používány virtuálními počítači a druhý se používá k šifrování dat zapsaných do úložiště objektů Blob v Azure.

### <a name="storage-service-encryption-sse"></a>Šifrování služby Storage (SSE)

SSE je povolená pro všechny účty úložiště a nejde zakázat. SSE automaticky šifruje vaše data při zápisu do služby Azure Storage. Při čtení dat ze služby Azure Storage, dešifruje se službou Azure Storage před vrácením. SSE umožňuje zabezpečit vaše data bez nutnosti měnit kód nebo přidejte kód do všech aplikací.

Můžete použít klíče spravované zákazníkem Microsoftu nebo vlastní klíče. Společnost Microsoft generuje spravovaných klíčů a zpracovává jejich zabezpečeného úložiště, stejně jako jejich regulární otočení, jak je definována zásadami Microsoftu pro interní. Další informace o použití vlastních klíčů najdete v tématu [šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

SSE automaticky šifruje data na všech úrovních výkonu (Standard a Premium), ve všech modelech nasazení (Azure Resource Manager a Classic) a ve všech službách Azure Storage (Blob, Queue, Table a File). 

### <a name="client-side-encryption"></a>Šifrování na straně klienta
Když hovoříte o šifrování přenášených dat jsme se zmínili šifrování na straně klienta. Tato funkce umožňuje programově zašifrovat data v klientské aplikaci před odesláním sítí k zápisu do služby Azure Storage a k programové dešifrování dat po načtení ze služby Azure Storage.

To poskytuje šifrování během přenosu, ale také poskytuje funkci šifrování v klidovém stavu. I když se data se šifrují během přenosu, doporučujeme využít předdefinované datové kontroly integrity, které zmírnění chyby sítě by to mělo dopad integritu dat pomocí protokolu HTTPS.

Příkladem, kdy tuto funkci můžete použít se, pokud máte webovou aplikaci, která ukládá objekty BLOB a načte objekty BLOB, a chcete, aby aplikace a data, která bude co nevíce zabezpečený. V takovém případě byste použili šifrování na straně klienta. Přenos dat mezi klientem a služby Azure Blob Service obsahuje šifrované prostředek a nikdo interpretovat přenášených dat a rekonstruovat do privátní objektů BLOB.

Šifrování na straně klienta je integrované do jazyka Java a .NET klientských knihoven pro úložiště, které pak použijte Key Vault rozhraní API Azure, usnadňuje implementaci. Proces šifrování a dešifrování dat používá techniku obálky a ukládá metadata, používá šifrování jednotlivých objektů úložiště. Například pro objekty BLOB, uloží ho v metadata objektu blob, zatímco pro fronty, přidá ho ke každé zprávě fronty.

Pro šifrování, sama můžete vygenerovat a spravovat vlastní šifrovací klíče. Můžete použít také klíče generovaný klientskou knihovnu pro úložiště Azure, nebo můžete mít generování klíčů služby Azure Key Vault. Můžete uložit šifrovací klíče ve službě storage klíčů v místním nebo uložit je do služby Azure Key Vault. Služba Azure Key Vault můžete udělit přístup k tajným kódům ve službě Azure Key Vault na konkrétní uživatele pomocí služby Azure Active Directory. To znamená, ne jenom kdokoli si služby Azure Key Vault a načtení klíčů, které používáte pro šifrování na straně klienta.

#### <a name="resources"></a>Zdroje a prostředky
* [Šifrování a dešifrování objektů BLOB ve službě Microsoft Azure Storage pomocí Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  Tento článek ukazuje, jak pomocí šifrování na straně klienta se službou Azure Key Vault, včetně vytvoření klíče KEK a uložit v trezoru pomocí Powershellu.
* [Šifrování na straně klienta a Azure Key Vault pro Microsoft Azure Storage](../storage-client-side-encryption.md)

  Tento článek poskytuje vysvětlení šifrování na straně klienta a poskytuje příklady použití klientskou knihovnu pro úložiště k šifrování a dešifrování prostředky ze čtyř služeb úložiště. Také hovoří o službě Azure Key Vault.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Použití Azure Disk Encryption pro šifrování disků, které jsou používány virtuálními počítači
Azure Disk Encryption je nová funkce. Tato funkce umožňuje šifrovat disky s operačním systémem a datové disky použít tak virtuální počítač IaaS. Pro Windows jsou šifrované jednotky pomocí standardní technologie šifrování nástrojem BitLocker. Pro Linux disky jsou šifrované pomocí technologii DM-Crypt. To je integrovaná s Azure Key Vault, aby bylo možné řídit a spravovat klíče pro šifrování disků.

Řešení podporuje následující scénáře pro virtuální počítače IaaS, pokud je povolen v Microsoft Azure:

* Integrace se službou Azure Key Vault
* Virtuální počítače úrovně Standard: [A, D, DS, G, GS a podobně řady virtuálních počítačů IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Povolení šifrování na Windows a virtuálních počítačů IaaS s Linuxem
* Zakázáním šifrování u operačního systému a datové disky pro virtuální počítače IaaS s Windows
* Zakázáním šifrování na datových jednotkách virtuálních počítačů IaaS s Linuxem
* Povolení šifrování na virtuální počítače IaaS, spuštěný klientský operační systém Windows
* Povolení šifrování na svazcích s cestami k připojení
* Povolení šifrování na virtuální počítače s Linuxem, které jsou nakonfigurované pro disk s použitím mdadm prokládání (RAID)
* Povolení šifrování na virtuální počítače s Linuxem s využitím LVM pro datové disky
* Povolení šifrování na virtuálních počítačích s Windows, které jsou nakonfigurované pomocí prostorů úložiště
* Jsou podporovány všech veřejných oblastech Azure

Toto řešení nepodporuje následující scénáře, funkce a technologie ve vydané verzi:

* Úroveň Basic virtuálních počítačů IaaS
* Zakázáním šifrování na disku s operačním systémem pro virtuální počítače IaaS s Linuxem
* Virtuální počítače IaaS, které jsou vytvořeny pomocí klasické metody vytvoření virtuálního počítače
* Integrace s vaší místní služba správy klíčů
* Služba soubory Azure (sdílený systém souborů), Network File System (NFS), dynamických svazků a virtuálních počítačů s Windows, které jsou nakonfigurované pro systémy založené na softwaru diskového pole RAID


> [!NOTE]
> Šifrování disku operačního systému Linux momentálně se podporuje v následujících distribucích systému Linux: RHEL 7.2 CentOS 7.2n a Ubuntu 16.04.
>
>

Tato funkce zajišťuje, že všechna data na disky virtuálního počítače se šifrují při nečinnosti ve službě Azure Storage.

#### <a name="resources"></a>Zdroje a prostředky
* [Azure Disk Encryption pro Windows a virtuální počítače s Linuxem v režimu IaaS](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Porovnání Azure Disk Encryption, SSE a šifrování na straně klienta

#### <a name="iaas-vms-and-their-vhd-files"></a>Virtuální počítače IaaS a jejich soubory virtuálního pevného disku

Pro datové disky používány virtuálními počítači IaaS se doporučuje Azure Disk Encryption. Pokud vytvoříte virtuální počítač s nespravovanými disky pomocí image z Azure Marketplace, Azure provádí [Nedávná kopírování](https://en.wikipedia.org/wiki/Object_copying) Image do úložiště účet v Azure Storage který není šifrovaný i v případě, že máte SSE povolena. Až se virtuální počítač vytvoří a spustí aktualizaci bitové kopie, začne SSE šifruje data. Z tohoto důvodu je nejvhodnější použít Azure Disk Encryption na virtuální počítače s nespravovanými disky vytvořené z imagí v Tržišti Azure Marketplace, pokud chcete, aby je plně zašifrované. Pokud vytvoříte virtuální počítač se službou Managed Disks, SSE šifruje všechna data ve výchozím nastavení používat klíče spravované platformy. 

Pokud převedete předem šifrovaných virtuálních počítačů do Azure v místním, budete moct nahrát šifrovací klíče do služby Azure Key Vault a pokračovat v používání šifrování pro tento virtuální počítač, které jste používali v místním. Azure Disk Encryption je povoleno tímto scénářem poradit.

Pokud máte nešifrované virtuálního pevného disku v místním, můžete nahrát do Galerie jako vlastní image a zřízení virtuálního počítače z něj. Pokud jste to pomocí šablon Resource Manageru, můžete požádat ho chcete zapnout Azure Disk Encryption po spuštění virtuálního počítače.

Pokud chcete přidat datový disk a připojit na virtuálním počítači, můžete zapnout v Azure Disk Encryption tento datový disk. Nejprve šifrovat tento disk data místně a vrstva modelu nasazení classic se proveďte opožděný zápis s úložištěm tak obsah úložiště je zašifrovaný.

#### <a name="client-side-encryption"></a>Šifrování na straně klienta
Šifrování na straně klienta je nejbezpečnější metodu šifrování dat, protože šifruje data před přenosu.  Ale vyžadují, přidat kód do vaší aplikace s použitím úložiště, které nemusíte chtít provést. V takových případech můžete použít HTTPS k zabezpečení dat při přenosu. Jakmile se data dosáhnou služby Azure Storage, se šifrují pomocí SSE.

Pomocí šifrování na straně klienta můžete šifrovat entity tabulky, fronty zpráv a objekty BLOB. 

Šifrování na straně klienta je kompletně spravované službou aplikace. Toto je nejbezpečnější úroveň přístupu, ale vyžaduje, abyste proveďte programové změny do vaší aplikace a zavést procesy správy klíčů. To byste použili, pokud chcete zvýšit zabezpečení během přenosu a chcete, aby vaše uložená data šifrování.

Šifrování na straně klienta je větší zátěže na straně klienta budete muset pro tento účet v plánech škálovatelnost, zejména v případě, že jsou šifrování a přenosu velkých objemů dat

#### <a name="storage-service-encryption-sse"></a>Šifrování služby Storage (SSE)

SSE se spravuje přes Azure Storage. SSE neposkytuje pro zabezpečení přenášených dat, ale šifrování dat při zápisu do služby Azure Storage. SSE nemá vliv na výkon služby Azure Storage.

Můžete k šifrování všech typů dat z účtu úložiště pomocí SSE (objekty BLOB bloku, doplňovací objekty BLOB, objekty BLOB stránky, data tabulky, fronty data a soubory).

Pokud máte archivu nebo knihovna soubory virtuálního pevného disku, které můžete použít jako základ pro vytváření nových virtuálních počítačů, můžete vytvořit nový účet úložiště a pak nahrajte soubory virtuálního pevného disku k tomuto účtu. Tyto soubory virtuálního pevného disku budou šifrovány pomocí služby Azure Storage.

Pokud máte povolené pro disky na virtuálním počítači Azure Disk Encryption, všechna nově zapsaná data je šifrovaná pomocí SSE a službou Azure Disk Encryption.

## <a name="storage-analytics"></a>Storage Analytics
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Použití Storage Analytics k monitorování typ autorizace
Pro každý účet úložiště můžete povolit Azure Storage Analytics provádět protokolování a ukládat data metrik. Jedná se o skvělý nástroj pro použití při chtít zkontrolovat metriku výkonu účtu úložiště, nebo potřebujete k řešení potíží s účtem služby storage, protože máte potíže s výkonem.

Další část dat, která se zobrazí v protokoly storage analytics je metodu ověřování používanou někdo při přístupu k úložišti. S úložištěm objektů Blob můžete například zobrazit používají sdílený přístupový podpis nebo klíče účtu úložiště nebo pokud byl objekt blob získat přístup k veřejné.

To může být užitečné, pokud jsou úzce důslednější přístup k úložišti. V úložišti objektů Blob můžete například nastavit všechny kontejnery do privátního a implementovat pomocí SAS služby v rámci vašich aplikací. Potom můžete zkontrolovat protokoly pravidelně a zda objektů BLOB jsou přístupné pomocí klíče účtu úložiště, což může znamenat, že porušení zabezpečení, nebo pokud objekty BLOB byly veřejné, ale neměly by být.

#### <a name="what-do-the-logs-look-like"></a>Co v protokolech vypadat?
Po povolení metriky účtu úložiště a protokolování na webu Azure portal, analytická data se začne rychle narůstat. Protokolování a metriky pro každou službu je oddělená; protokolování je zapisovány pouze v případě, že je aktivita v účtu úložiště, zatímco metriky zaprotokoluje se každou minutu, za hodinu nebo každý den, v závislosti na tom, jak ho nakonfigurujete.

Protokoly se ukládají v objektech BLOB bloku v kontejneru nazvaném $logs v účtu úložiště. Tento kontejner se automaticky vytvoří, když je povolená analytika úložiště. Po vytvoření tohoto kontejneru je nelze ji odstranit, i když odstraníte jeho obsah.

V kontejneru $logs je složka pro každou službu a pak podsložky existují pro rok/měsíc/den/hodina. Za hodinu jsou číslovány protokoly. Je to, jak bude vypadat adresářovou strukturu:

![Zobrazit soubory protokolu](./media/storage-security-guide/image1.png)

Se protokoluje při každé žádosti do služby Azure Storage. Zde je snímek souboru protokolu zobrazuje prvních několika polí.

![Snímek souboru protokolu](./media/storage-security-guide/image2.png)

Uvidíte, že můžete protokoly sledování jakýkoli druh volání do účtu úložiště.

#### <a name="what-are-all-of-those-fields-for"></a>Co jsou všechna tato pole pro?
Existuje článek uvedené v prostředcích níže, který obsahuje seznam mnoho polí v protokolech a jejich použití. Tady je seznam polí v pořadí:

![Snímek polí v souboru protokolu](./media/storage-security-guide/image3.png)

Nás zajímá položky pro GetBlob, a způsob, jakým jsou ověřeny, takže potřebujeme vyhledejte položky s typem operace "Get-Blob" a zkontrolovat stav žádosti o (čtvrtá</sup> sloupce) a typ autorizace (osmého</sup> sloupec).

Například v několik prvních řádků v ukázce výše, stavu žádosti je "Success" a typ autorizace je "ověřit". To znamená, že žádost byla oprávnění, pomocí klíče účtu úložiště.

#### <a name="how-is-access-to-my-blobs-being-authorized"></a>Jak se oprávnění pro moje objekty BLOB autorizaci?
Máme tři případy, které nás zajímají.

1. Objekt blob je veřejná a je přístupný pomocí adresy URL bez sdíleného přístupového podpisu. V tomto případě stavu žádosti je "AnonymousSuccess" a "anonymní" je typ autorizace.

   1.0; 2015-11-17T02:01:29.0488963Z; GetBlob; **AnonymousSuccess**; 200 124; 37; **Anonymní**; mystorage...
2. Objekt blob je privátní a byl použit pro sdílený přístupový podpis. V tomto případě stav žádosti je "SASSuccess" a "sas" je typ autorizace.

   1.0; 2015-11-16T18:30:05.6556115Z; GetBlob; **SASSuccess**, 200, 416, 64; **SAS**; mystorage...
3. Objekt blob je privátní a klíč úložiště byl použit k němu přistupovat. V takovém případě je stav žádosti o "**úspěch**"a typ autorizace je"**ověření**".

   1.0; 2015-11-16T18:32:24.3174537Z; GetBlob; **Úspěch**; 206 59; 22; **ověření**; mystorage...

Microsoft Message Analyzer můžete použít k zobrazení a tyto protokoly analyzovat. Zahrnuje funkce, vyhledávání a filtrování. Například můžete chtít vyhledat výskyty GetBlob zobrazíte při využití, které jste očekávali, to znamená, aby se zajistilo někdo není přístup k účtu úložiště nesprávně.

#### <a name="resources"></a>Zdroje a prostředky
* [Storage Analytics](../storage-analytics.md)

  Tento článek představuje přehled analýzy úložiště a jak je chcete povolit.
* [Formát úložiště analýzy protokolů](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Tento článek ukazuje formát úložiště analýzy protokolů a podrobně popisuje polí, která v něm, včetně ověřování typu, který určuje typ použitého pro požadavek ověřování.
* [Monitorování účtu úložiště na webu Azure Portal](../storage-monitor-storage-account.md)

  Tento článek ukazuje postup při konfiguraci monitorování metrik a protokolování pro účet úložiště.
* [Začátku do konce řešení potíží s používáním metrik Azure Storage a protokolování, AzCopy a analyzátoru zpráv](../storage-e2e-troubleshooting.md)

  Tento článek pojednává o řešení problémů pomocí Storage Analytics a ukazuje, jak používat Microsoft Message Analyzer.
* [Microsoft Message Analyzer Provozní příručka](https://technet.microsoft.com/library/jj649776.aspx)

  Tento článek slouží jako reference k Microsoft Message Analyzer a obsahuje odkazy na kurz, rychlý start a souhrnný přehled funkcí.

## <a name="cross-origin-resource-sharing-cors"></a>Sdílení prostředků mezi zdroji (CORS)
### <a name="cross-domain-access-of-resources"></a>Mezi doménami přístup k prostředkům
Pokud spuštěný v jedné doméně webový prohlížeč odešle požadavek HTTP pro prostředek z jiné domény, to se označuje jako žádost HTTP nepůvodního zdroje. Například stránky HTML obsluhovat z contoso.com pošle požadavek hostitelem fabrikam.blob.core.windows.net jpeg. Z bezpečnostních důvodů omezení prohlížeče požadavků HTTP mezi zdroji spustily z skripty, třeba JavaScript. To znamená to, když určitý kód JavaScript na webové stránce v doméně contoso.com požaduje tuto jpeg na fabrikam.blob.core.windows.net, prohlížeče nebude žádost.

Co to má udělat pomocí služby Azure Storage? Dobře, pokud ukládáte statické prostředky, jako jsou soubory JSON nebo XML dat v úložišti objektů Blob pomocí účtu úložiště se nazývá společnosti Fabrikam, domény pro prostředky bude fabrikam.blob.core.windows.net a contoso.com webová aplikace nebude možné k nim přistupovat pomocí JavaScript protože domény se liší. To je PRAVDA, pokud se snažíte volat jednu ze služby Azure Storage – například Table Storage –, který vrátí data JSON pro zpracování Javascriptového klienta.

#### <a name="possible-solutions"></a>Možná řešení
Přiřazení vlastní domény, jako je "storage.contoso.com" fabrikam.blob.core.windows.net je jeden způsob, jak tento problém vyřešit. Problém je, že tuto vlastní doménu můžete přiřadit jenom na jeden účet úložiště. Co když prostředky jsou uložené ve více účtech úložiště?

Dalším způsobem, jak tento problém vyřešíte je, aby sloužil jako proxy pro volání úložiště webové aplikace. To znamená, že pokud jsou nahrání souboru do úložiště objektů Blob, webová aplikace by buď zápisu místně a zkopírujte ho do úložiště objektů Blob, nebo by všechny jeho načtení do paměti a potom se zapsaly do úložiště objektů Blob. Alternativně můžete napsat vyhrazené webové aplikace (například webové rozhraní API), který nahraje místní soubory a zapisuje je do úložiště objektů Blob. V obou případech musíte počítat pro tuto funkci při určování škálovatelnost potřebuje.

#### <a name="how-can-cors-help"></a>Jak může pomoci CORS?
Azure Storage umožňuje povolit CORS – mezi sdílení zdrojů původu. Pro každý účet úložiště můžete zadat domén, které mají přístup k prostředkům v účtu úložiště. V našem případě uvedených výše jsme například povolení CORS pro účet úložiště fabrikam.blob.core.windows.net a nakonfigurujte ho chcete povolit přístup k contoso.com. Potom contoso.com webové aplikace můžete přímo přístup k prostředkům v fabrikam.blob.core.windows.net.

Jednou z věcí si je, že CORS umožňuje přístup, ale neposkytuje ověřování, která je požadována pro všechny jiné veřejný přístup k prostředkům úložiště. To znamená, že se že objekty BLOB můžete přistupovat pouze v případě, že jsou veřejné, nebo obsahovat sdílený přístupový podpis poskytuje vám tak příslušná oprávnění. Tabulky, fronty a soubory nemají přístup public a vyžadují SAS.

CORS je ve výchozím nastavení zakázána ke všem službám. CORS můžete povolit pomocí rozhraní REST API nebo klientskou knihovnu pro úložiště pro volání metod pro nastavení zásad služby. Když to uděláte, zahrnují pravidlo CORS, která je ve formátu XML. Tady je příklad pravidla CORS, která byla nastavena pomocí operace nastavit vlastnosti služby pro službu Blob Service účtu úložiště. Můžete provést danou operaci pomocí klientskou knihovnu pro úložiště nebo rozhraní REST API pro službu Azure Storage.

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

Zde je, co každý řádek znamená, že:

* **AllowedOrigins** říká, které neodpovídající domény můžete požadovat a přijímat data ze služby storage. To říká, že domény contoso.com a fabrikam.com můžete požádat o data z úložiště objektů Blob pro konkrétnímu účtu úložiště. Tento parametr můžete také nastavit na zástupný znak (\*) k povolení všech domén pro přístup k žádosti.
* **Hodnota AllowedMethods** Toto je seznam metod (akce požadavků HTTP), které se dá použít při vytvoření požadavku. V tomto příkladu jsou povoleny pouze PUT a GET. Tento parametr můžete nastavit na zástupný znak (\*) mají povolit všechny metody, který se má použít.
* **AllowedHeaders** jde hlavičky žádosti, které zdrojová doména může zadat při vytvoření požadavku. V tomto příkladu jsou povoleny všechny hlavičky metadat od verze x-ms-meta-data, x-ms-meta cíl a x-ms-meta-abc. Zástupný znak (\*) určuje, že je povolen libovolný začátek záhlaví se zadanou předponou.
* **ExposedHeaders** říká, které hlavičky odpovědi by se měly zobrazit v prohlížeči pro vystavitele žádosti. V tomto příkladu libovolné záhlaví počínaje "x-ms - meta-" se zveřejní.
* **MaxAgeInSeconds** Toto je maximální množství času, že prohlížeč bude ukládat do mezipaměti předběžné požadavky OPTIONS. (Další informace o předběžný požadavek, zkontrolujte na první článek dole.)

#### <a name="resources"></a>Zdroje a prostředky
Další informace o mechanismů jako CORS a jak se dá povolit projděte si tyto prostředky.

* [Prostředků mezi zdroji (CORS) podporu pro služby Azure Storage na Azure.com pro sdílení obsahu](../storage-cors-support.md)

  Tento článek obsahuje přehled CORS a jak nastavit pravidla pro službu jiného úložiště.
* [Podpora (CORS) pro služby Azure Storage na webu MSDN sdílení prostředků různého původu](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Toto je referenční dokumentaci pro podporu CORS pro služby Azure Storage. To obsahuje odkazy na články pro každou službu úložiště a ukazuje příklad a vysvětluje každý prvek v souboru CORS.
* [Microsoft Azure Storage: Představujeme CORS](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Toto je odkaz na článek počáteční blogu oznamujeme CORS a ukazuje, jak ho použít.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Nejčastější dotazy ohledně zabezpečení služby Azure Storage
1. **Jak můžete ověřit integritu objektů BLOB, které můžu jsem přenosu do nebo z úložiště Azure, pokud nelze použít protokol HTTPS?**

   Pokud z nějakého důvodu potřebujete používat protokol HTTP místo protokolu HTTPS a práci s objekty BLOB bloku, můžete použít algoritmus MD5 kontroluje k ověření integrity přenášených objekty BLOB. To vám pomůže s ochranou z chyb sítě nebo přenosu vrstvy, ale ne nutně zprostředkující útoky.

   Pokud používáte protokol HTTPS, který poskytuje zabezpečení na úrovni přenosu, pak pomocí kontroly MD5 je redundantní a zbytečné.

   Další informace najdete [Přehled služby Azure Blob MD5](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **A co FIPS pro USA Státní správy?**

   USA federální informace o zpracování Standard (FIPS) definuje kryptografické algoritmy, které jsou schválené pro použití v USA Federální vlády počítačové systémy pro ochranu citlivá data. Povolení standardu FIPS režimu ve Windows serveru nebo plochy říká operační systém by měla sloužit pouze standardu FIPS kryptografické algoritmy. Pokud aplikace používá algoritmy nedodržují předpisy, aplikace se přeruší. With.NET Framework verze 4.5.2 nebo novější, aplikace se automaticky přepne kryptografické algoritmy používat algoritmy odpovídající standardu FIPS, když je počítač v režimu FIPS.

   Microsoft, zůstane až do každého zákazníka můžete rozhodnout, jestli chcete povolit režim FIPS. Věříme, že neexistuje žádný závažný důvod pro zákazníky, kteří nejsou podléhající vládním předpisům, pokud chcete povolit režim FIPS ve výchozím nastavení.

### <a name="resources"></a>Zdroje a prostředky
* [Proč jsme už není doporučující "Režimu FIPS" už](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Tento blogový článek s přehledem podle standardu FIPS a vysvětluje, proč není umožňují režimu FIPS ve výchozím nastavení.
* [FIPS 140 ověření](https://technet.microsoft.com/library/cc750357.aspx)

  Tento článek obsahuje informace o tom, jak kryptografickými moduly a produkty společnosti Microsoft v souladu se standardem FIPS pro USA Federální vlády.
* ["Kryptografický modul systému: použití FIPS kompatibilní s algoritmy pro šifrování, hašování a podpisování" vliv na nastavení zabezpečení ve Windows XP a v novějších verzích Windows](https://support.microsoft.com/kb/811833)

  Tento článek pojednává o použití režimu FIPS ve starší počítače Windows.