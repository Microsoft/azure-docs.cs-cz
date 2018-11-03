---
title: Příprava k publikování nebo nasazení cloudové služby v sadě Visual Studio | Dokumentace Microsoftu
description: Další postupy pro nastavení účtu služby pro cloud a úložiště a nakonfigurovat svoji aplikaci Azure.
services: visual-studio-online
author: ghogen
manager: douge
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ghogen
ms.openlocfilehash: cbd72198dbdbe2dd49f398e98806773cbb11d0f7
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969388"
---
# <a name="prepare-to-publish-or-deploy-a-cloud-service-from-visual-studio"></a>Příprava k publikování nebo nasazení cloudové služby v sadě Visual Studio

Chcete-li publikovat projekt cloudové služby, musíte nastavit tyto služby jak je popsáno v tomto článku:

* A **Cloudová služba** ke spuštění vaší rolí v prostředí Azure a 
* A **účtu úložiště** , která poskytuje přístup ke službám Blob, Queue a Table.

## <a name="create-a-cloud-service"></a>Vytvoření cloudové služby

Cloudová služba spustí své role v prostředí Azure. Můžete vytvořit cloudovou službu v sadě Visual Studio nebo prostřednictvím [webu Azure portal](https://portal.azure.com/) jak je popsáno v následující části.

### <a name="create-a-cloud-service-from-visual-studio"></a>Vytvořit cloudovou službu v sadě Visual Studio

1. S dříve vytvořeném projektu cloudové služby, klikněte pravým tlačítkem na projekt vyberte **publikovat**.
1. V případě potřeby, přihlaste se pomocí Microsoft nebo účet organizace přidružený k vašemu předplatnému Azure a pak vyberte **Další** pro přechod **nastavení** stránky.
1. A **vytvořit Cloudovou službu a účet úložiště** se zobrazí dialogové okno (Pokud ne, vyberte **vytvořit nový** z **Cloudovou službu** seznamu).
1. Zadejte název velkých a malých písmen pro cloudovou službu, která je součástí adresy URL a musí být jedinečný. Také vyberte oblast nebo skupina vztahů a vyberte možnost replikace.

### <a name="create-a-cloud-service-through-the-azure-portal"></a>Vytvoření cloudové služby na webu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyberte **cloudové služby (klasické)** na levé straně stránky.
1. Vyberte **+ přidat**, potom zadejte požadované informace (DNS název předplatného, skupiny prostředků a umístění). Není nutné v tomto okamžiku nahrání balíčku, protože můžete udělat později v sadě Visual Studio.
1. Vyberte **vytvořit** proces dokončete.

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Účet úložiště poskytuje přístup ke službám Blob, Queue a Table. Můžete vytvořit účet úložiště pomocí sady Visual Studio nebo [webu Azure portal](https://portal.azure.com/).

### <a name="create-a-storage-account-from-visual-studio"></a>Vytvoření účtu úložiště ze sady Visual Studio

1. V **Průzkumníka řešení** s dříve vytvořeném projektu cloudové služby, vyhledejte **připojené služby** uzlu v rámci projektu role, klikněte pravým tlačítkem a vyberte **přidat připojenou službu**. (V sadě Visual Studio 2015, klikněte pravým tlačítkem myši **úložiště** uzel a vyberte možnost **vytvořit účet úložiště**.)
1. V **připojené služby** seznam, který se zobrazí, vyberte **cloudové úložiště se službou Azure Storage**.
1. V dialogovém okně služby Azure Storage, který se zobrazí, vyberte **+ vytvořit nový účet úložiště**, který se zobrazí dialogové okno, ve kterém můžete zadat vašeho předplatného, název fo účet, cenovou úroveň, skupinu prostředků a umístění.
1. Vyberte **vytvořit** po dokončení. Nový účet úložiště se zobrazí v seznamu účtů úložiště k dispozici v rámci vašeho předplatného.
1. Vyberte, že účet a vyberte **přidat**.

### <a name="create-a-storage-account-through-the-azure-portal"></a>Vytvoření účtu úložiště na webu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Vyberte **+ nová** v levém horním rohu.
1. Vyberte **úložiště** v části "Azure Marketplace," pak **účet úložiště – objekt blob, soubor, tabulka, fronta** z pravé strany.
1. Zadejte požadované informace (název, model nasazení a tak dále.
1. Vyberte **vytvořit** proces dokončete.

## <a name="configure-your-app-to-use-the-storage-account"></a>Konfigurace aplikace pro použití účtu úložiště

Po vytvoření účtu úložiště se k němu chce připojit ze sady Visual Studio automaticky aktualizuje konfigurace služby pro projekt, včetně adresy URL a přístupové klíče.

Pokud jste vytvořili cloudové služby pomocí sady Visual Studio **přidat připojenou službu**, připojení můžete zkontrolovat tak, že otevřete `ServiceConfiguration.Cloud.cscfg` a `ServiceConfiguration.Local.cscfg`.

Pokud jste vytvořili cloudové služby na webu Azure portal, postupujte podle stejných kroků v [vytvoření účtu úložiště ze sady Visual Studio](#create-a-storage-account-from-visual-studio) ale vyberte existující účet, spíše než vytvářet novou. Visual Studio pak aktualizuje konfiguraci za vás.

Chcete-li konfigurovat nastavení ručně, použijte stránky vlastností v sadě Visual Studio na příslušné roli v projektu cloudové služby (pravým tlačítkem myši na roli a vyberte **vlastnosti**). Další informace najdete v tématu [konfigurace připojovacího řetězce k účtu úložiště](https://docs.microsoft.com/azure/vs-azure-tools-multiple-services-project-configurations#configuring-a-connection-string-to-a-storage-account).

### <a name="about-access-keys"></a>Informace o přístupových klíčů

Na webu Azure portal zobrazuje adresy URL, můžete použít pro přístup k prostředkům v každém ze služby Azure storage a primární a sekundární přístupové klíče pro váš účet. Tyto klíče použijete k ověření požadavků na přístup služby úložiště.

Sekundární přístupový klíč poskytuje stejný přístup k vašemu účtu úložiště jako primární přístupový klíč a je generována jako záložní primární přístupový klíč k narušení. Kromě toho doporučujeme znovu vygenerovat přístupové klíče ke svému v pravidelných intervalech. Můžete upravit nastavení připojovacího řetězce používat sekundární klíč znovu vygenerovat primární klíč, a můžete je změnit a znovu vygenerovalo primární klíč použít, když znovu vygenerovat sekundární klíč.

## <a name="next-steps"></a>Další postup

Další informace o publikování aplikace do Azure ze sady Visual Studio, naleznete v tématu [publikování cloudové služby pomocí nástroje Azure](vs-azure-tools-publishing-a-cloud-service.md).
