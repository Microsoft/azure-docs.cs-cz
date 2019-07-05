---
title: Kopírování nebo přesun dat do služby Azure Storage pomocí AzCopy v10 | Dokumentace Microsoftu
description: AzCopy je nástroj příkazového řádku, který můžete použít ke zkopírování dat na, z nebo mezi účty úložiště. Tento článek pomůže stáhnout AzCopy, připojte se k účtu úložiště a pak přenos souborů.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 94aca33b2f12c1c39297221a856296dcca052b0f
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565799"
---
# <a name="get-started-with-azcopy"></a>Začínáme s AzCopy

AzCopy je nástroj příkazového řádku, který vám pomůže zkopírovat objekty BLOB nebo souborů do nebo z účtu úložiště. Tento článek pomůže stáhnout AzCopy, připojte se k účtu úložiště a pak přenos souborů.

> [!NOTE]
> AzCopy **V10** je aktuálně podporované verze nástroje AzCopy.
>
> Pokud je potřeba pomocí nástroje AzCopy **v8.1**, najdete v článku [v předchozí verzi AzCopy](#previous-version) části tohoto článku.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Stáhněte si nástroje AzCopy

Nejprve stáhněte spustitelný soubor AzCopy V10 do libovolného adresáře v počítači. 

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (cíl)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

> [!NOTE]
> Pokud chcete zkopírovat data do a z vašich [Azure Table storage](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) služby, potom nainstalovat [AzCopy verzi 7.3](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Spuštění nástroje AzCopy

Pro usnadnění zvažte přidání umístění adresáře ke spustitelnému souboru nástroje AzCopy do systémové cesty pro snadné použití. Tímto způsobem můžete zadat `azcopy` z libovolného adresáře v systému.

Pokud se rozhodnete nepřidávat adresář AzCopy do cesty, budete muset změňte adresáře na umístění nástroje AzCopy spustitelného souboru a typu `azcopy` nebo `.\azcopy` v prostředí Windows PowerShell příkazové řádky.

Chcete-li zobrazit seznam příkazů, zadejte `azcopy -h` a potom stiskněte klávesu ENTER.

Další informace o konkrétní příkaz, jednoduše uvést název příkazu (například: `azcopy list -h`).

![Vložená Nápověda](media/storage-use-azcopy-v10/azcopy-inline-help.png)

> [!NOTE] 
> Jako vlastník účtu služby Azure Storage nejsou automaticky přiřadit oprávnění pro přístup k datům. Předtím, než můžete dělat nic smysluplné pomocí nástroje AzCopy, musíte se rozhodnout, jak vám poskytnout přihlašovací údaje pro ověření do služby storage. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Zvolte, jak vám poskytnout přihlašovací údaje pro autorizaci

Můžete zadat přihlašovací údaje pro ověření pomocí Azure Active Directory (AD) nebo pomocí tokenu sdíleného přístupového podpisu (SAS).

Tuto tabulku použijte jako vodítko:

| Typ úložiště | Aktuálně podporované metody ověřování |
|--|--|
|**Blob Storage** | Azure AD & SAS |
|**Úložiště objektů BLOB (hierarchické oboru názvů)** | Azure AD & SAS |
|**Úložiště souborů** | Pouze SAS |

### <a name="option-1-use-azure-ad"></a>Option 1: Pomocí služby Azure AD

Pomocí služby Azure AD, můžete zadat přihlašovací údaje jednou, namísto nutnosti připojte SAS token pro každý příkaz.  

Úroveň ověřování, který je třeba vychází Určuje, zda chcete nahrát soubory nebo je právě stáhnout.

Pokud chcete stáhnout soubory, ověřte, že [čtecí modul dat pro úložiště objektů Blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) byla přiřazena identitu uživatele nebo instanční objekt. 

> [!NOTE]
> Identity uživatelů a instanční objekty jsou každého typu *objektu zabezpečení*, takže použijeme termín *objektu zabezpečení* pro zbývající část tohoto článku.

Pokud chcete nahrát soubory, ověřte, že jednu z těchto rolí se přiřadila do vašeho objektu zabezpečení:

- [Storage Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Vlastník dat úložiště objektů Blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Tyto role je možné přiřadit k vaší identity v některém z těchto oborů:

- Kontejner (systém souborů)
- Účet úložiště
- Skupina prostředků
- Předplatné

Zjistěte, jak ověřit a přiřazení rolí, najdete v článku [udělit přístup k Azure data objektů blob a fronty pomocí RBAC na webu Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE] 
> Mějte na paměti, která přiřazení rolí pro RBAC může trvat až pět minut na dokončení propagace.

Není nutné mít jednu z těchto rolí přiřadit k vaší objektu zabezpečení, pokud váš objekt zabezpečení se přidá do seznamu řízení přístupu (ACL) cílový kontejner nebo adresáře. V seznamu ACL váš objekt zabezpečení potřebuje oprávnění k zápisu na cílový adresář a oprávnění spouštět v kontejneru a každý nadřazený adresář.

Další informace najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Ověření identity uživatele

Po ověření, že vaši identitu uživatele se dostali potřebnou úroveň oprávnění, otevřete příkazový řádek, zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login
```

Pokud patříte do více než jedné organizace, zahrnují ID tenanta organizace, ke kterému patří účet úložiště.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Nahradit `<tenant-id>` zástupný prvek s ID tenanta organizace, ke kterému patří účet úložiště. Pokud chcete najít ID tenanta, vyberte **Azure Active Directory > Vlastnosti > ID adresáře** na webu Azure Portal.

Tento příkaz vrátí ověřovacího kódu a adresu URL webu. Otevřít web, zadejte kód a pak zvolte **Další** tlačítko.

![Vytvoření kontejneru](media/storage-use-azcopy-v10/azcopy-login.png)

Zobrazí se okno přihlášení. V tomto okně přihlaste ke svému účtu Azure pomocí přihlašovacích údajů k účtu Azure. Po úspěšném jste přihlášení, můžete zavřít okno prohlížeče a začít používat AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Ověřování instančního objektu

To je skvělá možnost, pokud máte v plánu pomocí nástroje AzCopy ve skriptu, který běží bez zásahu uživatele. 

Před spuštěním tohoto skriptu budete muset přihlásit interaktivně aspoň jednou tak, aby pomocí přihlašovacích údajů instančního objektu služby můžete zadat AzCopy.  Tyto přihlašovací údaje jsou uloženy v zabezpečené a šifrované souboru tak, aby váš skript nemá k poskytování těchto citlivých informací.

Tajný kód klienta nebo heslo certifikát, který je přidružený k objektu služby registrace aplikace se můžete přihlásit ke svému účtu. 

Další informace o vytvoření instančního objektu služby, najdete v článku [jak: Použití portálu k vytvoření aplikace a instančního objektu, který má přístup k prostředkům Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Další obecné informace o instančních objektech najdete v tématu [aplikace a instanční objekty v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Pomocí tajného klíče klienta

Začněte tím, že nastavení `AZCOPY_SPA_CLIENT_SECRET` proměnnou prostředí pro tajný kód klienta instančního objektu služby vaší registrace aplikace. 

> [!NOTE]
> Ujistěte se, nastavte tuto hodnotu z příkazového řádku a ne v prostředí proměnné nastavení operačního systému. Tímto způsobem, hodnota je k dispozici pouze pro aktuální relaci.

Tento příklad ukazuje, jak to může provést v prostředí PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Zvažte použití výzvu, jak je znázorněno v tomto příkladu. Tímto způsobem tajný klíč klienta nebude zobrazovat v historii příkazů v konzole. 

Dále zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login --service-principal --application-id <application-id>
```

Nahradit `<application-id>` zástupný prvek s ID aplikace instančního objektu služby vaší registrace aplikace.

##### <a name="using-a-certificate"></a>Použití certifikátu

Pokud chcete použít vlastní přihlašovací údaje pro autorizaci, můžete nahrát na server certifikát pro registraci vaší aplikace a pak používají tento certifikát se můžete přihlásit.

Kromě odeslání vašeho certifikátu registrace vaší aplikace, budete také muset mít kopii certifikát uloží do počítače nebo virtuálního počítače, ve kterém bude běžet AzCopy. Tuto kopii certifikátu musí být v. PFX nebo. PEM formátu a musí obsahovat privátní klíč. Privátní klíč by měl být chráněný heslem. Pokud používáte Windows a vaše certifikát existuje pouze v úložišti certifikátů, ujistěte se, že pro export tohoto certifikátu do souboru PFX (včetně privátního klíče). Pokyny najdete v tématu [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Dále nastavte `AZCOPY_SPA_CERT_PASSWORD` proměnnou prostředí, aby heslo certifikátu.

> [!NOTE]
> Ujistěte se, nastavte tuto hodnotu z příkazového řádku a ne v prostředí proměnné nastavení operačního systému. Tímto způsobem, hodnota je k dispozici pouze pro aktuální relaci.

Tento příklad ukazuje, jak to může provést v prostředí PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Dále zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file>
```

Nahradit `<path-to-certificate-file>` zástupný symbol relativní nebo úplnou cestu k souboru certifikátu. AzCopy uloží cestu k tomuto certifikátu, ale nebude uložení kopie certifikátu, tak zachovat tento certifikát na místě.

> [!NOTE]
> Zvažte použití výzvu, jak je znázorněno v tomto příkladu. Tímto způsobem, vaše heslo se nezobrazí v historii příkazů v konzole. 

### <a name="option-2-use-a-sas-token"></a>Option 2: Pomocí tokenu SAS

SAS token můžete připojit k každý zdroj nebo cíl URL, kterou používají vaše příkazů AzCopy.

Tento příklad příkazu rekurzivně kopíruje data z místního adresáře do kontejneru objektů blob. Fiktivní token SAS je připojen na konec objektu adresy URL kontejneru.

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Další informace o tokeny SAS a jak ho získat, najdete v článku [použití sdílených přístupových podpisů (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="transfer-files"></a>Přenos souborů

Když si ověřit vaši identitu a získat SAS token, můžete začít přenos souborů.

Příkazy v příkladu najdete v tématu některého z těchto článků.

- [Přenos dat pomocí AzCopy a blob storage](storage-use-azcopy-blobs.md)

- [Přenos dat pomocí AzCopy a file storage](storage-use-azcopy-files.md)

- [Přenos dat pomocí AzCopy a Amazon S3 intervalů](storage-use-azcopy-s3.md)

- [Přenos dat pomocí AzCopy a Azure Stackem úložiště](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Pomocí AzCopy ve skriptu

Před spuštěním tohoto skriptu budete muset přihlásit interaktivně aspoň jednou tak, aby pomocí přihlašovacích údajů instančního objektu služby můžete zadat AzCopy.  Tyto přihlašovací údaje jsou uloženy v zabezpečené a šifrované souboru tak, aby váš skript nemá k poskytování těchto citlivých informací. Příklady najdete v tématu [ověřování instančního objektu služby](#service-principal) části tohoto článku.

V průběhu času AzCopy [odkaz ke stažení](#download-and-install-azcopy) bude odkazovat na nové verze AzCopy. Pokud váš skript stáhne AzCopy, může být skript přestanou fungovat, pokud změní funkce, které váš skript, závisí na novější verzi AzCopy. 

Chcete-li těmto potížím vyhnout, získáte statické (zrušení měnící) odkaz na aktuální verzi AzCopy. Tímto způsobem váš skript stáhne přesně stejnou verzi nástroje AzCopy pokaždé, když běží.

Pokud chcete získat odkaz, spusťte tento příkaz:

| Operační systém  | Příkaz |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Pro Linux `--strip-components=1` na `tar` příkaz odebere složku nejvyšší úrovně, který obsahuje název verze a místo toho extrahuje binárního souboru přímo do aktuální složky. To umožňuje skript, který chcete aktualizovat na novou verzi `azcopy` stačí pouze aktualizovat `wget` adresy URL.

Adresa URL se zobrazí ve výstupu tohoto příkazu. Váš skript pak si můžete stáhnout nástroj AzCopy pomocí této adresy URL.

| Operační systém  | Příkaz |
|--------|-----------|
| **Linux** | `wget -O azcopyv10.tar https://azcopyvnext.azureedge.net/release20190301/azcopy_linux_amd64_10.0.8.tar.gz tar -xf azcopyv10.tar --strip-components=1 ./azcopy` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

## <a name="use-azcopy-in-storage-explorer"></a>V Průzkumníku služby Storage pomocí AzCopy

Pokud chcete využít výhody výkonu AzCopy, ale budete chtít pracovat se soubory pomocí Průzkumníka služby Storage namísto příkazového řádku, povolte AzCopy v Průzkumníku služby Storage. 

V Průzkumníku služby Storage vyberte **ve verzi Preview**->**použijte AzCopy k vylepšení nahrát objekt Blob a stažení**.

![Povolte AzCopy jako modul přenosu v Průzkumníku služby Storage](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Není nutné toto nastavení povolte, pokud jste povolili hierarchického oboru názvů na vašem účtu úložiště. Který je, protože Průzkumníka služby Storage automaticky pomocí nástroje AzCopy pro účty úložiště, které mají hierarchického oboru názvů.  

Průzkumník služby Storage používá klíč účtu k provádění operací, takže po přihlášení do Průzkumníka služby Storage, nebudete muset zadávat přihlašovací údaje pro další ověření.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>V předchozí verzi AzCopy

Pokud potřebujete předchozí verzi AzCopy (AzCopy v8.1), naleznete v některé z následujících odkazů:

- [AzCopy ve Windows (v8:)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy v Linuxu (v8:)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Konfigurace, optimalizovat a řešení potíží s AzCopy

Zobrazit [konfigurovat, optimalizovat a řešení potíží s AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Další postup

Pokud máte dotazy, problémy nebo obecnou zpětnou, odešlete je [na Githubu](https://github.com/Azure/azure-storage-azcopy) stránky.
