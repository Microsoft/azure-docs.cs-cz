---
title: Jak používat úložiště objektů (Blob) z Xamarin | Dokumentace Microsoftu
description: Klientská knihovna pro úložiště Azure pro Xamarin umožňuje vývojářům vytvářet svá nativní uživatelská rozhraní iOS, Android a Windows Store apps. Tento kurz ukazuje, jak pomocí Xamarinu můžete vytvořit aplikaci, která využívá úložiště objektů Blob v Azure.
services: storage
documentationcenter: xamarin
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.openlocfilehash: b35bec31035c0219bf34a31cb34e20f7dc3a72c5
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397025"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Používání úložiště Blob z Xamarin

Základ kódu Xamarin umožňuje vývojářům používat sdílené C# k vytvoření iOS, Android a Windows Store apps s jejich nativní uživatelská rozhraní. V tomto kurzu se dozvíte, jak používat Azure Blob storage s aplikací Xamarin. Pokud chcete další informace o službě Azure Storage, než se podíváme do kódu, naleznete v tématu [Úvod do Microsoft Azure Storage](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Vytvořit novou aplikaci Xamarin
Pro účely tohoto kurzu jsme budete vytvářet aplikace, která cílí na Android, iOS a Windows. Tato aplikace bude jednoduše vytvoření kontejneru a nahrání objektu blob do tohoto kontejneru. Budeme používat Visual Studio na Windows, ale stejné lze použít při vytváření aplikace pomocí Xamarin Studio v systému macOS.

Postupujte podle těchto kroků můžete vytvořit aplikaci:

1. Pokud jste tak dosud neučinili, stáhněte a nainstalujte [Xamarin pro Visual Studio](https://www.xamarin.com/download).
2. Otevřete Visual Studio a vytvořte prázdná aplikace (nativní přenosná): **soubor > Nový > Projekt > Cross-Platform > prázdný App(Native Portable)**.
3. Klikněte pravým tlačítkem na řešení v podokně Průzkumník řešení a vyberte **spravovat balíčky NuGet pro řešení**. Vyhledejte **WindowsAzure.Storage** a nainstalujte nejnovější stabilní verze do všech projektů ve vašem řešení.
4. Sestavte a spusťte váš projekt.

Teď byste měli mít aplikaci, která umožňuje klikněte na tlačítko, které zvýší čítač.

## <a name="create-container-and-upload-blob"></a>Vytvoření kontejneru a nahrání objektu blob
Části vašeho `(Portable)` projekt, přidáte nějaký kód do `MyClass.cs`. Tento kód vytvoří kontejner a odešle objekt blob do tohoto kontejneru. `MyClass.cs` by měl vypadat nějak takto:

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

Nezapomeňte nahradit "your_account_name_here" a "your_account_key_here" skutečný název účtu a klíč účtu. 

IOS, Android a Windows Phone, všechny projekty mají odkazy na přenosné projektu – to znamená, že můžou psát veškerý svým sdíleným kódem v jednom umístění a používat ve všech vašich projektech. Nyní přidáte následující řádek kódu do každého projektu, chcete-li začít využívat: `MyClass.performBlobOperation()`

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

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > souboru MainPage.xaml > MainPage.xaml.cs

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
Nyní můžete spustit tuto aplikaci v emulátoru Android nebo Windows Phone. Můžete také spustit tuto aplikaci v emulátoru systému iOS, ale to vyžaduje počítači Mac. Pro konkrétní pokyny o tom, jak to provést, najdete v dokumentaci k [připojení sady Visual Studio pro Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Po spuštění aplikace se vytvoří kontejner `mycontainer` ve vašem účtu úložiště. Objekt blob, měl by obsahovat `myblob`, který obsahuje text, `Hello, world!`. Můžete to ověřit pomocí [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak vytvořit aplikaci, více platforem v Xamarinu, která používá službu Azure Storage, zaměřují se na jeden scénář v úložišti objektů Blob. Ale můžete udělat mnohem více pouze úložiště objektů Blob, ale také pomocí tabulky, souboru a Queue Storage. Najdete další informace v následujících článcích:

* [Začínáme s úložištěm Azure Blob pomocí rozhraní .NET](storage-dotnet-how-to-use-blobs.md)
* [Úvod do služby soubory Azure](../files/storage-files-introduction.md)
* [Vývoj pro Soubory Azure pomocí .NET](../files/storage-dotnet-how-to-use-files.md)
* [Začínáme s úložištěm Azure Table pomocí rozhraní .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Začínáme s úložištěm Azure Queue pomocí rozhraní .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]