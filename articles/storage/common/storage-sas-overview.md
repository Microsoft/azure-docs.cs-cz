---
title: Udělení omezeného přístupu k datům pomocí sdílených přístupových podpisů (SAS)
titleSuffix: Azure Storage
description: Přečtěte si o použití sdílených přístupových podpisů (SAS) k delegování přístupu k Azure Storage prostředkům, včetně objektů blob, front, tabulek a souborů.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: b9882168cd063cb4448269cc6a4949778fe93fb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88509854"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Udělení omezeného přístupu k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)

Sdílený přístupový podpis (SAS) poskytuje zabezpečený delegovaný přístup k prostředkům ve vašem účtu úložiště, aniž by došlo k narušení zabezpečení vašich dat. U SAS máte podrobnější kontrolu nad tím, jak má klient přístup k datům. Můžete řídit, k jakým prostředkům má klient přístup, jaká oprávnění jsou na těchto prostředcích a jak dlouho je SAS platné, mezi další parametry.

## <a name="types-of-shared-access-signatures"></a>Typy sdílených přístupových podpisů

Azure Storage podporuje tři typy podpisů sdíleného přístupu:

- **SAS delegování uživatele.** SAS delegování uživatele je zabezpečené pomocí přihlašovacích údajů pro Azure Active Directory (Azure AD) a také podle oprávnění zadaných pro SAS. SAS delegování uživatele se vztahuje pouze na úložiště objektů BLOB.

    Další informace o SAS delegování uživatele najdete v tématu [Vytvoření SAS (REST API) delegování uživatele](/rest/api/storageservices/create-user-delegation-sas).

- **SAS služby.** Podpis SAS služby je zabezpečený pomocí klíče účtu úložiště. SAS služby deleguje přístup k prostředku pouze v jedné z Azure Storage Services: BLOB Storage, Queue Storage, Table Storage nebo Azure Files.

    Další informace o SAS služby najdete v tématu [Vytvoření SAS služby (REST API)](/rest/api/storageservices/create-service-sas).

