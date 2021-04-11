---
title: Autorizace přístupu k objektům blob pomocí AzCopy & Azure Active Directory | Microsoft Docs
description: Přihlašovací údaje autorizace pro operace AzCopy můžete zadat pomocí Azure Active Directory (Azure AD).
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/01/2021
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: dd3aeaf133c02ef54eceaff776ead34cc2318260
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220473"
---
# <a name="authorize-access-to-blobs-with-azcopy-and-azure-active-directory-azure-ad"></a>Autorizace přístupu k objektům blob pomocí AzCopy a Azure Active Directory (Azure AD)

Můžete zadat AzCopy s přihlašovacími údaji k autorizaci pomocí Azure AD. Tímto způsobem nebudete muset ke každému příkazu připojit token sdíleného přístupového podpisu (SAS). 

Začněte tím, že ověříte přiřazení rolí. Pak zvolte typ _objektu zabezpečení_ , který chcete autorizovat. [Identita uživatele](../../active-directory/fundamentals/add-users-azure-active-directory.md), [spravovaná identita](../../active-directory/managed-identities-azure-resources/overview.md)a [instanční objekt](../../active-directory/develop/app-objects-and-service-principals.md) jsou typu objektu zabezpečení.

Identita uživatele je libovolný uživatel, který má v Azure AD identitu. Je nejjednodušším objektem zabezpečení k autorizaci. Spravované identity a instanční objekty jsou skvělé možnosti, pokud plánujete používat AzCopy uvnitř skriptu, který běží bez zásahu uživatele. Spravovaná identita je lépe vhodná pro skripty, které běží na virtuálním počítači Azure a instanční objekt je lépe vhodný pro skripty, které běží místně. 

Další informace o AzCopy najdete v [Seznámení s AzCopy](storage-use-azcopy-v10.md).

## <a name="verify-role-assignments"></a>Ověření přiřazení rolí

Úroveň autorizace, kterou potřebujete, je založená na tom, jestli plánujete odeslat soubory, nebo je stačí stáhnout.

Pokud chcete pouze stahovat soubory, ověřte, zda byla role [čtečky dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) přiřazena identitě uživatele, spravované identitě nebo instančnímu objektu.

Chcete-li odeslat soubory, ověřte, zda byla k objektu zabezpečení přiřazena jedna z těchto rolí:

