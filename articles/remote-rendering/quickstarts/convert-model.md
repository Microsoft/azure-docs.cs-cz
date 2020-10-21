---
title: Převod modelu
description: Rychlý Start, který ukazuje kroky převodu pro vlastní model.
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: b2a15bcc9d9dce922470031fd07b66cf9899f0b3
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281358"
---
# <a name="quickstart-convert-a-model-for-rendering"></a>Rychlý start: Převod modelu pro vykreslování

V [rychlém startu: vykreslit model pomocí Unity](render-model.md), zjistili jste, jak pomocí ukázkového projektu Unity vykreslit vestavěný model. Tato příručka ukazuje, jak převést vlastní modely.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
>
> * Nastavení účtu Azure Blob Storage pro vstup a výstup
> * Nahrání a převod 3D model pro použití s Azure Remote rendering
> * Zahrnout převedený 3D model v aplikaci pro vykreslování

## <a name="prerequisites"></a>Předpoklady

* Kompletní [rychlé zprovoznění: vykreslení modelu pomocí Unity](render-model.md)
* Pro převod pomocí skriptu PowerShellu nainstalujte Azure PowerShell [(dokumentace)](/powershell/azure/) .
  * Otevření PowerShellu s právy správce
  * Spouštěl `Install-Module -Name Az -AllowClobber`

## <a name="overview"></a>Přehled

Zobrazovací jednotka na serveru nemůže pracovat přímo se zdrojovými formáty zdrojového modelu, jako je například FBX nebo GLTF. Místo toho vyžaduje, aby byl model ve speciálním binárním formátu.
Převodní služba spotřebovává modely z Azure Blob Storage a zapisuje převedené modely zpátky do poskytnutého kontejneru úložiště objektů BLOB v Azure.

Budete potřebovat:

