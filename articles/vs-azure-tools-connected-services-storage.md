---
title: Přidat Azure Storage pomocí připojené služby v sadě Visual Studio | Microsoft Docs
description: Přidejte úložiště Azure do vaší aplikace pomocí dialogu Visual Studio přidat připojení služby
services: visual-studio-online
author: ghogen
manager: douge
assetId: 521ec044-ad4b-4828-8864-01decde2e758
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/26/2017
ms.author: ghogen
ms.openlocfilehash: 3c5d3dc1d91a6f8bb1816b2985f86ec5c4a12e63
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31793592"
---
# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>Přidání úložiště Azure pomocí Visual Studio připojené Services
Pomocí sady Visual Studio, připojením některý z těchto do služby Azure Storage pomocí **přidat připojení služby** dialogové okno:

- Cloudová služba jazyka C#
- Rozhraní .NET back-end mobilní služby
- Web ASP.NET nebo služby
- ASP.NET základní služby
- Služba Azure webové úlohy 

Funkci připojené služby přidá všechny potřebné odkazy a kód připojení do projektu a odpovídajícím způsobem upravit konfigurační soubory. 

Po dokončení **přidat připojení služby** dialogové okno automaticky zobrazí dokumentaci s podrobnostmi o kroky potřebné k začít pracovat s úložištěm blob, fronty a tabulky.

## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Připojit k úložišti Azure pomocí dialogového okna připojení služby
1. Otevřete projekt v sadě Visual Studio

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem myši **připojené služby** uzel a z kontextovou nabídku a vyberte **přidat připojení službě**.
   
    ![Přidat Azure připojení služby](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. V **připojené služby** vyberte **úložiště v cloudu s Azure Storage**.
   
    ![Přidejte úložiště Azure](./media/vs-azure-tools-connected-services-storage/add-azure-storage.png)

1. V **Azure Storage** dialogovém okně vyberte stávající účet úložiště a vyberte **přidat**.
   
    Pokud potřebujete vytvořit účet úložiště, přejděte k dalšímu kroku. Jinak přejděte ke kroku 6.
    
    ![Přidat existující účet úložiště do projektu](./media/vs-azure-tools-connected-services-storage/select-azure-storage-account.png)

1. Vytvoření účtu úložiště: 
   
   1. Vyberte **vytvořit nový účet úložiště** v dolní části dialogového okna.

   1. Vyplňte **vytvořit účet úložiště** dialogové okno a vyberte **vytvořit**.
      
       ![Nový účet úložiště Azure](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)
      
   1. Když **Azure Storage** se zobrazí dialogové okno, v seznamu se zobrazí nový účet úložiště. V seznamu vyberte nový účet úložiště a vyberte **přidat**.

1. Úložiště připojené služby se zobrazí pod **odkazy na službu** uzlu projektu.
   
## <a name="how-your-project-is-modified"></a>Jak se mění projektu
Po dokončení dialogu přidá odkazy na Visual Studio a upraví určité konfigurační soubory. Určité změny závisí na typu projektu: 

- Projekt ASP.NET - [co se stalo – projekty ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513126)
- Projekt ASP.NET Core - [co se stalo – projekty ASP.NET 5](http://go.microsoft.com/fwlink/p/?LinkId=513124) 
- Projekt cloudové služby (webových rolí a rolí pracovního procesu) - [co se stalo – projekty cloudových služeb](http://go.microsoft.com/fwlink/p/?LinkId=516965)
- Projekt webové úlohy – [co se stalo – projekty webové úlohy](visual-studio/vs-storage-webjobs-what-happened.md)

## <a name="next-steps"></a>Další postup
- [Fóru MSDN: Úložiště Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
- [Blog týmu Microsoft Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
- [Dokumentace k Azure Storage](https://docs.microsoft.com/azure/storage/)