- [Přispěvatel dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [Vlastník dat v objektech blob služby Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

Tyto role se dají přiřadit k objektu zabezpečení v libovolném z těchto oborů:

- Kontejner (systém souborů)
- Účet úložiště
- Skupina prostředků
- Předplatné

Informace o tom, jak ověřit a přiřadit role, najdete v tématu [použití Azure Portal k přiřazení role Azure pro přístup k datům objektů BLOB a front](./storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Mějte na paměti, že rozšíření přiřazení rolí Azure může trvat až pět minut.

Pokud je do seznamu řízení přístupu (ACL) cílového kontejneru nebo adresáře přidaný objekt zabezpečení, nemusíte mít přiřazenou žádnou z těchto rolí. V seznamu ACL vyžaduje váš instanční objekt oprávnění k zápisu do cílového adresáře a oprávnění ke spuštění na kontejneru a v jednotlivých nadřazených adresářích.

Další informace najdete v tématu [model řízení přístupu v Azure Data Lake Storage Gen2](../blobs/data-lake-storage-access-control-model.md).

## <a name="authorize-a-user-identity"></a>Autorizovat identitu uživatele

Po ověření, že byla identitě uživatele přidělena potřebná úroveň autorizace, otevřete příkazový řádek, zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login
```

Pokud se zobrazí chyba, zkuste zahrnout ID tenanta organizace, ke které patří účet úložiště.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

`<tenant-id>`Zástupný symbol nahraďte ID tenanta organizace, které patří k účtu úložiště. ID tenanta zjistíte tak, že v Azure Portal vyberete **Azure Active Directory > vlastnosti > ID adresáře** .

Tento příkaz vrátí ověřovací kód a adresu URL webu. Otevřete web, zadejte kód a potom klikněte na tlačítko **Další** .

![Vytvoření kontejneru](media/storage-use-azcopy-v10/azcopy-login.png)

Zobrazí se okno přihlášení. V tomto okně se přihlaste k účtu Azure pomocí svých přihlašovacích údajů k účtu Azure. Po úspěšném přihlášení můžete zavřít okno prohlížeče a začít používat AzCopy.

<a id="managed-identity"></a>

## <a name="authorize-a-managed-identity"></a>Autorizace spravované identity

To je skvělý způsob, pokud plánujete používat AzCopy uvnitř skriptu, který se spouští bez zásahu uživatele, a skript se spouští z virtuálního počítače Azure (VM). Když použijete tuto možnost, nebudete muset na virtuální počítač ukládat žádné přihlašovací údaje.

Ke svému účtu se můžete přihlásit pomocí spravované identity v rámci systému, kterou jste povolili na svém VIRTUÁLNÍm počítači, nebo pomocí ID klienta, ID objektu nebo ID prostředku spravované identity přiřazené uživatelem, kterou jste přiřadili k VIRTUÁLNÍmu počítači.

Další informace o tom, jak povolit spravovanou identitu v rámci systému nebo vytvořit uživatelem přiřazenou identitu, najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

#### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Autorizovat pomocí spravované identity v rámci systému

Nejdřív se ujistěte, že máte na svém VIRTUÁLNÍm počítači povolenou spravovanou identitu na úrovni systému. Viz [spravovaná identita přiřazená systémem](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Pak v konzole příkazů zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login --identity
```

#### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Autorizovat pomocí spravované identity přiřazené uživatelem

Nejdřív se ujistěte, že jste na svém VIRTUÁLNÍm počítači povolili spravovanou identitu přiřazenou uživatelem. Podívejte se [na spravovanou identitu přiřazenou uživatelem](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Pak v konzole příkazů zadejte libovolný z následujících příkazů a potom stiskněte klávesu ENTER.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

`<client-id>`Zástupný symbol nahraďte ID klienta spravované identity přiřazené uživatelem.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

`<object-id>`Zástupný symbol nahraďte ID objektu spravované identity přiřazené uživatelem.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

`<resource-id>`Zástupný symbol nahraďte ID prostředku spravované identity přiřazené uživatelem.

<a id="service-principal"></a>

## <a name="authorize-a-service-principal"></a>Autorizovat instanční objekt

Tato možnost je skvělá, pokud plánujete používat AzCopy uvnitř skriptu, který běží bez zásahu uživatele, zejména při místním spuštění. Pokud máte v úmyslu spustit AzCopy na virtuálních počítačích, které běží v Azure, je snazší spravovat identitu spravované služby. Další informace najdete v části [autorizace spravované identity](#authorize-a-managed-identity) v tomto článku.

Před spuštěním skriptu se musíte interaktivně přihlásit aspoň jednou, abyste AzCopy mohli zadat přihlašovací údaje k instančnímu objektu.  Tyto přihlašovací údaje jsou uložené v zabezpečeném a zašifrovaném souboru, aby váš skript nemusel poskytovat citlivé informace.

K účtu se můžete přihlásit pomocí tajného klíče klienta nebo pomocí hesla certifikátu, který je přidružený k registraci aplikace vašeho objektu služby.

Další informace o vytváření instančního objektu najdete v tématu [Postupy: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](../../active-directory/develop/howto-create-service-principal-portal.md).

Další informace o hlavních instančních objektech naleznete v tématu [Application and Service Principal Objects in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md)

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>Autorizace instančního objektu pomocí tajného klíče klienta

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
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

`<application-id>`Zástupný symbol nahraďte ID aplikace registrace aplikace vašeho objektu služby. `<tenant-id>`Zástupný symbol nahraďte ID tenanta organizace, které patří k účtu úložiště. ID tenanta zjistíte tak, že v Azure Portal vyberete **Azure Active Directory > vlastnosti > ID adresáře** . 

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Autorizace instančního objektu pomocí certifikátu

Pokud dáváte přednost používání vlastních přihlašovacích údajů pro autorizaci, můžete k registraci aplikace nahrát certifikát a potom k přihlášení použít tento certifikát.

Kromě odeslání certifikátu do registrace aplikace budete také muset mít uloženou kopii certifikátu uloženého na počítači nebo virtuální počítač, kde bude AzCopy běžet. Tato kopie certifikátu by měla být v. PFX nebo. Formát PEM a musí obsahovat privátní klíč. Privátní klíč by měl být chráněn heslem. Pokud používáte systém Windows a váš certifikát existuje pouze v úložišti certifikátů, nezapomeňte tento certifikát exportovat do souboru PFX (včetně privátního klíče). Pokyny najdete v tématu [Export-vybíráte](/powershell/module/pkiclient/export-pfxcertificate) .

Dále nastavte `AZCOPY_SPA_CERT_PASSWORD` proměnnou prostředí na heslo certifikátu.

> [!NOTE]
> Ujistěte se, že jste tuto hodnotu nastavili z příkazového řádku, a ne do nastavení proměnných prostředí operačního systému. Tímto způsobem je hodnota k dispozici pouze pro aktuální relaci.

Tento příklad ukazuje, jak můžete tuto úlohu provést v prostředí PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Potom zadejte následující příkaz a stiskněte klávesu ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

`<path-to-certificate-file>`Zástupný text nahraďte relativní nebo úplnou cestou k souboru certifikátu. AzCopy uloží cestu k tomuto certifikátu, ale neuloží kopii certifikátu, proto nezapomeňte tento certifikát ponechat na místě. `<tenant-id>`Zástupný symbol nahraďte ID tenanta organizace, které patří k účtu úložiště. ID tenanta zjistíte tak, že v Azure Portal vyberete **Azure Active Directory > vlastnosti > ID adresáře** .

> [!NOTE]
> Zvažte použití výzvy, jak je znázorněno v tomto příkladu. Tímto způsobem se vaše heslo nezobrazí v historii příkazů vaší konzole. 

## <a name="authorize-without-a-secret-store"></a>Autorizovat bez tajného úložiště

`azcopy login`Příkaz načte token OAuth a pak umístí tento token do úložiště tajného kódu ve vašem systému. Pokud váš operační systém nemá úložiště tajného klíče, jako je například *Správce klíčů* Linux, `azcopy login` příkaz nebude fungovat, protože je nikde k umístění tokenu. 

Místo použití `azcopy login` příkazu můžete nastavit proměnné prostředí v paměti. Pak spusťte libovolný příkaz AzCopy. AzCopy načte token ověřování potřebný k dokončení operace. Po dokončení operace token zmizí z paměti. 

### <a name="authorize-a-user-identity"></a>Autorizovat identitu uživatele

Po ověření, že byla identitě uživatele přidělena potřebná úroveň autorizace, zadejte následující příkaz a stiskněte klávesu ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=DEVICE
```

Pak spusťte libovolný příkaz AzCopy (například: `azcopy list https://contoso.blob.core.windows.net` ).

Tento příkaz vrátí ověřovací kód a adresu URL webu. Otevřete web, zadejte kód a potom klikněte na tlačítko **Další** .

![Vytvoření kontejneru](media/storage-use-azcopy-v10/azcopy-login.png)

Zobrazí se okno přihlášení. V tomto okně se přihlaste k účtu Azure pomocí svých přihlašovacích údajů k účtu Azure. Po úspěšném přihlášení se operace může dokončit.

### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Autorizovat pomocí spravované identity v rámci systému

Nejdřív se ujistěte, že máte na svém VIRTUÁLNÍm počítači povolenou spravovanou identitu na úrovni systému. Viz [spravovaná identita přiřazená systémem](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Zadejte následující příkaz a stiskněte klávesu ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

Pak spusťte libovolný příkaz AzCopy (například: `azcopy list https://contoso.blob.core.windows.net` ).

### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Autorizovat pomocí spravované identity přiřazené uživatelem

Nejdřív se ujistěte, že jste na svém VIRTUÁLNÍm počítači povolili spravovanou identitu přiřazenou uživatelem. Podívejte se [na spravovanou identitu přiřazenou uživatelem](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Zadejte následující příkaz a stiskněte klávesu ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

Pak zadejte libovolný z následujících příkazů a potom stiskněte klávesu ENTER.

```bash
export AZCOPY_MSI_CLIENT_ID=<client-id>
```

`<client-id>`Zástupný symbol nahraďte ID klienta spravované identity přiřazené uživatelem.

```bash
export AZCOPY_MSI_OBJECT_ID=<object-id>
```

`<object-id>`Zástupný symbol nahraďte ID objektu spravované identity přiřazené uživatelem.

```bash
export AZCOPY_MSI_RESOURCE_STRING=<resource-id>
```

`<resource-id>`Zástupný symbol nahraďte ID prostředku spravované identity přiřazené uživatelem.

Po nastavení těchto proměnných můžete spustit libovolný AzCopy příkaz (například: `azcopy list https://contoso.blob.core.windows.net` ).

### <a name="authorize-a-service-principal"></a>Autorizovat instanční objekt

K účtu se můžete přihlásit pomocí tajného klíče klienta nebo pomocí hesla certifikátu, který je přidružený k registraci aplikace vašeho objektu služby.

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>Autorizace instančního objektu pomocí tajného klíče klienta

Zadejte následující příkaz a stiskněte klávesu ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_APPLICATION_ID=<application-id>
export AZCOPY_SPA_CLIENT_SECRET=<client-secret>
export AZCOPY_TENANT_ID=<tenant-id>
```

`<application-id>`Zástupný symbol nahraďte ID aplikace registrace aplikace vašeho objektu služby. `<client-secret>`Zástupný symbol nahraďte tajným klíčem klienta. `<tenant-id>`Zástupný symbol nahraďte ID tenanta organizace, které patří k účtu úložiště. ID tenanta zjistíte tak, že v Azure Portal vyberete **Azure Active Directory > vlastnosti > ID adresáře** . 

> [!NOTE]
> Zvažte použití výzvy ke shromáždění hesla od uživatele. Tímto způsobem se vaše heslo nezobrazí v historii příkazů. 

Pak spusťte libovolný příkaz AzCopy (například: `azcopy list https://contoso.blob.core.windows.net` ).

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Autorizace instančního objektu pomocí certifikátu

Pokud dáváte přednost používání vlastních přihlašovacích údajů pro autorizaci, můžete k registraci aplikace nahrát certifikát a potom k přihlášení použít tento certifikát.

Kromě odeslání certifikátu do registrace aplikace budete také muset mít uloženou kopii certifikátu uloženého na počítači nebo virtuální počítač, kde bude AzCopy běžet. Tato kopie certifikátu by měla být v. PFX nebo. Formát PEM a musí obsahovat privátní klíč. Privátní klíč by měl být chráněn heslem. 

Zadejte následující příkaz a stiskněte klávesu ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_CERT_PATH=<path-to-certificate-file>
export AZCOPY_SPA_CERT_PASSWORD=<certificate-password>
export AZCOPY_TENANT_ID=<tenant-id>
```

`<path-to-certificate-file>`Zástupný text nahraďte relativní nebo úplnou cestou k souboru certifikátu. AzCopy uloží cestu k tomuto certifikátu, ale neuloží kopii certifikátu, proto nezapomeňte tento certifikát ponechat na místě. `<certificate-password>`Zástupný symbol nahraďte heslem certifikátu. `<tenant-id>`Zástupný symbol nahraďte ID tenanta organizace, které patří k účtu úložiště. ID tenanta zjistíte tak, že v Azure Portal vyberete **Azure Active Directory > vlastnosti > ID adresáře** . 

> [!NOTE]
> Zvažte použití výzvy ke shromáždění hesla od uživatele. Tímto způsobem se vaše heslo nezobrazí v historii příkazů. 

Pak spusťte libovolný příkaz AzCopy (například: `azcopy list https://contoso.blob.core.windows.net` ).

## <a name="next-steps"></a>Další kroky

- Další informace o AzCopy najdete v [Seznámení s AzCopy](storage-use-azcopy-v10.md)

- Pokud máte dotazy, problémy nebo obecné názory, odešlete je [na stránku GitHub](https://github.com/Azure/azure-storage-azcopy) .