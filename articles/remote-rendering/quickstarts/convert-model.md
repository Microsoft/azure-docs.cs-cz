---
title: Převod modelu
description: Rychlý start, který zobrazuje kroky převodu pro vlastní model.
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: 084f32ec9c1574dfdaf29edc24a406444de9a91d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679923"
---
# <a name="quickstart-convert-a-model-for-rendering"></a>Úvodní příručka: Převod modelu pro vykreslování

V [úvodním startu: Render model s Unity](render-model.md), jste se naučili, jak pomocí projektu ukázky Unity k vykreslení předdefinovaného modelu. Tato příručka ukazuje, jak převést své vlastní modely.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
>
> * Nastavení účtu úložiště objektů blob Azure pro vstup a výstup
> * Nahrání a převod 3D modelu pro použití se vzdáleným vykreslováním Azure
> * Zahrnutí převedeného 3D modelu do aplikace pro vykreslování

## <a name="prerequisites"></a>Požadavky

* Kompletní [úvodní příručka: Vykreslení modelu pomocí Unity](render-model.md)
* Instalace Azure [PowerShellu (dokumentace)](https://docs.microsoft.com/powershell/azure/)
  * Otevření PowerShellu s právy správce
  * Spustit:`Install-Module -Name Az -AllowClobber`

## <a name="overview"></a>Přehled

Vykreslovací modul na serveru nemůže pracovat přímo se zdrojovými formáty modelu, jako je FBX nebo GLTF. Místo toho vyžaduje, aby byl model v proprietárním binárním formátu.
Služba převodu spotřebovává modely z úložiště objektů blob Azure a zapíše převedené modely zpět do kontejneru úložiště objektů blob Azure.

Budete potřebovat:

* Předplatné Azure
* Účet "StorageV2" ve vašem předplatném
* Kontejner úložiště objektů blob pro vstupní model
* Kontejner úložiště objektů blob pro vaše výstupní data
* Model pro převod, viz [ukázkové modely](../samples/sample-model.md)
  * Zobrazit seznam [podporovaných zdrojových formátů](../how-tos/conversion/model-conversion.md#supported-source-formats)
  * Chcete-li použít ukázkový převodový skript, ujistěte se, že připravíte vstupní složku, která obsahuje model a všechny externí závislosti (jako jsou externí textury nebo geometrie)

## <a name="azure-setup"></a>Nastavení Azure

Pokud ještě nemáte účet, přejděte [https://azure.microsoft.com/get-started/](https://azure.microsoft.com/get-started/)na , klikněte na možnost bezplatného účtu a postupujte podle pokynů.

Až budete mít účet Azure, přejděte na [https://ms.portal.azure.com/#home](https://ms.portal.azure.com/#home).

### <a name="storage-account-creation"></a>Vytvoření účtu úložiště

Chcete-li vytvořit úložiště objektů blob, musíte nejprve účet úložiště.
Chcete-li jej vytvořit, klikněte na tlačítko "Vytvořit zdroj":

![Azure – přidání prostředku](media/azure-add-a-resource.png)

Na nové obrazovce zvolte **Úložiště** na levé straně a potom **účet úložiště – objekt blob, soubor, tabulka, fronta** z dalšího sloupce:

![Azure – přidání úložiště](media/azure-add-storage.png)

Kliknutím na toto tlačítko se zobrazí následující obrazovka s vlastnostmi úložiště, které je třeba vyplnit:

![Nastavení Azure](media/azure-setup1.png)

Vyplňte formulář následujícím způsobem:

* Vytvořte novou skupinu prostředků z odkazu pod rozevíracím seznamem a pojmenujte tento **ARR_Tutorial**
* Pro **název účtu úložiště**zadejte jedinečný název zde. **Tento název musí být globálně jedinečný**, jinak bude existovat výzva, která vás informuje, že je název připraven. V rozsahu tohoto rychlého startu, pojmenujeme to **arrtutorialstorage**. Proto je třeba jej nahradit svým jménem pro každou událost v tomto rychlém startu.
* Vyberte **umístění** ve své blízkosti. V ideálním případě použijte stejné umístění jako pro nastavení vykreslování v jiném rychlém startu.
* **Výkon** nastavený na standardní
* **Typ účtu** nastavený na "StorageV2 (pro všeobecné účely v2)"
* **Replikace** nastavena na "Geograficky redundantní úložiště pro čtení (RA-GRS)"
* **Úroveň přístupu** nastavená na "Hot"

Žádná z vlastností na jiných kartách nemusí být změněna, takže můžete pokračovat **v "Review + create"** a potom postupujte podle pokynů k dokončení instalace.

Web vás nyní informuje o průběhu nasazení a nakonec nahlásí, že je vaše nasazení dokončeno. Klikněte na tlačítko **"Přejít na zdroj"** pro další kroky:

![Vytvoření Azure Storage dokončeno](./media/storage-creation-complete.png)

### <a name="blob-storage-creation"></a>Vytvoření úložiště objektů blob

Dále potřebujeme dvě kontejnery blob, jednu pro vstup a jednu pro výstup.

Z výše uvedeného tlačítka **"Přejít na zdroj"** se dostanete na stránku s panelem vlevo, který obsahuje nabídku seznamu. V tomto seznamu v kategorii **"Služba objektů blob"** klikněte na tlačítko **"Kontejnery":**

![Azure – přidání kontejnerů](./media/azure-add-containers.png)

Stisknutím tlačítka **"+ Kontejner"** vytvořte **vstupní** kontejner úložiště objektů blob.
Při vytváření použijte následující nastavení:
  
* Název = arrinput
* Úroveň veřejného přístupu = Soukromé

Po vytvoření kontejneru klikněte znovu na **+ Kontejner** a opakujte s těmito nastaveními pro **výstupní** kontejner:

* Name = arroutput
* Úroveň veřejného přístupu = Soukromé

Teď byste měli mít dva kontejnery pro ukládání objektů blob:

![Nastavení úložiště objektů blob](./media/blob-setup.png)

## <a name="run-the-conversion"></a>Spuštění převodu

Abychom usnadnili volání služby převodu majetku, poskytujeme nástrojový skript. Je umístěn ve složce *Skripty* a nazývá se **Conversion.ps1**.

Zejména tento skript

1. odešle všechny soubory v daném adresáři z místního disku do kontejneru vstupního úložiště.
1. volá [rozhraní REST API pro převod majetku,](../how-tos/conversion/conversion-rest-api.md) které načte data ze vstupního kontejneru úložiště a zahájí převod, který vrátí ID převodu
1. dotazování rozhraní API stavu převodu s id načteného převodu, dokud proces převodu neskončí úspěchem nebo neúspěchem
1. načte odkaz na převedený datový zdroj ve výstupním úložišti.

Skript čte jeho konfiguraci ze souboru *Scripts\arrconfig.json*. Otevřete tento soubor JSON v textovém editoru.

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

Konfigurace v rámci skupiny **accountSettings** (ID účtu a klíč) by měla být vyplněna podobně jako pověření v [render modelu s Unity quickstart](render-model.md).

Uvnitř skupiny **assetConversionSettings** nezapomeňte změnit **resourceGroup**, **blobInputAledName**a **blobOutputContainerName,** jak je vidět výše.
Všimněte si, že hodnota **arrtutorialstorage** musí být nahrazenjedinečný název, který jste vybrali při vytváření účtu úložiště.

Změňte **localAssetDirectoryPath** tak, aby přesměroval na adresář na disku, který obsahuje model, který chcete převést. Dávejte pozor, abyste správně unikli zpětným lomítkům\\("\\\\") v cestě pomocí dvojitých zpětná lomítka (" ").

Všechna data z cesty uvedené v **localAssetDirectoryPath** budou odeslána do kontejneru **blobObjektů blobName blob** pod podcestí danou **inputFolderPath**. Takže v příkladu konfigurace výše obsah\\adresáře\\"D: tmp robot" bude nahrán do kontejneru blob "arrinput" z účtu úložiště "arrtutorialstorage" pod cestou "robotConversion". Již existující soubory budou přepsány.

Změňte **inputAssetPath** na cestu modelu, který má být převeden - cesta je relativní k localAssetDirectoryPath. Jako oddělovač cesty\\použijte místo "" . Takže pro soubor "robot.fbx", který je\\umístěn\\přímo v "D: tmp robot" použijte "robot.fbx".

Jakmile byl model převeden, bude zapsán zpět do kontejneru úložiště dané **objektem blobOutputContainerName**. Podcestu lze zadat poskytnutím volitelného **outputFolderPath**. Ve výše uvedeném příkladu bude výsledný "robot.arrAsset" zkopírován do výstupního kontejneru objektů blob pod "convert/robot".

Nastavení konfigurace **outputAssetFileName** určuje název převedeného datového zdroje - parametr je volitelný a výstupní název souboru bude odvozen z názvu vstupního souboru jinak. 

Otevřete PowerShell a ujistěte se, že jste nainstalovali *Azure PowerShell,* jak je uvedeno v [požadavcích](#prerequisites). Pak se přihlaste k předplatnému:

```PowerShell
Connect-AzAccount -Subscription "<your Azure subscription id>"
```

Přecházte do `azure-remote-rendering\Scripts` adresáře a spusťte skript převodu:

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Měli byste vidět něco ![takového: Conversion.ps1](./media/successful-conversion.png)

Skript převodu generuje identifikátor URI *sdíleného přístupového podpisu (SAS)* pro převedený model. Nyní můžete zkopírovat tento identifikátor URI jako **název modelu** do ukázkové aplikace pro rychlý start (viz [Úvodní příručka: Vykreslení modelu pomocí Unity).](render-model.md)

![Nahradit model v Unity](./media/replace-model-in-unity.png)

 Ukázka by nyní měla načíst a vykreslit vlastní model!

## <a name="optional-re-creating-a-sas-uri"></a>Volitelné: Opětovné vytvoření identifikátoru URI SAS

Identifikátor URI SAS vytvořený skriptem převodu bude platný pouze po dobu 24 hodin. Po vypršení platnosti však není nutné znovu převést model. Místo toho můžete vytvořit nový SAS na portálu, jak je popsáno v dalších krocích:

1. Přejděte na web [Azure Portal](https://www.portal.azure.com).
1. Klikněte na prostředek ![ **účtu úložiště:** Přístup k podpisu](./media/portal-storage-accounts.png)
1. Na následující obrazovce klikněte na **Průzkumník úložiště** v levém panelu a najděte svůj výstupní model (*soubor ArrAsset)* v kontejneru úložiště objektů blob *arroutput.* Klikněte pravým tlačítkem myši na soubor a v ![místní nabídce: Přístup k podpisu vyberte získat sdílený **přístupový podpis.**](./media/portal-storage-explorer.png)
1. Otevře se nová obrazovka, na které můžete vybrat datum vypršení platnosti. Stiskněte **klávesu Create**a zkopírujte identifikátor URI, který se zobrazí v dalším dialogovém okně. Tento nový identifikátor URI nahrazuje dočasný identifikátor URI, který skript vytvořil.

## <a name="next-steps"></a>Další kroky

Nyní, když znáte základy, podívejte se na naše výukové programy, abyste získali hlubší znalosti.

Pokud se chcete dozvědět podrobnosti o převodu modelu, podívejte se [na rozhraní REST API převodu modelu](../how-tos/conversion/conversion-rest-api.md).

> [!div class="nextstepaction"]
> [Kurz: Nastavení projektu Unity od nuly](../tutorials/unity/project-setup.md)