- **SAS účtu.** Podpis SAS účtu je zabezpečený pomocí klíče účtu úložiště. SAS účtu deleguje přístup k prostředkům v jedné nebo více službách úložiště. Všechny operace, které jsou k dispozici prostřednictvím SAS služby nebo delegování uživatele, jsou také k dispozici prostřednictvím SAS účtu. Kromě toho můžete pomocí SAS účtu delegovat přístup k operacím, které platí na úrovni služby, jako je například **získat nebo nastavit vlastnosti služby** a **získat operace statistiky služby** . Můžete taky delegovat přístup k operacím čtení, zápis a odstranění pro kontejnery objektů blob, tabulky a sdílené složky, který se nedá vymezit přes SAS služby. 

    Další informace o SAS účtu získáte [vytvořením SAS (REST API) účtu](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> Microsoft doporučuje používat přihlašovací údaje Azure AD, pokud je to možné, a ne používat klíč účtu, který může být snáze ohrožen. Když návrh aplikace vyžaduje pro přístup k úložišti objektů BLOB sdílené přístupové podpisy, pomocí přihlašovacích údajů Azure AD vytvořte přidružení zabezpečení uživatele, pokud je to možné, pro zajištění nadřazeného zabezpečení. Další informace najdete v tématu [autorizace přístupu k objektům blob a frontám pomocí Azure Active Directory](storage-auth-aad.md).

Sdílený přístupový podpis může mít jednu ze dvou forem:

- **Ad hoc SAS:** Když vytvoříte ad hoc SAS, čas spuštění, čas vypršení platnosti a oprávnění pro SAS jsou všechny zadané v identifikátoru URI SAS (nebo mlčky předpokládaná, pokud je vynechaný počáteční čas). Jakýkoli typ SAS může být ad hoc SAS.
- **SAS služby s uloženými zásadami přístupu:** Uložené zásady přístupu se definují v kontejneru prostředků, což může být kontejner objektů blob, tabulka, fronta nebo sdílená složka. Zásady uloženého přístupu se dají použít ke správě omezení pro jeden nebo víc podpisů sdíleného přístupu služby. Při přidružení SAS služby k uložené zásadě přístupu SAS zdědí omezení pro &mdash; čas spuštění, čas vypršení platnosti a oprávnění &mdash; definovaná pro zásady uloženého přístupu.

> [!NOTE]
> SAS uživatele nebo podpis účtu musí být SAS ad hoc. Uložené zásady přístupu se nepodporují pro SAS uživatelů s delegováním nebo pro podpis účtu.

## <a name="how-a-shared-access-signature-works"></a>Způsob fungování sdíleného přístupového podpisu

Sdílený přístupový podpis je podepsaný identifikátor URI, který odkazuje na jeden nebo více prostředků úložiště a obsahuje token, který obsahuje speciální sadu parametrů dotazu. Token indikuje, jak může klient přistupovat k prostředkům. Jeden z parametrů dotazu, signatura, je vytvořen z parametrů SAS a podepsaný klíčem, který byl použit k vytvoření SAS. Tento podpis používá Azure Storage k autorizaci přístupu k prostředku úložiště.

### <a name="sas-signature-and-authorization"></a>Podpis a autorizace SAS

Token SAS můžete podepsat jedním ze dvou způsobů:

- Pomocí *klíče pro delegování uživatele* , který byl vytvořen pomocí pověření Azure Active Directory (Azure AD). Delegování uživatele je podepsané klíčem delegování uživatele.

    Aby se získal klíč pro delegování uživatelů a vytvořil SAS, musí být objekt zabezpečení služby Azure AD přiřazený k roli Azure, která zahrnuje akci **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Podrobné informace o rolích Azure s oprávněními k získání klíče pro delegování uživatelů najdete v tématu [Vytvoření SAS uživatele pro delegování (REST API)](/rest/api/storageservices/create-user-delegation-sas).

- Pomocí klíče účtu úložiště (sdílený klíč). Podpis SAS služby i účtu SAS jsou podepsané klíčem účtu úložiště. K vytvoření SAS podepsaného klíčem účtu musí aplikace mít přístup k tomuto klíči účtu.

Pokud požadavek obsahuje token SAS, je požadavek autorizován na základě toho, jak je token SAS podepsaný. Přístupový klíč nebo přihlašovací údaje, které použijete k vytvoření tokenu SAS, používá Azure Storage pro udělení přístupu ke klientovi, který má SAS.

Následující tabulka shrnuje, jak je každý typ tokenu SAS autorizovaný, když je součástí žádosti o Azure Storage:

| Typ SAS | Typ autorizace |
|-|-|
| SAS delegování uživatelů (jenom BLOB Storage) | Azure AD |
| SAS služby | Sdílený klíč |
| SAS účtu | Sdílený klíč |

Pokud je to možné, společnost Microsoft doporučuje používat k zajištění nadřazeného zabezpečení přidružení zabezpečení při delegování uživatelů.

### <a name="sas-token"></a>Token SAS

Token SAS je řetězec, který vygenerujete na straně klienta, například pomocí jedné z Azure Storage klientských knihoven. Token SAS není sledován jakýmkoli způsobem Azure Storage. Na straně klienta můžete vytvořit neomezený počet tokenů SAS. Když vytvoříte SAS, můžete ho distribuovat klientským aplikacím, které vyžadují přístup k prostředkům ve vašem účtu úložiště.

Pokud klientská aplikace poskytne identifikátor URI SAS pro Azure Storage v rámci žádosti, služba zkontroluje parametry a signaturu SAS a ověří, zda je platný pro autorizaci žádosti. Pokud služba ověřuje, zda je podpis platný, je žádost autorizována. V opačném případě je požadavek odmítnut s kódem chyby 403 (zakázáno).

Tady je příklad identifikátoru URI služby SAS, ve kterém je vidět identifikátor URI prostředku a token SAS:

![Součásti identifikátoru URI SAS služby](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Kdy použít sdílený přístupový podpis

Použijte SAS, pokud chcete zajistit zabezpečený přístup k prostředkům ve vašem účtu úložiště pro libovolného klienta, který v opačném případě nemá oprávnění k těmto prostředkům.

Běžným scénářem, kdy je užitečný SAS, je služba, kde uživatelé čtou a zapisují vlastní data do svého účtu úložiště. Ve scénáři, kde účet úložiště ukládá data uživatelů, existují dva typické modely návrhu:

1. Klienti nahrávají a stahují data přes front-endovou proxy službu, která provádí ověřování. Tato služba front-end proxy je výhodou pro povolení ověřování obchodních pravidel, ale pro velké objemy dat nebo velké objemy transakcí může být vytvoření služby, která se dá škálovat podle požadavků, náročné nebo obtížné.

   ![Diagram scénářů: front-end proxy služba](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Zjednodušená služba podle potřeby ověří klienta a potom vygeneruje sdílený přístupový podpis. Jakmile klientská aplikace obdrží SAS, může získat přístup k prostředkům účtu úložiště přímo s oprávněními definovanými SAS a intervalem povoleným SAS. Sdílený přístupový podpis snižuje potřebu směrování všech dat přes front-endovou proxy službu.

   ![Diagram scénáře: služba poskytovatele SAS](./media/storage-sas-overview/sas-storage-provider-service.png)

Mnoho skutečných služeb může používat hybridní z těchto dvou přístupů. Některá data mohou být například zpracována a ověřena prostřednictvím proxy serveru front-end, zatímco jiná data jsou ukládána nebo čtena přímo pomocí SAS.

Kromě toho je vyžadován SAS k autorizaci přístupu ke zdrojovému objektu při operaci kopírování v určitých scénářích:

- Při kopírování objektu blob do jiného objektu blob, který se nachází v jiném účtu úložiště, musíte k autorizaci přístupu ke zdrojovému objektu BLOB použít SAS. Volitelně můžete také použít SAS k autorizaci přístupu k cílovému objektu BLOB.
- Když zkopírujete soubor do jiného souboru, který se nachází v jiném účtu úložiště, musíte k autorizaci přístupu ke zdrojovému souboru použít SAS. Volitelně můžete také použít SAS k autorizaci přístupu k cílovému souboru.
- Při kopírování objektu blob do souboru nebo souboru do objektu BLOB je nutné použít SAS k autorizaci přístupu ke zdrojovému objektu, i když se zdrojové a cílové objekty nacházejí v rámci stejného účtu úložiště.

## <a name="best-practices-when-using-sas"></a>Osvědčené postupy při používání SAS

Pokud používáte sdílené přístupové podpisy ve svých aplikacích, je nutné mít na paměti dvě možná rizika:

- Pokud se SAS nevrátí, může ho použít kdokoli, kdo ho získá, což může potenciálně ohrozit váš účet úložiště.
- Pokud platnost SAS poskytnutá klientské aplikaci vyprší a aplikace nebude moct z vaší služby načíst nové přidružení zabezpečení, může být narušena funkčnost aplikace.

Následující doporučení pro použití sdílených přístupových podpisů pomáhají zmírnit tato rizika:

- K vytvoření nebo distribuci SAS **použijte vždy protokol HTTPS** . Pokud se SAS předává přes protokol HTTP a zachytí se útočník, který provádí útok prostředníkem, je schopen si ho přečíst a pak ho použít stejně, jako by to mohl mít zamýšleného uživatele, potenciálně ohrozit citlivá data nebo umožnit poškození dat uživatelem se zlými úmysly.
- **Pokud je to možné, použijte SAS delegování uživatele.** SAS delegování uživatelů zajišťuje vysoké zabezpečení pro SAS služby nebo k účtu SAS. Delegování uživatele je zabezpečené pomocí přihlašovacích údajů Azure AD, takže nemusíte svůj klíč účtu ukládat do svého kódu.
- **Pro SAS založte plán odvolání.** Ujistěte se, že jste připraveni odpovědět, pokud dojde k ohrožení zabezpečení SAS.
- **Definujte zásady uloženého přístupu pro SAS služby.** Zásady uloženého přístupu vám umožňují odvolat oprávnění pro SAS služby bez nutnosti znovu vygenerovat klíče účtu úložiště. V budoucnosti (nebo nekonečné) nastavte vypršení platnosti a ujistěte se, že je pravidelně aktualizované, aby se do budoucna přesunula víc.
- **Využijte dobu vypršení platnosti služby SAS SAS nebo účtu SAS účtu ad hoc.** Tímto způsobem platí, že i když dojde k ohrožení zabezpečení SAS, je platná pouze po krátkou dobu. Tento postup je zvlášť důležitý, pokud nemůžete odkazovat na zásadu uloženého přístupu. Doba vypršení platnosti také omezuje množství dat, která lze zapsat do objektu blob, omezením času, který je k dispozici pro nahrání.
- **Klienti v případě potřeby automaticky Obnovují SAS.** Klienti by měli obnovit správný čas před vypršením platnosti, aby bylo možné pokusy o opakování, pokud služba poskytující SAS není dostupná. Pokud se má vaše SAS použít pro malý počet okamžitých, krátkodobých operací, které se mají dokončit v rámci období vypršení platnosti, může to být zbytečné, protože se neočekává obnovení SAS. Pokud ale máte klienta, který provádí zpracování požadavků prostřednictvím SAS, pak se možnost vypršení platnosti stane hrát. Klíčovým aspektem je vyrovnávání nutnosti, aby SAS bylo krátkodobé (jak bylo uvedeno dříve), aby se zajistilo, že klient požaduje prodloužení na začátku. (aby nedocházelo k přerušení, protože platnost SAS vypršela před úspěšným obnovením).
- **Čas spuštění SAS buďte opatrní.** Pokud nastavíte čas zahájení pro SAS na aktuální čas, může docházet k chybám, které se během prvních několika minut dostanou nepřetržitě v důsledku různých počítačů s mírnými variacemi aktuální čas (označované jako časové zkosení). V části Obecné nastavte čas spuštění aspoň 15 minut v minulosti. Nebo ji vůbec nenastavte, což zajistí, že bude platit okamžitě ve všech případech. To samé platí i pro čas vypršení platnosti, ale mějte na paměti, že v obou směrech každé žádosti můžete obdržet až 15 minut hodinového zkosení. Pro klienty, kteří používají verzi REST starší než 2012-02-12, je maximální doba trvání SAS, která neodkazuje na zásadu uloženého přístupu 1 hodina, a všechny zásady, které nastavují delší dobu, než se nezdaří.
- **Buďte opatrní ve formátu data a času SAS.** Pokud nastavíte čas zahájení nebo vypršení platnosti pro SAS, pro některé nástroje (například pro nástroj příkazového řádku AzCopy) potřebujete, aby byl formát DateTime "+% Y-% m-% dT% H:%M:% SZ", konkrétně včetně sekund, aby fungoval s použitím tokenu SAS.  
- **Být specifické pro prostředek, který má být k dispozici.** Osvědčeným postupem zabezpečení je poskytnout uživateli minimální požadovaná oprávnění. Pokud uživatel potřebuje k jedné entitě oprávnění ke čtení, přidělte jim přístup pro čtení k této jedné entitě, a ne přístup pro čtení, zápis a odstranění u všech entit. To také pomáhá snížit škody, pokud dojde k ohrožení zabezpečení SAS, protože SAS má méně energie jako útočník.
- **Seznamte se s tím, že se Váš účet bude účtovat podle veškerého využití, včetně SAS.** Pokud zadáte přístup pro zápis do objektu blob, uživatel se může rozhodnout nahrát objekt BLOB 200 GB. Pokud jste jim udělili oprávnění ke čtení, můžou si ho stáhnout desetkrát, což za vás bude mít 2 TB za cenu. Znovu poskytněte omezená oprávnění, která pomáhají zmírnit potenciální akce uživatelů se zlými úmysly. Používejte krátkodobé SAS k omezení této hrozby (ale zaměříte se tak na konci času).
- **Ověří data zapsaná pomocí SAS.** Když klientská aplikace zapisuje data do svého účtu úložiště, mějte na paměti, že k těmto datům mohou nastat problémy. Pokud vaše aplikace vyžaduje, aby byla data ověřena nebo autorizována před tím, než je připravena k použití, měli byste toto ověření provést po zápisu dat a předtím, než je aplikace používá. Tento postup také chrání před poškozenými nebo škodlivými daty zapsanými na váš účet, a to buď uživatelem, který ho správně získal, nebo uživatelem, který zneužití nevráceného SAS.
- **Zjistěte, kdy nepoužívat SAS.** Někdy se může stát, že rizika spojená s určitou operací s vaším účtem úložiště převažují nad výhodami používání SAS. U takových operací vytvořte službu střední vrstvy, která po ověření obchodního pravidla, ověřování a auditování zapíše do účtu úložiště. Někdy je také jednodušší spravovat přístup jiným způsobem. Například pokud chcete, aby všechny objekty BLOB v kontejneru byly veřejně čitelné, můžete místo poskytování SAS každému klientovi pro přístup vytvořit kontejner jako veřejný.
- **K monitorování aplikace použijte protokoly Azure Monitor a Azure Storage.** Pomocí Azure Monitor a protokolování služby Storage Analytics můžete sledovat jakékoli špičky v případě výpadků autorizace z důvodu výpadku služby poskytovatele SAS nebo neúmyslného odebrání uložených zásad přístupu. Další informace najdete v tématu [Azure Storage metriky v Azure monitor](monitor-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) a [protokolování analýza úložiště Azure](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>Začínáme s SAS

Chcete-li začít se signaturami sdíleného přístupu, přečtěte si následující články pro každý typ SAS.

### <a name="user-delegation-sas"></a>SAS delegování uživatelů

- [Vytvoření SAS delegování uživatele pro kontejner nebo objekt BLOB pomocí PowerShellu](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Vytvoření SAS delegování uživatele pro kontejner nebo objekt BLOB pomocí Azure CLI](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Vytvoření SAS pro delegování uživatelů pro kontejner nebo objekt BLOB pomocí .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>SAS služby

- [Vytvoření SAS služby pro kontejner nebo objekt BLOB s využitím .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>SAS účtu

- [Vytvoření SAS účtu pomocí .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Další kroky

- [Delegovat přístup pomocí sdíleného přístupového podpisu (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Vytvoření SAS delegování uživatele (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Vytvoření SAS služby (REST API)](/rest/api/storageservices/create-service-sas)
- [Vytvoření SAS účtu (REST API)](/rest/api/storageservices/create-account-sas)
