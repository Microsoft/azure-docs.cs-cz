---
title: 'Kurz: Kontrola dat pomocí Azure dosah (Preview)'
description: V tomto kurzu spustíte úvodní sadu, která nastaví datovou sadu a pak zkontroluje data ze zdrojů dat do vašeho katalogu Azure dosah.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 16692ac75f0ab6df0c8ee1bebef393848ca066b8
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676538"
---
# <a name="tutorial-scan-data-with-azure-purview-preview"></a>Kurz: Kontrola dat pomocí Azure dosah (Preview)

> [!IMPORTANT]
> Služba Azure dosah je aktuálně ve verzi PREVIEW. [Doplňkové podmínky použití pro](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview Microsoft Azure zahrnují další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.

V této *pěti řadě kurzů* se seznámíte se základy správy řízení dat napříč datovou částí pomocí Azure dosah (Preview). Datovou kopii, kterou vytvoříte v této části kurzu, se používá pro zbytek řady.

V části 1 této série kurzů budete:

> [!div class="checklist"]
>
> * Vytvořte datovou nemovitost s různými datovými zdroji Azure.
> * Naskenujte data do katalogu.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Účet Azure dosah](create-catalog-portal.md).
* [Startovní sada](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip) , která bude nasazovat datovou sadu.

> [!NOTE]
> Startovní sada je dostupná jenom pro Windows.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="create-a-data-estate"></a>Vytvořit datovou nemovitost

V této části spustíte skripty startovní sady, abyste vytvořili simulovanou datovou nemovitost. Datová nemovitost je portfolio všech dat, která vlastní společnost. Skript startovní sady provede následující akce:

* Vytvoří účet úložiště objektů BLOB v Azure a naplní účet daty.
* Vytvoří účet Azure Data Lake Storage Gen2.
* Vytvoří instanci Azure Data Factory a přidruží ji k Azure dosah.
* Nastaví a aktivuje kanál aktivity kopírování mezi účty Azure Blob Storage a Azure Data Lake Storage Gen2.
* Nabídne přidružený Azure Data Factory k Azure dosah.

### <a name="prepare-to-run-the-starter-kit"></a>Příprava na spuštění Úvodní sady

Pomocí těchto kroků nastavíte klientský software úvodní sady na počítači s Windows:

1. [Stáhněte si Startovní sadu](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip)a extrahujte její obsah do umístění dle vašeho výběru.


1. V počítači zadejte do vyhledávacího pole na hlavním panelu Windows **PowerShell** . V seznamu hledání klikněte pravým tlačítkem myši na **Windows PowerShell** a vyberte **Spustit jako správce**.

1. V okně PowerShellu zadejte následující příkaz a nahraďte `<path-to-starter-kit>` cestu ke složce extrahovaných souborů počáteční sady.

   ```powershell
   dir -Path <path-to-starter-kit> -Recurse | Unblock-File
   ```