* Předplatné Azure
* Účet StorageV2 v předplatném
* Kontejner úložiště objektů BLOB pro vstupní model
* Kontejner úložiště objektů BLOB pro vaše výstupní data
* Model, který se má převést, najdete v tématu [ukázkové modely](../samples/sample-model.md) .
  * Zobrazit seznam [podporovaných formátů zdroje](../how-tos/conversion/model-conversion.md#supported-source-formats)
  * Chcete-li použít vzorový skript pro převod, ujistěte se, že jste připravili vstupní složku, která obsahuje model a všechny externí závislosti (například externí textury nebo geometrie).

## <a name="azure-setup"></a>Nastavení Azure

Pokud ještě nemáte účet, přejděte na [https://azure.microsoft.com/get-started/](https://azure.microsoft.com/get-started/) , klikněte na možnost bezplatný účet a postupujte podle pokynů.

Jakmile budete mít účet Azure, pokračujte na [https://ms.portal.azure.com/#home](https://ms.portal.azure.com/#home) .

### <a name="storage-account-creation"></a>Vytvoření účtu úložiště

Pokud chcete vytvořit úložiště objektů blob, budete nejdřív potřebovat účet úložiště.
Pokud ho chcete vytvořit, klikněte na tlačítko vytvořit prostředek:

![Azure – přidat prostředek](media/azure-add-a-resource.png)

Na nové obrazovce zvolte na levé straně **úložiště** a potom **účet úložiště – objekt blob, soubor, tabulka, fronta** z dalšího sloupce:

![Azure – přidání úložiště](media/azure-add-storage.png)

Po kliknutí na toto tlačítko se zobrazí následující obrazovka s vlastnostmi úložiště, které se mají vyplnit:

![Nastavení Azure](media/azure-setup1.png)

Vyplňte formulář následujícím způsobem:

* Na odkaz pod rozevíracím seznamem vytvořte novou skupinu prostředků a pojmenujte ji **ARR_Tutorial**
* Do pole **název účtu úložiště**zadejte jedinečný název. **Tento název musí být globálně jedinečný**. v opačném případě se zobrazí výzva s dotazem, že název je již obsazen. V rámci tohoto rychlého startu pojmenujte IT **arrtutorialstorage**. V tomto rychlém startu je tedy budete muset nahradit vaším jménem pro všechny výskyty.
* Vyberte **umístění** , které je blízko vás. V ideálním případě používejte stejné umístění, které se používá k nastavení vykreslování v druhém rychlém startu.
* **Výkon** je nastavený na standardní.
* **Typ účtu** je nastavený na StorageV2 (obecné účely v2).
* **Replikace** je nastavená na geograficky redundantní úložiště s přístupem pro čtení (RA-GRS).
* **Úroveň přístupu** nastavená na Hot

Žádná z vlastností na jiných kartách se nesmí měnit, takže můžete pokračovat pomocí **příkazu "zkontrolovat + vytvořit"** a potom postupovat podle pokynů k dokončení instalace.

Web teď informuje o průběhu nasazení a sestav "vaše nasazení je hotové". V dalších krocích klikněte na tlačítko **Přejít ke zdroji** :

![Vytváření Azure Storage dokončeno](./media/storage-creation-complete.png)

### <a name="blob-storage-creation"></a>Vytváření úložiště objektů BLOB

Dál potřebujeme dva kontejnery objektů blob, jednu pro vstup a jednu pro výstup.

Na výše uvedeném tlačítku **Přejít k prostředku** se dostanete na stránku s panelem vlevo, který obsahuje nabídku seznam. V tomto seznamu pod kategorií **"BLOB Service"** klikněte na tlačítko **kontejnery** :

![Azure – přidat kontejnery](./media/azure-add-containers.png)

Stisknutím tlačítka **+ kontejner** vytvořte **vstupní** kontejner úložiště objektů BLOB.
Při vytváření použijte následující nastavení:
  
* Název = arrinput
* Úroveň veřejného přístupu = soukromá

Po vytvoření kontejneru znovu klikněte na **+ kontejner** a opakujte akci s těmito nastaveními pro **výstupní** kontejner:

* Název = arroutput
* Úroveň veřejného přístupu = soukromá

Teď byste měli mít dva kontejnery úložiště objektů BLOB:

![Instalace Blob Storage](./media/blob-setup.png)

## <a name="run-the-conversion"></a>Spuštění převodu

Existují tři různé způsoby, jak aktivovat převod modelu:

### <a name="1-conversion-via-the-arrt-tool"></a>1. převod pomocí nástroje ARRT

K zahájení převodu a interakci s vykresleným výsledkem je k dispozici [nástroj založený na uživatelském rozhraní s názvem ARRT](./../samples/azure-remote-rendering-asset-tool.md) .
![ARRT](./../samples/media/azure-remote-rendering-asset-tool.png "Snímek obrazovky ARRT")

### <a name="2-conversion-via-a-powershell-script"></a>2. převod pomocí skriptu PowerShellu

Aby bylo snazší volat službu převodu assetů, poskytujeme skript nástrojů. Je umístěný ve složce *Scripts* a nazývá se **Conversion.ps1**.

Konkrétně tento skript

1. nahraje všechny soubory v daném adresáři z místního disku do vstupního kontejneru úložiště.
1. volá [REST API převodu assetu](../how-tos/conversion/conversion-rest-api.md), která načte data z kontejneru vstupního úložiště a spustí převod, který vrátí ID převodu.
1. cyklické dotazování na rozhraní API stavu převodu s načteným ID převodu, dokud se proces převodu neukončí s úspěchem nebo selháním
1. Načte odkaz na převedený prostředek ve výstupním úložišti.

Skript načte svou konfiguraci ze souboru *Scripts\arrconfig.jsv*. Otevřete tento soubor JSON v textovém editoru.

```json
{
    "accountSettings": {
        "arrAccountId": "8*******-****-****-****-*********d7e",
        "arrAccountKey": "R***************************************l04=",
        "region": "<your-region>"
    },
    "renderingSessionSettings": {
        "vmSize": "standard",
        "maxLeaseTime": "1:00:00"
    },
    "assetConversionSettings": {
        "localAssetDirectoryPath": "D:\\tmp\\robot",
        "resourceGroup": "ARR_Tutorial",
        "storageAccountName": "arrexamplestorage",
        "blobInputContainerName": "arrinput",
        "inputFolderPath": "robotConversion",
        "inputAssetPath": "robot.fbx",
        "blobOutputContainerName": "arroutput",
        "outputFolderPath":"converted/robot",
        "outputAssetFileName": "robot.arrAsset"
    }
}
```

Konfigurace v rámci skupiny **accountSettings** (ID účtu a klíč) by měla být vyplněna analogkou k přihlašovacím údajům v [modelu vykreslit model pomocí služby Unity pro rychlý Start](render-model.md).

Ve skupině **assetConversionSettings** se ujistěte, že měníte skupinu **Resources**, **blobInputContainerName**a **blobOutputContainerName** , jak vidíte výše.
Všimněte si, že hodnota **arrtutorialstorage** musí být nahrazena jedinečným názvem, který jste vybrali během vytváření účtu úložiště.

Změňte **localAssetDirectoryPath** tak, aby odkazoval na adresář na disku, který obsahuje model, který chcete převést. Pozor na správné řídicí lomítka (" \\ ") v cestě pomocí dvojitých zpětných lomítek (" \\ \\ ").

Všechna data z cesty zadané v **localAssetDirectoryPath** se nahrají do kontejneru objektů BLOB **blobInputContainerName** pod podcestou určenou **inputFolderPath**. Proto se v příkladu konfigurace nad rámec obsahu adresáře "D: \\ TMP \\ robot" nahraje do kontejneru objektů blob "arrinput" účtu úložiště "arrtutorialstorage" v cestě "robotConversion". Již existující soubory budou přepsány.

Změňte **inputAssetPath** na cestu k modelu, který se má převést – cesta je relativní k localAssetDirectoryPath. Jako oddělovač cest použijte "/" místo " \\ ". Takže pro soubor robot. FBX, který se nachází přímo v "D: \\ TMP \\ robot", použijte "robot. FBX".

Po převodu modelu se tento model zapíše zpátky do kontejneru úložiště, který je zadaný pomocí **blobOutputContainerName**. Je možné zadat dílčí cestu zadáním volitelné **outputFolderPath**. V předchozím příkladu bude výsledný "robot. arrAsset" zkopírován do výstupního kontejneru objektů BLOB v části převedený/robot.

Nastavení konfigurace **outputAssetFileName** Určuje název převedeného prostředku – parametr je nepovinný a název výstupního souboru bude odvozený ze vstupního souboru jinak.

Otevřete PowerShell, ujistěte se, že jste nainstalovali *Azure PowerShell* , jak je uvedeno v části [požadavky](#prerequisites). Pak se přihlaste k předplatnému pomocí následujícího příkazu a postupujte podle pokynů na obrazovce:

```PowerShell
Connect-AzAccount
```

> [!NOTE]
> V případě, že má vaše organizace více než jedno předplatné, možná budete muset zadat SubscriptionId a argumenty tenanta. Vyhledejte podrobnosti v [dokumentaci Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

Přejděte do `azure-remote-rendering\Scripts` adresáře a spusťte převodní skript:

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Mělo by se zobrazit něco podobného: ![Conversion.ps1](./media/successful-conversion.png)

### <a name="3-conversion-via-api-calls"></a>3. převod prostřednictvím volání rozhraní API

Jazyk C# i rozhraní API pro C++ poskytují vstupní bod pro interakci se službou:
* [C# AzureFrontend. StartAssetConversionAsync ()](/dotnet/api/microsoft.azure.remoterendering.azurefrontend.startassetconversionasync)
* [C++ AzureFrontend:: StartAssetConversionAsync ()](/cpp/api/remote-rendering/azurefrontend#startassetconversionasync)


## <a name="insert-new-model-into-quickstart-sample-app"></a>Vložení nového modelu do ukázkové aplikace pro rychlé zprovoznění

Převodní skript generuje identifikátor URI *sdíleného přístupového podpisu (SAS)* pro převedený model. Teď můžete tento identifikátor URI zkopírovat jako **název modelu** do ukázkové aplikace pro rychlé zprovoznění (viz [rychlý Start: vykreslení modelu pomocí Unity](render-model.md)).

![Nahradit model v Unity](./media/replace-model-in-unity.png)

 Ukázka by teď měla načíst a vykreslit vlastní model.

## <a name="optional-re-creating-a-sas-uri"></a>Volitelné: opětovné vytvoření identifikátoru URI SAS

Identifikátor URI SAS vytvořený skriptem pro převod bude platný pouze 24 hodin. Po vypršení platnosti však nemusíte model znovu převádět. Místo toho můžete vytvořit nové SAS na portálu, jak je popsáno v následujících krocích:

1. Přejděte na web [Azure Portal](https://www.portal.azure.com).
1. Klikněte na prostředek **účtu úložiště** : ![ snímek obrazovky, který zvýrazní vybraný prostředek účtu úložiště.](./media/portal-storage-accounts.png)
1. Na následující obrazovce klikněte na Průzkumník služby **Storage** na levém panelu a v kontejneru úložiště objektů BLOB *arroutput* Najděte svůj výstupní model (soubor *. arrAsset* ). Klikněte pravým tlačítkem na soubor a v místní nabídce vyberte **získat sdílený přístupový podpis** : ![ přístup k podpisu](./media/portal-storage-explorer.png)
1. Otevře se nová obrazovka, kde můžete vybrat datum vypršení platnosti. Stiskněte **vytvořit**a zkopírujte identifikátor URI, který je zobrazen v následujícím dialogovém okně. Tento nový identifikátor URI nahrazuje dočasný identifikátor URI, který skript vytvořil.

## <a name="next-steps"></a>Další kroky

Teď, když znáte základy, podíváme se na naše kurzy, abyste získali podrobnější znalosti.

Pokud se chcete seznámit s podrobnostmi o převodu modelu, podívejte [se na převod modelu REST API](../how-tos/conversion/conversion-rest-api.md).

> [!div class="nextstepaction"]
> [Kurz: zobrazení vzdáleně generovaných modelů](../tutorials/unity/view-remote-models/view-remote-models.md)