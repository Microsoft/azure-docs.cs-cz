---
title: Konfigurace a použití emulátoru úložiště se sadou Visual Studio | Microsoft Docs
description: Konfigurace a použití emulátoru úložiště se sadou Visual Studio
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
ms.openlocfilehash: 08e3f5d5bb32d15b9d8d164c898d2b8d7a90108c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969716"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Konfigurace a použití emulátoru úložiště se sadou Visual Studio
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Přehled
Vývojové prostředí Azure SDK zahrnuje emulátor úložiště, což je nástroj, který simuluje služby objektů blob, front a úložiště, které jsou k dispozici v Azure na místním vývojovém počítači. Pokud vytváříte cloudovou službu, která využívá služby úložiště Azure, nebo napíšete jakoukoli externí aplikaci, která volá služby úložiště, můžete svůj kód místně otestovat proti emulátoru úložiště. Nástroje Azure pro Microsoft Visual Studio integrují správu emulátoru úložiště do sady Visual Studio. Nástroje Azure inicializují databázi emulátoru úložiště při prvním použití, spustí službu emulátoru úložiště při spuštění nebo ladění kódu ze sady Visual Studio a poskytuje přístup k datům emulátoru úložiště jen pro čtení prostřednictvím Průzkumník služby Azure Storage.

Podrobné informace o emulátoru úložiště, včetně požadavků na systém a vlastních pokynů ke konfiguraci, najdete v tématu [použití emulátoru Azure Storage pro vývoj a testování](storage/common/storage-use-emulator.md).

> [!NOTE]
> Mezi simulací emulátoru úložiště a službami úložiště Azure je několik rozdílů. Informace o konkrétních rozdílech najdete v tématu [rozdíly mezi emulátorem úložiště a Azure Storage službami](storage/common/storage-use-emulator.md) v dokumentaci k sadě Azure SDK.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Konfigurace připojovacího řetězce pro emulátor úložiště
Pro přístup k emulátoru úložiště z kódu v rámci role budete chtít nakonfigurovat připojovací řetězec, který odkazuje na emulátor úložiště a později se dá změnit tak, aby odkazoval na účet služby Azure Storage. Připojovací řetězec je konfigurační nastavení, které vaše role může číst za běhu, aby se mohla připojit k účtu úložiště. Další informace o tom, jak vytvořit připojovací řetězce, najdete v tématu [konfigurace Azure Storage připojovacích řetězců](/azure/storage/common/storage-configure-connection-string).

> [!NOTE]
> Odkaz na účet emulátoru úložiště můžete vrátit z kódu pomocí vlastnosti **DevelopmentStorageAccount** . Tento přístup funguje správně, pokud chcete k emulátoru úložiště získat přístup z kódu, ale pokud plánujete publikování aplikace v Azure, budete muset vytvořit připojovací řetězec pro přístup k účtu služby Azure Storage a úpravou kódu pro použití tohoto připojení. před publikováním řetězec. Pokud přepínáte mezi účtem emulátoru úložiště a účtem Azure Storage často, připojovací řetězec zjednodušuje tento proces.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>Inicializace a spuštění emulátoru úložiště
Můžete určit, že při spuštění nebo ladění služby v aplikaci Visual Studio aplikace Visual Studio automaticky spustí emulátor úložiště. V Průzkumník řešení otevřete místní nabídku pro projekt **Azure** a vyberte **vlastnosti**. Na kartě **vývoj** v seznamu **emulátor Start Azure Storage** vyberte **hodnotu true** (Pokud již tato hodnota není nastavená).

Při prvním spuštění nebo ladění služby ze sady Visual Studio emulátor úložiště spustí proces inicializace. Tento proces rezervuje místní porty pro emulátor úložiště a vytvoří databázi emulátoru úložiště. Po dokončení tohoto procesu není potřeba ho spouštět znovu, dokud neodstraníte databázi emulátoru úložiště.

> [!NOTE]
> Od vydání verze Azure Tools od června 2012 se emulátor úložiště ve výchozím nastavení spouští ve službě SQL Express LocalDB. V dřívějších verzích nástrojů Azure se emulátor úložiště spouští na výchozí instanci SQL Express 2005 nebo 2008, kterou musíte nainstalovat, než budete moct nainstalovat sadu Azure SDK. Emulátor úložiště můžete také spustit s pojmenovanou instancí SQL Express nebo s pojmenovanou nebo výchozí instancí Microsoft SQL Server. Pokud potřebujete nakonfigurovat emulátor úložiště tak, aby běžel s instancí jinou než výchozí instance, přečtěte si téma [použití emulátoru Azure Storage pro vývoj a testování](storage/common/storage-use-emulator.md).
> 
> 

Emulátor úložiště poskytuje uživatelské rozhraní pro zobrazení stavu místních služeb úložiště a pro jeho spuštění, zastavení a resetování. Po spuštění služby emulátoru úložiště můžete zobrazit uživatelské rozhraní nebo spustit nebo zastavit službu kliknutím pravým tlačítkem myši na ikonu oznamovací oblasti pro Microsoft Azure Emulator na hlavním panelu systému Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Zobrazení dat emulátoru úložiště v Průzkumník serveru
Uzel Azure Storage v Průzkumník serveru umožňuje zobrazit data a změnit nastavení pro data objektů BLOB a tabulek v účtech úložiště, včetně emulátoru úložiště. Další informace najdete v tématu [Správa prostředků Azure Blob Storage pomocí Průzkumník služby Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) .