1. Zadejte následující příkaz, který nainstaluje rutiny Azure.

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```

1. Pokud se zobrazí výzva s upozorněním, *poskytovatel NuGet je nutný k pokračování*, zadejte **Y** a stiskněte ENTER.

1. Pokud se zobrazí výzva s upozorněním, *nedůvěryhodné úložiště* **, zadejte a a potom** stiskněte klávesu ENTER.

Může trvat až minutu, než PowerShell nainstaluje požadované moduly.

### <a name="collect-data-needed-to-run-the-scripts"></a>Shromažďování dat potřebných ke spouštění skriptů

Před spuštěním skriptů PowerShellu pro zavedení katalogu Získejte hodnoty následujících argumentů, které se použijí ve skriptech:

* TenantID
   1. Na portálu [Azure Portal](https://portal.azure.com) vyberte **Azure Active Directory**.
   1. V části **Spravovat** v levém navigačním podokně vyberte možnost **vlastnosti**. Pak vyberte ikonu kopírování pro **ID tenanta** a uložte hodnotu do schránky. Vložte hodnotu v textovém editoru pro pozdější použití.

* SubscriptionID
   1. V Azure Portal vyhledejte a vyberte název instance Azure dosah, kterou jste vytvořili jako požadavek.
   1. Vyberte část **Přehled** a uložte identifikátor GUID pro **ID předplatného**.

   > [!NOTE]
   > - Ujistěte se, že používáte stejné předplatné jako ten, ve kterém jste vytvořili účet Azure dosah. Toto je stejné předplatné, které bylo umístěno v seznamu povolených.
   > - Po spuštění startovní sady by v Azure dosah mohla chybět čára. Důvodem je to, že Data Factory vytvořená sadou Starter má v dosah chybějící oprávnění. Vyberte [**odkaz na tento dokument**](how-to-link-azure-data-factory.md#view-existing-data-factory-connections)  , abyste se ujistili, že je Data Factory nakonfigurovaná správná a přiřazená příslušná role v dosah.


* Název_katalogu: název účtu Azure dosah, který jste vytvořili v části [Vytvoření účtu Azure dosah](create-catalog-portal.md).

* CatalogResourceGroupName: název skupiny prostředků, ve které jste vytvořili účet Azure dosah.

### <a name="verify-permissions"></a>Ověření oprávnění

Pomocí těchto kroků přidáte uživatele, který spouští skript, do účtu Azure dosah, který byl vytvořen jako požadavek. Uživatelé potřebují role správce dat *dosah data kurátor* a *dosah* . 

Pokud jste účet Azure dosah vytvořili sami, automaticky se vám dává přístup a tato část může přeskočit.

1. Na Azure Portal na stránce účty služby dosah a vyberte účet Azure dosah, který chcete upravit.

1. Na stránce účtu vyberte kartu **řízení přístupu (IAM)** a **+ Přidat**.

1. Vyberte **Přidat přiřazení role**.

1. Jako *roli* zadejte **dosah data kurátor role** .
 
1. Pro *přiřazení přístupu k* použijte výchozí hodnotu. Výchozí hodnota by měla být **User, Group nebo instanční objekt**.

1. Do pole **Vybrat** zadejte jméno uživatele, který spouští skript.

1. Vyberte **Uložit**.

1. Opakujte předchozí postup s *rolí* nastavenou na **dosah role správce zdrojů dat**.

Další informace najdete v tématu [oprávnění katalogu](catalog-permissions.md).

### <a name="run-the-client-side-setup-scripts"></a>Spuštění skriptů pro instalaci na straně klienta

Po dokončení konfigurace katalogu spusťte v okně PowerShellu následující skripty, abyste vytvořili assety a nahradili zástupné hodnoty hodnotami, které jste předtím shromáždili.

1. Pomocí následujícího příkazu přejděte do adresáře úvodní sady. Nahraďte `path-to-starter-kit` cestou složky extrahované souboru.

   ```powershell
   cd <path-to-starter-kit>
   ```

1. Následující příkaz nastaví zásady spouštění pro místní počítač. Pokud se zobrazí výzva ke změně zásad spouštění **, zadejte** *Ano všem* .

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Připojte se k Azure pomocí následujícího příkazu. Nahraďte zástupné symboly `TenantID` a `SubscriptionID`.

   ```powershell
   .\RunStarterKit.ps1 -ConnectToAzure -TenantId <TenantID> `
   -SubscriptionId <SubscriptionID>
   ```

   Když příkaz spustíte, zobrazí se automaticky otevírané okno, abyste se mohli přihlásit pomocí přihlašovacích údajů Azure Active Directory.


