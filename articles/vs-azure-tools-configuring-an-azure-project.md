---
title: Konfigurace projektu Azure cloud service pomocí sady Visual Studio | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat projekt cloudové služby Azure v sadě Visual Studio, v závislosti na vašich požadavcích pro daný projekt.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 609d6965-05cc-47b1-82dc-c76a92d4f295
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/06/2017
ms.author: ghogen
ms.openlocfilehash: 0e2e03f7a492f5018b99145a63bb1b7432d3e0b3
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42059411"
---
# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Konfigurace projektu Azure cloud service pomocí sady Visual Studio
Projekt Azure cloud service, můžete nakonfigurovat v závislosti na vašich požadavcích pro daný projekt. Můžete nastavit vlastnosti projektu v těchto kategoriích:

- **Publikovat do cloudového Azure** – můžete nastavit vlastnosti a ujistěte se, že není možné omylem odstranit existující cloudové služby nasadit do Azure.
- **Spuštění nebo ladění cloudové služby na místním počítači** – můžete vybrat konfiguraci služby, kterou chcete použít a určit, jestli chcete spustit emulátor úložiště Azure.
- **Ověřit balíček cloudové služby, když se vytvoří** – se můžete rozhodnout používat zpracovávat všechna upozornění jako chyby tak, aby bylo možné zajistit, že balíček cloudové služby nasadí bez problémů. 

## <a name="steps-to-configure-an-azure-cloud-service-project"></a>Postup konfigurace projektu Azure cloud service
1. Otevřete nebo vytvořte projekt cloudové služby v sadě Visual Studio

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a v místní nabídce vyberte **vlastnosti**.
   
1. Na stránce vlastností projektu, vyberte **vývoj** kartu.

    ![Nabídku vlastností projektu](./media/vs-azure-tools-configuring-an-azure-project/solution-explorer-project-properties-menu.png)

1. Nastavte **příkazový řádek před odstraněním stávajícího nasazení** k **True**. Toto nastavení pomůže zajistit, že nechtěně neodstraníte stávajícího nasazení v Azure

1. Vyberte požadovaný **konfigurace služby** udávajících konfiguraci služby, kterou chcete použít při spuštění nebo ladění místní cloudové služby. Další informace o tom, jak změnit konfiguraci služby pro roli najdete v tématu [ke konfiguraci role pro cloudové služby Azure s využitím sady Visual Studio](./vs-azure-tools-configure-roles-for-cloud-service.md).

1. Nastavte **emulátoru úložiště Azure spustit** k **True** spustit emulátor úložiště Azure, při spouštění nebo ladění místní cloudové služby.

1. Nastavte **zpracovávat upozornění jako chyby** k **True** k Ujistěte se, že nelze publikovat, pokud jsou chyby ověření balíčku.

1. Nastavte **používat porty webového projektu** k **True** abyste měli jistotu, že vaše webová role používá stejný port pokaždé, když ji spustí místně v rámci služby IIS Express.

1. Na panelu nástrojů sady Visual Studio vyberte **Uložit**.

## <a name="next-steps"></a>Další postup
- [Konfigurace projektu Azure v použití několika konfigurací služby](vs-azure-tools-multiple-services-project-configurations.md)

