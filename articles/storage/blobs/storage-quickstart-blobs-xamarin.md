---
title: 'Rychlý Start: knihovna Azure Blob Storage V12 – Xamarin'
description: V tomto rychlém startu se naučíte používat klientskou knihovnu Azure Blob Storage verze 12 s Xamarin k vytvoření kontejneru a objektu BLOB v úložišti objektů BLOB (objekt). V dalším kroku se dozvíte, jak stáhnout objekt blob do mobilního zařízení a jak zobrazit seznam všech objektů BLOB v kontejneru.
author: codemillmatt
ms.author: masoucou
ms.date: 10/09/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: dab938a526aa89f6fe5a014e10869bd8da8b475d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98802349"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-with-xamarin"></a>Rychlý Start: V12 klientské knihovny Azure Blob Storage pomocí Xamarin

Začínáme s klientskou knihovnou Azure Blob Storage V12 s Xamarin. Azure Blob Storage je řešení úložiště objektů od Microsoftu pro Cloud. Postupujte podle kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy. Služba Blob Storage je optimalizovaná pro ukládání velkých objemů nestrukturovaných dat.

Pomocí V12 klientské knihovny Azure Blob Storage s Xamarin to:

* Vytvoření kontejneru
* Nahrání objektu blob do Azure Storage
* Výpis všech objektů BLOB v kontejneru
* Stažení objektu blob do zařízení
* Odstranění kontejneru

Odkazy odkazů:

* [Referenční dokumentace k rozhraní API](/dotnet/api/azure.storage.blobs)
* [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Balíček (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Ukázka](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Účet úložiště Azure – [Vytvoření účtu úložiště](../common/storage-account-create.md)
* Visual Studio s nainstalovanou nebo [Visual Studio pro Macovou](/visualstudio/mac/installation?view=vsmac-2019) [úlohou pro vývoj mobilních aplikací .NET](/xamarin/get-started/installation/?pivots=windows)

## <a name="setting-up"></a>Nastavení
    
V této části se seznámíte s přípravou projektu pro práci s klientskou knihovnou Azure Blob Storage V12 s Xamarin.
    
### <a name="create-the-project"></a>Vytvoření projektu

1. Otevřete Visual Studio a vytvořte prázdnou aplikaci Forms.
1. Pojmenujte ji: BlobQuickstartV12

### <a name="install-the-package"></a>Instalace balíčku

1. V podokně Průzkumník řešení klikněte pravým tlačítkem na řešení a vyberte **Spravovat balíčky NuGet pro řešení**.
1. Vyhledejte **Azure. Storage. BLOBs** a nainstalujte nejnovější stabilní verzi do všech projektů ve vašem řešení.

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře **BlobQuickstartV12** :

1. Otevřete soubor *MainPage. XAML* v editoru.
1. Odeberte vše mezi `<ContentPage></ContentPage>` prvky a nahraďte níže uvedeným:

```xaml
<StackLayout HorizontalOptions="Center" VerticalOptions="Center">

    <Button x:Name="uploadButton" Text="Upload Blob" Clicked="Upload_Clicked"  IsEnabled="False"/>
    <Button x:Name="listButton" Text="List Blobs" Clicked="List_Clicked"  IsEnabled="False" />
    <Button x:Name="downloadButton" Text="Download Blob" Clicked="Download_Clicked"  IsEnabled="False" />
    <Button x:Name="deleteButton" Text="Delete Container" Clicked="Delete_Clicked" IsEnabled="False" />

    <Label Text="" x:Name="resultsLabel" HorizontalTextAlignment="Center" Margin="0,20,0,0" TextColor="Red" />
        
</StackLayout>
```

[!INCLUDE [storage-quickstart-credentials-xamarin-include](../../../includes/storage-quickstart-credentials-xamarin-include.md)]

## <a name="object-model"></a>Objektový model

Azure Blob Storage je optimalizovaná pro ukládání obrovských objemů nestrukturovaných dat. Jde o data, která nevyhovují konkrétnímu datovému modelu nebo definici, například textová nebo binární data. Úložiště objektů BLOB nabízí tři typy prostředků:

* Účet úložiště
* Kontejner v účtu úložiště
* Objekt BLOB v kontejneru

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury služby Blob Storage](./media/storage-blobs-introduction/blob1.png)

Pro interakci s těmito prostředky použijte následující třídy .NET:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): `BlobServiceClient` Třída umožňuje manipulovat s Azure Storage prostředky a kontejnery objektů BLOB.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): `BlobContainerClient` Třída umožňuje manipulovat s kontejnery Azure Storage a jejich objekty blob.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): `BlobClient` Třída umožňuje manipulovat s objekty blob Azure Storage.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): `BlobDownloadInfo` Třída představuje vlastnosti a obsah vrácený stažením objektu BLOB.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny Azure Blob Storage pro .NET v aplikaci Xamarin. Forms:

