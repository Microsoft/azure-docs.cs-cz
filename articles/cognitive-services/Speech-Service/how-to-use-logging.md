---
title: Protokolování sady SDK řeči – hlasové služby
titleSuffix: Azure Cognitive Services
description: Povolte protokolování v sadě SDK řeči.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 75eaea22c4809eda78e54514961d13113b4a5f3a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59012445"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Povolení protokolování v sadou SDK pro řeč

Protokolování do souboru je volitelná funkce pro zpracování řeči SDK. Během vývoje protokolování poskytuje další informace a diagnostiku v sadě SDK Speeck základní součásti. To se dá nastavit tak, že nastavíte vlastnost `Speech_LogFilename` řeči konfigurace objektu na umístění a název souboru protokolu. Protokolování se aktivovat globálně, až rozlišovače je vytvořený z této konfigurace a nelze deaktivovat, později. Název souboru protokolu nelze změnit během spuštění protokolování relace.

> [!NOTE]
> Protokolování je dostupná ve všech podporovaných řeči SDK programovacích jazyků, s výjimkou jazyka JavaScript.

## <a name="sample"></a>Ukázka

Na objekt konfigurace je zadán název souboru protokolu. S ohledem `SpeechConfig` jako příklad a za předpokladu, že jste vytvořili instanci volána `config`:

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

Můžete vytvořit rozpoznávání z objektu konfigurace. Tato možnost povolí protokolování pro všechny nástroje pro rozpoznávání.

> [!NOTE]
> Pokud jste vytvořili `SpeechSynthesizer` z objektu konfigurace neumožní protokolování. Pokud je však povoleno protokolování, obdržíte také Diagnostika z `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Vytvoření souboru protokolu na různých platformách

Pro Windows nebo Linux lze soubor protokolu v libovolnou cestu, kterou má uživatel oprávnění k zápisu. Oprávnění k zápisu do umístění systému souborů v jiných operačních systémech může omezené nebo ve výchozím nastavení omezen.

### <a name="universal-windows-platform-uwp"></a>Univerzální platforma Windows (UWP)

Aplikace UWP musí být, že soubory protokolu míst v jednom umístění dat aplikace (místní, roamingu nebo dočasné). Soubor protokolu je vytvořit ve složce místní aplikace:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Více o přístup k souborům oprávnění pro aplikace UPW je k dispozici [tady](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Soubor protokolu můžete uložit do interního úložiště, externí úložiště nebo adresář mezipaměti. Soubory vytvořené v interním úložišti nebo adresář mezipaměti jsou privátní pro aplikaci. Doporučuje se vytvořit soubor protokolu v externím úložišti.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Výše uvedený kód se uloží do souboru protokolu k externímu úložišti v kořenovém adresáři adresáře specifické pro aplikaci. Uživatel přístup k souboru se správcem soubor (obvykle v `Android/data/ApplicationName/logfile.txt`). Soubor se odstraní, když se aplikace odinstaluje.

Budete potřebovat k vyžádání `WRITE_EXTERNAL_STORAGE` oprávnění v souboru manifestu:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Více o datech a soubor úložiště pro aplikace pro Android je k dispozici [tady](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Jsou k dispozici pouze adresářů v izolovaném prostoru aplikace. Soubory lze vytvořit v dokumentech, knihovny a dočasného adresáře. Soubory v adresáři dokumenty lze k dispozici pro uživatele. Následující fragment kódu ukazuje vytvoření souboru protokolu do adresáře dokumentů aplikace:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Chcete-li získat přístup k souboru vytvořeného, přidejte pod vlastností `Info.plist` seznam vlastností aplikace:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Více o iOS systém souborů je k dispozici [tady](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte naše ukázky na Githubu](https://aka.ms/csspeech/samples)

