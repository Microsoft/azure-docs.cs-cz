---
title: Protokolování sady SDK řeči – služba rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Informace o povolení protokolování v sadě Speech SDK (C++, C#, Python, Objective-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 707a0f801a739a7a91cee19635e609305cd8f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805786"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Povolení protokolování v sadě Speech SDK

Protokolování do souboru je volitelná funkce sady Speech SDK. Během vývoje protokolování poskytuje další informace a diagnostiku z klíčových součástí sady Speech SDK. To může být povoleno `Speech_LogFilename` nastavením vlastnosti objektu konfigurace řeči na umístění a název souboru protokolu. Protokolování bude aktivováno globálně, jakmile je nástroj pro rozpoznávání vytvořen z této konfigurace a nelze jej později zakázat. Název souboru protokolu nelze změnit během spuštěné relace protokolování.

> [!NOTE]
> Protokolování je k dispozici od sady Speech SDK verze 1.4.0 ve všech podporovaných programovacích jazycích sady Speech SDK, s výjimkou jazyka JavaScript.

## <a name="sample"></a>Ukázka

Název souboru protokolu je určen pro konfigurační objekt. Vezmeme-li jako `SpeechConfig` příklad a za předpokladu, že jste vytvořili instanci s názvem `config`:

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

Z objektu config můžete vytvořit nástroj pro rozpoznávání. To umožní protokolování pro všechny nástroje pro rozpoznávání.

> [!NOTE]
> Pokud vytvoříte `SpeechSynthesizer` z konfiguračního objektu, protokolování nepovolí. Pokud je však protokolování povoleno, obdržíte také diagnostiku z rozhraní `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Vytvoření souboru protokolu na různých platformách

Pro Windows nebo Linux může být soubor protokolu v libovolné cestě, pro kterou má uživatel oprávnění k zápisu. Oprávnění k zápisu do umístění systému souborů v jiných operačních systémech mohou být ve výchozím nastavení omezena nebo omezena.

### <a name="universal-windows-platform-uwp"></a>Univerzální platforma Windows (UPW)

Aplikace UPW musí být umísťována soubory protokolu do jednoho z datových umístění aplikace (místní, roamingové nebo dočasné). Soubor protokolu lze vytvořit v místní složce aplikace:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Další informace o oprávnění k přístupu k souborům pro aplikace UPW jsou k dispozici [zde](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Soubor protokolu můžete uložit do interního úložiště, externího úložiště nebo do adresáře mezipaměti. Soubory vytvořené v interním úložišti nebo v adresáři mezipaměti jsou pro aplikaci soukromé. Je vhodnější vytvořit soubor protokolu v externím úložišti.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Výše uvedený kód uloží soubor protokolu do externího úložiště v kořenovém adresáři specifického pro aplikaci. Uživatel může přistupovat k souboru pomocí `Android/data/ApplicationName/logfile.txt`správce souborů (obvykle v ). Soubor bude odstraněn při odinstalaci aplikace.

Musíte také požádat o `WRITE_EXTERNAL_STORAGE` oprávnění v souboru manifestu:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Další informace o ukládání dat a souborů pro aplikace pro Android jsou k dispozici [zde](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Přístupné jsou pouze adresáře uvnitř izolovaného prostoru aplikace. Soubory lze vytvářet v dokumentech, knihovně a dočasných adresářích. Soubory v adresáři dokumentů mohou být zpřístupněny uživateli. Následující fragment kódu ukazuje vytvoření souboru protokolu v adresáři dokumentu aplikace:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Chcete-li získat přístup k vytvořenému `Info.plist` souboru, přidejte níže uvedené vlastnosti do seznamu vlastností aplikace:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Více o systému souborů iOS je k dispozici [zde](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prohlédněte si naše ukázky na GitHubu](https://aka.ms/csspeech/samples)
