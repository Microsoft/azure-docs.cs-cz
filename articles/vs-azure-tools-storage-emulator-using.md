---
title: Konfigurace a použití emulátoru úložiště pomocí sady Visual Studio | Dokumentace Microsoftu
description: Konfigurace a použití emulátoru úložiště pomocí sady Visual Studio
services: visual-studio-online
author: ghogen
manager: douge
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: e4a72729cfe69a810e0eec0a0ac6ddb87a468932
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42060112"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Konfigurace a použití emulátoru úložiště pomocí sady Visual Studio
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Přehled
Vývojové prostředí sady Azure SDK zahrnuje emulátor úložiště, nástroj, který simuluje objektů Blob, Queue a Table storage služeb dostupných v Azure na svém místním vývojovém počítači. Pokud jste vytváření cloudové služby, která využívá služby Azure storage nebo zápis všechny externí aplikaci, která volá služby úložiště, můžete otestovat kódu místně v emulátoru úložiště. Nástroje Azure pro sadu Microsoft Visual Studio integrovat správu emulátoru úložiště do sady Visual Studio. Nástroje Azure inicializovat emulátor databáze úložiště při prvním použití spuštění emulátoru služby storage při spouštění nebo ladění vašeho kódu ze sady Visual Studio a poskytuje přístup jen pro čtení k datům emulátor úložiště přes Průzkumníka služby Azure Storage.

Podrobné informace na emulátor úložiště, včetně požadavky na systém a vlastní konfigurace pokyny najdete v tématu [použití emulátoru úložiště Azure pro vývoj a testování](storage/common/storage-use-emulator.md).

> [!NOTE]
> Existují určité rozdíly ve funkcích mezi simulace emulátoru úložiště a služby Azure storage. Zobrazit [rozdíly mezi emulátoru úložiště a služby Azure Storage](storage/common/storage-use-emulator.md) v dokumentaci k sadě Azure SDK pro informace o konkrétních rozdílech.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Konfigurace připojovací řetězec pro emulátor úložiště
Pro přístup k emulátoru úložiště z kódu v rámci role, můžete nakonfigurovat připojovací řetězec, který odkazuje na emulátor úložiště a, který lze později změnit tak, aby odkazoval na účet úložiště Azure. Připojovací řetězec je nastavení konfigurace, který může číst vaši roli v době běhu pro připojení k účtu úložiště. Další informace o tom, jak vytvořit připojovací řetězce, naleznete v tématu [konfigurace aplikace Azure](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

> [!NOTE]
> Může vrátit odkaz na účet emulátoru úložiště v kódu s použitím **DevelopmentStorageAccount** vlastnost. Tento přístup funguje správně, pokud chcete získat přístup z vašeho kódu na emulátor úložiště, ale pokud máte v plánu můžete publikovat svoji aplikaci do Azure, budete muset vytvořit připojovací řetězec pro přístup k účtu služby Azure storage a upravte kód Refaktorovat pro použití tohoto připojení řetězec před publikováním. Pokud chcete přepnout mezi emulátor účet úložiště a účtu služby Azure storage často, bude tento proces zjednodušit připojovací řetězec.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>Inicializace a spouštění v emulátoru úložiště
Můžete určit, že při spuštění nebo ladění služby v sadě Visual Studio, Visual Studio automaticky spustí emulátoru úložiště. V Průzkumníku řešení otevřete místní nabídku pro váš **Azure** projekt a zvolte **vlastnosti**. Na **vývoj** kartě **spustit emulátor úložiště Azure** klikněte na položku **True** (Pokud již není nastavena tato hodnota).

Při prvním spuštění nebo ladění služby ze sady Visual Studio, emulátor úložiště spustí inicializace procesu. Tento proces rezervuje místní porty pro emulátor úložiště a vytvoří databázi emulátoru úložiště. Jakmile budete hotovi, není potřeba tento proces spustit znovu, dokud je neodstraní databázi emulátoru úložiště.

> [!NOTE]
> Od verze z června 2012 nástrojů Azure, emulátor úložiště, ve výchozím nastavení spustí, v SQL serveru Express LocalDB. V dřívějších verzích nástrojů Azure, emulátor úložiště spouští pro výchozí instanci systému SQL Express 2005 nebo 2008, který je nutno nainstalovat před instalací Azure SDK. Můžete také spustit emulátor úložiště pro pojmenovaná instance SQL Express nebo pojmenovaná nebo výchozí instance systému Microsoft SQL Server. Pokud je nutné nakonfigurovat na emulátor úložiště, chcete-li spustit jinou než výchozí instanci, naleznete v tématu [použití emulátoru úložiště Azure pro vývoj a testování](storage/common/storage-use-emulator.md).
> 
> 

Emulátor úložiště poskytuje uživatelské rozhraní pro zobrazení stavu služby místní úložiště a chcete spustit, zastavit a obnovit je. Po zahájení službu emulátor úložiště můžete zobrazit uživatelské rozhraní nebo spuštění nebo zastavení služby kliknutím pravým tlačítkem myši ikonu v oznamovací oblasti pro Microsoft Azure Emulator na hlavním panelu Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Zobrazení dat emulátoru úložiště v Průzkumníku serveru
Uzel Azure Storage v Průzkumníku serveru umožňuje zobrazení dat a změnit nastavení pro data objektů blob a tabulky v rámci účtů úložiště, včetně emulátoru úložiště. Zobrazit [prostředků Správa služby Azure Blob Storage pomocí Storage Exploreru](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) Další informace.

