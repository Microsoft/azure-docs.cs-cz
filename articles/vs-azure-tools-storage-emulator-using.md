---
title: Konfigurace a pomocí emulátoru úložiště pomocí sady Visual Studio | Microsoft Docs
description: Konfigurace a pomocí emulátoru úložiště pomocí sady Visual Studio
services: visual-studio-online
author: ghogen
manager: douge
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: c502d5e0869d35ded5c3ba7e790da0558d219e0e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Konfigurace a pomocí emulátoru úložiště pomocí sady Visual Studio
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Přehled
Vývojové prostředí sady Azure SDK zahrnuje emulátor úložiště, nástroj, který simuluje Blob, fronty a tabulky úložiště služby v Azure k dispozici na místním vývojovém počítači. Pokud jste vytváření cloudové služby, které používá služby Azure storage nebo zápis žádné externí aplikace, která volá služby úložiště, můžete otestovat kód místně emulátoru úložiště. Nástroje Azure pro sadu Microsoft Visual Studio integrovat správu emulátoru úložiště do sady Visual Studio. Nástroje Azure inicializace databáze emulátor úložiště na první použití, spustí službu emulátor úložiště při spouštění nebo ladění kódu ze sady Visual Studio a poskytuje přístup jen pro čtení k datům emulátor úložiště pomocí Průzkumníka úložiště Azure.

Podrobné informace o emulátor úložiště, včetně požadavky na systém a vlastní konfigurace pokyny najdete v tématu [použití emulátoru úložiště Azure pro vývoj a testování](storage/common/storage-use-emulator.md).

> [!NOTE]
> Existují určité rozdíly ve funkcích mezi simulace emulátor úložiště a služby úložiště Azure. V tématu [rozdíly mezi emulátoru úložiště a služby úložiště Azure](storage/common/storage-use-emulator.md) v dokumentaci k Azure SDK pro informace o konkrétních rozdílech.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Konfigurace připojovací řetězec pro emulátor úložiště
Pro přístup k emulátoru úložiště z kódu v rámci role, můžete nakonfigurovat připojovací řetězec, který odkazuje na emulátor úložiště a který lze později změnit tak, aby odkazoval na účet úložiště Azure. Připojovací řetězec je nastavení konfigurace, který může číst vaše role při běhu pro připojení k účtu úložiště. Další informace o tom, jak vytvořit připojovací řetězce najdete v tématu [konfigurace aplikace Azure](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

> [!NOTE]
> Vrátíte odkaz na účet emulátor úložiště z vašeho kódu pomocí **DevelopmentStorageAccount** vlastnost. Tento postup funguje správně, pokud chcete získat přístup z vašeho kódu emulátor úložiště, ale pokud máte v plánu pro publikování aplikace do Azure, budete muset vytvořit připojovací řetězec pro přístup k účtu úložiště Azure a upravit kód a použít tento připojovací řetězec před publikováním. Pokud přepínáte mezi účtem emulátor úložiště a účet úložiště Azure často, bude tento proces zjednodušit připojovací řetězec.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>Inicializace a spouštění emulátor úložiště
Můžete určit, že při spuštění nebo ladění služby v sadě Visual Studio, Visual Studio automaticky spustí emulátor úložiště. V Průzkumníku řešení otevřete místní nabídku pro vaše **Azure** projektu a zvolte **vlastnosti**. Na **vývoj** ve **spusťte emulátor úložiště Azure** vyberte **True** (Pokud již není nastaven na tuto hodnotu).

Při prvním spuštění nebo ladění služby ze sady Visual Studio, spustí se emulátor úložiště inicializace procesu. Tento proces rezervuje místní porty pro emulátor úložiště a vytvoří databázi emulátoru úložiště. Po dokončení tohoto procesu není nutné znovu spustit, pokud databáze, emulátor úložiště se odstraní.

> [!NOTE]
> Od června 2012 verze nástroje Azure, emulátor úložiště, ve výchozím nastavení spustí, v SQL Express LocalDB. V dřívějších verzích nástroje Azure emulátor úložiště spuštěno u výchozí instance systému SQL Express 2005 nebo 2008, který je nutno nainstalovat předtím, než můžete nainstalovat sadu Azure SDK. Můžete také spustit emulátor úložiště proti pojmenované instance SQL Express nebo pojmenovaná nebo výchozí instanci serveru Microsoft SQL Server. Pokud potřebujete nakonfigurovat emulátor úložiště spouštění instance než výchozí instance, najdete v článku [použití emulátoru úložiště Azure pro vývoj a testování](storage/common/storage-use-emulator.md).
> 
> 

Emulátor úložiště poskytuje uživatelské rozhraní pro zobrazení stavu služby místní úložiště a spuštění, zastavení a nastavit je. Jakmile byla spuštěna služba emulátor úložiště, můžete zobrazit uživatelské rozhraní nebo spustit nebo zastavit službu kliknutím pravým tlačítkem myši na ikonu v oznamovací oblasti pro emulátor sady Microsoft Azure na hlavním panelu Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Zobrazení dat emulátoru úložiště v Průzkumníku serveru
V uzlu úložiště Azure v Průzkumníku serveru umožňuje zobrazovat data a změnit nastavení pro data objektu blob a tabulky ve účty úložiště, včetně emulátor úložiště. V tématu [prostředků spravovat Azure Blob Storage pomocí Storage Exploreru](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) Další informace.

