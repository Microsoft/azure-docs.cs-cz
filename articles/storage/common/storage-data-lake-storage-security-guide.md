---
title: Příručka zabezpečení služby Azure Data Lake Storage Gen2 Storage | Dokumentace Microsoftu
description: Podrobnosti o mnoho metod zabezpečení Azure Storage, včetně, ale nikoli výhradně RBAC a šifrování služby Storage
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/07/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: fce7beeda352b9add3603fb74c558ad1b64fac2a
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895512"
---
# <a name="azure-data-lake-storage-gen2-security-guide"></a>Příručka zabezpečení služby Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2, je sada možností integrovaných v účtech Azure Storage. V důsledku toho všechny odkazy v tomto článku jsou určené pro účet služby Azure Storage s hierarchického oboru názvů povolené (Data Lake Storage Gen2 možnosti).

- Všechna data zapsaná do služby Azure Storage budou automaticky šifrována pomocí [šifrování služby Storage (SSE)](storage-service-encryption.md). Další informace najdete v tématu [oznamujeme výchozí šifrování objektů BLOB Azure, soubory, tabulky a fronty úložiště](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Azure Active Directory (Azure AD) a řízení přístupu na základě Role (RBAC) jsou podporovány pro službu Azure Storage pro operace správy zdrojů a operace s daty, následujícím způsobem:
    - Můžete přiřadit role RBAC omezená na účet úložiště pro objekty zabezpečení a pomocí služby Azure AD povolit operace správy zdrojů, například správu klíčů.
    - Integrace se službou Azure AD je podporována v pro operace s daty ve službě Azure Storage. Můžete přiřadit role RBAC omezená na předplatné, skupinu prostředků, účet úložiště nebo jednotlivé systému souborů k objektu zabezpečení nebo spravovanou identitu pro prostředky Azure. Další informace najdete v tématu [ověření přístupu ke službě Azure Storage pomocí Azure Active Directory](storage-auth-aad.md).
- Delegovaný přístup k datové objekty ve službě Azure Storage lze udělit prostřednictvím [sdílené přístupové podpisy](../storage-dotnet-shared-access-signature-part-1.md).

Tento článek obsahuje základní informace o každé z těchto funkcí zabezpečení, které lze použít s Azure Storage. Odkazy jsou k dispozici na články, které vám poskytne podrobné údaje o jednotlivých funkcí, takže můžete snadno provést další šetření na každého tématu.

Tady jsou témata popsaná v tomto článku:

* [Zabezpečení roviny správy](#management-plane-security) – zabezpečení vašeho účtu úložiště

  Rovina správy se skládá z prostředků používá ke správě vašeho účtu úložiště. Tento oddíl popisuje model nasazení Azure Resource Manageru a jak řídit přístup k účtům úložiště pomocí řízení přístupu na základě Role (RBAC). Také řeší, správu klíčů účtu úložiště a jak znovu vygenerovat je.
* [Zabezpečení RP roviny dat](#data-plane-security) – zabezpečení přístupu k datům

  V této části se podíváme na povolení přístupu k objektům skutečná data ve vašem účtu úložiště, například soubory a adresáře, pomocí sdílené přístupové podpisy a uložené zásady přístupu. Budeme se zabývat podpis SAS na úrovni služby a SAS na úrovni účtu. Uvidíme také tom, jak omezit přístup ke konkrétní IP adresu (nebo rozsah IP adres), jak omezit protokol použitý pro protokol HTTPS a odvoláte sdíleného přístupového podpisu bez čekání na vypršení platnosti.
* [Šifrování během přenosu](#encryption-in-transit)

Tato část popisuje, jak zabezpečit data při přenosu ho do nebo z účtu úložiště s Data Lake Storage Gen2 povolené. Budeme mluvit o doporučené použití protokolu HTTPS.

## <a name="management-plane-security"></a>Zabezpečení roviny správy

Rovina správy se skládá z operací, které ovlivňují samotného účtu úložiště. Můžete například vytvořit nebo odstranit účet úložiště, získání seznamu účtů úložiště v rámci předplatného, načtení klíčů účtu úložiště nebo obnovit klíče účtu úložiště.

Tato příručka se zaměřuje na nasazení, což znamená, že pro vytvoření účtů úložiště s funkcemi, Data Lake Storage Gen2 je model Resource Manager. Účty úložiště Resource Manageru, spíše než poskytne přístup k celé předplatné můžete řídit přístup k více omezený úroveň k rovině správy pomocí řízení přístupu na základě Role (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Jak zabezpečit svůj účet úložiště pomocí řízení přístupu na základě Role (RBAC)

Povězme si o co RBAC je a jak ji použít. Každé předplatné Azure zahrnuje službu (adresář) Azure Active Directory. Uživatelům, skupinám a aplikacím z tohoto adresáře lze udělit přístup ke správě prostředků v předplatném Azure, které používají model nasazení Resource Manager. Tento typ zabezpečení se označuje jako řízení přístupu na základě Role (RBAC). Chcete-li spravovat tento přístup, použijte **řízení přístupu (IAM)** na webu Azure Portal.

Pomocí modelu Resource Manager kam si ukládáte účtu úložiště v prostředku skupiny a řízení přístupu k rovině správy tohoto účtu konkrétní úložiště pomocí Azure Active Directory. Například můžete konkrétním uživatelům udělit přístup klíče účtu úložiště, zatímco ostatní uživatelé mohou zobrazit informace o účtu úložiště, ale nelze přístupové klíče účtu úložiště.

#### <a name="granting-access"></a>Udělení přístupu

Přístup uděluje přiřazením příslušné role RBAC pro uživatele, skupiny nebo aplikace v oboru pravé. Chcete-li udělit přístup k celé předplatné, přiřaďte role na úrovni předplatného. Tím, že uděluje oprávnění na samotnou skupinu prostředků můžete udělit přístup ke všem prostředkům ve skupině prostředků. Konkrétní role můžete také přiřadit ke konkrétním prostředkům, jako je například účty úložiště.

Tady jsou hlavní body, které je potřeba vědět o použití RBAC pro přístup k operace správy účtu úložiště Azure:

* Možnost mají oprávnění pro přístup k objektům data v účtu úložiště můžete udělit oprávnění ke čtení klíčů účtu úložiště a tento uživatel potom můžete pomocí těchto klíčů přístup k souborům a adresářům.
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
* [RBAC: Vestavěné role](../../role-based-access-control/built-in-roles.md)

  Tento článek podrobně popisuje všechny vestavěné role dostupné v RBAC.
* [Principy nasazení podle modelu Resource Manager a klasického nasazení](../../azure-resource-manager/resource-manager-deployment-model.md)

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

Další možností je umístění klíče účtu úložiště do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) jako tajný klíč a mít vaše aplikace načíst z něj klíč. Potom když se znovu vygenerovat klíč a aktualizace služby Azure Key Vault, aplikace nebudete muset znovu nasadit, protože jejich vyzvedne, až bude nový klíč ze služby Azure Key Vault automaticky. Může mít jen pro čtení klíče pokaždé, když je potřebujete, nebo můžete mezipaměti v paměti a pokud selže při jeho použití, klíč znovu načíst ze služby Azure Key Vault.

Pomocí služby Azure Key Vault také přidává další úroveň zabezpečení pro vaše klíče úložiště. Pokud použijete tuto metodu, budete mít klíče pevně zakódované úložiště nikdy v konfiguračním souboru, který odebere tento způsob případného někdo získal přístup ke klíčům bez konkrétní oprávnění.

Další výhodou používání služby Azure Key Vault je, že můžete také řídit přístup k svoje klíče pomocí služby Azure Active Directory. To znamená, že můžete udělit přístup k několika málo až po aplikace, které potřebují k načtení klíče ze služby Azure Key Vault a vědět, že jiné aplikace nebudou moct bez nutnosti přidělit jim konkrétně oprávnění pro přístup.

> [!NOTE]
> Microsoft doporučuje používat pouze jeden z klíčů ve všech vašich aplikací ve stejnou dobu. Pokud používáte klíč 1 na některých místech a klíč 2 v jiných, nebude možné obměna klíčů bez některé aplikace došlo ke ztrátě přístupu.

#### <a name="resources"></a>Zdroje a prostředky

* [Spravovat nastavení účtu úložiště na webu Azure Portal](storage-account-manage.md)
* [Referenční informace o rozhraní REST API pro poskytovatele prostředků Azure Storage](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Zabezpečení roviny dat
Zabezpečení roviny dat odkazuje na metody používané k zabezpečení datové objekty uložené ve službě Azure Storage. Zaznamenali jsme metody k šifrování dat a zabezpečení během přenosu dat, ale jak do můžete přejít o řízení přístupu k objektům?

Existují tři možnosti pro autorizaci přístupu k datovým objektům ve službě Azure Storage, včetně:

- Používání služby Azure AD k autorizaci přístupu k systémy souborů a front. Azure AD poskytuje výhody oproti další přístupy k ověřování, včetně odstraňují potřebu tajné kódy ukládat v kódu. Další informace najdete v tématu [ověření přístupu ke službě Azure Storage pomocí Azure Active Directory](storage-auth-aad.md). 
- K ověřování přístupu prostřednictvím sdíleného klíče pomocí klíče účtu úložiště. Ověřování pomocí sdíleného klíče vyžaduje uložení klíče účtu úložiště ve vaší aplikaci, takže Microsoft doporučuje místo toho pomocí služby Azure AD, kde je to možné. Pro produkční aplikace, nebo pro autorizaci přístupu k Azure tabulky a soubory pokračujte v používání sdíleného klíče, integrace služby Azure AD je ve verzi preview.
- Použití sdílených přístupových podpisů udělit řízenými oprávněními pro konkrétní datové objekty určitou dobu.

Kromě omezení přístupu prostřednictvím ověřování, můžete také použít [virtuální sítí a bran firewall](storage-network-security.md) omezit přístup k účtu úložiště na základě pravidel sítě.  Tento přístup umožňuje odepřít přístup k veřejné internetové přenosy a udělte přístup jenom ke konkrétním virtuálním sítím Azure nebo veřejná internetová rozsahy IP adres.

### <a name="storage-account-keys"></a>Klíče účtu úložiště

Klíče účtu úložiště jsou 512 bitů řetězce, které vytvořil Azure, kterou, spolu s názvem účtu úložiště, můžete použít pro přístup k objektům dat uložených v účtu úložiště.

Například můžete číst soubory. Mnohé z těchto akcí lze provést prostřednictvím webu Azure portal nebo pomocí jednoho z mnoha aplikací Průzkumníka služby Storage. Můžete také napsat kód k používání rozhraní API REST k provedení těchto operací.

Jak je popsáno v části na [zabezpečení roviny správy](#management-plane-security), přístup k úložišti klíčů pro účet úložiště pomocí modelu Azure Resource Manageru můžete řídit pomocí řízení přístupu na základě Role (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Jak delegovat přístup k objektům ve vašem účtu pomocí sdílené přístupové podpisy a uložené zásady přístupu

Sdílený přístupový podpis je řetězec obsahující token zabezpečení, který lze připojit k identifikátoru URI, který umožňuje delegovat přístup k objektům úložiště a zadat omezení, jako jsou oprávnění a datový/časový rozsah přístupu.

Můžete udělit přístup k souborům nebo adresářům.

Jednoho zákazníka může poskytnout token SAS, můžete použít k nahrávání obrázků do systému souborů v úložišti objektů Blob a udělit oprávnění k aplikaci pro webové, přečtěte si tyto obrázky. V obou případech je oddělené oblasti zájmu – každá aplikace mohou být zadány pouze přístup, který vyžadují, abyste mohli provádět jejich úloh. Je to možné prostřednictvím použití sdílených přístupových podpisů.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Proč chcete použít sdílené přístupové podpisy

Proč byste měli použít SAS místo pouze poskytnutí klíč účtu úložiště, což je mnohem snadnější? Poskytnutí klíč účtu úložiště je třeba sdílení klíčů království vašeho úložiště. Uděluje plný přístup. Někdo by mohla používat klíče a jejich celá knihovna nahrát do účtu úložiště. Také se může nahradit soubory verze napadeným viry nebo ukrást vaše data. Poskytne neomezený přístup, k vašemu účtu úložiště je objekt, který by se neměl brát lehce.

Se sdílenými přístupovými podpisy můžete udělit pouze oprávnění požadovaná pro omezené množství času klienta. Například pokud je někdo po nahrání souboru do vašeho účtu, můžete jim udělíte přístup pro zápis pro dostatek času k nahrání souboru (v závislosti na velikosti souboru samozřejmě). A pokud změníte své rozhodnutí, můžete odvolat tento přístup.

Kromě toho můžete určit, aby byly omezené na IP adresu nebo rozsah IP adres pro Azure externí žádosti pomocí SAS. Můžete také vyžadovat, požadavky přicházejí pomocí konkrétní protokolu (HTTPS nebo HTTP/HTTPS). To znamená, že pokud chcete povolit provoz protokolu HTTPS, můžete nastavit požadovaný protokol HTTPS pouze a budou blokovány přenosy HTTP.

#### <a name="definition-of-a-shared-access-signature"></a>Definice sdíleného přístupového podpisu

Sdílený přístupový podpis je sada připojí k adrese URL odkazující na prostředek parametry dotazu.

který poskytuje informace o povoleného přístupu a dobu, pro který je povolen přístup. Tady je příklad; Pomocí tohoto identifikátoru URI poskytuje přístup pro čtení do objektu blob pro pět minut. Parametry dotazu SAS musí být kódovaná v řetězci URL, jako je například 3A % dvojtečkou (:) nebo % 20 mezerou.

```
http://mystorage.dfs.core.windows.net/myfilesystem/myfile.txt (URL to the file)
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

* SAS úrovni služby slouží pro přístup k určitým prostředkům v účtu úložiště. Některé příklady jsou načítá se seznam souborů v systému souborů nebo stažení souboru.
* Cokoli, co SAS úrovni služby slouží pro přístup k lze SAS úrovni účtu. Kromě toho poskytuje možnosti pro prostředky, které se nedá vymezit přes SAS úrovně služeb, jako je například schopnost vytvářet systémy souborů.

#### <a name="creating-a-sas-uri"></a>Vytváří se identifikátor URI SAS

1. Vytvořit identifikátor URI na vyžádání, definovat všechny parametry dotazu pokaždé, když.

   Tento přístup je flexibilní, ale pokud máte logickou sadu parametrů, které jsou podobné pokaždé, když pomocí zásady přístupu uložené je lepší představu.
2. Můžete vytvořit zásadu přístupu uložené pro celý systém souborů, sdílené složky, tabulek nebo front. Pak je můžete použít jako základ pro identifikátory URI SAS, které vytvoříte. Oprávnění podle principu uložené zásady přístupu můžete snadno odvolat. Můžete mít až o pěti zásad definovaných na jednotlivé systému souborů, fronty, tabulky a sdílené složky.

   Například pokud se chystáte mají mnoho lidí čtení objektů BLOB v konkrétní systému souborů, můžete vytvořit uložené zásady přístupu, který říká "udělit přístup pro čtení" a další nastavení, které budou stejné pokaždé, když. Potom můžete vytvořit identifikátor URI SAS pomocí nastavení zásad přístupu uložené a zadáte datum a čas vypršení platnosti. Výhodou je, že není nutné zadat všechny parametry dotazu pokaždé, když.

#### <a name="revocation"></a>Odvolání

Předpokládejme, že došlo k napadení vašeho SAS, nebo chcete změnit z důvodu podnikového zabezpečení nebo požadavky na dodržování legislativních předpisů. Jak odvoláte přístup k prostředkům pomocí tohoto SAS To závisí na tom, jak vytvořit identifikátor URI SAS.

Pokud používáte ad hoc identifikátory URI, máte tři možnosti. Může vystavovat tokeny SAS se zásadami pro konec platnosti krátký a počkejte SAS vyprší. Můžete přejmenovat nebo odstranit prostředek (za předpokladu, že token, který byl omezená na jeden objekt). Klíče účtu úložiště můžete změnit. Poslední možnost může mít významný dopad, v závislosti na tom, kolik services používají tento účet úložiště a pravděpodobně není něco, co chcete udělat bez některé plánování.

Pokud používáte SAS odvozené ze zásad přístupu uložené, může přístup odebrat zrušením uložené zásady přístupu – můžete jenom ho změnit tak, aby již vypršela, nebo ji úplně odebrat. To se projeví okamžitě a zruší platnost všech SAS vytvořené pomocí této zásady přístupu uložené. Aktualizace nebo odebrání zásady přístupu uložené může dopad uživatelé s přístupem k této konkrétní systému souborů, sdílené složky, tabulky nebo fronty přes SAS, ale pokud klienti se zapisují, požádá o novou SAS při původní stane neplatná, bude to fungovat bez problémů.

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

## <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

### <a name="storage-service-encryption-sse"></a>Šifrování služby Storage (SSE)

SSE je povolená pro všechny účty úložiště a nejde zakázat. SSE automaticky šifruje vaše data při zápisu do služby Azure Storage. Při čtení dat ze služby Azure Storage, dešifruje se službou Azure Storage před vrácením. SSE umožňuje zabezpečit vaše data bez nutnosti měnit kód nebo přidejte kód do všech aplikací.

Můžete použít klíče spravované zákazníkem Microsoftu nebo vlastní klíče. Společnost Microsoft generuje spravovaných klíčů a zpracovává jejich zabezpečeného úložiště, stejně jako jejich regulární otočení, jak je definována zásadami Microsoftu pro interní. Další informace o použití vlastních klíčů najdete v tématu [šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](storage-service-encryption-customer-managed-keys.md).