1. K spuštění Úvodní sady použijte následující příkaz. Nahraďte `CatalogName` `TenantID` `SubscriptionID` `NewResourceGroupName` `CatalogResourceGroupName` zástupné symboly,,, a. Pro `NewResourceGroupName` použijte jedinečný název (jenom s malými alfanumerickými znaky) pro skupinu prostředků, která bude obsahovat datovou nemovitost.

   > [!IMPORTANT]
   > **Newresourcegroupname** používají pouze čísla a malá písmena a musí být kratší než 17 znaků. **Neexistují velká písmena velkých a malých písmen a speciální znaky jsou povoleny.** Toto omezení pochází z pravidel pojmenování účtů úložiště.

   ```powershell
   .\RunStarterKit.ps1 -CatalogName <CatalogName> -TenantId <TenantID>`
   -ResourceGroup <newresourcegroupname> `
   -SubscriptionId <SubscriptionID> `
   -CatalogResourceGroup <CatalogResourceGroupName>
   ```

Nastavení prostředí může trvat až 10 minut. Během této doby se můžou zobrazit různá překryvná okna, která můžete ignorovat. Nezavírejte okno **BlobDataCreator.exe** ; Po dokončení se automaticky zavře.

Až se zobrazí zpráva `Executing Copy pipeline xxxxxxxxxx-487e-4fc4-9628-92dd8c2c732b` , počkejte na spuštění a dokončení jiné instance **BlobDataCreator.exe** .

> [!IMPORTANT]
> V případě, že počet aktivních úloh přestane být klesající, můžete zavřít okno Tvůrce objektů BLOB a přejít do okna PowerShellu.

Po dokončení procesu se vytvoří skupina prostředků s názvem, který jste zadali. Účty Azure Data Factory, Azure Blob Storage a Azure Data Lake Storage Gen2 jsou všechny v této skupině prostředků. Skupina prostředků je obsažená v předplatném, které jste zadali.

## <a name="scan-data-into-the-catalog"></a>Kontrola dat v katalogu

Kontrola je proces, při kterém se katalog připojuje přímo ke zdroji dat podle uživatelem zadaného plánu. Katalog odráží firemní data prostřednictvím kontroly, zákroku, portálu a rozhraní API. Mezi cíle patří zkoumání toho, co je uvnitř, extrakce schémat a pokus o pochopení sémantiky. V této části nastavíte kontrolu datových zdrojů, které jste vygenerovali pomocí úvodní sady.

Skript startovní sady, který jste spustili, vytvořili dva zdroje dat, Azure Blob Storage a Azure Data Lake Storage Gen2. Tyto zdroje dat můžete naskenovat do katalogu v jednom okamžiku.

### <a name="authenticate-to-your-storage-with-managed-identity"></a>Ověření ve vašem úložišti pomocí spravované identity

Při vytvoření účtu se automaticky vytvoří spravovaná identita se stejným názvem jako účet Azure dosah. Předtím, než budete moct kontrolovat data, je potřeba udělit oprávnění role Azure dosah k účtům úložiště.

1. Přejděte do skupiny prostředků vytvořené úvodní sadou a vyberte svůj účet úložiště objektů BLOB.

1. V navigační nabídce vlevo vyberte **Access Control (IAM)** . Pak vyberte **+ Přidat**.

1. Nastavte roli na **čtečku dat objektů BLOB úložiště** a zadejte název svého účtu Azure dosah pro **možnost vybrat**. Pak vyberte **Uložit** a udělte tomuto přiřazení role vašemu účtu dosah.

   :::image type="content" source="media/tutorial-scan-data/add-role-assignment.png" alt-text="Přidat přiřazení role":::

1. Opakujte předchozí kroky pro Azure Data Lake Storage Gen2.

### <a name="scan-your-data-sources"></a>Kontrola zdrojů dat

1. V [Azure Portal](https://portal.azure.com) přejděte do svého prostředku Azure dosah a vyberte *otevřít dosah Studio*. Automaticky přejdete na domovskou stránku aplikace dosah Studio.

1. Na webové stránce vašeho katalogu vyberte **zdroje** a vyberte **zaregistrovat**. Pak vyberte **Azure Blob Storage** a **pokračujte**.

   :::image type="content" source="media/tutorial-scan-data/add-blob-storage.png" alt-text="Registrovat prostředek úložiště objektů BLOB":::

1. Na stránce **Registrovat zdroje** zadejte **název**. Vyberte **název účtu úložiště** účtu služby Azure Blob Storage, který jste dříve vytvořili pomocí Startovní sady. Účet má název `<YourResourceGroupName>adcblob` . Vyberte **Dokončit**.

   :::image type="content" source="./media/tutorial-scan-data/register-azure-blob-storage.png" alt-text="Snímek obrazovky s nastavením pro registraci zdroje dat služby Azure Blob Storage" border="true":::

1. V zobrazení mapa **zdrojů dat** vyberte na dlaždici BLOB Storage Azure možnost **Nová kontrola** .

   :::image type="content" source="./media/tutorial-scan-data/select-setup-scan.png" alt-text="Snímek obrazovky ukazující, jak vybrat nastavení kontroly ze zdroje dat" border="true":::

1. Na stránce **prohledávání** zadejte název kontroly, v rozevíracím seznamu **pověření** vyberte spravovanou identitu a **pokračujte**.

   :::image type="content" source="media/tutorial-scan-data/scan-blob.png" alt-text="Kontrola úložiště objektů BLOB v Azure dosah":::

1. Můžete určit rozsah kontroly na jednotlivé objekty blob. V tomto kurzu ponechte všechny prostředky zaškrtnuté, aby kontrolovaly vše a **pokračovalo**.

   :::image type="content" source="media/tutorial-scan-data/scope-your-scan.png" alt-text="Určení rozsahu kontroly úložiště":::

1. Vyberte výchozí sadu pravidel skenování a **pokračujte**.

1. Můžete nastavit aktivační událost kontroly pro opakující se kontroly. Pro tento kurz vyberte **jednou** a **pokračujte**.

1. Dokončete kontrolu výběrem **Uložit a spustit** .

1. Zopakováním předchozích kroků prohledejte účet Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:
> [!div class="checklist"]
>
> * Spusťte skript startovní sady a nastavte prostředí pro datovou podstatu.
> * Naskenujte data do katalogu Azure dosah.

Přejděte k dalšímu kurzu, kde se dozvíte, jak přejít na domovskou stránku a vyhledat Asset.

> [!div class="nextstepaction"]
> [Navigace na domovské stránce a vyhledání assetu](tutorial-asset-search.md)
