---
title: Kopírování nebo přesun dat do Azure Storage pomocí AzCopy v10 za účelem | Microsoft Docs
description: AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování dat do, z nebo mezi účty úložiště. Tento článek vám pomůže stáhnout AzCopy, připojit se k vašemu účtu úložiště a pak přenést soubory.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 6b5be5271e2ff579d93cb70f7c8da93d861d4dc0
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648731"
---
# <a name="get-started-with-azcopy"></a>Začínáme s AzCopy

AzCopy je nástroj příkazového řádku, který můžete použít ke kopírování objektů BLOB nebo souborů do nebo z účtu úložiště. Tento článek vám pomůže stáhnout AzCopy, připojit se k vašemu účtu úložiště a pak přenést soubory.

> [!NOTE]
> AzCopy **v10 za účelem** je aktuálně podporovaná verze AzCopy.
>
> Pokud potřebujete použít AzCopy **v 8.1**, přečtěte si část [Použití předchozí verze AzCopy](#previous-version) tohoto článku.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Stáhnout AzCopy

Nejdřív Stáhněte spustitelný soubor AzCopy v10 za účelem do libovolného adresáře v počítači.

- [Systém Windows](https://aka.ms/downloadazcopy-v10-windows) věřitel
- [Linux](https://aka.ms/downloadazcopy-v10-linux) tar
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) věřitel

AzCopy v10 za účelem je jenom spustitelný soubor, takže nemusíte nic instalovat.

> [!NOTE]
> Pokud chcete kopírovat data do služby [Azure Table Storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) a z ní, nainstalujte [AzCopy verze 7,3](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Spustit AzCopy

Pro usnadnění práce zvažte možnost Přidat umístění adresáře spustitelného souboru AzCopy do systémové cesty pro snadné použití. Tímto způsobem můžete zadat `azcopy` libovolný adresář v systému.

Pokud se rozhodnete Nepřidávat adresář AzCopy do cesty, budete muset změnit adresáře na umístění spustitelného souboru AzCopy a typu `azcopy` nebo `.\azcopy` v příkazovém řádku prostředí Windows PowerShell.

Pokud chcete zobrazit seznam příkazů, zadejte `azcopy -h` a potom stiskněte klávesu ENTER.

Chcete-li získat informace o konkrétním příkazu, stačí zahrnout název příkazu (například: `azcopy list -h`).

![Vložená Help](media/storage-use-azcopy-v10/azcopy-inline-help.png)

> [!NOTE] 
> Jako vlastník účtu Azure Storage nemáte automaticky přiřazená oprávnění pro přístup k datům. Než budete moct s AzCopy dělat něco smysluplného, budete se muset rozhodnout, jak zadáte přihlašovací údaje pro autorizaci službě úložiště. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Vyberte způsob poskytování autorizačních přihlašovacích údajů.

Autorizační přihlašovací údaje můžete zadat pomocí Azure Active Directory (AD) nebo pomocí tokenu sdíleného přístupového podpisu (SAS).

Tuto tabulku použijte jako vodítko:

| Typ úložiště | Aktuálně podporovaná metoda autorizace |
|--|--|
|**Blob Storage** | & SAS pro Azure AD |
|**Úložiště objektů BLOB (hierarchické obor názvů)** | & SAS pro Azure AD |
|**Úložiště souborů** | Pouze SAS |

### <a name="option-1-use-azure-active-directory"></a>Možnost 1: Použít Azure Active Directory

Pomocí Azure Active Directory můžete zadat přihlašovací údaje místo toho, abyste museli k jednotlivým příkazům připojit token SAS.  

> [!NOTE]
> Pokud v aktuální verzi plánujete zkopírovat objekty blob mezi účty úložiště, budete muset ke každé zdrojové adrese URL připojit token SAS. Token SAS můžete vynechat pouze z cílové adresy URL. Příklady najdete v tématu [kopírování objektů BLOB mezi účty úložiště](storage-use-azcopy-blobs.md).

Úroveň autorizace, kterou potřebujete, je založená na tom, jestli plánujete odeslat soubory, nebo je stačí stáhnout.

Pokud chcete jenom stahovat soubory, ověřte, že je čtečka [dat objektů BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) přiřazená identitě uživatele, spravované identitě nebo instančnímu objektu.

> Identity uživatelů, spravované identity a instanční objekty jsou typu *objektu zabezpečení*, takže budeme používat pojem *zabezpečení* pro zbývající část tohoto článku.

Chcete-li odeslat soubory, ověřte, zda byla k objektu zabezpečení přiřazena jedna z těchto rolí:

- [Přispěvatel dat objektu BLOB služby Storage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Vlastník dat objektu BLOB služby Storage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Tyto role se dají přiřadit k objektu zabezpečení v libovolném z těchto oborů:

- Kontejner (systém souborů)
- Účet úložiště
- Resource group
- Subscription

Informace o tom, jak ověřit a přiřadit role, najdete [v tématu udělení přístupu k datům Azure Blob a Queue do fronty pomocí RBAC v Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Pamatujte na to, že přiřazení rolí RBAC může trvat až pět minut.

Pokud je do seznamu řízení přístupu (ACL) cílového kontejneru nebo adresáře přidaný objekt zabezpečení, nemusíte mít přiřazenou žádnou z těchto rolí. V seznamu ACL vyžaduje váš instanční objekt oprávnění k zápisu do cílového adresáře a oprávnění ke spuštění na kontejneru a v jednotlivých nadřazených adresářích.

Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Ověření identity uživatele

Po ověření, že byla identitě uživatele přidělena potřebná úroveň autorizace, otevřete příkazový řádek, zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login
```

Pokud patříte do více než jedné organizace, uveďte ID tenanta organizace, ke které patří účet úložiště.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

`<tenant-id>` Zástupný symbol nahraďte ID tenanta organizace, které patří k účtu úložiště. ID tenanta zjistíte tak, že v Azure Portal vyberete **Azure Active Directory > vlastnosti > ID adresáře** .

Tento příkaz vrátí ověřovací kód a adresu URL webu. Otevřete web, zadejte kód a potom klikněte na tlačítko **Další** .

![Vytvoření kontejneru](media/storage-use-azcopy-v10/azcopy-login.png)

Zobrazí se okno přihlášení. V tomto okně se přihlaste k účtu Azure pomocí svých přihlašovacích údajů k účtu Azure. Po úspěšném přihlášení můžete zavřít okno prohlížeče a začít používat AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Ověření instančního objektu

Tato možnost je skvělá, pokud plánujete používat AzCopy uvnitř skriptu, který běží bez zásahu uživatele, zejména při místním spuštění. Pokud máte v úmyslu spustit AzCopy na virtuálních počítačích, které běží v Azure, je snazší spravovat identitu spravované služby. Další informace najdete v části [ověřování spravované identity](#managed-identity) v tomto článku.

Před spuštěním skriptu se musíte interaktivně přihlašovat aspoň jednou, abyste AzCopy mohli zadat přihlašovací údaje k instančnímu objektu.  Tyto přihlašovací údaje jsou uložené v zabezpečeném a zašifrovaném souboru, aby váš skript nemusel poskytovat citlivé informace.

K účtu se můžete přihlásit pomocí tajného klíče klienta nebo pomocí hesla certifikátu, který je přidružený k registraci aplikace vašeho objektu služby.

Další informace o vytváření instančního objektu najdete v [tématu How to: Portál můžete použít k vytvoření aplikace a instančního objektu služby Azure AD, který má](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)přístup k prostředkům.

Další informace o hlavních instančních objektech naleznete v tématu [Application and Service Principal Objects in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Použití tajného klíče klienta

Začněte nastavením `AZCOPY_SPA_CLIENT_SECRET` proměnné prostředí na tajný klíč klienta registrace aplikace objektu služby.

> [!NOTE]
> Ujistěte se, že jste tuto hodnotu nastavili z příkazového řádku, a ne do nastavení proměnných prostředí operačního systému. Tímto způsobem je hodnota k dispozici pouze pro aktuální relaci.

Tento příklad ukazuje, jak to lze provést v prostředí PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Zvažte použití výzvy, jak je znázorněno v tomto příkladu. Tímto způsobem se vaše heslo nezobrazí v historii příkazů vaší konzole.  

Potom zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login --service-principal --application-id <application-id>
```

`<application-id>` Zástupný symbol nahraďte ID aplikace registrace aplikace vašeho objektu služby.

##### <a name="using-a-certificate"></a>Použití certifikátu

Pokud dáváte přednost používání vlastních přihlašovacích údajů pro autorizaci, můžete k registraci aplikace nahrát certifikát a pak ho použít k přihlášení.

Kromě odeslání certifikátu do registrace aplikace budete také muset mít uloženou kopii certifikátu uloženého na počítači nebo virtuální počítač, kde bude AzCopy běžet. Tato kopie certifikátu by měla být v. PFX nebo. Formát PEM a musí obsahovat privátní klíč. Privátní klíč by měl být chráněn heslem. Pokud používáte systém Windows a váš certifikát existuje pouze v úložišti certifikátů, nezapomeňte tento certifikát exportovat do souboru PFX (včetně privátního klíče). Pokyny najdete v tématu [Export-vybíráte](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps) .

Dále nastavte `AZCOPY_SPA_CERT_PASSWORD` proměnnou prostředí na heslo certifikátu.

> [!NOTE]
> Ujistěte se, že jste tuto hodnotu nastavili z příkazového řádku, a ne do nastavení proměnných prostředí operačního systému. Tímto způsobem je hodnota k dispozici pouze pro aktuální relaci.

Tento příklad ukazuje, jak můžete tuto úlohu provést v prostředí PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Potom zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file>
```

`<path-to-certificate-file>` Zástupný text nahraďte relativní nebo plně kvalifikovanou cestou k souboru certifikátu. AzCopy uloží cestu k tomuto certifikátu, ale neuloží kopii certifikátu, proto nezapomeňte tento certifikát ponechat na místě.

> [!NOTE]
> Zvažte použití výzvy, jak je znázorněno v tomto příkladu. Tímto způsobem se vaše heslo nezobrazí v historii příkazů vaší konzole. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>Ověření spravované identity

To je skvělý způsob, pokud plánujete používat AzCopy uvnitř skriptu, který se spouští bez zásahu uživatele, a skript se spouští z virtuálního počítače Azure (VM). Když použijete tuto možnost, nebudete muset na virtuální počítač ukládat žádné přihlašovací údaje.

Ke svému účtu se můžete přihlásit pomocí spravované identity v rámci systému, kterou jste povolili na svém VIRTUÁLNÍm počítači, nebo pomocí ID klienta, ID objektu nebo ID prostředku spravované identity přiřazené uživatelem, kterou jste přiřadili k VIRTUÁLNÍmu počítači.

Další informace o tom, jak povolit spravovanou identitu v rámci systému nebo vytvořit uživatelem přiřazenou identitu, najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

##### <a name="using-a-system-wide-managed-identity"></a>Použití spravované identity v rámci systému

Nejdřív se ujistěte, že máte na svém VIRTUÁLNÍm počítači povolenou spravovanou identitu na úrovni systému. Viz [spravovaná identita přiřazená systémem](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity).

Pak v konzole příkazů zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Použití spravované identity přiřazené uživatelem

Nejdřív se ujistěte, že jste na svém VIRTUÁLNÍm počítači povolili spravovanou identitu přiřazenou uživatelem. Podívejte se [na spravovanou identitu přiřazenou uživatelem](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Pak v konzole příkazů zadejte libovolný z následujících příkazů a potom stiskněte klávesu ENTER.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

`<client-id>` Zástupný symbol nahraďte ID klienta spravované identity přiřazené uživatelem.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

`<object-id>` Zástupný symbol nahraďte ID objektu spravované identity přiřazené uživatelem.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

`<resource-id>` Zástupný symbol nahraďte ID prostředku spravované identity přiřazené uživatelem.

### <a name="option-2-use-a-sas-token"></a>Možnost 2: Použít token SAS

Token SAS můžete připojit ke každé zdrojové nebo cílové adrese URL, která se používá v příkazech AzCopy.

Tento ukázkový příkaz rekurzivně kopíruje data z místního adresáře do kontejneru objektů BLOB. Fiktivní token SAS je připojen na konec adresy URL kontejneru.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Další informace o tokenech SAS a o tom, jak je získat, najdete v tématu [použití sdílených přístupových podpisů (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview).

## <a name="transfer-files"></a>Přenos souborů

Po ověření identity nebo získání tokenu SAS můžete začít přenášet soubory.

Ukázkové příkazy najdete v některém z těchto článků.

- [Přenos dat pomocí AzCopy a BLOB Storage](storage-use-azcopy-blobs.md)

- [Přenos dat pomocí AzCopy a úložiště souborů](storage-use-azcopy-files.md)

- [Přenos dat pomocí kontejnerů AzCopy a Amazon S3](storage-use-azcopy-s3.md)

- [Přenos dat pomocí úložiště AzCopy a Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Použití AzCopy ve skriptu

### <a name="obtain-a-static-download-link"></a>Získat odkaz na statický ke stažení

V průběhu času bude [odkaz ke stažení](#download-and-install-azcopy) AzCopy ukazovat na nové verze AzCopy. Pokud váš skript stahuje AzCopy, může skript přestat fungovat, pokud novější verze AzCopy upraví funkce, na kterých váš skript závisí.

Aby se tyto problémy předešly, Získejte statický (nezměněný) odkaz na aktuální verzi AzCopy. Díky tomu váš skript při každém spuštění stáhne stejnou přesnou verzi AzCopy.

Chcete-li získat odkaz, spusťte tento příkaz:

| Operační systém  | Příkaz |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Pro Linux `--strip-components=1` `tar` příkaz v příkazu odebere složku na nejvyšší úrovni, která obsahuje název verze, a místo toho extrahuje binární soubor přímo do aktuální složky. Tím umožníte, aby se skript aktualizoval pomocí nové verze `azcopy` nástroje, a to tak, že `wget` aktualizuje adresu URL.

Adresa URL se zobrazí ve výstupu tohoto příkazu. Skript pak může stáhnout AzCopy pomocí této adresy URL.

| Operační systém  | Příkaz |
|--------|-----------|
| **Linux** | `wget -O azcopyv10.tar https://azcopyvnext.azureedge.net/release20190301/azcopy_linux_amd64_10.0.8.tar.gz tar -xf azcopyv10.tar --strip-components=1 ./azcopy` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Sekvence speciálních znaků v tokenech SAS

V dávkových souborech, které `.cmd` mají rozšíření, je nutné `%` řídicí znaky, které se zobrazí v tokenech SAS. To lze provést přidáním znaku sčítání `%` vedle existujících `%` znaků v řetězci tokenu SAS.

## <a name="use-azcopy-in-storage-explorer"></a>Použití AzCopy v Průzkumník služby Storage

Pokud chcete využívat výhody výkonu AzCopy, ale dáváte přednost použití Průzkumník služby Storage místo příkazového řádku pro interakci se soubory, pak povolte AzCopy v Průzkumník služby Storage.

V Průzkumník služby Storage**pro lepší nahrávání a stahování objektů BLOB**vyberte **Preview**->použít AzCopy.

![Povolit AzCopy jako modul přenosu v Průzkumník služby Azure Storage](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Toto nastavení nemusíte povolit, pokud jste povolili hierarchický obor názvů v účtu úložiště. To je proto, že Průzkumník služby Storage automaticky používá AzCopy v účtech úložiště, které mají hierarchický obor názvů.  

Průzkumník služby Storage používá k provádění operací klíč účtu, takže po přihlášení k Průzkumník služby Storage nebudete muset zadávat další přihlašovací údaje pro autorizaci.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Použít předchozí verzi AzCopy

Pokud potřebujete použít předchozí verzi AzCopy (AzCopy v 8.1), podívejte se na některý z následujících odkazů:

- [AzCopy ve Windows (V8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy on Linux (V8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurace, optimalizace a řešení potíží s AzCopy

Viz [konfigurace, optimalizace a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Další postup

Pokud máte dotazy, problémy nebo obecné názory, odešlete je [na stránku GitHub](https://github.com/Azure/azure-storage-azcopy) .
