---
title: Příprava na publikování nebo nasazení cloudové služby ze sady Visual Studio | Microsoft Docs
description: Další postupy pro nastavení cloudu a úložiště účet služby a konfiguraci aplikace Azure.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/10/2017
ms.author: ghogen
ms.openlocfilehash: 8a7d6f114bfa10170cdfe7126e01a35b02affd20
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="prepare-to-publish-or-deploy-a-cloud-service-from-visual-studio"></a>Příprava na publikování nebo nasazení cloudové služby ze sady Visual Studio

K publikování projektu cloudové služby, musíte nastavit tyto služby jak je popsáno v tomto článku:

* A **Cloudová služba** ke spuštění vaší rolí v prostředí Azure a 
* A **účet úložiště** , který poskytuje přístup ke službám Blob, fronty a tabulky.

## <a name="create-a-cloud-service"></a>Vytvoření cloudové služby

Cloudové služby role běží v prostředí Azure. Můžete vytvořit cloudovou službu v sadě Visual Studio nebo prostřednictvím [portál Azure](https://portal.azure.com/) jak je popsáno v následujících částech.

### <a name="create-a-cloud-service-from-visual-studio"></a>Vytvoření cloudové služby ze sady Visual Studio

1. S dříve vytvořený projekt cloudové služby, klikněte pravým tlačítkem na projekt vyberte **publikovat**.
1. V případě potřeby, přihlaste se pomocí Microsoft nebo účtu organizace, které jsou přidružené k předplatnému Azure a pak vyberte **Další** pro přechod **nastavení** stránky.
1. A **vytvoření cloudové služby a účet úložiště** otevře se dialogové okno (Pokud není, vyberte **vytvořit nový** z **Cloudová služba** seznamu).
1. Zadejte název bez rozlišování velikosti písmen pro cloudovou službu, která je součástí adresy URL a musí být jedinečný. Také vyberte oblast nebo skupinu vztahů a vyberte možnost replikace.

### <a name="create-a-cloud-service-through-the-azure-portal"></a>Vytvoření cloudové služby prostřednictvím portálu Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyberte **cloudové služby (klasické)** na levé straně stránky.
1. Vyberte **+ přidat**, zadejte požadované informace (DNS název, předplatné, skupinu prostředků a umístění). Není nutné balíček nyní odeslat, protože můžete udělat později v sadě Visual Studio.
1. Vyberte **vytvořit** proces dokončete.

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Účet úložiště poskytuje přístup ke službám Blob, fronty a tabulky. Můžete vytvořit účet úložiště pomocí sady Visual Studio nebo [portál Azure](https://portal.azure.com/).

### <a name="create-a-storage-account-from-visual-studio"></a>Vytvoření účtu úložiště ze sady Visual Studio

1. V **Průzkumníku řešení** s dříve vytvořený projekt cloudové služby, vyhledejte **připojené služby** uzel v rámci projekt role, klikněte pravým tlačítkem a vyberte **přidat připojení službě**. (V sadě Visual Studio 2015, klikněte pravým tlačítkem myši **úložiště** uzel a vyberte možnost **vytvořit účet úložiště**.)
1. V **připojené služby** seznamu, který se zobrazí, vyberte **úložiště v cloudu s Azure Storage**.
1. V dialogovém okně Azure Storage, který se zobrazí, vyberte **+ vytvořit nový účet úložiště**, který zobrazí dialogové okno, ve kterém můžete zadat vašeho předplatného, název fo účet, cenovou úroveň, skupinu prostředků a umístění.
1. Vyberte **vytvořit** po dokončení. Nový účet úložiště se zobrazí v seznamu účtů úložiště k dispozici v rámci vašeho předplatného.
1. Vyberte, že účet a vyberte **přidat**.

### <a name="create-a-storage-account-through-the-azure-portal"></a>Vytvoření účtu úložiště prostřednictvím portálu Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyberte **+ nový** nahoře vlevo.
1. Vyberte **úložiště** v části "Azure Marketplace," pak **účet úložiště – objekt blob, soubor, tabulka, fronta** z pravé strany.
1. Zadejte požadované informace (název, model nasazení a tak dále.
1. Vyberte **vytvořit** proces dokončete.

## <a name="configure-your-app-to-use-the-storage-account"></a>Konfigurace aplikace pro použití účtu úložiště

Po vytvoření účtu úložiště k němu připojuje ze sady Visual Studio automaticky aktualizuje konfigurace služby pro projekt, včetně adres URL a přístupové klíče.

Pokud jste vytvořili pomocí sady Visual Studio cloudové služby **přidat připojení službě**, připojení můžete zkontrolovat otevřením `ServiceConfiguration.Cloud.cscfg` a `ServiceConfiguration.Local.cscfg`.

Pokud jste vytvořili cloudové služby prostřednictvím portálu Azure, postupujte podle stejných kroků v [vytvořit účet úložiště ze sady Visual Studio](#create-a-storage-account-from-visual-studio) ale vybrat existující účet místo vytvoření nového. Visual Studio pak aktualizuje konfigurace pro vás.

Konfigurovat nastavení ručně, použijte stránky vlastností v sadě Visual Studio pro příslušné role v projekt cloudové služby (klikněte pravým tlačítkem na roli a vyberte **vlastnosti**). Další informace najdete v tématu [konfiguraci připojovacího řetězce k účtu úložiště](https://docs.microsoft.com/azure/vs-azure-tools-multiple-services-project-configurations#configuring-a-connection-string-to-a-storage-account).

### <a name="about-access-keys"></a>O přístupové klávesy

Portál Azure zobrazuje adresy URL, můžete použít pro přístup k prostředkům v každé služby Azure storage a primární a sekundární přístupové klíče pro váš účet. Tyto klíče se používají k ověření požadavků na služby úložiště přístup.

Sekundární přístupový klíč poskytuje stejný přístup k účtu úložiště jako primární přístupový klíč a je generována jako záložní ohrožené primární přístupový klíč. Kromě toho se doporučuje znovu vygenerovat klíče pro přístup k v pravidelných intervalech. Můžete upravit nastavení připojovacího řetězce používat sekundární klíč obnovit primární klíč, pak můžete upravit ji používat se znova vygeneroval primární klíč obnovit sekundární klíč.

## <a name="next-steps"></a>Další postup

Další informace o publikování aplikace do Azure ze sady Visual Studio, najdete v části [publikování cloudové služby pomocí nástroje Azure](vs-azure-tools-publishing-a-cloud-service.md).
