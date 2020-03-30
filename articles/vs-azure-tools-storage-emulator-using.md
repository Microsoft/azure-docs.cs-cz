---
title: Konfigurace a používání emulátoru úložiště pomocí sady Visual Studio | Dokumenty společnosti Microsoft
description: Konfigurace a používání emulátoru úložiště, nástroj, který simuluje služby úložiště objektů Blob, fronty a tabulky dostupné v Azure na místním vývojovém počítači.
services: visual-studio-online
author: ghogen
manager: jillfra
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: a6f853924416cce2440ca15767044029b20e651f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75450723"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Konfigurace a použití emulátoru úložiště v sadě Visual Studio

[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Přehled

Vývojové prostředí Sady Azure SDK zahrnuje emulátor úložiště, nástroj, který simuluje služby úložiště objektů Blob, Fronty a Tabulka dostupné v Azure na místním vývojovém počítači. Pokud vytváříte cloudovou službu, která využívá služby úložiště Azure, nebo zapisujete jakoukoli externí aplikaci, která volá služby úložiště, můžete kód otestovat místně proti emulátoru úložiště. Nástroje Azure pro Microsoft Visual Studio integrují správu emulátoru úložiště do sady Visual Studio. Nástroje Azure inicializují databázi emulátoru úložiště při prvním použití, spustí službu emulátoru úložiště při spuštění nebo ladění kódu z Visual Studia a poskytují přístup k datům emulátoru úložiště jen pro čtení prostřednictvím Průzkumníka úložiště Azure.

Podrobné informace o emulátoru úložiště, včetně systémových požadavků a vlastních pokynů ke konfiguraci, najdete [v tématu Použití emulátoru úložiště Azure pro vývoj a testování](storage/common/storage-use-emulator.md).

> [!NOTE]
> Existují určité rozdíly ve funkčnosti mezi simulací emulátoru úložiště a službami úložiště Azure. Informace o konkrétních rozdílech najdete v [tématu Rozdíly mezi emulátorem úložiště a službami Azure Storage Services](storage/common/storage-use-emulator.md) v dokumentaci k azure sdk.

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Konfigurace připojovacího řetězce pro emulátor úložiště

Chcete-li získat přístup k emulátoru úložiště z kódu v rámci role, budete chtít nakonfigurovat připojovací řetězec, který odkazuje na emulátor úložiště a který lze později změnit tak, aby přecštoupoval na účet úložiště Azure. Připojovací řetězec je nastavení konfigurace, které vaše role může číst za běhu pro připojení k účtu úložiště. Další informace o vytváření připojovacích řetězců najdete [v tématu Konfigurace připojovacích řetězců úložiště Azure](/azure/storage/common/storage-configure-connection-string).

> [!NOTE]
> Můžete vrátit odkaz na účet emulátoru úložiště z vašeho kódu pomocí **DevelopmentStorageAccount** vlastnost. Tento přístup funguje správně, pokud chcete získat přístup k emulátoru úložiště z vašeho kódu, ale pokud máte v plánu publikovat aplikaci do Azure, budete muset vytvořit připojovací řetězec pro přístup k účtu úložiště Azure a upravit kód pro použití tohoto připojení před publikováním. Pokud často přepínáte mezi účtem emulátoru úložiště a účtem úložiště Azure, tento proces zjednoduší připojovací řetězec.

## <a name="initializing-and-running-the-storage-emulator"></a>Inicializace a spuštění emulátoru úložiště

Můžete určit, že při spuštění nebo ladění služby v sadě Visual Studio visual studio automaticky spustí emulátor úložiště. V Průzkumníku řešení otevřete místní nabídku pro váš projekt **Azure** a zvolte **Vlastnosti**. Na kartě **Vývoj** v seznamu **Spustit emulátor úložiště Azure** zvolte **True** (pokud ještě není nastavená na tuto hodnotu).  Některé typy projektů nemají kartu **Vývoj.** Pokud tomu tak je, můžete povolit nebo zakázat spuštění emulátoru úložiště nastavením `StartDevelopmentStorage` prvku v souboru projektu. Nastavte ji na **Hodnotu True,** chcete-li ji povolit, nebo **false** zakázat.  Například v projektu Azure Functions otevřete soubor projektu pro úpravy a upravte kód XML následujícím způsobem:

```xml
  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
    <StartDevelopmentStorage>True</StartDevelopmentStorage>
  </PropertyGroup>
```

Při prvním spuštění nebo ladění služby z aplikace Visual Studio spustí emulátor úložiště inicializační proces. Tento proces rezervuje místní porty pro emulátor úložiště a vytvoří databázi emulátoru úložiště. Po dokončení není nutné tento proces znovu spustit, pokud není odstraněna databáze emulátoru úložiště.

> [!NOTE]
> Počínaje červnovou verzí nástrojů Azure 2012 se emulátor úložiště ve výchozím nastavení spustí v místní databázi SQL Express. V dřívějších verzích nástrojů Azure se emulátor úložiště spustí proti výchozí instanci SQL Express 2005 nebo 2008, kterou je třeba nainstalovat před instalací sady Azure SDK. Emulátor úložiště můžete spustit také proti pojmenované instanci služby SQL Express nebo pojmenované nebo výchozí instanci serveru Microsoft SQL Server. Pokud potřebujete nakonfigurovat emulátor úložiště tak, aby běžel proti jiné instanci než výchozí instanci, [přečtěte si informace o použití emulátoru úložiště Azure pro vývoj a testování](storage/common/storage-use-emulator.md).

Emulátor úložiště poskytuje uživatelské rozhraní pro zobrazení stavu místních služeb úložiště a jejich spuštění, zastavení a obnovení. Po spuštění služby emulátoru úložiště můžete zobrazit uživatelské rozhraní nebo spustit nebo zastavit službu kliknutím pravým tlačítkem myši na ikonu oznamovací oblasti emulátoru Microsoft Azure na hlavním panelu windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Zobrazení dat emulátoru úložiště v Průzkumníkovi serveru

Uzel Azure Storage v Průzkumníkovi serveru umožňuje zobrazit data a změnit nastavení dat objektů blob a tabulky v účtech úložiště, včetně emulátoru úložiště. Další informace najdete [v tématu Správa prostředků úložiště objektů blob Azure pomocí Průzkumníka úložišť.](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)

