---
title: Kopírování nebo přesun dat do Služby Azure Storage pomocí AzCopy v10 | Dokumenty společnosti Microsoft
description: AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování dat do účtů úložiště, z nich nebo mezi nimi. Tento článek vám pomůže stáhnout AzCopy, připojit se k účtu úložiště a pak přenést soubory.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 90adb3b973b6952d10ef89e4b40d0fc68489e224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061095"
---
# <a name="get-started-with-azcopy"></a>Začínáme s nástrojem AzCopy

AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování objektů BLOB nebo souborů do nebo z účtu úložiště. Tento článek vám pomůže stáhnout AzCopy, připojit se k účtu úložiště a pak přenést soubory.

> [!NOTE]
> AzCopy **V10** je aktuálně podporovaná verze AzCopy.
>
> Pokud potřebujete použít AzCopy **v8.1**, podívejte se na [použití předchozí verze azCopy](#previous-version) části tohoto článku.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Stažení AzCopy

Nejprve stáhněte spustitelný soubor AzCopy V10 do libovolného adresáře v počítači. AzCopy V10 je jen spustitelný soubor, takže není co instalovat.

- [Windows 64-bit](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32bitový](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (dehet)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Tyto soubory jsou komprimovány jako zip soubor (Windows a Mac) nebo tar soubor (Linux). Chcete-li stáhnout a dekomprimovat soubor tar na Linuxu, podívejte se do dokumentace k distribuci Linuxu.

> [!NOTE]
> Pokud chcete kopírovat data do a ze služby [úložiště Azure Table,](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) nainstalujte [AzCopy verze 7.3](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>Spustit AzCopy

Pro větší pohodlí zvažte přidání umístění adresáře spustitelného souboru AzCopy do systémové cesty pro snadné použití. Tímto způsobem `azcopy` můžete psát z libovolného adresáře v systému.

Pokud se rozhodnete nepřidávat adresář AzCopy do cesty, budete muset změnit adresáře na umístění spustitelného souboru AzCopy a typu `azcopy` nebo `.\azcopy` v příkazových krucích prostředí Windows PowerShell.

Chcete-li zobrazit seznam příkazů, zadejte a `azcopy -h` stiskněte klávesu ENTER.

Chcete-li se dozvědět o konkrétním příkazu, stačí `azcopy list -h`uvést název příkazu (například: ).

![Inline nápověda](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Podrobné referenční dokumentace pro každý příkaz a parametr příkazu naleznete [v tématu azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> Jako vlastník vašeho účtu Azure Storage vám nejsou automaticky přiřazena oprávnění k přístupu k datům. Než budete moci s AzCopy udělat něco smysluplného, musíte se rozhodnout, jak poskytnete autorizační pověření službě úložiště. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Zvolte, jak budete zajišťovali autorizační pověření.

Autorizační pověření můžete zadat pomocí služby Azure Active Directory (AD) nebo pomocí tokenu sdíleného přístupového podpisu (SAS).

Tuto tabulku použijte jako vodítko:

| Typ úložiště | Aktuálně podporovaná metoda autorizace |
|--|--|
|**Blob Storage** | Azure AD & SAS |
|**Úložiště objektů blob (hierarchický obor názvů)** | Azure AD & SAS |
|**File Storage** | Pouze SAS |

### <a name="option-1-use-azure-active-directory"></a>Možnost 1: Použití služby Azure Active Directory

Pomocí služby Azure Active Directory můžete zadat přihlašovací údaje jednou místo nutnosti připojit token SAS ke každému příkazu.  

> [!NOTE]
> Pokud v aktuální verzi plánujete kopírovat objekty BLOB mezi účty úložiště, budete muset ke každé zdrojové adrese URL připojit token SAS. Token SAS můžete vynechat pouze z cílové adresy URL. Příklady najdete v [tématu Kopírování objektů BLOB mezi účty úložiště](storage-use-azcopy-blobs.md).

Úroveň autorizace, kterou potřebujete, je založena na tom, zda plánujete nahrát soubory nebo je jen stáhnout.

Pokud chcete pouze stáhnout soubory, ověřte, zda byla [čtečka dat objektů blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) přiřazena k vaší identitě uživatele, spravované identitě nebo instančnímu objektu služby.

> Identity uživatelů, spravované identity a instanční objekty jsou každý typ *zaregistrovaný objekt zabezpečení*, takže budeme používat termín *zaregistrovaný objekt zabezpečení* pro zbytek tohoto článku.

Chcete-li nahrát soubory, ověřte, zda byla k objektu zabezpečení přiřazena jedna z těchto rolí:

- [Přispěvatel dat objektu blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Vlastník dat objektu blob úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Tyto role lze přiřadit k objektu zabezpečení v některém z těchto oborů:

- Kontejner (souborový systém)
- Účet úložiště
- Skupina prostředků
- Předplatné

Informace o tom, jak ověřit a přiřadit role, najdete v [tématu Udělení přístupu k datům objektů blob Azure a fronty pomocí RBAC na webu Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Mějte na paměti, že přiřazení rolí RBAC může trvat až pět minut k šíření.

Pokud je objekt zabezpečení přidán do seznamu řízení přístupu (ACL) cílového kontejneru nebo adresáře, nemusíte mít jednu z těchto rolí přiřazenou k objektu zabezpečení. V seznamu ACL vyžaduje váš objekt zabezpečení oprávnění k zápisu do cílového adresáře a spouštět oprávnění pro kontejner a každý nadřazený adresář.

Další informace najdete [v tématu Řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Ověření identity uživatele

Po ověření, že identita uživatele byla přidělena potřebnou úroveň autorizace, otevřete příkazový řádek, zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login
```

Pokud patříte do více než jedné organizace, uveďte ID klienta organizace, do které patří účet úložiště.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Nahraďte `<tenant-id>` zástupný symbol ID klienta organizace, do které účet úložiště patří. Pokud chcete Najít ID klienta, vyberte na webu Azure Portal **> ID adresáře Služby Azure > vlastnosti služby Azure.**

Tento příkaz vrátí ověřovací kód a adresu URL webu. Otevřete web, zadejte kód a pak zvolte tlačítko **Další.**

![Vytvoření kontejneru](media/storage-use-azcopy-v10/azcopy-login.png)

Zobrazí se přihlašovací okno. V tomto okně se přihlaste ke svému účtu Azure pomocí přihlašovacích údajů účtu Azure. Po úspěšném přihlášení můžete zavřít okno prohlížeče a začít používat AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Ověření instančního objektu

To je skvělá volba, pokud máte v plánu použít AzCopy uvnitř skriptu, který běží bez interakce s uživatelem, zejména při spuštění v místním prostředí. Pokud máte v plánu spustit AzCopy na virtuálních počítačích, které běží v Azure, identita spravované služby je jednodušší spravovat. Další informace najdete v tomto článku v části [Ověření spravované identity.](#managed-identity)

Před spuštěním skriptu je třeba se přihlásit interaktivně alespoň jednou, abyste mohli azCopy poskytnout pověření instančního objektu.  Tato pověření jsou uložena v zabezpečeném a zašifrovaném souboru, takže skript nemusí poskytovat tyto citlivé informace.

Ke svému účtu se můžete přihlásit pomocí tajného klíče klienta nebo pomocí hesla certifikátu, který je přidružen k registraci aplikace instančního objektu.

Další informace o vytváření instančního objektu najdete v [tématu Postup: Pomocí portálu vytvořte instanční objekt Azure AD, který má přístup k prostředkům](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Další informace o objektech zabezpečení služeb obecně najdete v tématu [Objekty zaregistrovaných aplikace a služby ve službě Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Použití tajného klíče klienta

Začněte nastavením proměnné `AZCOPY_SPA_CLIENT_SECRET` prostředí na tajný klíč klienta registrace aplikace hlavního povinného servisu.

> [!NOTE]
> Tuto hodnotu nastavíte na příkazovém řádku, nikoli v nastavení proměnných prostředí operačního systému. Tímto způsobem je hodnota k dispozici pouze pro aktuální relaci.

Tento příklad ukazuje, jak to můžete udělat v prostředí PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Zvažte použití výzvy, jak je znázorněno v tomto příkladu. Tímto způsobem se vaše heslo nezobrazí v historii příkazů konzole.  

Dále zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

Nahraďte `<application-id>` zástupný symbol ID aplikace registrace aplikace instančního objektu. Nahraďte `<tenant-id>` zástupný symbol ID klienta organizace, do které účet úložiště patří. Pokud chcete Najít ID klienta, vyberte na webu Azure Portal **> ID adresáře Služby Azure > vlastnosti služby Azure.** 

##### <a name="using-a-certificate"></a>Použití certifikátu

Pokud dáváte přednost použití vlastních přihlašovacích údajů pro autorizaci, můžete nahrát certifikát do registrace aplikace a pak se pomocí tohoto certifikátu přihlásit.

Kromě nahrání certifikátu do registrace aplikace budete muset mít kopii certifikátu uloženou do počítače nebo virtuálního počítače, kde bude AzCopy spuštěna. Tato kopie certifikátu by měla být v . PFX nebo . PEM a musí obsahovat soukromý klíč. Soukromý klíč by měl být chráněn heslem. Pokud používáte Systém Windows a váš certifikát existuje pouze v úložišti certifikátů, nezapomeňte exportovat tento certifikát do souboru PFX (včetně soukromého klíče). Pokyny naleznete v [tématu Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Dále nastavte `AZCOPY_SPA_CERT_PASSWORD` proměnnou prostředí na heslo certifikátu.

> [!NOTE]
> Tuto hodnotu nastavíte na příkazovém řádku, nikoli v nastavení proměnných prostředí operačního systému. Tímto způsobem je hodnota k dispozici pouze pro aktuální relaci.

Tento příklad ukazuje, jak můžete provést tento úkol v prostředí PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Dále zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Nahraďte `<path-to-certificate-file>` zástupný symbol relativní nebo plně kvalifikovanou cestou k souboru certifikátu. AzCopy uloží cestu k tomuto certifikátu, ale neuloží kopii certifikátu, takže nezapomeňte tento certifikát ponechat na místě. Nahraďte `<tenant-id>` zástupný symbol ID klienta organizace, do které účet úložiště patří. Pokud chcete Najít ID klienta, vyberte na webu Azure Portal **> ID adresáře Služby Azure > vlastnosti služby Azure.**

> [!NOTE]
> Zvažte použití výzvy, jak je znázorněno v tomto příkladu. Tímto způsobem se vaše heslo nezobrazí v historii příkazů konzole. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>Ověření spravované identity

To je skvělá volba, pokud máte v plánu použít AzCopy uvnitř skriptu, který běží bez interakce s uživatelem a skript běží z virtuálního počítače Azure (VM). Při použití této možnosti nebudete muset ukládat žádná pověření na virtuálním počítači.

Ke svému účtu se můžete přihlásit pomocí spravované identity pro celý systém, kterou jste povolili na virtuálním počítači, nebo pomocí ID klienta, ID objektu nebo ID prostředku uživatelem přiřazené spravované identity, kterou jste přiřadili k virtuálnímu počítači.

Další informace o povolení spravované identity pro celý systém nebo vytvoření spravované identity přiřazené uživateli najdete v [tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí portálu Azure](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

##### <a name="using-a-system-wide-managed-identity"></a>Použití spravované identity pro celý systém

Nejprve se ujistěte, že jste na virtuálním počítači povolili spravovanou identitu celého systému. Viz [Spravovaná identita přiřazená systémem](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity).

Potom do příkazové konzole zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Použití spravované identity přiřazené uživateli

Nejprve se ujistěte, že jste na virtuálním počítači povolili spravovanou identitu přiřazenou uživateli. Viz [Spravovaná identita přiřazená uživatelem](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Potom do příkazové konzole zadejte některý z následujících příkazů a stiskněte klávesu ENTER.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Nahraďte `<client-id>` zástupný symbol ID klienta spravované identity přiřazené uživateli.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Nahraďte `<object-id>` zástupný symbol ID objektu spravované identity přiřazené uživateli.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Nahraďte `<resource-id>` zástupný symbol ID prostředku spravované identity přiřazené uživateli.

### <a name="option-2-use-a-sas-token"></a>Možnost 2: Použití tokenu SAS

Ke každé zdrojové nebo cílové adrese URL, které se používají v příkazech AzCopy, můžete připojit token SAS.

Tento příklad příkazu rekurzivně zkopíruje data z místního adresáře do kontejneru objektů blob. Fiktivní token SAS je připojen na konec adresy URL kontejneru.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Další informace o tokenech SAS a o tom, jak je získat, najdete [v tématu Používání sdílených přístupových podpisů (SAS).](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

## <a name="transfer-files"></a>Přenos souborů

Po ověření identity nebo získání tokenu SAS můžete začít přenášet soubory.

Chcete-li najít ukázkové příkazy, podívejte se na některý z těchto článků.

- [Přenos dat pomocí úložiště Objektů blob AzCopy a objektů blob](storage-use-azcopy-blobs.md)

- [Přenos dat pomocí AzCopy a ukládání souborů](storage-use-azcopy-files.md)

- [Přenos dat pomocí kbelíků AzCopy a Amazon S3](storage-use-azcopy-s3.md)

- [Přenos dat pomocí úložiště AzCopy a Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Použití AzCopy ve skriptu

### <a name="obtain-a-static-download-link"></a>Získat statický odkaz ke stažení

V průběhu času bude [odkaz ke stažení](#download-and-install-azcopy) AzCopy odkazovat na nové verze AzCopy. Pokud váš skript stáhne AzCopy, může skript přestat fungovat, pokud novější verze AzCopy upraví funkce, na kterých váš skript závisí.

Chcete-li se těmto problémům vyhnout, získejte statický (neměnící) odkaz na aktuální verzi AzCopy. Tímto způsobem, váš skript stáhne stejnou přesnou verzi AzCopy pokaždé, když běží.

Chcete-li získat odkaz, spusťte tento příkaz:

| Operační systém  | Příkaz |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Pro Linux `--strip-components=1` na `tar` příkaz odebere složku nejvyšší úrovně, která obsahuje název verze a místo toho extrahuje binární přímo do aktuální složky. To umožňuje aktualizovat skript s novou `azcopy` verzí pouze `wget` aktualizací adresy URL.

Adresa URL se zobrazí ve výstupu tohoto příkazu. Skript pak můžete stáhnout AzCopy pomocí této adresy URL.

| Operační systém  | Příkaz |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Uniknout speciální znaky v tokenech SAS

V dávkových souborech, které mají `.cmd` příponu, budete muset uniknout `%` znaky, které se zobrazí v tokenech SAS. Můžete to udělat přidáním `%` další znak `%` vedle existující znaky v řetězci tokenu SAS.

### <a name="run-scripts-by-using-jenkins"></a>Spouštění skriptů pomocí Jenkinse

Pokud chcete ke spuštění skriptů použít [Jenkinse,](https://jenkins.io/) nezapomeňte na začátek skriptu umístit následující příkaz.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>Použití AzCopy v Průzkumníku úložiště Azure

[Průzkumník úložiště](https://azure.microsoft.com/features/storage-explorer/) používá AzCopy k provádění všech operací přenosu dat. [Průzkumník a úložiště](https://azure.microsoft.com/features/storage-explorer/) můžete použít, pokud chcete využít výhody výkonu AzCopy, ale dáváte přednost použití grafického uživatelského rozhraní, nikoli příkazového řádku pro interakci se soubory.

Průzkumník úložiště používá klíč vašeho účtu k provádění operací, takže po přihlášení do Průzkumníka úložiště nebudete muset zajišťovat další autorizační pověření.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Použití předchozí verze AzCopy

Pokud potřebujete použít předchozí verzi AzCopy (AzCopy v8.1), podívejte se na jeden z následujících odkazů:

- [AzCopy ve Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy v Linuxu (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurace, optimalizace a řešení potíží s azcopy

Viz [Konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Další kroky

Pokud máte dotazy, problémy nebo obecnou zpětnou vazbu, odešlete je na stránku [GitHub.](https://github.com/Azure/azure-storage-azcopy)
