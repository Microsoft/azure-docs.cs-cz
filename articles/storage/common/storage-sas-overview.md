---
title: Udělení omezeného přístupu k datům pomocí sdílených přístupových podpisů (SAS)
titleSuffix: Azure Storage
description: Přečtěte si o používání sdílených přístupových podpisů (SAS) k delegování přístupu k prostředkům Úložiště Azure, včetně objektů BLOB, front, tabulek a souborů.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7a5967f52a187fe289c6fb1ca72af2d5fd17f010
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255233"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Udělit omezený přístup k prostředkům Azure Storage pomocí sdílených přístupových podpisů (SAS)

Sdílený přístupový podpis (SAS) poskytuje zabezpečený delegovaný přístup k prostředkům v účtu úložiště, aniž by bylo ohroženo zabezpečení vašich dat. S SAS máte podrobnou kontrolu nad tím, jak může klient přistupovat k vašim datům. Můžete určit, jaké prostředky klient může přistupovat, jaká oprávnění mají na tyto prostředky a jak dlouho SAS je platný, mimo jiné parametry.

## <a name="types-of-shared-access-signatures"></a>Typy sdílených přístupových podpisů

Azure Storage podporuje tři typy sdílených přístupových podpisů:

- **Uživatel delegování SAS.** Uživatelské delegování SAS je zabezpečeno přihlašovacími údaji služby Azure Active Directory (Azure AD) a také oprávněními určenými pro SAS. Uživatelské delegování SAS platí pouze pro úložiště objektů Blob.

    Další informace o uživatelskédelegování SAS naleznete [v tématu Vytvoření uživatelské delegování SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas).

- **Služba SAS.** Služba SAS je zabezpečena klíčem účtu úložiště. Služba SAS deleguje přístup k prostředku v jenom v jedné ze služeb Azure Storage: úložiště objektů blob, úložiště front, úložiště tabulek nebo soubory Azure. 

    Další informace o službě SAS naleznete v [tématu Vytvoření služby SAS (REST API).](/rest/api/storageservices/create-service-sas)

