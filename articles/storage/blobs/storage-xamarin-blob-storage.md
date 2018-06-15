---
title: Jak používat úložiště objektů (objekt Blob) z Xamarin | Microsoft Docs
description: Klientská knihovna pro úložiště Azure pro Xamarin umožňuje vývojářům vytvářet iOS, Android a aplikací pro Windows Store s jejich nativní uživatelská rozhraní. Tento kurz ukazuje, jak vytvořit aplikaci, která používá Azure Blob storage pomocí Xamarin.
services: storage
documentationcenter: xamarin
author: michaelhauss
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.openlocfilehash: 31dbaeb1dd998d8d27af5eff0fa293117ef7f471
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31414204"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Používání úložiště Blob z Xamarin

Codebase – Xamarin umožňuje vývojářům používat sdílené C# k vytvoření iOS, Android a aplikací pro Windows Store s jejich nativní uživatelského rozhraní. V tomto kurzu se dozvíte, jak používat úložiště objektů Blob Azure pomocí aplikace Xamarin. Pokud vás zajímají další informace o službě Azure Storage, než začnete kód, najdete [Úvod do Microsoft Azure Storage](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Vytvoření nové aplikace Xamarin
V tomto kurzu jsme budete vytvářet aplikace, která je cílena, Android, iOS a Windows. Tato aplikace bude jednoduše vytvořit kontejner a nahrát objekt blob do tohoto kontejneru. Budeme používat Visual Studio v systému Windows, ale stejné learnings lze použít při vytváření aplikace pomocí Xamarin Studio v systému macOS.

Postupujte podle těchto kroků můžete vytvořit aplikaci:

1. Pokud jste tak ještě neučinili, stáhněte a nainstalujte [Xamarin pro Visual Studio](https://www.xamarin.com/download).
2. Otevřete Visual Studio a vytvořit prázdnou aplikaci (nativní přenosné): **soubor > Nový > Projekt > napříč platformami > prázdné App(Native Portable)**.
3. Klikněte pravým tlačítkem na řešení v podokně Průzkumník řešení a vyberte **spravovat balíčky NuGet pro řešení**. Vyhledejte **WindowsAzure.Storage** a nainstalujte nejnovější stabilní verze na všechny projekty v řešení.
4. Sestavte a spusťte projekt.

Teď byste měli mít aplikaci, která umožňuje klikněte na tlačítko, které zvýší čítače.

## <a name="create-container-and-upload-blob"></a>Vytvoření kontejneru a nahrát objekt blob
Části vašeho `(Portable)` projektu přidáte kód, který `MyClass.cs`. Tento kód vytvoří kontejner a odešle objekt blob do tohoto kontejneru. `MyClass.cs` by měl vypadat asi takto:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

Ujistěte se, že "your_account_name_here" a "your_account_key_here" nahraďte skutečný název svého účtu a klíč účtu. 

Systémem iOS, Android a Windows Phone, které mají projekty všechny odkazy na přenosné projektu – což znamená, že všechny sdílené kódu můžete napsat v jednom umístění a použít napříč všemi svými projekty. Pro každý projekt, chcete-li začít využívat můžete nyní přidejte následující řádek kódu: `MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>Spuštění aplikace
Teď můžete spustit tuto aplikaci v emulátoru Android nebo Windows Phone. Můžete taky spustit tuto aplikaci v emulátoru iOS, ale to bude vyžadovat macu. Pro konkrétní pokyny o tom, jak to provést, přečtěte si dokumentaci pro [připojení Visual Studio pro Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Po spuštění aplikace, vytvoří kontejner `mycontainer` ve vašem účtu úložiště. Objekt blob, měl by obsahovat `myblob`, text, na kterém je `Hello, world!`. Můžete to ověřit pomocí [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak vytvořit aplikaci různé platformy v Xamarinu, která používá Azure Storage, konkrétně zaměřené na jeden scénář v úložišti objektů Blob. Však můžete provést mnohem víc s ne jenom úložiště objektů Blob, ale také pomocí tabulky, souboru a Queue Storage. Podrobnosti naleznete další informace v následujících článcích:

* [Začínáme s úložištěm Azure Blob pomocí rozhraní .NET](storage-dotnet-how-to-use-blobs.md)
* [Úvod do Azure soubory](../files/storage-files-introduction.md)
* [Vývoj pro Soubory Azure pomocí .NET](../files/storage-dotnet-how-to-use-files.md)
* [Začínáme s úložištěm Azure Table pomocí rozhraní .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Začínáme s úložištěm Azure Queue pomocí rozhraní .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]