* [Vytvořit proměnné úrovně třídy](#create-class-level-variables)
* [Vytvoření kontejneru](#create-a-container)
* [Nahrání objektů blob do kontejneru](#upload-blobs-to-a-container)
* [Seznam objektů blob v kontejneru](#list-the-blobs-in-a-container)
* [Stáhnout objekty blob](#download-blobs)
* [Odstranění kontejneru](#delete-a-container)

### <a name="create-class-level-variables"></a>Vytvořit proměnné úrovně třídy

Následující kód deklaruje několik proměnných úrovně třídy. Pro komunikaci se službou Azure Blob Storage v celé zbývající části této ukázky.

Jsou to kromě připojovacího řetězce pro účet úložiště nastaveného v části [Konfigurace připojovacího řetězce úložiště](#configure-your-storage-connection-string) .

Přidejte tento kód jako proměnné úrovně třídy do souboru *MainPage. XAML. cs* :

```csharp
string storageConnectionString = "{set in the Configure your storage connection string section}";
string fileName = $"{Guid.NewGuid()}-temp.txt";

BlobServiceClient client;
BlobContainerClient containerClient;
BlobClient blobClient;
```

### <a name="create-a-container"></a>Vytvoření kontejneru

Určete název nového kontejneru. Následující kód připojí hodnotu identifikátoru GUID k názvu kontejneru, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy kontejnerů musí být malými písmeny. Další informace o pojmenování kontejnerů a objektů blob najdete v tématu [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Vytvořte instanci třídy [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) . Pak zavolejte metodu [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) a vytvořte kontejner v účtu úložiště.

Přidejte tento kód do souboru *MainPage. XAML. cs* :

```csharp
protected async override void OnAppearing()
{            
    string containerName = $"quickstartblobs{Guid.NewGuid()}";
    
    client = new BlobServiceClient(storageConnectionString);
    containerClient = await client.CreateBlobContainerAsync(containerName);

    resultsLabel.Text = "Container Created\n";

    blobClient = containerClient.GetBlobClient(fileName);

    uploadButton.IsEnabled = true;
}
```

### <a name="upload-blobs-to-a-container"></a>Nahrání objektů blob do kontejneru

Následující fragment kódu:

1. Vytvoří `MemoryStream` text.
1. Nahraje text do objektu BLOB voláním funkce [UploadAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.uploadblobasync#Azure_Storage_Blobs_BlobContainerClient_UploadBlobAsync_System_String_System_IO_Stream_System_Threading_CancellationToken_) třídy [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) a jejím předáním do souboru FileName a `MemoryStream` textu. Tato metoda vytvoří objekt blob, pokud ještě neexistuje, a přepíše ho, pokud už existoval.

Přidejte tento kód do souboru *MainPage. XAML. cs* :

```csharp
async void Upload_Clicked(object sender, EventArgs e)
{                                    
    using MemoryStream memoryStream = new MemoryStream(Encoding.UTF8.GetBytes("Hello World!"));

    await containerClient.UploadBlobAsync(fileName, memoryStream);

    resultsLabel.Text += "Blob Uploaded\n";

    uploadButton.IsEnabled = false;
    listButton.IsEnabled = true;
}
```

### <a name="list-the-blobs-in-a-container"></a>Seznam objektů blob v kontejneru

Seznam objektů BLOB v kontejneru zavoláním metody [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) . V tomto případě byl do kontejneru přidán pouze jeden objekt blob, takže operace výpisu vrátí pouze jeden objekt BLOB.

Přidejte tento kód do souboru *MainPage. XAML. cs* :

```csharp
async void List_Clicked(object sender, EventArgs e)
{            
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        resultsLabel.Text += blobItem.Name + "\n";                
    }

    listButton.IsEnabled = false;
    downloadButton.IsEnabled = true;
}
```

### <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte dřív vytvořený objekt BLOB voláním metody [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) . Vzorový kód zkopíruje `Stream` reprezentace objektu BLOB nejprve do `MemoryStream` a potom do, `StreamReader` aby se text mohl zobrazit.

Přidejte tento kód do souboru *MainPage. XAML. cs* :

```csharp
async void Download_Clicked(object sender, EventArgs e)
{
    BlobDownloadInfo downloadInfo = await blobClient.DownloadAsync();

    using MemoryStream memoryStream = new MemoryStream();
    
    await downloadInfo.Content.CopyToAsync(memoryStream);
    memoryStream.Position = 0;

    using StreamReader streamReader = new StreamReader(memoryStream);

    resultsLabel.Text += "Blob Contents: \n";
    resultsLabel.Text += await streamReader.ReadToEndAsync();
    resultsLabel.Text += "\n";

    downloadButton.IsEnabled = false;
    deleteButton.IsEnabled = true;
}
```

### <a name="delete-a-container"></a>Odstranění kontejneru

Následující kód vyčistí prostředky, které aplikace vytvořila, odstraněním celého kontejneru pomocí [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync).

Aplikace před odstraněním objektu BLOB a kontejneru nejprve vyzve k potvrzení. Tato možnost je vhodná pro ověření, že se prostředky správně vytvořily, než se odstraní.

Přidejte tento kód do souboru *MainPage. XAML. cs* :

```csharp
async void Delete_Clicked(object sender, EventArgs e)
{            
    var deleteContainer = await Application.Current.MainPage.DisplayAlert("Delete Container",
        "You are about to delete the container proceeed?", "OK", "Cancel");

    if (deleteContainer == false)
        return;

    await containerClient.DeleteAsync();

    resultsLabel.Text += "Container Deleted";

    deleteButton.IsEnabled = false;
}
```

## <a name="run-the-code"></a>Spuštění kódu

Po spuštění aplikace se nejprve vytvoří kontejner, který se zobrazí. Pak budete muset kliknout na tlačítka, aby se nahrály, vypisovat, stáhly objekty BLOB a odstranily kontejner.

Pokud chcete aplikaci spustit ve Windows, stiskněte klávesu F5. Pokud chcete aplikaci spustit na Macu, stiskněte kombinaci kláves Cmd + Enter.

Aplikace se po každé operaci zapisuje na obrazovku. Výstup aplikace je podobný následujícímu příkladu:

```output
Container Created
Blob Uploaded
98d9a472-8e98-4978-ba4f-081d69d2e6f8-temp.txt
Blob Contents:
Hello World!
Container Deleted
```

Před zahájením procesu čištění ověřte, že výstup obsahu objektu BLOB na obrazovce odpovídá hodnotě, která se nahrála.

Po ověření hodnot potvrďte výzvu k odstranění kontejneru a dokončete ukázku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nahrávat, stahovat a vypisovat objekty BLOB pomocí Azure Blob Storage klientské knihovny V12 pomocí Xamarin.

Pokud chcete zobrazit ukázkové aplikace služby Blob Storage, pokračujte:

> [!div class="nextstepaction"]
> [Ukázka sady Azure Blob Storage SDK V12 Xamarin](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

* Kurzy, ukázky, rychlé starty a další dokumentace najdete na webu [Azure pro vývojáře mobilních](/azure/mobile-apps)aplikací.
* Další informace o Xamarin najdete v tématu [Začínáme s Xamarin](/xamarin/get-started/).