- **Účet SAS.** Účet SAS je zabezpečený klíčem účtu úložiště. SAS účtu deleguje přístup k prostředkům v jedné nebo více službách úložiště. Všechny operace dostupné prostřednictvím služby nebo uživatelské delegování SAS jsou také k dispozici prostřednictvím účtu SAS. Navíc s účtem SAS můžete delegovat přístup k operacím, které platí na úrovni služby, jako je **například získat nebo nastavit vlastnosti služby** a **získat služby statistiky** operací. Můžete taky delegovat přístup k operacím čtení, zápis a odstranění pro kontejnery objektů blob, tabulky a sdílené složky, který se nedá vymezit přes SAS služby. 

    Další informace o účtu SAS, [vytvořte účet SAS (REST API)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> Společnost Microsoft doporučuje používat přihlašovací údaje Azure AD, pokud je to možné, jako osvědčený postup zabezpečení, nikoli pomocí klíče účtu, který může být snadněji ohrožen. Když návrh aplikace vyžaduje sdílené přístupové podpisy pro přístup k úložišti objektů Blob, použijte přihlašovací údaje Azure AD k vytvoření sas delegování uživatelů, pokud je to možné pro vyšší zabezpečení.

Sdílený přístupový podpis může mít jednu ze dvou forem:

- **Ad hoc SAS:** Při vytváření ad hoc SAS, čas zahájení, čas vypršení platnosti a oprávnění pro SAS jsou všechny zadány v Identifikátor u URI SAS (nebo implicitní, pokud je vynechán čas zahájení). Jakýkoli typ SAS může být ad hoc SAS.
- **Služba SAS s uloženými zásadami přístupu:** Uložená zásada přístupu je definována v kontejneru prostředků, což může být kontejner objektů blob, tabulka, fronta nebo sdílená složka. Uložené zásady přístupu lze použít ke správě omezení pro jeden nebo více podpisů sdíleného přístupu služby. Když přidružíte službu SAS k uložené zásadě přístupu, SAS zdědí omezení čas&mdash;zahájení, čas vypršení platnosti a oprávnění&mdash;definovaná pro zásady uloženého přístupu.

> [!NOTE]
> Uživatelské delegování SAS nebo účet SAS musí být ad hoc SAS. Uložené zásady přístupu nejsou podporovány pro delegování uživatele SAS nebo účet SAS.

## <a name="how-a-shared-access-signature-works"></a>Jak funguje sdílený přístupový podpis

Sdílený přístupový podpis je podepsaný identifikátor URI, který odkazuje na jeden nebo více prostředků úložiště a obsahuje token, který obsahuje speciální sadu parametrů dotazu. Token označuje, jak k prostředkům může klient přistupovat. Jeden z parametrů dotazu, podpis, je vytvořen z parametrů SAS a podepsán klíčem, který byl použit k vytvoření SAS. Tento podpis používá Azure Storage k autorizaci přístupu k prostředku úložiště.

### <a name="sas-signature"></a>Podpis SAS

SAS můžete podepsat jedním ze dvou způsobů:

- S *klíčem delegování uživatele,* který byl vytvořen pomocí přihlašovacích údajů služby Azure Active Directory (Azure AD). Sas uživatelské delegování je podepsáno klíčem delegování uživatele.

    Chcete-li získat klíč delegování uživatele a vytvořit SAS, musí být primárnímu zabezpečení Azure AD přiřazena role řízení přístupu (RBAC) na základě rolí, která zahrnuje akci **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Podrobné informace o rolích RBAC s oprávněními k získání klíče delegování uživatele naleznete v [tématu Vytvoření uživatelského delegování SAS (REST API).](/rest/api/storageservices/create-user-delegation-sas)

- S klíčem účtu úložiště. Služba SAS i účet SAS jsou podepsány klíčem účtu úložiště. Chcete-li vytvořit SAS, který je podepsán pomocí klíče účtu, musí mít aplikace přístup ke klíči účtu.

### <a name="sas-token"></a>Token SAS

Token SAS je řetězec, který generujete na straně klienta, například pomocí jedné z klientských knihoven Azure Storage. Token SAS není sledován službou Azure Storage žádným způsobem. Můžete vytvořit neomezený počet tokenů SAS na straně klienta. Po vytvoření SAS, můžete distribuovat do klientských aplikací, které vyžadují přístup k prostředkům v účtu úložiště.

Když klientská aplikace poskytuje Identifikátor URI SAS do služby Azure Storage jako součást požadavku, služba zkontroluje parametry SAS a podpis, aby ověřila, zda je platná pro autorizaci požadavku. Pokud služba ověří, zda je podpis platný, je požadavek autorizován. V opačném případě je požadavek odmítnut s kódem chyby 403 (Zakázáno).

Tady je příklad identifikátoru URI služby SAS, který zobrazuje identifikátor URI prostředku a token SAS:

![Součásti identifikátoru URI služby SAS](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Kdy použít sdílený přístupový podpis

SAS použijte, pokud chcete poskytnout zabezpečený přístup k prostředkům v účtu úložiště každému klientovi, který jinak nemá oprávnění k těmto prostředkům.

Běžný scénář, kde je užitečné SAS je služba, kde uživatelé číst a zapisovat vlastní data do účtu úložiště. Ve scénáři, kde účet úložiště ukládá data uživatelů, existují dva typické modely návrhu:

1. Klienti nahrávají a stahují data přes front-endovou proxy službu, která provádí ověřování. Tato front-endová proxy služba má tu výhodu, že umožňuje ověření obchodních pravidel, ale pro velké množství dat nebo velkoobjemových transakcí může být vytvoření služby, která může škálovat tak, aby odpovídala poptávce, nákladné nebo obtížné.

   ![Diagram scénáře: Front-endproxy služba](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Zjednodušená služba podle potřeby ověří klienta a potom vygeneruje sdílený přístupový podpis. Jakmile klientská aplikace obdrží SAS, mohou přistupovat k prostředkům účtu úložiště přímo s oprávněními definovanými SAS a pro interval povolený SAS. Sdílený přístupový podpis snižuje potřebu směrování všech dat přes front-endovou proxy službu.

   ![Diagram scénářů: Služba zprostředkovatele SAS](./media/storage-sas-overview/sas-storage-provider-service.png)

Mnoho služeb v reálném světě může používat hybrid těchto dvou přístupů. Například některá data mohou být zpracována a ověřena prostřednictvím front-endproxy, zatímco jiná data jsou uložena a/nebo přečtena přímo pomocí SAS.

Kromě toho SAS je nutné autorizovat přístup ke zdrojovému objektu v operaci kopírování v určitých scénářích:

- Když zkopírujete objekt blob do jiného objektu blob, který je umístěn v jiném účtu úložiště, musíte použít SAS k autorizaci přístupu ke zdrojovému objektu blob. Volitelně můžete použít SAS autorizovat přístup k cílovému objektu blob také.
- Při kopírování souboru do jiného souboru, který je umístěn v jiném účtu úložiště, musíte použít SAS autorizovat přístup ke zdrojovému souboru. Volitelně můžete použít SAS autorizovat přístup k cílovému souboru také.
- Když zkopírujete objekt blob do souboru nebo soubor do objektu blob, musíte použít SAS k autorizaci přístupu ke zdrojovému objektu, i když jsou zdrojové a cílové objekty umístěny ve stejném účtu úložiště.

## <a name="best-practices-when-using-sas"></a>Osvědčené postupy při používání SAS

Při použití sdílených přístupových podpisů v aplikacích je třeba si být vědom a vědomy se dvou potenciálních rizik:

- Pokud je únik sas, může ji použít každý, kdo ji získá, což může potenciálně ohrozit váš účet úložiště.
- Pokud vyprší platnost SAS poskytované klientské aplikaci a aplikace nemůže načíst nový SAS z vaší služby, pak funkce aplikace může být bráněno.

Následující doporučení pro použití sdílených přístupových podpisů mohou pomoci zmírnit tato rizika:

- **Vždy používejte protokol HTTPS** k vytvoření nebo distribuci SAS. Pokud je SAS předán přes HTTP a zachycen, útočník provádějící útok man-in-the-middle je schopen číst SAS a pak jej použít stejně jako zamýšlený uživatel mohl mít, potenciálně ohrožení citlivých dat nebo povolení poškození dat uživatelem se zlými úmysly.
- **Pokud je to možné, použijte uživatelské delegování SAS.** Uživatelské delegování SAS poskytuje vynikající zabezpečení služby SAS nebo s sas účtu. Uživatelské delegování SAS je zabezpečená přihlašovacími údaji Azure AD, takže není nutné ukládat klíč účtu s kódem.
- **Mají plán odvolání na místě pro SAS.** Ujistěte se, že jste připraveni reagovat, pokud dojde k ohrožení zabezpečení SAS.
- **Definujte uložené zásady přístupu pro službu SAS.** Uložené zásady přístupu poskytují možnost odvolat oprávnění pro službu SAS bez nutnosti opětovného vygenerování klíčů účtu úložiště. Nastavte vypršení platnosti na tyto velmi daleko v budoucnu (nebo nekonečné) a ujistěte se, že je pravidelně aktualizován přesunout dál do budoucnosti.
- **Používejte krátkodobé doby vypršení platnosti na službě SAS ad hoc SAS nebo účtu SAS.** Tímto způsobem i v případě, že je ohrožena SAS, je platný pouze na krátkou dobu. Tento postup je obzvláště důležité, pokud nelze odkazovat na zásady uložené přístupu. Krátkodobé doby vypršení platnosti také omezit množství dat, které lze zapsat do objektu blob omezením čas k dispozici pro nahrávání do něj.
- **Klienti v případě potřeby automaticky obnovují SAS.** Klienti by měli obnovit SAS v předstihu před vypršením platnosti, aby byl čas pro opakování, pokud služba poskytující SAS není k dispozici. Pokud vaše SAS je určen pro malý počet okamžité, krátkodobé operace, které se očekává, že bude dokončena v rámci období vypršení platnosti, pak to může být zbytečné, protože sas se neočekává, že bude obnovena. Pokud však máte klienta, který běžně podává žádosti prostřednictvím sas, pak se do hry dostane možnost vypršení platnosti. Klíčovým aspektem je vyvážit potřebu SAS být krátkodobý (jak již bylo uvedeno) s potřebou zajistit, že klient žádá o obnovení dostatečně brzy (aby se zabránilo narušení z důvodu SAS končí před úspěšným obnovením).
- **Buďte opatrní s časem zahájení SAS.** Pokud nastavíte čas zahájení pro SAS do **teď**, pak z důvodu zkosení hodin (rozdíly v aktuálním čase podle různých počítačů), selhání může být pozorováno přerušovaně po dobu prvních několika minut. Obecně platí, že nastavte čas zahájení alespoň 15 minut v minulosti. Nebo ji vůbec nenastavujte, což bude ve všech případech okamžitě platné. Totéž obecně platí i pro čas vypršení platnosti – nezapomeňte, že můžete sledovat až 15 minut hodiny zkosení v obou směrech na jakýkoli požadavek. Pro klienty, kteří používají verzi REST před 2012-02-12, maximální doba trvání pro SAS, která neodkazuje na zásady uloženého přístupu, je 1 hodina a všechny zásady určující delší období, než které se nezdaří.
- **Buďte opatrní s formátem datatime SAS.** Pokud nastavíte čas zahájení a/nebo vypršení platnosti pro SAS, pro některé nástroje (například pro nástroj příkazového řádku AzCopy) potřebujete formát datatime být '+%Y-%m-%dT%H:%M:%SZ', konkrétně včetně sekund, aby mohl pracovat pomocí tokenu SAS.  
- **Buďte konkrétní s prostředkem, ke který má být přistupuje.** Osvědčeným postupem zabezpečení je poskytnout uživateli minimální požadovaná oprávnění. Pokud uživatel potřebuje pouze přístup pro čtení k jedné entitě, udělte mu přístup pro čtení k této jedné entitě a nepřístup pro čtení, zápis/odstranění všem entitám. To také pomáhá snížit poškození, pokud je ohrožena SAS, protože SAS má méně energie v rukou útočníka.
- **Mějte na klíče, že váš účet bude účtován za jakékoli použití, včetně sas.** Pokud zadáte přístup pro zápis do objektu blob, uživatel může zvolit nahrát 200 GB objektblob. Pokud jste jim také udělili přístup ke čtení, mohou se rozhodnout, že si je 10krát stáhnou, což vám vynaloží 2 TB v nákladech na výstup. Opět poskytují omezená oprávnění, která pomáhají zmírnit potenciální akce uživatelů se zlými úmysly. Ke snížení této hrozby použijte krátkodobé SAS (ale mějte na paměti zkosení hodin na koncovém čase).
- **Ověřte data zapsaná pomocí SAS.** Když klientská aplikace zapisuje data do vašeho účtu úložiště, mějte na paměti, že mohou být problémy s těmito daty. Pokud vaše aplikace vyžaduje, aby data byla ověřena nebo autorizována před tím, než je připravena k použití, měli byste toto ověření provést po zapsání dat a před použitím vaší aplikací. Tento postup také chrání před poškozenými nebo škodlivými daty zapisovanými do vašeho účtu, a to buď uživatelem, který správně získal SAS, nebo uživatelem využívajícím uniklé SAS.
- **Vědět, kdy nepoužívat SAS.** Někdy rizika spojená s konkrétní operací proti účtu úložiště převažují nad výhodami používání SAS. Pro takové operace vytvořte službu střední vrstvy, která zapisuje do vašeho účtu úložiště po provedení ověření, ověřování a auditování obchodních pravidel. Někdy je také jednodušší spravovat přístup jinými způsoby. Například pokud chcete, aby všechny objekty BLOB v kontejneru veřejně čitelné, můžete vytvořit kontejner veřejné, spíše než poskytovat SAS pro každého klienta pro přístup.
- **Ke sledování vaší aplikace použijte protokoly Azure Monitor a Azure Storage.** Azure Monitor a protokolování analýzy úložiště můžete použít ke sledování jakéhokoli nárůstu selhání autorizace z důvodu výpadku ve službě poskytovatele SAS nebo neúmyslného odebrání zásad y uloženého přístupu. Další informace najdete v tématu [metriky Azure Storage v Azure Monitor](storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) a Azure Storage [Analytics protokolování](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>Začínáme se sazí SAS

Pokud chcete začít se sdílenými přístupovými podpisy, přečtěte si následující články pro každý typ SAS.

### <a name="user-delegation-sas"></a>Uživatelské delegování SAS

- [Vytvoření uživatelskédelegování SAS pro kontejner nebo objekt blob s Prostředím PowerShell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Vytvoření uživatelskédelegování SAS pro kontejner nebo objekt blob s rozhraním příkazového příkazu Azure](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Vytvoření uživatelskédelegování SAS pro kontejner nebo objekt blob s rozhraním .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Služba SAS

- [Vytvoření služby SAS pro kontejner nebo objekt blob s rozhraním .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>Účet SAS

- [Vytvoření účtu SAS s rozhraním .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Další kroky

- [Delegování přístupu pomocí sdíleného přístupového podpisu (ROZHRANÍ REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Vytvoření uživatelskédelegace SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Vytvoření služby SAS (REST API)](/rest/api/storageservices/create-service-sas)
- [Vytvoření účtu SAS (REST API)](/rest/api/storageservices/create-account-sas)
